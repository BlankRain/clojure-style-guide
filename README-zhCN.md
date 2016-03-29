# Clojure Style  论抠脚的正确姿势

> Role models are important.(角色建模是重要的.) <br/>
> -- Officer Alex J. Murphy / RoboCop

 宝宝推出的最佳实践,有助于Clojure程序员维护代码.
 本指南针反应了现实中使用的用法,且用来坚定那些超级棒人们却拒绝实践的理想.


本指南针分割为若干相关部分.宝宝尽量指明它们背后的联系.(如果被省略,那肯定是联系太明显)


宝宝未能详尽所有规范.它们大多数来源于宝宝的职业码农生涯,Clojure社区反馈,或其他优质Clojure资源,比如
["Clojure Programming"](http://www.clojurebook.com/)
和 ["The Joy of Clojure"](http://joyofclojure.com/).


本指南针持续维护中,某些部分缺失或不完整,某些规范缺少范例或范例说服性不强.在不久的将来,都会一一解决.目前,先这么着吧.

请注意,Clojure社区也有一份
[库编码规范](http://dev.clojure.org/display/community/Library+Coding+Standards),

你可以生成一份PDF或HTML文档.用这个神器:
[Transmuter](https://github.com/TechnoGate/transmuter).

还有其他语言的译文哦:

* [英文原著](https://github.com/bbatsov/clojure-style-guide/blob/master/README.md)
* [中国盗版](https://github.com/BlankRain/clojure-style-guide/edit/master/README-zhCN.md)
* [日本盗版](https://github.com/totakke/clojure-style-guide/blob/ja/README.md)
* [韩国盗版](https://github.com/kwakbab/clojure-style-guide/blob/master/README-koKO.md)

## 目录

* [源码组织及布局](#源码组织及布局)
* [语法](#语法)
* [命名](#命名)
* [集合](#集合)
* [重要差异](#重要差异)
* [字符串](#字符串)
* [异常](#异常)
* [宏](#宏)
* [注释](#注释)
    * [注释注解](#注释注解)
* [信条](#信条)
* [工具](#工具)

## 源码组织及布局

> 几乎所有人都觉得："宝宝的最棒,其他的都是渣.".  
> 放弃"宝宝的",这才对了. <br/>
> -- Jerry Coffin (on indentation)



* <a name="spaces"></a>
   **空格** 缩进,别用tab.
<sup>[[link](#spaces)]</sup>

* <a name="body-indentation"></a>
两个空格缩进表体.  它包括所有的 `def` 形式,特殊形式,宏,本地绑定(比如: `loop`, `let`,
`when-let`) 或其他宏 像 `when`, `cond`, `as->`, `cond->`, `case`,
`with-*` 等.
<sup>[[link](#body-indentation)]</sup>


    ```Clojure
    ;; good 好
    (when something
      (something-else))

    (with-out-str
      (println "Hello, ")
      (println "world!"))

    ;; bad - four spaces 坏 四个空格
    (when something
        (something-else))

    ;; bad - one space 坏 一个空格
    (with-out-str
     (println "Hello, ")
     (println "world!"))
    ```

* <a name="vertically-align-fn-args"></a>
  垂直对齐的函数或宏,参数换行
<sup>[[link](#vertically-align-fn-args)]</sup>

    ```Clojure
    ;; good
    (filter even?
            (range 1 10))

    ;; bad
    (filter even?
      (range 1 10))
    ```

* <a name="one-space-indent"></a>
函数或宏,参数间一个空格. 无参的,函数名在一条线上.
<sup>[[link](#one-space-indent)]</sup>

    ```Clojure
    ;; good
    (filter
     even?
     (range 1 10))

    (or
     ala
     bala
     portokala)

    ;; bad - two-space indent
    (filter
      even?
      (range 1 10))

    (or
      ala
      bala
      portokala)
    ```

* <a name="vertically-align-let-and-map"></a>
  垂直对齐 `let`绑定 和 map 关键字.
<sup>[[link](#vertically-align-let-and-map)]</sup>

    ```Clojure
    ;; good
    (let [thing1 "some stuff"
          thing2 "other stuff"]
      {:thing1 thing1
       :thing2 thing2})

    ;; bad
    (let [thing1 "some stuff"
      thing2 "other stuff"]
      {:thing1 thing1
      :thing2 thing2})
    ```

* <a name="optional-new-line-after-fn-name"></a>
  当没有函数文档时,在函数名或参数向量后换行.
<sup>[[link](#optional-new-line-after-fn-name)]</sup>

    ```Clojure
    ;; good
    (defn foo
      [x]
      (bar x))

    ;; good
    (defn foo [x]
      (bar x))

    ;; bad
    (defn foo
      [x] (bar x))
    ```

* <a name="multimethod-dispatch-val-placement"></a>
   多重方法转发值(`dispatch-val`)与函数名一条线上.  
<sup>[[link](#multimethod-dispatch-val-placement)]</sup>


    ```Clojure
    ;; good
    (defmethod foo :bar [x] (baz x))

    (defmethod foo :bar
      [x]
      (baz x))

    ;; bad
    (defmethod foo
      :bar
      [x]
      (baz x))

    (defmethod foo
      :bar [x]
      (baz x))
    ```

* <a name="docstring-after-fn-name"></a>
   给函数加文档的时候,注意不要加错地方了,文档的位置在函数名和参数之间,不是在参数后面.
   放在方法体内,并不会报错,但是,作为函数文档的那个Var 会找不到值的.
<sup>[[link](#docstring-after-fn-name)]</sup>

    ```Clojure
    ;; good
    (defn foo
      "docstring"
      [x]
      (bar x))

    ;; bad
    (defn foo [x]
      "docstring"
      (bar x))
    ```

* <a name="oneline-short-fn"></a>
   简短的函数,方法体和参数放一行.  
<sup>[[link](#oneline-short-fn)]</sup>

    ```Clojure
    ;; good
    (defn foo [x]
      (bar x))

    ;; good for a small function body
    (defn foo [x] (bar x))

    ;; good for multi-arity functions
    (defn foo
      ([x] (bar x))
      ([x y]
       (if (predicate? x)
         (bar x)
         (baz x))))

    ;; bad
    (defn foo
      [x] (if (predicate? x)
            (bar x)
            (baz x)))
    ```

* <a name="multiple-arity-indentation"></a>
   每个函数定义体与它的参数头垂直对齐  
<sup>[[link](#multiple-arity-indentation)]</sup>

    ```Clojure
    ;; good
    (defn foo
      "I have two arities."
      ([x]
       (foo x 1))
      ([x y]
       (+ x y)))

    ;; bad - extra indentation  额外的缩进
    (defn foo
      "I have two arities."
      ([x]
        (foo x 1))
      ([x y]
        (+ x y)))
    ```

* <a name="multiple-arity-order"></a> 函数的实现按传参个数由少到多排序. 通常情况下,函数的多重实现,是由一个K个参数的实现全方位决定了整个函数的行为. 然后呢,其他N参的实现是通过给这个K参函数传不同的参数.
  <sup>[[link](#multiple-arity-order)]</sup>

    ```Clojure
    ;; good - it's easy to scan for the nth arity 查找第N个实现很方便
    (defn foo
      "I have two arities."
      ([x]
       (foo x 1))
      ([x y]
       (+ x y)))

    ;; okay - the other arities are applications of the two-arity
    (defn foo
      "I have two arities."
      ([x y]
       (+ x y))
      ([x]
       (foo x 1))
      ([x y z & more]
       (reduce foo (foo x (foo y z)) more)))

    ;; bad - unordered for no apparent reason
    (defn foo
      ([x] 1)
      ([x y z] (foo x (foo y z)))
      ([x y] (+ x y))
      ([w x y z & more] (reduce foo (foo w (foo x (foo y z))) more)))
    ```

* <a name="align-docstring-lines"></a>
  多行文档,每行都缩进
<sup>[[link](#align-docstring-lines)]</sup>

    ```Clojure
    ;; good
    (defn foo
      "Hello there. This is
      a multi-line docstring."
      []
      (bar))

    ;; bad
    (defn foo
      "Hello there. This is
    a multi-line docstring."
      []
      (bar))
    ```

* <a name="crlf"></a>
  使用Unix-风格换行符. (*BSD/Solaris/Linux/OSX 用户默认就是, Windows 用户得额外小心了.)
<sup>[[link](#crlf)]</sup>

    * 如果你使用git,你可能想通过添加如下配置来保护你的项目免受windows 行尾符的乱入.

    ```
    bash$ git config --global core.autocrlf true
    ```

* <a name="bracket-spacing"></a>
  If any text precedes an opening bracket(`(`, `{` and
`[`) or follows a closing bracket(`)`, `}` and `]`), separate that
text from that bracket with a space. Conversely, leave no space after
an opening bracket and before following text, or after preceding text
and before a closing bracket.
<sup>[[link](#bracket-spacing)]</sup>

    ```Clojure
    ;; good
    (foo (bar baz) quux)

    ;; bad
    (foo(bar baz)quux)
    (foo ( bar baz ) quux)
    ```

> Syntactic sugar causes semicolon cancer. <br/>
> -- Alan Perlis

* <a name="no-commas-for-seq-literals"></a>
  序列集合不用逗号分隔
<sup>[[link](#no-commas-for-seq-literals)]</sup>

    ```Clojure
    ;; good
    [1 2 3]
    (1 2 3)

    ;; bad
    [1, 2, 3]
    (1, 2, 3)
    ```

* <a name="opt-commas-in-map-literals"></a>
  Consider enhancing the readability of map literals via judicious use
of commas and line breaks.
<sup>[[link](#opt-commas-in-map-literals)]</sup>

    ```Clojure
    ;; good
    {:name "Bruce Wayne" :alter-ego "Batman"}

    ;; good and arguably a bit more readable
    {:name "Bruce Wayne"
     :alter-ego "Batman"}

    ;; good and arguably more compact
    {:name "Bruce Wayne", :alter-ego "Batman"}
    ```

* <a name="gather-trailing-parens"></a>
  Place all trailing parentheses on a single line instead of distinct lines.
<sup>[[link](#gather-trailing-parens)]</sup>

    ```Clojure
    ;; good; single line
    (when something
      (something-else))

    ;; bad; distinct lines
    (when something
      (something-else)
    )
    ```

* <a name="empty-lines-between-top-level-forms"></a>
  Use empty lines between top-level forms.
<sup>[[link](#empty-lines-between-top-level-forms)]</sup>

    ```Clojure
    ;; good
    (def x ...)

    (defn foo ...)

    ;; bad
    (def x ...)
    (defn foo ...)
    ```

    An exception to the rule is the grouping of related `def`s together.

    ```Clojure
    ;; good
    (def min-rows 10)
    (def max-rows 20)
    (def min-cols 15)
    (def max-cols 30)
    ```

* <a name="no-blank-lines-within-def-forms"></a>
  Do not place blank lines in the middle of a function or
macro definition.  An exception can be made to indicate grouping of
pairwise constructs as found in e.g. `let` and `cond`.
<sup>[[link](#no-blank-lines-within-def-forms)]</sup>

* <a name="80-character-limits"></a>
  Where feasible, avoid making lines longer than 80 characters.
<sup>[[link](#80-character-limits)]</sup>

* <a name="no-trailing-whitespace"></a>
  Avoid trailing whitespace.
<sup>[[link](#no-trailing-whitespace)]</sup>

* <a name="one-file-per-namespace"></a>
  Use one file per namespace.
<sup>[[link](#one-file-per-namespace)]</sup>

* <a name="comprehensive-ns-declaration"></a>
  Start every namespace with a comprehensive `ns` form, comprised of
  `refer`s, `require`s, and `import`s, conventionally in that order.
<sup>[[link](#comprehensive-ns-declaration)]</sup>

    ```Clojure
    (ns examples.ns
      (:refer-clojure :exclude [next replace remove])
      (:require [clojure.string :as s :refer [blank?]]
                [clojure.set :as set]
                [clojure.java.shell :as sh])
      (:import java.util.Date
               java.text.SimpleDateFormat
               [java.util.concurrent Executors
                                     LinkedBlockingQueue]))
    ```

* <a name="prefer-require-over-use"></a>
  In the `ns` form prefer `:require :as` over `:require :refer` over `:require
  :refer :all`.  Prefer `:require` over `:use`; the latter form should be
  considered deprecated for new code.
<sup>[[link](#prefer-require-over-use)]</sup>

    ```Clojure
    ;; good
    (ns examples.ns
      (:require [clojure.zip :as zip]))

    ;; good
    (ns examples.ns
      (:require [clojure.zip :refer [lefts rights]))

    ;; acceptable as warranted
    (ns examples.ns
      (:require [clojure.zip :refer :all]))

    ;; bad
    (ns examples.ns
      (:use clojure.zip))
    ```

* <a name="no-single-segment-namespaces"></a>
  Avoid single-segment namespaces.
<sup>[[link](#no-single-segment-namespaces)]</sup>

    ```Clojure
    ;; good
    (ns example.ns)

    ;; bad
    (ns example)
    ```

* <a name="namespaces-with-5-segments-max"></a>
  Avoid the use of overly long namespaces (i.e., more than 5 segments).
<sup>[[link](#namespaces-with-5-segments-max)]</sup>

* <a name="10-loc-per-fn-limit"></a>
  Avoid functions longer than 10 LOC (lines of code). Ideally, most
  functions will be shorter than 5 LOC.
<sup>[[link](#10-loc-per-fn-limit)]</sup>

* <a name="4-positional-fn-params-limit"></a>
  Avoid parameter lists with more than three or four positional parameters.
<sup>[[link](#4-positional-fn-params-limit)]</sup>

* <a name="forward-references"></a>
  Avoid forward references.  They are occasionally necessary, but such occasions
  are rare in practice.
<sup>[[link](#forward-references)]</sup>

## Syntax

* <a name="ns-fns-only-in-repl"></a>
  Avoid the use of namespace-manipulating functions like `require` and
  `refer`. They are entirely unnecessary outside of a REPL
  environment.
<sup>[[link](#ns-fns-only-in-repl)]</sup>

* <a name="declare"></a>
  Use `declare` to enable forward references when forward references are
  necessary.
<sup>[[link](#declare)]</sup>

* <a name="higher-order-fns"></a>
  Prefer higher-order functions like `map` to `loop/recur`.
<sup>[[link](#higher-order-fns)]</sup>

* <a name="pre-post-conditions"></a>
  Prefer function pre and post conditions to checks inside a function's body.
<sup>[[link](#pre-post-conditions)]</sup>

    ```Clojure
    ;; good
    (defn foo [x]
      {:pre [(pos? x)]}
      (bar x))

    ;; bad
    (defn foo [x]
      (if (pos? x)
        (bar x)
        (throw (IllegalArgumentException. "x must be a positive number!")))
    ```

* <a name="dont-def-vars-inside-fns"></a>
  Don't define vars inside functions.
<sup>[[link](#dont-def-vars-inside-fns)]</sup>

    ```Clojure
    ;; very bad
    (defn foo []
      (def x 5)
      ...)
    ```

* <a name="dont-shadow-clojure-core"></a>
  Don't shadow `clojure.core` names with local bindings.
<sup>[[link](#dont-shadow-clojure-core)]</sup>

    ```Clojure
    ;; bad - you're forced to use clojure.core/map fully qualified inside
    (defn foo [map]
      ...)
    ```

* <a name="alter-var"></a>
  Use `alter-var-root` instead of `def` to change the value of a var.
<sup>[[link]](#alter-var)</sup>

    ```Clojure
    ;; good
    (def thing 1) ; value of thing is now 1
    ; do some stuff with thing
    (alter-var-root #'thing (constantly nil)) ; value of thing is now nil

    ;; bad
    (def thing 1)
    ; do some stuff with thing
    (def thing nil)
    ; value of thing is now nil
    ```

* <a name="nil-punning"></a>
  Use `seq` as a terminating condition to test whether a sequence is
  empty (this technique is sometimes called *nil punning*).
<sup>[[link](#nil-punning)]</sup>

    ```Clojure
    ;; good
    (defn print-seq [s]
      (when (seq s)
        (prn (first s))
        (recur (rest s))))

    ;; bad
    (defn print-seq [s]
      (when-not (empty? s)
        (prn (first s))
        (recur (rest s))))
    ```

* <a name="to-vector"></a>
  Prefer `vec` over `into` when you need to convert a sequence into a vector.
<sup>[[link](#to-vector)]</sup>

    ```Clojure
    ;; good
    (vec some-seq)

    ;; bad
    (into [] some-seq)
    ```

* <a name="when-instead-of-single-branch-if"></a>
  Use `when` instead of `(if ... (do ...)`.
<sup>[[link](#when-instead-of-single-branch-if)]</sup>

    ```Clojure
    ;; good
    (when pred
      (foo)
      (bar))

    ;; bad
    (if pred
      (do
        (foo)
        (bar)))
    ```

* <a name="if-let"></a>
  Use `if-let` instead of `let` + `if`.
<sup>[[link](#if-let)]</sup>

    ```Clojure
    ;; good
    (if-let [result (foo x)]
      (something-with result)
      (something-else))

    ;; bad
    (let [result (foo x)]
      (if result
        (something-with result)
        (something-else)))
    ```

* <a name="when-let"></a>
  Use `when-let` instead of `let` + `when`.
<sup>[[link](#when-let)]</sup>

    ```Clojure
    ;; good
    (when-let [result (foo x)]
      (do-something-with result)
      (do-something-more-with result))

    ;; bad
    (let [result (foo x)]
      (when result
        (do-something-with result)
        (do-something-more-with result)))
    ```

* <a name="if-not"></a>
  Use `if-not` instead of `(if (not ...) ...)`.
<sup>[[link](#if-not)]</sup>

    ```Clojure
    ;; good
    (if-not pred
      (foo))

    ;; bad
    (if (not pred)
      (foo))
    ```

* <a name="when-not"></a>
  Use `when-not` instead of `(when (not ...) ...)`.
<sup>[[link](#when-not)]</sup>

    ```Clojure
    ;; good
    (when-not pred
      (foo)
      (bar))

    ;; bad
    (when (not pred)
      (foo)
      (bar))
    ```

* <a name="when-not-instead-of-single-branch-if-not"></a>
  Use `when-not` instead of `(if-not ... (do ...)`.
<sup>[[link](#when-not-instead-of-single-branch-if-not)]</sup>

    ```Clojure
    ;; good
    (when-not pred
      (foo)
      (bar))

    ;; bad
    (if-not pred
      (do
        (foo)
        (bar)))
    ```

* <a name="not-equal"></a>
  Use `not=` instead of `(not (= ...))`.
<sup>[[link](#not-equal)]</sup>

    ```Clojure
    ;; good
    (not= foo bar)

    ;; bad
    (not (= foo bar))
    ```

* <a name="printf"></a>
  Use `printf` instead of `(print (format ...))`.
<sup>[[link](#printf)]</sup>

    ```Clojure
    ;; good
    (printf "Hello, %s!\n" name)

    ;; ok
    (println (format "Hello, %s!" name))
    ```

* <a name="multiple-arity-of-gt-and-ls-fns"></a>
  When doing comparisons, keep in mind that Clojure's functions `<`,
  `>`, etc. accept a variable number of arguments.
<sup>[[link](#multiple-arity-of-gt-and-ls-fns)]</sup>

    ```Clojure
    ;; good
    (< 5 x 10)

    ;; bad
    (and (> x 5) (< x 10))
    ```

* <a name="single-param-fn-literal"></a>
  Prefer `%` over `%1` in function literals with only one parameter.
<sup>[[link](#single-param-fn-literal)]</sup>

    ```Clojure
    ;; good
    #(Math/round %)

    ;; bad
    #(Math/round %1)
    ```

* <a name="multiple-params-fn-literal"></a>
  Prefer `%1` over `%` in function literals with more than one parameter.
<sup>[[link](#multiple-params-fn-literal)]</sup>

    ```Clojure
    ;; good
    #(Math/pow %1 %2)

    ;; bad
    #(Math/pow % %2)
    ```

* <a name="no-useless-anonymous-fns"></a>
  Don't wrap functions in anonymous functions when you don't need to.
<sup>[[link](#no-useless-anonymous-fns)]</sup>

    ```Clojure
    ;; good
    (filter even? (range 1 10))

    ;; bad
    (filter #(even? %) (range 1 10))
    ```

* <a name="no-multiple-forms-fn-literals"></a>
  Don't use function literals if the function body will consist of
  more than one form.
<sup>[[link](#no-multiple-forms-fn-literals)]</sup>

    ```Clojure
    ;; good
    (fn [x]
      (println x)
      (* x 2))

    ;; bad (you need an explicit do form)
    #(do (println %)
         (* % 2))
    ```

* <a name="complement"></a>
  Favor the use of `complement` versus the use of an anonymous function.
<sup>[[link](#complement)]</sup>

    ```Clojure
    ;; good
    (filter (complement some-pred?) coll)

    ;; bad
    (filter #(not (some-pred? %)) coll)
    ```

    This rule should obviously be ignored if the complementing predicate
    exists in the form of a separate function (e.g. `even?` and `odd?`).

* <a name="comp"></a>
  Leverage `comp` when it would yield simpler code.
<sup>[[link](#comp)]</sup>

    ```Clojure
    ;; Assuming `(:require [clojure.string :as str])`...

    ;; good
    (map #(str/capitalize (str/trim %)) ["top " " test "])

    ;; better
    (map (comp str/capitalize str/trim) ["top " " test "])
    ```

* <a name="partial"></a>
  Leverage `partial` when it would yield simpler code.
<sup>[[link](#partial)]</sup>

    ```Clojure
    ;; good
    (map #(+ 5 %) (range 1 10))

    ;; (arguably) better
    (map (partial + 5) (range 1 10))
    ```

* <a name="threading-macros"></a>
  Prefer the use of the threading macros `->` (thread-first) and `->>`
(thread-last) to heavy form nesting.
<sup>[[link](#threading-macros)]</sup>

    ```Clojure
    ;; good
    (-> [1 2 3]
        reverse
        (conj 4)
        prn)

    ;; not as good
    (prn (conj (reverse [1 2 3])
               4))

    ;; good
    (->> (range 1 10)
         (filter even?)
         (map (partial * 2)))

    ;; not as good
    (map (partial * 2)
         (filter even? (range 1 10)))
    ```

* <a name="else-keyword-in-cond"></a>
  Use `:else` as the catch-all test expression in `cond`.
<sup>[[link](#else-keyword-in-cond)]</sup>

    ```Clojure
    ;; good
    (cond
      (< n 0) "negative"
      (> n 0) "positive"
      :else "zero"))

    ;; bad
    (cond
      (< n 0) "negative"
      (> n 0) "positive"
      true "zero"))
    ```

* <a name="condp"></a>
  Prefer `condp` instead of `cond` when the predicate & expression don't
  change.
<sup>[[link](#condp)]</sup>

    ```Clojure
    ;; good
    (cond
      (= x 10) :ten
      (= x 20) :twenty
      (= x 30) :thirty
      :else :dunno)

    ;; much better
    (condp = x
      10 :ten
      20 :twenty
      30 :thirty
      :dunno)
    ```

* <a name="case"></a>
  Prefer `case` instead of `cond` or `condp` when test expressions are
compile-time constants.
<sup>[[link](#case)]</sup>

    ```Clojure
    ;; good
    (cond
      (= x 10) :ten
      (= x 20) :twenty
      (= x 30) :forty
      :else :dunno)

    ;; better
    (condp = x
      10 :ten
      20 :twenty
      30 :forty
      :dunno)

    ;; best
    (case x
      10 :ten
      20 :twenty
      30 :forty
      :dunno)
    ```

* <a name="shor-forms-in-cond"></a>
  Use short forms in `cond` and related.  If not possible give visual
hints for the pairwise grouping with comments or empty lines.
<sup>[[link](#shor-forms-in-cond)]</sup>

    ```Clojure
    ;; good
    (cond
      (test1) (action1)
      (test2) (action2)
      :else   (default-action))

    ;; ok-ish
    (cond
      ;; test case 1
      (test1)
      (long-function-name-which-requires-a-new-line
        (complicated-sub-form
          (-> 'which-spans multiple-lines)))

      ;; test case 2
      (test2)
      (another-very-long-function-name
        (yet-another-sub-form
          (-> 'which-spans multiple-lines)))

      :else
      (the-fall-through-default-case
        (which-also-spans 'multiple
                          'lines)))
    ```

* <a name="set-as-predicate"></a>
  Use a `set` as a predicate when appropriate.
<sup>[[link](#set-as-predicate)]</sup>

    ```Clojure
    ;; good
    (remove #{1} [0 1 2 3 4 5])

    ;; bad
    (remove #(= % 1) [0 1 2 3 4 5])

    ;; good
    (count (filter #{\a \e \i \o \u} "mary had a little lamb"))

    ;; bad
    (count (filter #(or (= % \a)
                        (= % \e)
                        (= % \i)
                        (= % \o)
                        (= % \u))
                   "mary had a little lamb"))
    ```

* <a name="inc-and-dec"></a>
  Use `(inc x)` & `(dec x)` instead of `(+ x 1)` and `(- x 1)`.
<sup>[[link](#inc-and-dec)]</sup>

* <a name="pos-and-neg"></a>
  Use `(pos? x)`, `(neg? x)` & `(zero? x)` instead of `(> x 0)`,
`(< x 0)` & `(= x 0)`.
<sup>[[link](#pos-and-neg)]</sup>

* <a name="list-star-instead-of-nested-cons"></a>
  Use `list*` instead of a series of nested `cons` invocations.
<sup>[[link](#list-star-instead-of-nested-cons)]</sup>

    ```Clojure
    ;; good
    (list* 1 2 3 [4 5])

    ;; bad
    (cons 1 (cons 2 (cons 3 [4 5])))
    ```

* <a name="sugared-java-interop"></a>
  Use the sugared Java interop forms.
<sup>[[link](#sugared-java-interop)]</sup>

    ```Clojure
    ;;; object creation
    ;; good
    (java.util.ArrayList. 100)

    ;; bad
    (new java.util.ArrayList 100)

    ;;; static method invocation
    ;; good
    (Math/pow 2 10)

    ;; bad
    (. Math pow 2 10)

    ;;; instance method invocation
    ;; good
    (.substring "hello" 1 3)

    ;; bad
    (. "hello" substring 1 3)

    ;;; static field access
    ;; good
    Integer/MAX_VALUE

    ;; bad
    (. Integer MAX_VALUE)

    ;;; instance field access
    ;; good
    (.someField some-object)

    ;; bad
    (. some-object someField)
    ```

* <a name="compact-metadata-notation-for-true-flags"></a>
  Use the compact metadata notation for metadata that contains only
  slots whose keys are keywords and whose value is boolean `true`.
<sup>[[link](#compact-metadata-notation-for-true-flags)]</sup>

    ```Clojure
    ;; good
    (def ^:private a 5)

    ;; bad
    (def ^{:private true} a 5)
    ```

* <a name="private"></a>
  Denote private parts of your code.
<sup>[[link](#private)]</sup>

    ```Clojure
    ;; good
    (defn- private-fun [] ...)

    (def ^:private private-var ...)

    ;; bad
    (defn private-fun [] ...) ; not private at all

    (defn ^:private private-fun [] ...) ; overly verbose

    (def private-var ...) ; not private at all
    ```

* <a name="access-private-var"></a>
  To access a private var (e.g. for testing), use the `@#'some.ns/var` form.
<sup>[[link](#access-private-var)]</sup>

* <a name="attach-metadata-carefully"></a>
  Be careful regarding what exactly do you attach metadata to.
<sup>[[link](#attach-metadata-carefully)]</sup>

    ```Clojure
    ;; we attach the metadata to the var referenced by `a`
    (def ^:private a {})
    (meta a) ;=> nil
    (meta #'a) ;=> {:private true}

    ;; we attach the metadata to the empty hash-map value
    (def a ^:private {})
    (meta a) ;=> {:private true}
    (meta #'a) ;=> nil
    ```

## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* <a name="ns-naming-schemas"></a>
  When naming namespaces favor the following two schemas:
<sup>[[link](#ns-naming-schemas)]</sup>

    * `project.module`
    * `organization.project.module`

* <a name="lisp-case-ns"></a>
  Use `lisp-case` in composite namespace segments(e.g. `bruce.project-euler`)
<sup>[[link](#lisp-case-ns)]</sup>

* <a name="lisp-case"></a>
  Use `lisp-case` for function and variable names.
<sup>[[link](#lisp-case)]</sup>

    ```Clojure
    ;; good
    (def some-var ...)
    (defn some-fun ...)

    ;; bad
    (def someVar ...)
    (defn somefun ...)
    (def some_fun ...)
    ```

* <a name="CamelCase-for-protocols-records-structs-and-types"></a>
  Use `CamelCase` for protocols, records, structs, and types. (Keep
  acronyms like HTTP, RFC, XML uppercase.)
<sup>[[link](#CamelCase-for-protocols-records-structs-and-types)]</sup>

* <a name="pred-with-question-mark"></a>
  The names of predicate methods (methods that return a boolean value)
  should end in a question mark
  (e.g., `even?`).
<sup>[[link](#pred-with-question-mark)]</sup>

    ```Clojure
    ;; good
    (defn palindrome? ...)

    ;; bad
    (defn palindrome-p ...) ; Common Lisp style
    (defn is-palindrome ...) ; Java style
    ```

* <a name="changing-state-fns-with-exclamation-mark"></a>
  The names of functions/macros that are not safe in STM transactions
  should end with an exclamation mark (e.g. `reset!`).
<sup>[[link](#changing-state-fns-with-exclamation-mark)]</sup>

* <a name="arrow-instead-of-to"></a>
  Use `->` instead of `to` in the names of conversion functions.
<sup>[[link](#arrow-instead-of-to)]</sup>

    ```Clojure
    ;; good
    (defn f->c ...)

    ;; not so good
    (defn f-to-c ...)
    ```

* <a name="earmuffs-for-dynamic-vars"></a>
  Use `*earmuffs*` for things intended for rebinding (ie. are dynamic).
<sup>[[link](#earmuffs-for-dynamic-vars)]</sup>

    ```Clojure
    ;; good
    (def ^:dynamic *a* 10)

    ;; bad
    (def ^:dynamic a 10)
    ```

* <a name="dont-flag-constants"></a>
  Don't use a special notation for constants; everything is assumed a constant
  unless specified otherwise.
<sup>[[link](#dont-flag-constants)]</sup>

* <a name="underscore-for-unused-bindings"></a>
  Use `_` for destructuring targets and formal argument names whose
  value will be ignored by the code at hand.
<sup>[[link](#underscore-for-unused-bindings)]</sup>

    ```Clojure
    ;; good
    (let [[a b _ c] [1 2 3 4]]
      (println a b c))

    (dotimes [_ 3]
      (println "Hello!"))

    ;; bad
    (let [[a b c d] [1 2 3 4]]
      (println a b d))

    (dotimes [i 3]
      (println "Hello!"))
    ```

* <a name="idiomatic-names"></a>
  Follow `clojure.core`'s example for idiomatic names like `pred` and `coll`.
<sup>[[link](#idiomatic-names)]</sup>

    * in functions:
        * `f`, `g`, `h` - function input
        * `n` - integer input usually a size
        * `index`, `i` - integer index
        * `x`, `y` - numbers
        * `xs` - sequence
        * `m` - map
        * `s` - string input
        * `re` - regular expression
        * `coll` - a collection
        * `pred` - a predicate closure
        * `& more` - variadic input
        * `xf` - xform, a transducer
    * in macros:
        * `expr` - an expression
        * `body` - a macro body
        * `binding` - a macro binding vector

## Collections

> It is better to have 100 functions operate on one data structure
> than to have 10 functions operate on 10 data structures. <br/>
> -- Alan J. Perlis

* <a name="avoid-lists"></a>
  Avoid the use of lists for generic data storage (unless a list is
  exactly what you need).
<sup>[[link](#avoid-lists)]</sup>

* <a name="keywords-for-hash-keys"></a>
  Prefer the use of keywords for hash keys.
<sup>[[link](#keywords-for-hash-keys)]</sup>

    ```Clojure
    ;; good
    {:name "Bruce" :age 30}

    ;; bad
    {"name" "Bruce" "age" 30}
    ```

* <a name="literal-col-syntax"></a>
  Prefer the use of the literal collection syntax where
  applicable. However, when defining sets, only use literal syntax
  when the values are compile-time constants.
<sup>[[link](#literal-col-syntax)]</sup>

    ```Clojure
    ;; good
    [1 2 3]
    #{1 2 3}
    (hash-set (func1) (func2)) ; values determined at runtime

    ;; bad
    (vector 1 2 3)
    (hash-set 1 2 3)
    #{(func1) (func2)} ; will throw runtime exception if (func1) = (func2)
    ```

* <a name="avoid-index-based-coll-access"></a>
  Avoid accessing collection members by index whenever possible.
<sup>[[link](#avoid-index-based-coll-access)]</sup>

* <a name="keywords-as-fn-to-get-map-values"></a>
  Prefer the use of keywords as functions for retrieving values from
  maps, where applicable.
<sup>[[link](#keywords-as-fn-to-get-map-values)]</sup>

    ```Clojure
    (def m {:name "Bruce" :age 30})

    ;; good
    (:name m)

    ;; more verbose than necessary
    (get m :name)

    ;; bad - susceptible to NullPointerException
    (m :name)
    ```

* <a name="colls-as-fns"></a>
  Leverage the fact that most collections are functions of their elements.
<sup>[[link](#colls-as-fns)]</sup>

    ```Clojure
    ;; good
    (filter #{\a \e \o \i \u} "this is a test")

    ;; bad - too ugly to share
    ```

* <a name="keywords-as-fns"></a>
  Leverage the fact that keywords can be used as functions of a collection.
<sup>[[link](#keywords-as-fns)]</sup>

    ```Clojure
    ((juxt :a :b) {:a "ala" :b "bala"})
    ```

* <a name="avoid-transient-colls"></a>
  Avoid the use of transient collections, except for
performance-critical portions of the code.
<sup>[[link](#avoid-transient-colls)]</sup>

* <a name="avoid-java-colls"></a>
  避免使用Java集合
<sup>[[link](#avoid-java-colls)]</sup>

* <a name="avoid-java-arrays"></a>
  除非Java互操作或性能原因,避免使用Java数组.
<sup>[[link](#avoid-java-arrays)]</sup>

## Mutation

### Refs

* <a name="refs-io-macro"></a>
  Consider wrapping all I/O calls with the `io!` macro to avoid nasty
surprises if you accidentally end up calling such code in a
transaction.
<sup>[[link](#refs-io-macro)]</sup>

* <a name="refs-avoid-ref-set"></a>
  Avoid the use of `ref-set` whenever possible.
<sup>[[link](#refs-avoid-ref-set)]</sup>

    ```Clojure
    (def r (ref 0))

    ;; good
    (dosync (alter r + 5))

    ;; bad
    (dosync (ref-set r 5))
    ```

* <a name="refs-small-transactions"></a>
  Try to keep the size of transactions (the amount of work encapsulated in them)
as small as possible.
<sup>[[link](#refs-small-transactions)]</sup>

* <a name="refs-avoid-short-long-transactions-with-same-ref"></a>
  Avoid having both short- and long-running transactions interacting
  with the same Ref.
<sup>[[link](#refs-avoid-short-long-transactions-with-same-ref)]</sup>

### Agents

* <a name="agents-send"></a>
  Use `send` only for actions that are CPU bound and don't block on I/O
  or other threads.
<sup>[[link](#agents-send)]</sup>

* <a name="agents-send-off"></a>
  Use `send-off` for actions that might block, sleep, or otherwise tie
  up the thread.
<sup>[[link](#agents-send-off)]</sup>

### Atoms

* <a name="atoms-no-update-within-transactions"></a>
  STM事务里面,避免Atom类型的更新.
<sup>[[link](#atoms-no-update-within-transactions)]</sup>

* <a name="atoms-prefer-swap-over-reset"></a>
 尽可能使用 `swap!` 而不是 `reset!`.
<sup>[[link](#atoms-prefer-swap-over-reset)]</sup>

    ```Clojure
    (def a (atom 0))

    ;; good
    (swap! a + 5)

    ;; not as good
    (reset! a 5)
    ```

## 字符串

* <a name="prefer-clojure-string-over-interop"></a>
  字符串操作函数, `clojure.string`库 胜过 Java交互 胜过 自己撸.
<sup>[[link](#prefer-clojure-string-over-interop)]</sup>

    ```Clojure
    ;; good
    (clojure.string/upper-case "bruce")

    ;; bad
    (.toUpperCase "bruce")
    ```

## 异常

* <a name="reuse-existing-exception-types"></a>
  重用现有异常类型. 一般Clojure代码里 &mdash;若真的需要丢异常 &mdash; 丢个标准类型的异常
  (比如. `java.lang.IllegalArgumentException`,
  `java.lang.UnsupportedOperationException`,
  `java.lang.IllegalStateException`, `java.io.IOException`).
<sup>[[link](#reuse-existing-exception-types)]</sup>

* <a name="prefer-with-open-over-finally"></a>
  宝宝喜欢 `with-open` 然后 `finally`.
<sup>[[link](#prefer-with-open-over-finally)]</sup>

## 宏

* <a name="dont-write-macro-if-fn-will-do"></a>
  函数能搞定的不写宏
<sup>[[link](#dont-write-macro-if-fn-will-do)]</sup>

* <a name="write-macro-usage-before-writing-the-macro"></a>
  先写个宏的使用示例,再写宏
<sup>[[link](#write-macro-usage-before-writing-the-macro)]</sup>

* <a name="break-complicated-macros"></a>
  如若可能,把宏拆分成小的函数
<sup>[[link](#break-complicated-macros)]</sup>

* <a name="macros-as-syntactic-sugar"></a>
  宏通常是用来提供语法糖.且宏的核心应是个简单函数.这样干,可提升组合性.
<sup>[[link](#macros-as-syntactic-sugar)]</sup>

* <a name="syntax-quoted-forms"></a>
  比起手撸列表,更推荐语法引用形式.
<sup>[[link](#syntax-quoted-forms)]</sup>

## 注释



> 我是好代码,我为自己代言. 
> 过多的解释意味着提升的空间.
> 提升它.
> 文档上让它更清晰. <br/>
> -- 史蒂芬 麦克 科奈尔

* <a name="self-documenting-code"></a>
  努力让你的代码自注释.
<sup>[[link](#self-documenting-code)]</sup>

* <a name="four-semicolons-for-heading-comments"></a>
 注释标题四个分号开头
<sup>[[link](#four-semicolons-for-heading-comments)]</sup>
 等级注释三个分号开头
* <a name="three-semicolons-for-top-level-comments"></a>
 
<sup>[[link](#three-semicolons-for-top-level-comments)]</sup>

* <a name="two-semicolons-for-code-fragment"></a>
 在你要注释的代码段前写注释. 两个分号开头,然后对齐.  

<sup>[[link](#two-semicolons-for-code-fragment)]</sup>

* <a name="one-semicolon-for-margin-comments"></a>
  边沿注释一个分号开头.
<sup>[[link](#one-semicolon-for-margin-comments)]</sup>

* <a name="semicolon-space"></a>
  分号和文本间总是一个空格.
<sup>[[link](#semicolon-space)]</sup>

    ```Clojure
    ;;;; Frob Grovel

    ;;; This section of code has some important implications:
    ;;;   1. Foo.
    ;;;   2. Bar.
    ;;;   3. Baz.

    (defn fnord [zarquon]
      ;; If zob, then veeblefitz.
      (quux zot
            mumble             ; Zibblefrotz.
            frotz))
    ```

* <a name="english-syntax"></a>
  Comments longer than a word begin with a capital letter and use
  punctuation. Separate sentences with
  [one space](http://en.wikipedia.org/wiki/Sentence_spacing).
<sup>[[link](#english-syntax)]</sup>

* <a name="no-superfluous-comments"></a>
  Avoid superfluous comments.
<sup>[[link](#no-superfluous-comments)]</sup>

    ```Clojure
    ;; bad
    (inc counter) ; increments counter by one
    ```

* <a name="comment-upkeep"></a>
  Keep existing comments up-to-date. An outdated comment is worse than no comment
at all.
<sup>[[link](#comment-upkeep)]</sup>

* <a name="dash-underscore-reader-macro"></a>
  Prefer the use of the `#_` reader macro over a regular comment when
you need to comment out a particular form.
<sup>[[link](#dash-underscore-reader-macro)]</sup>

    ```Clojure
    ;; good
    (+ foo #_(bar x) delta)

    ;; bad
    (+ foo
       ;; (bar x)
       delta)
    ```

> Good code is like a good joke - it needs no explanation. <br/>
> -- Russ Olsen

* <a name="refactor-dont-comment"></a>
  Avoid writing comments to explain bad code. Refactor the code to
  make it self-explanatory. ("Do, or do not. There is no try." --Yoda)
<sup>[[link](#refactor-dont-comment)]</sup>

### Comment Annotations

* <a name="annotate-above"></a>
  Annotations should usually be written on the line immediately above
  the relevant code.
<sup>[[link](#annotate-above)]</sup>

* <a name="annotate-keywords"></a>
  The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
<sup>[[link](#annotate-keywords)]</sup>

* <a name="indent-annotations"></a>
  If multiple lines are required to describe the problem, subsequent
  lines should be indented as much as the first one.
<sup>[[link](#indent-annotations)]</sup>

* <a name="sing-and-date-annotations"></a>
  Tag the annotation with your initials and a date so its relevance can
  be easily verified.
<sup>[[link](#sing-and-date-annotations)]</sup>

    ```Clojure
    (defn some-fun
      []
      ;; FIXME: This has crashed occasionally since v1.2.3. It may
      ;;        be related to the BarBazUtil upgrade. (xz 13-1-31)
      (baz))
    ```

* <a name="rare-eol-annotations"></a>
  In cases where the problem is so obvious that any documentation would
  be redundant, annotations may be left at the end of the offending line
  with no note. This usage should be the exception and not the rule.
<sup>[[link](#rare-eol-annotations)]</sup>

    ```Clojure
    (defn bar
      []
      (sleep 100)) ; OPTIMIZE
    ```

* <a name="todo"></a>
  Use `TODO` to note missing features or functionality that should be
  added at a later date.
<sup>[[link](#todo)]</sup>

* <a name="fixme"></a>
  Use `FIXME` to note broken code that needs to be fixed.
<sup>[[link](#fixme)]</sup>

* <a name="optimize"></a>
  Use `OPTIMIZE` to note slow or inefficient code that may cause
  performance problems.
<sup>[[link](#optimize)]</sup>

* <a name="hack"></a>
  Use `HACK` to note "code smells" where questionable coding practices
  were used and should be refactored away.
<sup>[[link](#hack)]</sup>

* <a name="review"></a>
  Use `REVIEW` to note anything that should be looked at to confirm it
  is working as intended. For example: `REVIEW: Are we sure this is how the
  client does X currently?`
<sup>[[link](#review)]</sup>

* <a name="document-annotations"></a>
  Use other custom annotation keywords if it feels appropriate, but be
  sure to document them in your project's `README` or similar.
<sup>[[link](#document-annotations)]</sup>

## 信条

* <a name="be-functional"></a>
  函数式编码 , 只在需要变的时候变.
<sup>[[link](#be-functional)]</sup>

* <a name="be-consistent"></a>
  坚定信仰. 理想世界, 坚持我们的信仰.
<sup>[[link](#be-consistent)]</sup>

* <a name="common-sense"></a>
  使用常识
<sup>[[link](#common-sense)]</sup>

## 工具

君子性非异也,善假于物也. 社区里面有好些神器,有助于你码出姿势常见的Clojure代码.

* [Slamhound](https://github.com/technomancy/slamhound) 根据代码,自动生成`ns`的工具.
* [kibit](https://github.com/jonase/kibit) 静态代码分析器.使用
  [core.logic](https://github.com/clojure/core.logic) 去检索代码模式,给你找出可能已有的函数或宏.
 

# 献一份力

宝宝所写并不是金规玉律.宝宝渴望与对Clojure编码(抠脚姿势)有兴趣的人一块讨论.这样我们就能为社区做贡献,共建社会主义新农村,欧耶.

欢迎大家来提意见和建议.多多感谢大家来捧场!

当然你也可以通过: [gittip](https://www.gittip.com/bbatsov)来点财力上的支持..

(此时此刻,好想把我的支付宝放这里啊啊啊...)

[![通过Gittip支持](https://rawgithub.com/twolfson/gittip-badge/0.2.0/dist/gittip.png)](https://www.gittip.com/bbatsov)

# 授权

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
本文授权于
[Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/deed.en_US)

# 流芳一下

再好的东西,没人晓得也是个渣. 微博上来一发,分享到朋友圈.每个赞,粉,黑都会让本文进步一点.那么,最好的文档在哪里?不用怀疑,在你的分享里.

查尔斯,<br/>
[波之大](https://twitter.com/bbatsov)

