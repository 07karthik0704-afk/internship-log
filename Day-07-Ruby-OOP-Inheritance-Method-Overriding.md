# Day 7 — Ruby OOP: Inheritance & Method Overriding

## Learning goals

By the end of these notes, you should be able to:
- Explain classes and objects.
- Understand `initialize` and instance variables.
- Use inheritance to reuse behavior from a parent class.
- Override a parent method in a child class.
- Use `super` to call a parent method.
- Understand Ruby's method lookup chain and `ancestors`.
- Check whether an object responds to a method using `respond_to?`.

---

## 1. Classes and Objects

A **class** is a blueprint for creating objects. An **object** is an instance created from a class.

```ruby
class Student
  def initialize(name)
    @name = name
  end

  def introduce
    puts "Hello, my name is #{@name}"
  end
end

student1 = Student.new("Karthik")
student2 = Student.new("Arun")

student1.introduce
student2.introduce
```

Output:

```text
Hello, my name is Karthik
Hello, my name is Arun
```

### Key points
- `class Student` defines a class named `Student`.
- `Student.new("Karthik")` creates a new `Student` object.
- `initialize` is called automatically when `.new` creates an object.
- `name` is a parameter passed into `initialize`.
- `@name` is an **instance variable**. Each object can store its own value.
- `introduce` is an instance method.

Here, `student1` stores `"Karthik"` and `student2` stores `"Arun"`. Their instance data is separate.

### Instance variables

Instance variables begin with `@` and belong to an object.

```ruby
class Student
  def initialize(name)
    @name = name
  end
end

first = Student.new("Karthik")
second = Student.new("Arun")
```

Conceptually:
- `first` has `@name` set to `"Karthik"`.
- `second` has `@name` set to `"Arun"`.

The `@name` variable is not automatically accessible from outside the object. You can later learn methods such as `attr_reader` to expose values.

---

## 2. Inheritance

**Inheritance** lets a child class reuse methods defined in a parent class.

Use `<` to indicate inheritance:

```ruby
class Vehicle
  def start
    puts "Vehicle started"
  end
end

class Car < Vehicle
end

car = Car.new
car.start
```

Output:

```text
Vehicle started
```

`Car` does not define `start`, but it inherits that method from `Vehicle`.

### Why use inheritance?

Inheritance can help when classes share common behavior. For example, `Car` and `Bike` might both inherit common methods from `Vehicle`.

```ruby
class Vehicle
  def stop
    puts "Vehicle stopped"
  end
end

class Car < Vehicle
end

class Bike < Vehicle
end

Car.new.stop
Bike.new.stop
```

Both objects can call `stop` because both classes inherit from `Vehicle`.

**Remember:** inheritance is a way to reuse and specialize behavior. It is not necessary for every relationship between classes.

---

## 3. Method Overriding

**Method overriding** happens when a child class defines a method with the same name as a method in its parent class. The child version is used for objects of that child class.

```ruby
class Animal
  def sound
    puts "Some animal sound"
  end
end

class Dog < Animal
  def sound
    puts "Woof!"
  end
end

Animal.new.sound
Dog.new.sound
```

Output:

```text
Some animal sound
Woof!
```

`Animal` uses its own `sound` method. `Dog` uses the overridden `sound` method.

### Why override a method?

A child class may need more specific behavior than the general behavior provided by its parent.

```ruby
class Animal
  def sound
    puts "An animal makes a sound"
  end
end

class Cat < Animal
  def sound
    puts "Meow!"
  end
end
```

---

## 4. The `super` Keyword

Use `super` inside an overridden method when you want to call the matching method from the parent class.

```ruby
class Vehicle
  def start
    puts "Vehicle started"
  end
end

class Car < Vehicle
  def start
    super
    puts "Car is ready"
  end
end

Car.new.start
```

Output:

```text
Vehicle started
Car is ready
```

Execution order:
1. Ruby calls `Car#start`.
2. `super` calls `Vehicle#start`.
3. The parent method prints `Vehicle started`.
4. Execution returns to `Car#start`.
5. The child method prints `Car is ready`.

### `super` with arguments

```ruby
class Person
  def initialize(name)
    @name = name
  end
end

class Student < Person
  def initialize(name, course)
    super(name)
    @course = course
  end
end

student = Student.new("Karthik", "MCA")
```

`super(name)` calls the parent initializer and passes `name` to it. The parent stores the name in `@name`, and the child stores the course in `@course`.

Important distinction:
- `super` forwards the current method's arguments automatically.
- `super()` calls the parent method with **no arguments**.
- `super(name)` calls the parent method with the specified argument.

Use the form that matches the behavior you want.

---

## 5. Method Lookup and `ancestors`

