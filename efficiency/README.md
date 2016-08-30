<!---
   Description of the Hands-On: Efficiency activity.

   @author     Dean Hendrix (dh@auburn.edu)
   @version    2016-08-30
-->

# Hands-On: Efficiency

This activity focuses on empirically measuring a program's running time. By completing this activity you should

- Gain an ability to measure the running time of programs
- Gain an understanding of factors that affect running time
- Gain an ability to characterize a program's time complexity


## Set-up

1. Open the `COMP2210/labs` directory on your Engineering H: drive. (If you didn't complete the previous lab where you created this directory structure, do so now.)

1. Create the `lab05` directory.

1. Download the zip file associated with this lab, store it in the `COMP2210/labs/lab05` directory, and unzip the file.

      - [`lab05.zip`](http://www.eng.auburn.edu/~hendrtd/comp2210/labs/lab05/lab05.zip)

1. Open jGRASP to the `lab05` directory.


## Measuring running time

Java provides two methods for measuring time: `System.nanoTime()` and `System.currentTimeMillis()`. We could use either for our purposes in this lab, but we will use `nanoTime()` since it is expressly designed to measure *elapsed time*. Note that the `currentTimeMillis()` method is designed to measure "wall-clock" time. For more information on these methods, see the following links.

- [A StackOverflow post](http://stackoverflow.com/questions/351565/system-currenttimemillis-vs-system-nanotime)
- [Java 8 API for `System.nanoTime()`](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#nanoTime--)
- [Java 8 API for `System.currentTimeMillis()`](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#currentTimeMillis--)

To measure how long method `foo` takes to run we could do the following:

```java
  long start = System.nanoTime();
  foo();
  long elapsedTime = System.nanoTime() - start;
```

The value in `elapsedTime` represents the number of nanoseconds that method `foo` required, according to the JVM's internal time source. (Of course `nanoTime` requires some time itself and the subtraction takes time, so `elapsedTime` isn't *strictly* the running time of `foo`.) For a more useful display value we could provide this time estimate in seconds.

```java
  double time = elapsedTime / 1_000_000_000d;
  System.out.printf("%4.3f", time);
```

`TimingCode.java` illustrates timing multiple runs of method `foo` and using an average value as its running time in seconds.

1. Open `TimingCode.java` in jGRASP and compile it.

1. Run the program and observe the output.

1. Interact with this code and make sure that you understand everything that it does and how you might apply it for your own purposes.


## Improving performance: avoid unnecessary work

The crux of making code more efficient is avoiding unnecessary work. Sometimes this can mean being more careful in how the code is written; for example, making sure a search algorithm terminates as soon as the result of the search can be known.

The two search methods below illustrate this idea.

```java
   // exits only after examining the entire list
   private static <T> boolean searchA(List<T> list, T target){
      boolean found = false;
      for (T element : list) {
         if (element.equals(target)) {
            found = true;
         }
      }
      return found;
   }

   // exits as soon as it knows the status of the search
   private static <T> boolean searchB(List<T> list, T target){
      for (T element : list) {
         if (element.equals(target)) {
            return true;
         }
      }
      return false;
   }
```

On average, we would expect `searchB` to perform better than `searchA` since it exits the loop and returns `true` as soon as `target` is found. This is an example of efficiency improvements we should always make, mainly because the code is just ... *better*. That's a subjective judgment, but I think most of us would agree that `searchB` is more efficient *and* more appealing.

Note that the worst-case performance of both `searchA` and `searchB` is the same - both will examine every element of the list before terminating. But on "average-case" searches we should see a performance difference.

The `EarlyExit` class demonstrates how you can measure this performance difference by building large arrays and repeatedly timing average-case searches.

1. Open `EarlyExit.java` in jGRASP and compile it.

1. Run this program and observe its output.

1. Interact with this code and make sure that you understand everything that it does and how you might apply it for your own purposes.


## Characterizing time complexity

More important that measuring running time *per se* is being able to characterize an algorithm's *time complexity*. The time complexity of an algorithm dictates how **scalable** the algorithm is; that is, whether or not the algorithm's running time will be practical as the problem size increases.

A concrete way to think about scalability is to ask the question "What happens to running time if the problem becomes twice as large as it currently is?" Is our algorithm still useful if the problem size we expect suddenly doubles? What if it doubles again? Characterizing the algorithm's time complexity is the first step in understanding the algorithm's scalability.

One approach to characterizing time complexity is to empirically answer the doubling questions above by timing the implemented algorithm on increasing problem sizes. By making observations of how the running time is affected by doubling the size of the input, we can predict what the algorithm's time complexity is. (Strictly speaking, the notion of time makes sense for an *implemented* algorithm -- a program -- but not for an *algorithm*. We could perhaps speak in terms of the "work" or "computational steps" required by an algorithm, but "time" only makes sense when referring to a running program. Even so, the terms *time complexity* and *time* are used in the context of algorithms and we are expected to implicitly understand the distinction.)

The time complexity of many algorithms can described by a polynomial function. The running time of such an algorithm thus satisfies the relationship $T(N) \propto c \cdot f(N)$, where $N$ is a measure of the size of the problem the algorithm is solving, $T(N)$ is a function that describes the time required by the algorithm for a given problem size, $c$ is a constant, and $f(N) = N^k$ for some $k > 0$. The function $f$ is called the *order of growth* of the algorithm's running time since the amount of time required by the algorithm as the problem size increases grows in proportion to the function $f$.

Of course not all algorithms have polynomial time complexity. Common orders of growth that we will see in this course include $\log N$, $N$, $N\log N$, $N^2$, $N^3$, $2^N$, and $N!$. For the purposes of this lab, however, we will restrict ourselves to talking about algorithms with time complexity proportional to a *polynomial*.

Timing a such a program as its input increases by a factor of two allows us to generate data like the following.

| **N**   | **T(N)**    | **Ratio**       |
| :---:   | :---:       | :---:           |
| N       | T(N)        |                 |
| 2N      | T(2N)       | T(2N) / T(N)    |
| 4N      | T(4N)       | T(4N) / T(2N)   |
| 8N      | T(8N)       | T(8N) / T(4N)   |
| ...     | ...         | ...             |
| 2^k*N   | T(2^k*N)    | T(2^k*N) / T(2^(k-1)*N) |


   $N$        $T(N)$          $Ratio$
--------    ----------     --------------------
  $N_0$      $T(N_0)$        ---
  $N_1$      $T(N_1)$       $T(N_1)~/~T(N_0)$
  $N_2$      $T(N_2)$       $T(N_2)~/~T(N_1)$
$\dots$      $\dots$        $\dots$
  $N_i$      $T(N_i)$       $T(N_i)~/~T(N_{i-1})$
$\dots$      $\dots$        $\dots$
  $N_m$      $T(N_m)$       $T(N_m)~/~T(N_{m-1})$
---------------------------------------------


Since $\frac{N_i}{N_{i-1}} = 2$ and $T(N) \propto cN^k$, then

\begin{equation}
\frac{T(N_i)}{T(N_{i-1})} = \frac{T(2N_{i})}{T(N_{i})} \propto \frac{c \cdot(2N_{i})^{k}}{c \cdot N^{k}_{i}} = \frac{c \cdot 2^k \cdot N_{i}^k}{c \cdot N_{i}^k} = 2^k.
\end{equation}

So by observing the value that the ratio of $T(N_{i})~/~T(N_{i-1})$ converges toward, we can set this value equal to $2^{k}$ and simply solve for $k$, which is the power (order) of the polynomial that describes the time complexity of our algorithm. For example, the following table suggests that the underlying algorithm being timed has time complexity proportional to $N^2$.


     $N$       $T(N)$ (sec.)                  $Ratio$
--------    ----------------     --------------------
    250                0.061                      ---
    500                0.042                     1.35
   1000                0.112                     2.67
   2000                0.340                     3.04
   4000                1.298                     3.82
   8000                5.334                     4.11
  16000               21.880                     4.10
  32000               85.763                     3.92
  64000              345.634                     4.03
-----------------------------------------------------


The `TimeComplexity` class demonstrates how you can generate data that will allow you to characterize polynomial time complexity.

1. Open `TimeComplexity.java` in jGRASP and compile it.

1. Run this program and observe its output.

1. Interact with this code and make sure that you understand everything that it does and how you might apply it for your own purposes.

