# Reduce

## Learning Goals

- Explain reduction
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

Let’s walk through the first method and then we’ll discuss the difference
between the two. Here’s the code for the first method:

```java
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<Integer> nums = List.of(1, 2, 3);
        Optional<Integer> sum = nums.stream().reduce((curSum, num) -> curSum + num);
        System.out.println(sum.get()); // 6
    }
}
```

The accumulator function has the `curSum` and `num` parameter. The `curSum`
parameter represents the partial result of the reduction while the `num`
parameter represents the current in the stream that’s being accessed.

| Iteration | curSum | num | return value |
| --------- | ------ | --- | ------------ |
| 1         | 1      | 2   | 1 + 2 = 3    |
| 2         | 3      | 3   | 3 + 3 = 6    |

Initially, `curSum` is assigned the value of the first element in the stream
(`1` in this case) and `num` is assigned the value of the 2nd element. On the
first iteration, the `curSum` value (`1`) and the `num` value (`2`) are added
and returned. The returned value becomes the value of `curSum` in the next
iteration.

For the second iteration, the same operation is performed to get a new value for
`curSum`. Since there are no elements left, the `reduce` method returns an
`Optional`.

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
        List<Integer> nums = List.of(1, 2, 3);
        int sum = nums.stream().reduce(6, (curSum, num) -> curSum + num);
        System.out.println(sum); // 12
    }
}
```

The `reduce` method works exactly as before but with different initial values.

| Iteration | curSum | num | return value |
| --------- | ------ | --- | ------------ |
| 1         | 6      | 1   | 6 + 1 = 7    |
| 2         | 8      | 2   | 7 + 2 = 9    |
| 3         | 10     | 3   | 9 + 3 = 12   |

## Conclusion

The `reduce` is method is incredibly versatile, especially so when it’s preceded
by intermediate operations like `map` and `filter`.
