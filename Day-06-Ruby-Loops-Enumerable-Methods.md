# Day 6 — Ruby Loops & Enumerable Methods

## 1. What Is a Loop?

A **loop** runs a block of code repeatedly. Loops help us avoid writing the same code many times.

```ruby
for i in 1..5
  puts i
end
```

Output:

```text
1
2
3
4
5
```

---

# Part A — Ruby Loops

## 2. `for` Loop

A `for` loop iterates through a range or collection.

```ruby
for i in 1..5
  puts i
end
```

- `for` starts the loop.
- `i` stores the current value.
- `1..5` includes both `1` and `5`.
- `puts i` prints the current value.
- `end` closes the loop.

### Inclusive and exclusive ranges

```ruby
1..5   # Includes 1, 2, 3, 4, 5
1...5  # Includes 1, 2, 3, 4; excludes 5
```

### Looping through an array

```ruby
numbers = [10, 20, 30]

for number in numbers
  puts number
end
```

Here, `number` holds each **value** in the array, not its index.

### Looping using indexes

```ruby
numbers = [10, 20, 30]

for i in 0..2
  puts numbers[i]
end
```

Array indexes start at `0`:
- `numbers[0]` is `10`
- `numbers[1]` is `20`
- `numbers[2]` is `30`

> Use an index-based loop when you need the position. When you only need each value, looping directly over the array is simpler.

---

## 3. `while` Loop

A `while` loop repeats **while its condition is true**.

```ruby
i = 1

while i <= 3
  puts i
  i += 1
end
```

Output:

```text
1
2
3
```

How it works:

1. `i = 1` initializes the variable.
2. `i <= 3` is checked before each iteration.
3. `puts i` prints the current value.
4. `i += 1` increases `i` by one.
5. When `i` becomes `4`, the condition is false and the loop stops.

**Important:** Update the variable when needed. If `i` never changes in this example, the loop will run forever.

---

## 4. `loop do`

`loop do` repeats continuously unless the loop is stopped, usually with `break`.

```ruby
i = 1

loop do
  puts i
  i += 1
  break if i > 3
end
```

Output:

```text
1
2
3
```

`break if i > 3` means: stop the loop if `i > 3` is true.

The order matters: this example prints `i`, increments it, and then checks the `break` condition.

---

## 5. `until` Loop

An `until` loop repeats **until its condition becomes true**. In other words, it continues while the condition is false.

```ruby
i = 1

until i > 3
  puts i
  i += 1
end
```

Output:

```text
1
2
3
```

When `i` becomes `4`, `i > 3` is true, so the loop stops.

### `while` vs `until`

- `while condition`: repeat while the condition is true.
- `until condition`: repeat while the condition is false.

---

## 6. Loop Control Keywords

### `break` — exit the loop

```ruby
for i in 1..5
  break if i == 3
  puts i
end
```

Output:

```text
1
2
```

When `i` is `3`, `break` stops the loop before `puts i` runs.

### `next` — skip the current iteration

```ruby
for i in 1..5
  next if i == 3
  puts i
end
```

Output:

```text
1
2
4
5
```

When `i` is `3`, Ruby skips the rest of that iteration and moves to the next one.

### `redo` — repeat the current iteration

`redo` restarts the current iteration from the beginning without checking the loop condition again or moving to the next value.

Use it carefully: if the repeated code never changes the situation that triggered `redo`, the loop may repeat forever.

### Summary

| Keyword | Meaning |
|---|---|
| `break` | Stop the loop |
| `next` | Skip to the next iteration |
| `redo` | Repeat the current iteration |

---

# Part B — Enumerable Methods

Enumerable methods provide convenient ways to process collections such as arrays.

## 7. `select` — keep matching elements

`select` returns a new array containing elements for which the condition is true. It does not modify the original array.

```ruby
numbers = [10, 20, 30, 40, 50]

result = numbers.select { |n| n > 25 }

puts result.inspect
# => [30, 40, 50]

puts numbers.inspect
# => [10, 20, 30, 40, 50]
```

Read `{ |n| n > 25 }` as: “For each value `n`, keep it if `n` is greater than `25`.”

## 8. `select!` — modify the original array when changed

`select!` removes elements that do not match the condition, modifying the original array if any elements are removed.

```ruby
numbers = [10, 20, 30, 40, 50]

numbers.select! { |n| n > 25 }

puts numbers.inspect
# => [30, 40, 50]
```

If no elements need to be removed, `select!` returns `nil` and leaves the array unchanged.

---

## 9. `reject` — exclude matching elements

`reject` returns a new array containing elements for which the condition is false. The original array remains unchanged.

```ruby
numbers = [10, 20, 30, 40, 50]

result = numbers.reject { |n| n > 30 }

puts result.inspect
# => [10, 20, 30]

puts numbers.inspect
# => [10, 20, 30, 40, 50]
```

`reject` does the opposite of `select`: it excludes elements that match the condition.

## 10. `reject!` — modify the original array when changed

`reject!` removes elements that match the condition, modifying the original array if any elements are removed.

```ruby
numbers = [10, 20, 30, 40, 50]

numbers.reject! { |n| n > 30 }

puts numbers.inspect
# => [10, 20, 30]
```

If no elements are removed, `reject!` returns `nil` and leaves the array unchanged.

### `select` and `reject` comparison

