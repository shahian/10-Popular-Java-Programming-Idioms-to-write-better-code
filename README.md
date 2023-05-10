# 10-Popular-Java-Programming-Idioms-to-write-better-code
These are all excellent Java programming idioms that can help developers write better and more efficient code. By following these best practices, you can improve the readability, maintainability, and performance of your Java applications.

To recap, some of the popular Java programming idioms include using interfaces wherever possible, leveraging the power of enums to create singletons, checking wait() conditions in loops, using entrySet to loop over HashMaps, and closing streams in their own try block.

It's also important to use appropriate interfaces like Collection or List when defining method return types or argument types, use Iterator to traverse lists instead of for loops with get() methods, use Arrays.asList() or List.of() to initialize collections, and write code with Dependency Injection in mind to make your classes testable and modular.

By incorporating these best practices into your Java programming workflow, you can become a more efficient and effective developer while creating more robust and reliable software.

1. Calling equals() on String literals or known objects:

This helps prevent potential NullPointerException by invoking equals() on the known object instead of the variable.
For a long time while writing Java code, I used to invoke equals method like below:

```
if(givenString.equals("YES")){
// do something.
}
```

It’s natural because it reads better but its not safe. You can prevent a potential NullPointerException by calling equals() on String literal, if one object is happen to be Sring literal or on known object e.g.

```
"TRUE".equals(givenBoolean)
"YES".equals(givenString)
```

if you do reverse e.g. givenBoolean.equals(“YES”) then it will throw NullPointerException if givenBoolean is null, but if you follow this idiom then it will just return false without throwing any NPE. This is a much better code, its safe and robust. In fact this is one of the popular ways to avoid NullPointerException in Java.

2. Using entrySet() to loop over HashMap:

Instead of using the key set, iterating over the entry set allows direct access to both keys and values, improving efficiency.
I used to loop over HashMap using key set as shown below :

```
Set keySet = map.keyset();
for(Key k : keySet){
value v = map.get(k);
print(k, v)
}
```

This does another lookup to get the value from Map, which could be O(n) in worst case. if you need both key and value, then its better to iterate over entry set rather than key set.

```
Entry entrySet = map.entrySet();
for(Entry e : entrySet){
Key k = e.getKey();
Value v = e.getValue();
}
```

This is more efficient because you are getting value directly from object, which is always O(1).

3. Using Enum as Singleton:

Declaring a singleton using the Enum type ensures thread safety, robustness, and guarantees a single instance even during serialization and deserialization.

I wish, I had know that we can write Singleton in just one line in Java as :

```
public enum Singleton{
  INSTANCE;
}
```

It’s thread-safe, robust and Java guaranteed just one instance even in case of Serialization and Deserialization.


4. Using Arrays.asList() or List.of() to initialize collections:

These idioms provide a concise way to initialize lists or sets with values, avoiding repetitive add() calls.

Even If I know elements in advance, I used to initialize collection like this :

```
List<String> listOfCurrencies = new ArrayList<>();
listOfCurrencies.add("USD/AUD");
listOfCurrencies.add("USD/JPY");
listOfCurrencies.add("USD/INR");
```

This is quite verbose, thankfully you can do all this in just one line by using this idiom, which take advantage and of Arrays.asList() and Collection’s copy constructor, as shown below :

```
List listOfPairs = new ArrayList(Arrays.asList("USD/AUD", "USD/JPY");
```

Even though Arrays.asList() returns a List, we need pass its output to ArrayList’s constructor because list returned by Arrays.asList() is of fixed length, you cannot add or remove elements there. BTW, its not just list but you can create Set or any other Collection as well e.g.

```
Set primes = new HashSet(Arrays.asList(2, 3, 5, 7);
```

And, from Java 9 onwards, you can use methods like List.of() and Set.of() to create a List and Set with values. They are actually better option because they return Immutable List and Set as shown below:

```
List<Stirng> newList = List.of("One", "Two", "Infinity");
```  

5.Checking wait() conditions in a loop:

When using inter-thread communication with wait() and notify(), it's important to check the waiting condition in a loop to handle spurious notifications..

  When I first started writing inter-thread communication code using wait(), notify() and notifyAll() method, I used if block to check if waiting condition is true or not, before calling wait() and notify() as shown below :
  
```
synchronized(queue) {
  if(queue.isFull()){
  queue.wait();
  }
}
```

Thankfully, I didn’t face any issue but I realized my mistake when I read Effective Java Item of wait() and notify(), which states that you should check waiting condition in loop because it’s possible for threads to get spurious notification, and its also possible that before you do anything the waiting condition is imposed again.

So correct idiom to call wait() and notify() as following, I mean checking the queue is full or not should be inside synchronized block not outside the block:

```
synchronized(queue) {
  while(queue.isFull()){
   queue.wait();
  }
}
```
  
6. Catching CloneNotSupportedException and returning a subclass instance:

When implementing the clone() method, catching CloneNotSupportedException is unnecessary, as it will never be thrown for classes that implement the Cloneable interface. Also, returning a subtype helps reduce the need for explicit casting.

Even though Object cloning functionality of Java is heavily criticized for its poor implementation, if you have to implement clone() then following couple of best practices and using below idiom will help reducing pain :
  
```
public Course clone() {
   Course c = null;
   try {
     c = (Course)super.clone();
   } catch (CloneNotSupportedException e) {} // Won't happen
   return c;
}
```