When you call a method, Ruby searches for an implementation using its **method lookup chain**.

For a simple inheritance example, Ruby checks the child class first, then its parent class, and continues up the chain.

```ruby
class Animal
  def eat
    puts "This animal eats food"
  end
end

class Dog < Animal
end

dog = Dog.new
dog.eat
```

Output:

```text
This animal eats food
```

Ruby cannot find `eat` directly in `Dog`, so it finds the method in `Animal`.

### Inspect the lookup chain

```ruby
class Animal
end

class Dog < Animal
end

p Dog.ancestors
```

The result begins with `Dog`, followed by `Animal`. It also includes Ruby's other ancestors, such as `Object`, `Kernel`, and `BasicObject`; the exact list depends on the class and included modules.

`ancestors` is useful when investigating where a method may come from, especially in Rails applications where modules and mixins are common.

---

## 6. `respond_to?`

`respond_to?` checks whether an object can respond to a method.

```ruby
class Student
  def study
    puts "Studying"
  end
end

student = Student.new

puts student.respond_to?(:study)
puts student.respond_to?(:play)
```

Output:

```text
true
false
```

- `:study` is a **symbol** representing the method name.
- The object responds to `study` because that method exists.
- The object does not respond to `play` because no such method is defined for it or inherited by it.

You can use this check before calling a method when the available behavior may vary:

```ruby
if student.respond_to?(:study)
  student.study
end
```

This is useful for debugging and for working with objects that may expose different methods.

---

## 7. Quick Comparison

| Concept | Meaning | Example |
|---|---|---|
| Class | Blueprint for objects | `class Student` |
| Object | Instance of a class | `Student.new("Karthik")` |
| `initialize` | Runs when `.new` creates an object | `def initialize(name)` |
| Instance variable | Stores data on an object | `@name` |
| Inheritance | Child reuses parent behavior | `class Car < Vehicle` |
| Overriding | Child provides its own version of a method | `def sound` in `Dog` |
| `super` | Calls the matching parent method | `super` |
| `ancestors` | Shows the class/module lookup chain | `Dog.ancestors` |
| `respond_to?` | Checks whether an object responds to a method | `student.respond_to?(:study)` |

---

## 8. Practice Exercises

Try answering these before checking the answers.

### Exercise 1 — Objects and instance variables

```ruby
class Student
  def initialize(name)
    @name = name
  end

  def introduce
    puts "Hi, I am #{@name}"
  end
end

student1 = Student.new("Maya")
student2 = Student.new("Ravi")

student1.introduce
student2.introduce
```

**Question:** What two lines are printed?

### Exercise 2 — Inheritance

```ruby
class Machine
  def run
    puts "Machine running"
  end
end

class Printer < Machine
end

Printer.new.run
```

**Question:** What is the output, and why?

### Exercise 3 — Overriding

```ruby
class Animal
  def sound
    puts "Generic sound"
  end
end

class Cat < Animal
  def sound
    puts "Meow"
  end
end

Cat.new.sound
```

**Question:** What is the output?

### Exercise 4 — `super`

```ruby
class Parent
  def greet
    puts "Hello from parent"
  end
end

class Child < Parent
  def greet
    super
    puts "Hello from child"
  end
end

Child.new.greet
```

**Question:** In what order are the lines printed?

### Exercise 5 — `respond_to?`

```ruby
class Book
  def read
  end
end

book = Book.new

p book.respond_to?(:read)
p book.respond_to?(:write)
```

**Question:** What are the two Boolean results?

### Exercise 6 — Think like a developer

You have a parent class `Payment` with a method `process`. A child class `CardPayment` needs a specialized `process` implementation but also wants to run the parent's process first.

**Question:** Which Ruby feature would you use inside `CardPayment#process` to call the parent's method?

---

## 9. Answer Key

1. `Hi, I am Maya` then `Hi, I am Ravi`.
2. `Machine running` — `Printer` inherits `run` from `Machine`.
3. `Meow` — the child method overrides the parent method.
4. `Hello from parent` then `Hello from child`.
5. `true` then `false`.
6. Use `super` inside `CardPayment#process`.

---

## 10. Mini Revision Checklist

Before moving on, make sure you can explain these in your own words:

- [ ] What is the difference between a class and an object?
- [ ] What happens when `.new` is called?
- [ ] What does an instance variable such as `@name` store?
- [ ] How does `class Car < Vehicle` work?
- [ ] What is method overriding?
- [ ] How does `super` differ from calling a method directly?
- [ ] What does `Dog.ancestors` show?
- [ ] What does `respond_to?(:study)` check?

**Practical tip:** Run each code example in a small Ruby file or in `irb`, change the values, and predict the output before running it. Being able to explain the output is more valuable than simply memorizing the syntax.
