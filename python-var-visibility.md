---
title: Области видимости переменных в Python 
layout: default
---

В питоне -- как и во многих других языках для прикладного программирования -- 
сильны принципы структурного программирования, одна только обязательность отступов чего стоит.

Структурное программирование -- о делении программ на блоки. Как именно устроена "видимость" переменных
в Python? Что будет, если исопльзовать имена пеерменных во внутренних блоакх? Кажется, как минимум, должно быть верно следующее:

```python
  for i in range(2):
    for i in range(3):
      print(i, end=" ")
    print()
  # напечатает
  # 0 1 2
  # 0 1 2
```

и это так и есть. Компилятор какого-нибудь другого языка увидел бы здесь ошибку, так как переменная, в котооорй хранятся для цикла.
	
Actually, a concise rule for Python Scope resolution, from Learning Python, 3rd. Ed.. (These rules are specific to variable names, not attributes. If you reference it without a period, these rules apply)

LEGB Rule.

L, Local — Names assigned in any way within a function (def or lambda)), and not declared global in that function.

E, Enclosing function locals — Name in the local scope of any and all enclosing functions (def or lambda), from inner to outer.

G, Global (module) — Names assigned at the top-level of a module file, or declared global in a def within the file.

B, Built-in (Python) — Names preassigned in the built-in names module : open,range,SyntaxError,...

So, in the case of

code1
class Foo:
   code2
   def spam.....
      code3
      for code4..:
       code5
       x()
The for loop does not have its own namespace. In LEGB order, the scopes would be

L : local, in the current def.

E : Enclosed function, any enclosing functions (if def spam was in another def)

G : Global. Were there any declared globally in the module?

B : Any builtin x() in Python.