This idiom leverages the fact that clone() will never throw CloneNotSupportedException, if a class implements Cloneable interface. Returning Subtype is known as covariant method overriding and available from Java 5 but helps to reduce client side casting e.g. you client can now clone object without casting e.g.

Course javaBeginners = new Course("Java", 100, 10);
Course clone = javaBeginners.clone();
Earlier, even now with Date class, you have to explicitly cast the output of clone method as shown below :

```
Date d = new Date();
Date clone = (Date) d.clone();
```

7. Using interfaces wherever possible:

Favoring interface types over concrete classes in method signatures, return types, and variable types allows for flexibility and easier swapping of implementations.

Even though I have been programming from long time, I have yet to realize full potential of interfaces. When I started coding, I used to use concrete classes e.g. ArrayList, Vector and HashMap to define return type of method, variable types or method argument types, as shown below :

```
ArrayList<Integer> listOfNumbers = new ArrayList();
public ArrayList<Integer> getNumbers(){
   return listOfNumbers;
}
public void setNumbers(ArrayList<Integer> numbers){
   listOfNumbers = numbers;
}
```

This is Ok, but its not flexible. You cannot pass another list to your methods, even though it is better than ArrayList and if tomorrow you need to switch to another implementation, you will have to change to all the places.

Instead of doing this, you should use appropriate interface type e.g. if you need list i.e. ordered collection with duplicates then use java.util.List, if you need Set i.e. unordered collection without duplicates then use java.util.Set and if you just need a container then use Collection. This gives flexibility to pass alternative implementation.
```

List<Integer> listOfNumbers;
public List<Integer> getNumberS(){
return listOfNumbers;
}
public void setNumbers(List<Integer> listOfNumbers){
this.listOfNumbers = listOfNumbers;
}
```

If you want, you can even go one step further and use extends keyword in Generics as well for example you can define List as List<? extends Number> and then you can pass List<Integer> or List<Short> to this method.

8. Using Iterator to traverse lists:

Iterating over lists using an Iterator is a safer approach, as it supports various implementations (e.g., LinkedList) and avoids potential issues with multi-threading.

There are multiple ways to traverse or loop over a List in Java e.g. for loop with index, advanced for loop and Iterator. I used to use for loop with get() method as shown below :
```

for(int i =0; i<list.size; i++){
  String name = list.get(i)
}
```

This works fine if you are iterating over ArrayList but given you are looping over List, its possible that List could be LinkedList or any other implementation, which might not support random access functionality e.g LinkedList.

In that case time complexity of this loop will shoot up to N² becase get() is O(n) for LL.

Using loop to go over List also has a disadvantage in terms of multi-threading e.g. CopyOnWriteArrayList — one thread changing list while another thread iterates over it using size() / get() leads to that IndexOutOfBoundsException.

On the other hand Iterator is the standard way or idiomatic way to traverse a List as shown below :
```

Iterator itr = list.iterator();
while(itr.hasNext()){
String name = itr.next();
}
```

It’s safe and also guard against unpredictable behavior.

9. Writing code with Dependency Injection in mind:

Designing classes with dependency injection (passing dependencies through constructors) promotes loose coupling and makes the code easier to test and maintain.

It was not long ago, I used to write code like this :

```
public Game {
private HighScoreService service = HighScoreService.getInstance();
   public showLeaderBoeard(){
      List listOfTopPlayers = service.getLeaderBoard(); 
     System.out.println(listOfTopPlayers);
   }
}
```

This code looks quite familiar and many of us will pass it on code review but it’s not how you should write your modern Java code. This code has three main problems :

1) Game class is tightly coupled with HighScoreService class, its not possible to test Game class in isolation. You must need HighScoreService class.

2) Even if you create a HighScoreService class you cannot test Game reliably if your HighScoreService is making network connection, downloading data from servers etc. Problem is, you can not use a Mock instead of actual object here, which makes it difficult to test.

You can get rid of these issues by writing your Java class as POJO and using Dependency Injection as shown below :

```
public Game {
private HighScoreService service;
public Game(HighScoreService svc){
this.service = svc;
}
public showLeaderBoeard(){
List listOfTopPlayers = service.getLeaderBoard(); 
System.out.println(listOfTopPlayers);
}
}
```

10. Closing streams in their own try block or using try-with-resources:

Streams should be closed in separate try blocks or, even better, using the try-with-resources statement, which automatically closes the resources after use.
These idioms contribute to writing cleaner, more efficient, and maintainable Java code. However, it's essential to consider the specific context and requirements of your project when applying them.

I used to close streams, InputStream and OutputStream like this :

```
InputStream is = null;
OutputStream os = null;

try { 

is = new FileInputStream("application.json")
os = new FileOutPutStream("application.log")
}catch (IOException io) {
}finally {
     is.close(); 
     os.close()
}
```

problems, if first stream will throw exception then close from second will never be called. You can read more about this pattern in my earlier article, right way to close Stream in Java.

You can also use try with resource in Java to write similar code, its actually bit easier now:

```

try (InputStream is = new FileInputStream("application.json");
     OutputStream os = new FileOutputStream("application.log")) {
// code to read from input stream and write to output stream
} catch (IOException e) {
// exception handling code
}

```

In this version, we declare the input and output streams in the try-with-resources statement, separated by a semicolon. The try block is followed by the catch block to handle any IOExceptions that might occur.

The beauty of this approach is that we no longer need to worry about closing the streams in the finally block since they will automatically be closed when the try block completes.