| Method | Elements kept in result | Modifies original array? |
|---|---|---|
| `select` | Elements that match | No |
| `select!` | Elements that match | Yes, if changed |
| `reject` | Elements that do not match | No |
| `reject!` | Elements that do not match | Yes, if changed |

**Memory trick:** `select` keeps matches; `reject` rejects matches. The `!` versions can modify the original collection.

---

## 11. `any?` — does at least one element match?

`any?` returns `true` if at least one element satisfies the condition. Otherwise, it returns `false`.

```ruby
numbers = [10, 20, 30]

numbers.any? { |n| n > 25 }
# => true

numbers.any? { |n| n > 50 }
# => false
```

## 12. `all?` — do all elements match?

`all?` returns `true` only when every element satisfies the condition.

```ruby
numbers = [10, 20, 30]

numbers.all? { |n| n >= 10 }
# => true

numbers.all? { |n| n > 15 }
# => false
```

The second condition is false for `10`, so not all elements match.

### `any?` vs `all?`

| Method | Question | Returns `true` when |
|---|---|---|
| `any?` | Does at least one match? | One or more elements match |
| `all?` | Do all elements match? | Every element matches |

---

## 13. `map` — transform every element

`map` runs the block for each element and returns a new array containing the block's results. It does not modify the original array by default.

```ruby
numbers = [1, 2, 3, 4]

result = numbers.map { |n| n * 2 }

puts result.inspect
# => [2, 4, 6, 8]

puts numbers.inspect
# => [1, 2, 3, 4]
```

Each value is doubled, and the results are collected into a new array.

## 14. `collect` — same as `map`

In Ruby, `collect` and `map` are aliases. They do the same thing.

```ruby
numbers = [3, 6, 9]

numbers.collect { |n| n / 3 }
# => [1, 2, 3]

numbers.map { |n| n / 3 }
# => [1, 2, 3]
```

Developers commonly use `map`, but it is useful to recognize `collect` in existing code.

---

## 15. `each` — perform an action for every element

`each` visits each element and runs the block. It is commonly used for actions such as printing values or processing records. It returns the original collection.

```ruby
numbers = [10, 20, 30]

numbers.each do |n|
  puts n
end
```

Output:

```text
10
20
30
```

### Why `each` is different from `map`

```ruby
numbers = [2, 4, 6]

result = numbers.each { |n| n * 10 }

puts result.inspect
# => [2, 4, 6]
```

The multiplication expressions are evaluated, but `each` does not collect those values. It returns the original array.

Use `map` when you want to collect the transformed values:

```ruby
result = numbers.map { |n| n * 10 }

puts result.inspect
# => [20, 40, 60]
```

### `each` vs `map`

| Method | Main purpose | Return value in these examples |
|---|---|---|
| `each` | Perform an action for each element | Original array |
| `map` / `collect` | Transform each element | New array of results |

---

# Part C — Quick Reference

## 16. Loop reference

| Loop | How it works |
|---|---|
| `for` | Iterates through a range or collection |
| `while` | Repeats while the condition is true |
| `loop do` | Repeats until explicitly stopped |
| `until` | Repeats while the condition is false |

## 17. Enumerable reference

| Method | Purpose | Changes original array? |
|---|---|---|
| `select` | Keep matching elements | No |
| `select!` | Keep matching elements | Yes, if changed |
| `reject` | Exclude matching elements | No |
| `reject!` | Exclude matching elements | Yes, if changed |
| `any?` | Check whether at least one matches | No |
| `all?` | Check whether all match | No |
| `map` | Transform each element into a new array | No |
| `collect` | Same as `map` | No |
| `each` | Perform an action for every element | No, not by default |

## 18. Choosing the right method

Given:

```ruby
numbers = [10, 20, 30, 40, 50]
```

- Keep values greater than `25` in a new array: `numbers.select { |n| n > 25 }`
- Exclude values greater than `30` from the result, preserving the original: `numbers.reject { |n| n > 30 }`
- Check whether any value is greater than `45`: `numbers.any? { |n| n > 45 }`
- Check whether all values are at least `10`: `numbers.all? { |n| n >= 10 }`
- Double every value into a new array: `numbers.map { |n| n * 2 }`
- Print every value: `numbers.each { |n| puts n }`

## Key Learning — Day 6

- Loops repeat code.
- `break` stops a loop, `next` skips an iteration, and `redo` repeats the current iteration.
- `select` keeps matching elements; `reject` excludes matching elements.
- The `!` versions of `select` and `reject` modify the original array when a change is needed.
- `any?` checks whether at least one element matches; `all?` checks whether every element matches.
- `map` and `collect` transform elements and return a new array.
- `each` iterates over elements and returns the original collection.

---

## Suggested practice

Try these in a Ruby console or a small Ruby file:

1. Print numbers from `1` to `10` with a `for` loop.
2. Use a `while` loop to print `5`, `4`, `3`, `2`, `1`.
3. Use `select` to keep even numbers from `[1, 2, 3, 4, 5, 6]`.
4. Use `reject` to remove numbers less than `4` from `[1, 2, 3, 4, 5, 6]`.
5. Use `any?` to check if `[3, 7, 9]` contains a number greater than `8`.
6. Use `all?` to check if every number in `[10, 20, 30]` is positive.
7. Use `map` to triple each number in `[2, 4, 6]`.
8. Compare the return values of `each` and `map` using the same array.
