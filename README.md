# Reduce

## Learning Goals

- Explain reduction as a terminal stream operation
- Use the `reduce` method

## What is a Reduce Operation?

Reduction is a terminal operation. It takes all the elements in a stream and
produces a single value. The `reduce` method performs the reduction operation in
Java.

## Using the `reduce` Method

There are 2 common ways of calling the `reduce` method:

- `Optional<T> reduce(BinaryOperator<T> accumulator)`
- `T reduce(T identity, BinaryOperator<T> accumulator`

### Without a Defined Initial Value

Let’s walk through the first method, and then we’ll discuss the difference
between the two. 

The first method accepts an accumulation 
function `BinaryOperator<T>` for some data type `T` as a parameter.
Recall a binary operator is a function that has 2 parameters
and returns an optional result of the same type.  

For example, the static method `Integer.sum` is a binary operator
that has two `int` parameters and returns an `int`:

`static int	sum(int a, int b)`

Before we use the `Integer.sum` method for reduction, we'll first look
at an example using a lambda expression as the accumulator function.
The lambda expression produces the sum of the two parameters `(a, b) -> a + b`:

```java
import java.util.List;
import java.util.Optional;

public class Example1 {
    public static void main(String[] args) {
        List<Integer> nums = List.of(5, 8, 50, 9);
        Optional<Integer> sum = nums.stream().reduce((a, b) -> a + b);
        System.out.println(sum.get()); // 72
    }
}
```

The `a` parameter represents the partial result of the reduction while the `b`
parameter represents the current element in the stream that’s being accessed.

| Iteration | a   | b   | return value |
|-----------|-----|-----|--------------|
| 1         | 5   | 8   | 5  +  8 = 13 |
| 2         | 13  | 50  | 13 + 50 = 63 |
| 3         | 63  | 9   | 63 +  9 = 72 |

Initially, `a` is assigned the value of the first element in the stream
(`5` in this case) and `b` is assigned the value of the 2nd element `8`.
On the first iteration, the `a` value (`5`) and the `b` value (`8`) are added
and `13` is returned from the accumulator function. 

In the second iteration, `a` is the value returned from the first iteration `13`,
and `b` is assigned the 3rd list element `50`.  The new sum `63` is returned.

In the third iteration, `a` is the value returned from the second iteration `63`,
and `b` is assigned the 4th and last element `9`.  The new sum `72` is returned.

Since there are no elements left, the `reduce` method returns an `Optional[72]`.

The program prints the result of the reduction:

```text
72
```

### With a Defined Initial Value

Here’s the second `reduce` method signature from above for reference:

`T reduce(T identity, BinaryOperator<T> accumulator`.

The `identity` parameter is used to assign the initial value of the partial
result (first parameter of the `BinaryOperator` function). Without the
`identity` parameter, the first element in the stream is instead used as the
first partial result.

Note that the return type is `T` instead of an `Optional` because the `reduce`
method will return the initial value if a stream is empty.

```java
public class Example {
    public static void main(String[] args) {
        List<Integer> nums = List.of(5, 8, 50, 9);
        int sum = nums.stream().reduce(0, (a, b) -> a + b);
        System.out.println(sum); // 72
    }
}
```

The `reduce` method works exactly as before but with different initial values,
and an additional iteration.

| Iteration | a   | b   | return value  |
|-----------|-----|-----|---------------|
| 1         | 0   | 5   | 0 + 5 = 5     |
| 2         | 5   | 8   | 5 + 8 = 13    |
| 3         | 13  | 50  | 13 + 50 = 63  |
| 4         | 63  | 9   | 63 + 9 = 72   |


If we were to perform a reduction using multiplication,
the identity parameter would be `1` rather than `0`
to calculate the product `1*5*8*50*9 = 18000`:

```java
List<Integer> nums = List.of(5, 8, 50, 9);
int product = nums.stream().reduce(1, (a, b) -> a * b);
System.out.println(product); // 18000
```


## Passing a method reference for the reduction


The static method `Integer.sum` is a binary operator; thus,
we can use lambda expression `(a, b) -> Integer.sum(a,b)`
as the accumulator function in the reduction. We can also simply
use the static method reference `Integer::sum`:

```java
import java.util.List;
import java.util.Optional;

public class Example1b {
    public static void main(String[] args) {
        List<Integer> nums = List.of(5, 8, 50, 9);
        Optional<Integer> sum = nums.stream().reduce(Integer::sum);
        System.out.println(sum.get()); // 72
    }
}
```
 
Let's look at a reduction involving the `Employee` class:

```java
public class Employee {
    private String name;
    private double salary;
    private int age;

    public Employee(String name, double salary, int age) {
        this.name = name;
        this.salary = salary;
        this.age = age;
    }

    public String getName() {return name;}

    public double getSalary() {return salary;}

    public int getAge() {return age;}

}
```

We can create a stream pipeline using `map` to get the employee salaries,
then `reduce` to sum the salaries. 

The value returned from the reduction is `double` rather than `Optional`
because we pass an initial value `0.0` as the identity parameter. 
If the stream was empty, the initial value `0.0` would be returned.

```java
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<Employee> employees = List.of(
                new Employee("fred", 42000.0, 24),
                new Employee("devon", 48000.0, 27),
                new Employee("pat", 77000.0, 32));

        //compute total payroll
        double totalPayroll = employees.stream()
                .map(Employee::getSalary)
                .reduce(0.0,Double::sum);

        System.out.println(totalPayroll);
    }
}
```

If we call `reduce` without passing the identity parameter,
the stream could be empty so `reduce` returns an `Optional`:

```java
Optional<Double> totalPayroll = employees.stream()
        .map(Employee::getSalary)
        .reduce(Double::sum);

System.out.println(totalPayroll.get());
```

## Summary

The `reduce` is method is incredibly versatile, especially when it’s preceded
by intermediate operations like `map` and `filter`.


## Resources

[Java 11 Integer](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Integer.html)  
[Java 11 Stream](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Stream.html)
