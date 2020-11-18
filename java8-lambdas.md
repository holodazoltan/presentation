java8 functional programming
-----

# Functional programming requirement

## Functions as first class objects
Create an "instance" of a function, as have a variable reference that function instance. In Java, methods are not, Lambda expressions are first class object.

## Pure functions
- The execution of the function has no side effects.
- The return value of the function depends only on the input parameters passed to the function

## Higher order functions
The function takes one or more functions as parameters or the function returns another function as result. In Java, the closest we can get to a higher order function is a function (method) that takes one or more lambda expressions as parameters, and returns another lambda expression

## Pure functional programming has a set of rules to follow too:
- No state: a function cannot reference any state outside of the function (member variables)
- No side effects: a function cannot change any state outside of the function (member variables)
- Immutable variables: immutable variables makes it easier to avoid side effects
- Favour recursion over looping: recursion uses function calls to achieve looping

# Java functional style programming

```java
public interface StateChangeListener {
    public void onStateChange(State oldState, State newState);
}
```

## Passing behavior before Java 8
java7 anonymous interface implementation

```java
stateOwner.addStateListener(new StateChangeListener() {
    public void onStateChange(State oldState, State newState) {
        System.out.println("State changed...");
    }
});
```

Issues:
- encapsulate action in an object
- misses the programmer's intent
- compiled to own classes

## Lambda expressions
Lambda expressions allow you to define an anonymous function that is not bound to an identifier

```java
StateChangeListener lambda = (oldState, newState) -> {
    System.out.println("State changed...");
}
stateOwner.addStateListener(lambda);
```

java8 one line lambda expression
type inference: the interface type of the parameter can be inferred from the method declaration
```java
stateOwner.addStateListener(
    (oldState, newState) -> System.out.println("State changed")
);
```

```java
(argument part) -> body part
```

parameters:
```java
() -> {System.out.println("Zero parameter lambda");}
(param) -> {System.out.println("One parameter: " + param);} || param -> System.out.println("parameter w/o (): " + param);
(param1, param2, ...) -> System.out.println("Multiple parameters: " + param1 + ", " + param2);
```

# Functional Interfaces
an interface that contains only a single abstract (unimplemented) method, contains any default and static methods

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

lambda use the `invokedynamic` instruction, to defer the translation strategy at runtime.

## Built-in Functional Interfaces

### java.util.function.Function

```java
public interface Function<T,R> {
    public <R> apply(T parameter);
}
```

### java.util.function.Predicate
takes a single value as parameter, and returns true or falsea

```java
Predicate<String> namesStartingWithS = name -> name.startsWith("s");
```

### java.util.function.UnaryOperator<T>
takes a single parameter and returns a parameter of the same type

```java
UnaryOperator<Person> unaryOperator = 
        (person) -> { person.name = "New Name"; return person; };
```

### java.util.function.BinaryOperator<T>
takes two parameters and returns a single value

```java
BinaryOperator<MyValue> binaryOperator =
        (value1, value2) -> { value1.add(value2); return value1; };
```

### java.util.function.Supplier<T>
takes no parameter and returns a single value.

```java
Supplier<Integer> supplier = () -> new Integer((int) (Math.random() * 1000D));
```

### java.util.function.Consumer<T>
take a parameter without returning any value

```java
Consumer<Integer> consumer = (value) -> System.out.println(value);
```

## Method References as Lambdas
`Function<String, Integer> strToLength = str -> str.length();`
`Function<String, Integer> strToLength = String::length;`

### Static method
`Function<List<Integer>, Integer> maxFn = Collections::max`

### Parameter method reference
interface and method signature (params and return value) should matched
`Finder finder = String::indexOf;`
`Finder finder = (s1, s2) -> s1.indexOf(s2);`

### Instance method references
interface and method signature (params and return value) should matched
`Deserializer des = stringConverter::convertToInt;`

### constructor
`Factory factory = String::new;`

## Lambda examples

### list.forEach
```java
List<String> list = new ArrayList<>();
list.add("One");
list.add("Abc");
list.add("BCD");

list.forEach(s -> System.out.println(s));
```

### list.removeIf
```java
List<String> list = new ArrayList<>();
list.add("One");
list.add("Abc");
list.add("BCD");

String deletable = "One"

list.removeIf(s -> s.equals(deletable));
```

### list.replaceAll
```java
List<String> list = new ArrayList<>();
list.add("One");
list.add("Abc");
list.add("BCD");

UnaryOperator<Person> unaryOpt = s -> modify(s); 
list.replaceAll(unaryOpt);

private static String modify(String s){
  return s + "concat";
}

```

### map.getOrDefault
```java
String defaultValue = "No Student";
String stdName = studentMap.getOrDefault(102, defaultValue);
```

### map.replace
```java
Map<Integer, String> cityMap = new HashMap<>();
cityMap.put(101, "Pest");
cityMap.put(102, "Buda");
String value = cityMap.replace(102, k -> "Budapest");
```

### map.replaceAll
```java
Map<Integer, String> cityMap = new HashMap<>();
cityMap.put(101, "Pest");
cityMap.put(102, "Buda");
String value = cityMap.replaceAll((k,v) -> v + "-" + k);
```

### map.computeIfPresent
```java
Map<Integer, String> cityMap = new HashMap<>();
cityMap.put(101, "Pest");
cityMap.put(102, "Buda");
String value = cityMap.computeIfAbsent(102, k -> "Budapest");
```

### map.computeIfAbsent
```java
Map<Integer, String> cityMap = new HashMap<>();
cityMap.put(101, "Pest");
cityMap.put(102, "Buda");
String value = cityMap.computeIfAbsent(103, k -> "Budapest");
```

### Comparator with Collections.sort
```java
List<String> list = new ArrayList<>();
list.add("One");
list.add("Abc");
list.add("BCD");

Comparator<String> comparator = (a, b) -> a.compareTo(b);

if (reversed){
    comparator = comparator.reversed();
}

Collections.sort(list, comparator);

System.out.println(list);
```

### Comparator with list.sort
```java
List<String> list = new ArrayList<>();
list.add("One");
list.add("Abc");
list.add("BCD");

Comparator<String> comparator = (a, b) -> a.compareTo(b);

list.sort(comparator);

System.out.println(list);
```

### Comparator with Arrays.sort
```java
String[] array = {"One", "Abc", "BCD"};

Comparator<String> comparator = (a, b) -> a.compareTo(b);

Arrays.sort(array, comparator);

System.out.println(list);
```

### Comparator.comparing
```java
Comparator<Student> nameComparator = Comparator.comparing(Student::getName);
Comparator<Student> nameComparator = Comparator.comparing(Student::getName, (s1, s2) -> s2.compareTo(s1)); 
```

### Comparator with SortedSet
```java
Comparator<Student> ageComparator = Comparator.comparing(Student::getAge);
TreeSet<Student> myTreeSet = new TreeSet<>(ageComparator);
```

### Comparator with SortedMap
```java
Comparator<Student> ageComparator = Comparator.comparing(Student::getAge);
TreeMap<Student, String> myTreeMap = new TreeMap<>(ageComparator);
```

