---
title: Java+MLLib mwe, Naive Bayes for text classification
---

```Java
package ru.stachek66.mwe.ml.spark;

import org.apache.spark.SparkConf;
import org.apache.spark.SparkContext;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.mllib.classification.NaiveBayes;
import org.apache.spark.mllib.classification.NaiveBayesModel;
import org.apache.spark.mllib.feature.HashingTF;
import org.apache.spark.mllib.feature.IDF;
import org.apache.spark.mllib.feature.IDFModel;
import org.apache.spark.mllib.linalg.Vector;
import org.apache.spark.mllib.regression.LabeledPoint;
import scala.Tuple2;
import scala.reflect.ClassTag;
import scala.reflect.ClassTag$;

import java.io.File;
import java.util.ArrayList;
import java.util.Collections;

/**
 * Used dataset
 * http://scikit-learn.org/stable/datasets/twenty_newsgroups.html
 * Yes, the code is not as charming as it could have been,
 * but the intended purpose of it is to get one's hands dirty.
 * <p/>
 * Author:      alexeyev
 * Date:        13.11.15
 */
public class SparkTCE {

    static SparkConf conf = new SparkConf().setMaster("local[4]").setAppName("naive");
    static SparkContext sc = new SparkContext(conf);

    public static void main(String[] args) {

        final File dir = new File("/home/aam/data/20_newsgroup/");

        double label = 0.0;

        // very sh*t, Spark
        ClassTag<LabeledPoint> tag = ClassTag$.MODULE$.apply(LabeledPoint.class);
        JavaRDD<LabeledPoint> df = sc.emptyRDD(tag).toJavaRDD();

        //todo: a better job parallelizing all this        
        for (File subdir : dir.listFiles()) {

            System.out.println("TOPIC " + subdir.getName());
            
            final Double finalLabel = label;

            final JavaRDD<ArrayList<String>> splittedTexts =
                    sc.wholeTextFiles(subdir.getAbsolutePath(), 1)
                            .toJavaRDD()
                            .map(tuple -> {
                                final ArrayList<String> arr = new ArrayList<String>();
                                Collections.addAll(arr, tuple._2().split(" "));
                                return arr;
                            });

            final HashingTF hashingTF = new HashingTF();
            final JavaRDD<Vector> featurizedData = hashingTF.transform(splittedTexts);

            final IDF idf = new IDF();
            final IDFModel idfModel = idf.fit(featurizedData);

            final JavaRDD<LabeledPoint> rescaledData =
                    idfModel
                            .transform(featurizedData)
                            .map(vector -> new LabeledPoint(finalLabel, vector));

            df = df.union(rescaledData);
            label += 1;
        }

        final JavaRDD<LabeledPoint>[] splits = df.randomSplit(new double[]{0.8, 0.2});
        final JavaRDD<LabeledPoint> training = splits[0];
        final JavaRDD<LabeledPoint> test = splits[1];
        final NaiveBayesModel model = NaiveBayes.train(training.rdd(), 1.0);

        final JavaPairRDD<Double, Double> predictionAndLabel =
                test.mapToPair(p -> new Tuple2<>(model.predict(p.features()), p.label()));

        final double accuracy = predictionAndLabel
                .filter(pl -> pl._1().equals(pl._2()))
                .count()
                / (double) test.count();
        System.out.println("ACCURACY " + accuracy);
    }
}

//<dependencies>
//  <dependency>
//      <groupId>org.apache.spark</groupId>
//      <artifactId>spark-core_2.11</artifactId>
//      <version>1.5.1</version>
//  </dependency>
//
//  <dependency>
//      <groupId>org.apache.spark</groupId>
//      <artifactId>spark-mllib_2.11</artifactId>
//      <version>1.5.1</version>
//   </dependency>
//</dependencies>
```
