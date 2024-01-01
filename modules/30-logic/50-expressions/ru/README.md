
Во многих языках, для того чтобы внутри вычисляемых выражений использовать логическое ветвление, приходится использовать специфические варианты конструкции `if` (как это сделано в Python) или же вовсе особые *тернарные операторы*.

Здесь лиспоподобные языки — и Racket в частности — обладают одним важным преимуществом: в этих языках всё есть выражение. Поэтому отдельные варианты условных конструкций не нужны, вместо этого можно использовать `if`, `case`, `cond` как часть любого другого выражения! Вот парочка примеров:

```scheme
(displayln (if #t "Ok" "Oops")) ; => Ok
(displayln (when #f "Ok"))      ; => #<void>
```

Заметьте, во втором случае условие для `when` было ложным, поэтому всё выражение `when` вычислилось в специальное "пустое" значение, но тем не менее вычислилось! И функция `displayln` вывела это значение на экран, пусть даже и в таком своеобразном виде.

Если помнить об этом свойстве языка, то можно писать довольно-таки сложные выражения, не выделяя промежуточные вычисления в переменные.

```scheme
(define (classify x)
  (cond
    [(< x 0) "Negative"]
    [(case x
       [(13 42 100500) #t]
       [else #f]) "Special"]
    [else "Boring"]))
```

В этом примере используются сильные стороны `cond` и `case`: первый хорошо справляется с выбором по условию, а второй хорошо проверяет на совпадение с конкретными значениями, выступая при этом в качестве того самого условия для `cond`.

И раз уж речь зашла о выносе подвыражений в переменные, то тут у Racket тоже всё хорошо. В других языках иной раз приходится делать присваивание значений переменным из условной конструкции или обходиться тернарным оператором. В Racket же можно использовать обычный `let`:

```scheme
(define (classify x)
  (let ([is-special
         (case x
           [(13 42 100500) #t]
           [else #f])])
    (cond
      [(< x 0) "Negative"]
      [is-special "Special"]
      [else "Boring"])))
```

> Этот вариант читается лучше, чем предыдущий, но имеет один недостаток: значение `is-special` вычисляется в любом случае, тогда как в предыдущем варианте его вычислять не нужно, если выполнилось условие, проверяющее аргумент на отрицательность.