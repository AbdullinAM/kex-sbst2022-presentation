% Kex at the 2022 SBST Tool Competition
% Azat Abdullin

# Overview

Kex\footnote{https://github.com/vorpal-research/kex/tree/sbst2022}

* a platform for analysis of JVM programs with main\newline focus on automatic test generation
* based on symbolic execution
* uses an approach called Reanimator to produce JUnit 4 test cases
* research prototype, under development

Kex-reflection\footnote{https://github.com/vorpal-research/kex/tree/sbst2022-reflection}

* similar to Kex
* uses Java Reflection API to produce test cases

################################################################################

# Kex overview\footnote{Azat M Abdullin and Vladimir Itsykson. 2022. Kex: A platform for analysis of
JVM programs. Information and Control Systems 1 (2022), 30–43. http://www.ius.ru/index.php/ius/article/view/15201}


:::::::::::::: {.columns}
::: {.column width="80%"}
![](kexOverview)
:::
::::::::::::::


################################################################################

# Kfg: CFG for JVM bytecode


:::::::::::::: {.columns}
::: {.column width="50%"}
\vspace{-1mm}
![KFG](kfgExample)
:::
::: {.column width="50%"}

\vspace{25mm}

* instrumentation
* loop canonicalization
* loop unrolling
* etc.
:::
::::::::::::::


################################################################################

# Predicate state: IR for SMT-specific transformations


:::::::::::::: {.columns}
::: {.column width="60%"}
\scriptsize
```java
(
  @S kotlin/jvm/internal/Intrinsics.class.checkNotNullParameter(arg$0, 'a')
  @S kotlin/jvm/internal/Intrinsics.class.checkNotNullParameter(arg$1, 'b')
  @S %0 = arg$0.getX()
  @S %1 = arg$1.getX()
  @S %2 = java/lang/Math.class.max(%0, %1)
  @S %3 = arg$0.getY()
  @S %4 = arg$1.getY()
  @S %5 = java/lang/Math.class.min(%3, %4)
  @S %6 = %2 < %5
) -> (
  @P %6 = false
  @S %7 = *(java/lang/System.class.out)
  @S %7.println('success')
  @S %11 = %2 - %5
  @S <retval> = %11
)
```
:::
::: {.column width="40%"}

\vspace{25mm}

* slicing
* memory spacing
* inlining
* reification
:::
::::::::::::::

################################################################################

# Kex-reflection: generating test cases from test inputs


:::::::::::::: {.columns}
::: {.column width="40%"}
\scriptsize
Generated test inputs
\vspace{5mm}

```java
instance:
term757 = org/example/Test {}

args:
term758 = org/example/Point {
    (x, int) = 0
    (y, int) = 2
}

term759 = org/example/Point {
    (x, int) = -2147483647
    (y, int) = 1
}
```
:::
::: {.column width="60%"}
\scriptsize
Test case
```java
@Before
public void setup() throws Throwable {
  term1 = newInstance(Class.forName("example.Test"));
  term2 = newInstance(Class.forName("example.Point"));
  setIntField(term2, term2.getClass(), "x", 0);
  setIntField(term2, term2.getClass(), "y", 2);
  term5 = newInstance(Class.forName("example.Point"));
  setIntField(term5, term5.getClass(), "x", -2147483647);
  setIntField(term5, term5.getClass(), "y", 1);
}
@Test
public void test() throws Throwable {
  Class<?> klass = Class.forName("example.Test");
  Class<?>[] argTypes = {Class.forName("example.Point"),
    Class.forName("example.Point")};
  Object[] args = {term2, term5};
  callMethod(klass, "foo", argTypes, term1, args);
}
```
:::
::::::::::::::


################################################################################

# Reanimator: generating test cases from test inputs


:::::::::::::: {.columns}
::: {.column width="40%"}
\scriptsize
Generated test inputs
\vspace{5mm}

```java
instance:
term757 = org/example/Test {}

args:
term758 = org/example/Point {
    (x, int) = 0
    (y, int) = 2
}

term759 = org/example/Point {
    (x, int) = -2147483647
    (y, int) = 1
}
```
:::
::: {.column width="60%"}
\scriptsize
Test case
\vspace{5mm}

```java
@Test
public void testbb8() throws Throwable {
  Test term757 = new Test();
  Point term758 = new Point(2);
  Point term759 = new Point(1);
  term759.setValue(-2147483647);
  term757.foo(term758, term759);
}
```
:::
::::::::::::::


################################################################################

# Kex weaknesses

* symbolic execution
  * resource intensive
* test generation
  * Reanimator
  	* not always able to generate test case from inputs
  	* resource intensive
  * reflection
  	* produces tests that are hard to understand

################################################################################

# Kex at the SBST 2021 competition

* score of $44.21$ and fifth place
* failed to produce test cases for 5 out of 6 projects
* competition has revealed a lot of technical issues

################################################################################

# Results

* **Kex** scored 80.94 and ranked seventh
* **Kex-reflection** scored 133.18 and ranked fifth

\begin{table}[tb]
\begin{center}
\begin{tabular}{|r|c|c|c|c|}
\hline
& \multicolumn{2}{c|}{\textbf{Kex}} & \multicolumn{2}{c|}{\textbf{Kex-reflection}} \\ \hline
\textbf{Project} & \textbf{30 s} & \textbf{120 s} & \textbf{30 s} & \textbf{120 s} \\ \hline

FASTJSON & 12.91 & 14.16 & 21.39 & 22.18 \\ \hline
GUAVA & 31.06 & 35.33 & 24.25 & 25.19 \\ \hline
SEATA & 22.06 & 29.53 & 18.03 & 22.88 \\ \hline
SPOON & 23.06 & 25.14 & 27.94 & 30.52 \\ \hline
\textbf{Overall average} & 21.55 & 25.13 & 22.79 & 24.96 \\ \hline
\textbf{3 project average$^a$} & 25.39 & 30.00 & 23.41 & 26.20 \\ \hline
\end{tabular} 
\end{center}
\label{table:results}
$^a$\footnotesize{excluding the benchmarks of FastJSON project}
\end{table}

################################################################################

# Conclusion


* fixed the major technical issues and successfully\newline performed on all of the projects 
* improved the results in comparison with the previous year
* the tools still need to be improved to be competitive\newline with the other participants

################################################################################

# Contact information

<!-- link -->
<azat.aam@gmail.com>

<https://github.com/vorpal-research/kex>

\vspace{15mm}

<!-- columns -->
:::::::::::::: {.columns}
::: {.column width="50%"}
\vspace{1mm}
![](polytech)
:::
::::::::::::::

################################################################################
