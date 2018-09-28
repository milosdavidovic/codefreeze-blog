---
layout: single
title: 'Avoiding Null References In C#'
date: '2017-09-04 11:25:25 +0200'
categories: 'dotnet'
tags: csharp dotnet null option conditional
published: true
comments: true
header:
  teaser: /assets/images/common/csharp-logo.png
---

This post will give some ideas on how to eliminate null references in code, as they are identified as common source of bugs and pure code design. In object-oriented software we often struggle with null references, and miss-use them to pass some information to the client, like that method was not executed successfully, or some item was not found.
This could lead to lots of bugs, unpredictable behavior of the application and less readable code.
Here are some ways we could use to reduce the number of null references in our code.

## Content

[TryXXX Methods](#tryxxx-methods)

[Conditional Result Object ](#conditional-result-object)

[Option (Maybe) Objects](#option-objects)

[Null Objects](#null-objects)

[Summary](#summary)


## TryXXX methods

This is a common way of providing information on whether the method executed successfully and avoid returning of the null object if method fails. Chances are you encountered these when using C# primitive types and their TryParse methods, which operate in the same way.

    
```csharp
public bool TrySendRequest(out string response)
{
    var client = new SomeClient();
    try
    {
        resonse = client.SendRequest();
        return true;
    }
    catch(Exception ex)
    {
        //log error
        return false;
    }
}
```

This is sort of "easy way out" and the code is more predictable and readable. 
The downside is that this doesn't help us much and client code will still need to check returned boolean value when calling this TrySendRequest method.


## Conditional Result Object 

This Conditional<T> class is a kind of a wrapper around the actual object of interest, and it additionally provides Boolean property (Success) which indicates the presence (and validity) of the Result object.

```cs
class Conditional<T>
{
    public T Result { get; private set; }
    public bool Success { get; private set; }
    
    public Conditional()
    {
        this.Result = default(T);
        this.Success = false;
    }
    
    public Conditional(T value)
    {
        this.Result = value;
        this.Success = true;
    }
}
```

This approach was similar effect like TrySomething method and branching around Boolean value (Success) is still there.

## Option Objects

Lets explain what is an issue here. We are invoking a method and expect some value to be returned. The problem is that we are not sure if returned value will be null. It is certainly possible. So we have to protect our code with guard statements, or NullReferenceException will arise. This leads to a lot of if statements, but more importantly, every now and then someone will forget to check whether an object is null and the application could crash.  

The idea behind Option (Maybe) objects is to transform our object to a collection of objects of the same type implementing IEnumerable<T> interface and use the power of Linq to Objects to perform logic execution. Inside Option class, there are two constructors at our disposal. First accepts no parameters and it just creates an empty collection of objects. The other one is used in case we indeed have an object and it creates a collection with only one item. This Option class is now used to represent our object in the form of a collection and it will never be null. If the collection is empty, no action will be executed and without exception.
Here is one simple example of the problematic situation:


```cs
static void Main(string[] args)
{
    Leprechaun lucky = new Leprechaun(new Gold(10));

    var stolenGold = lucky.SurrenderGold();

    if(stolenGold != null)
    {
        Console.WriteLine("{0} gold coins stolen from leprechaun.", 
                          gold.Amount);
    }
}

public class Leprechaun
{
    public Gold Stash { get; private set; }

    public Leprechaun(){ }
    
    public Leprechaun(Gold gold)
    {
        Stash = gold;
    }
    
    public Gold SurrenderGold()
    {
        return Stash;
    }
}

public class Gold
{
    public int Amount { get; set; } 
    
    public Gold(int amount)
    {
        Amount = amount;
    }
}
```

SurrenderGold method returns Gold object and we are not sure whether it will contain some value or it will be null. So we need to check that in client code, in order to avoid raising an exception.
This is where Option (or Maybe) object come into place. We could change code so SurrenderGold method returns Option<T> (T is Gold in this case), and here is the new implementation:


```csharp
public class Option<T> : IEnumerable<T>
{
    private readonly IEnumerable<T> values;

    private Option()
    {
        this.values = new T[0];
    }

    private Option(T value)
    {
        this.values = new T[1] { value };
    }

    public static Option<T> None() => new Option<T>();

    public static Option<T> Some(T value) => new Option<T>(value);

    public IEnumerator<T> GetEnumerator()
    {
        return this.values.GetEnumerator();
    }

    System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
    {
        return this.values.GetEnumerator();
    }
}
```

Basically, the idea is to wrap a reference object into type extending IEnumerable<T> interface, and hence the object becomes a collection with zero or one object. We can now iterate through this collection and execute the same operation like we would on a single object.
The client code can be written as follows:


```cs
static void Main(string[] args)
{
    Leprechaun lucky = new Leprechaun(new Gold(10));

    var stolenGold = lucky.SurrenderGold();

    foreach(var gold in stolenGold)
    {
        Console.WriteLine("{0} gold coins stolen from leprechaun.", gold);
    }
}

public class Leprechaun
{
    public Gold Stash { get; private set; }

    public Leprechaun(){ }
    
    public Leprechaun(Gold gold)
    {
        Stash = gold;
    }

    public Option<Gold> SurenderGold()
    {
        if (Stash != null)
            return Option<Gold>.Some(new Gold(10));
        else
            return Option<Gold>.None();
    }
}
```

Now we don't care if there is gold or not, because if a collection is empty, nothing will be executed. 
We could also hide foreach loop by creating extension for Enumerable<T> class as follows:


```cs
public static class EnumerableExtensions
{
    public static void Do<T>(this Enumerable<T> sequence, Action<T> action)
    {
        foreach(T obj in sequence)
            action(obj);
    }
}
```

So the final form of the client code should be little more readable and simpler because we now have the power of Linq To Objects syntax at our disposal: 

```cs
static void Main(string[] args)
{
    Leprechaun lucky = new Leprechaun(new Gold(10));

    var stolenGold = lucky.SurrenderGold();

    stolenGold.Do(a=>
    {
        Console.WriteLine("{0} gold coins stolen from leprechaun.", a);
    });
}
```

This approach requires some additional coding, but as a reward allows us to write code in using Linq syntax and doesn't require null checks. The probability of null-related bugs is reduced.

## Null Objects

Creating "null" class object eg. objects which are neutral respecting business logic. They are implementing the same interface like real objects, but its actions don't cause any concrete functionality (non-functional objects). This implementation shields the client from having to perform null checks.
Example of a problematic situation:

    
```cs
public class Article
{
    public string Name {get; private set;}
    public decimal Price {get; private set;}
    private IDiscount _discount;
    
    public Article(string name, string price, IDiscount discount)
    {
        if(name == null)
            return new ArgumentException("name");
        if(price == null)
            return new ArgumentException("price");
        
        Name = name;
        Price = price;
        _discount = discount;
    }
    
    public decimal DiscountedPrice()
    {
        // Possiblity of _discount to be null 
        // forces us to perform check and branch
        if(_discount != null)
            return _discount.Apply(this.Price);
        else
            return this.Price;
    }
}

public Interface IDiscount
{
    decimal Apply(decimal price)
}


public class NewCustomerDiscount : IDiscount
{
    public decimal Apply(decimal price)
    {
        return price * 0.95;
    }
}

public class HappyHourDiscount : IDiscount
{
    public decimal Apply(decimal price)
    {
        return decimal * 0.8;
    }
}
```

In this current implementation, we expect IDiscount object to be passed to the Article class through constructor and problem arises when we don't want to give discounts on the specific Article. The only option we have now is to pass null and that will signal that no discount should be applied. We use null to pass information. This is bad practice as our intentions are not clear and chances are that only person who wrote this code knows this secret. This effect maintainability and readability of the code.
Here is the example of the client code:


```csharp
public static void Main (string[] args)
{
    var shoes = new Article("black shoes", 
           100.00, 
           new HappyHourDiscount());
    var hat = new Article("red hat", 
           30.00, 
           new NewCustomerDiscount());
    var jacket = new Article("blue jacket", 
           80.00, 
           null); // We don't want to give discount on jackets
}
```
Passing null instead of discount object will cause ArgumentNull exception when calling Apply, so now the code will branch on the null check to avoid this. One solution to this problem is to introduce the null object => Class that will implement the same interface like real objects, but it will behave like null e.g. it will do nothing. In our example it will just return regular price with no discount, same behavior like we had before, but the code looks a lot cleaner.

```csharp
public class NoDiscount : IDiscount
{
    public decimal Apply(decimal price)
    {
        return price;
    }
}

public class Article
{
    public string Name {get; private set;}
    public decimal Price {get; private set;}
    private IDiscount _discount;
    
    public Article(string name, string price, IDiscount discount)
    {
        if(name == null)
            return new ArgumentException("name");
        if(price == null)
            return new ArgumentException("price");
        if(discount == null)
            return new ArgumentException("discount");
        Name = name;
        Price = price;
        _discount = discount;
    }
    
    public decimal DiscountedPrice()
    {
        return _discount.Apply(this.Price);
    }
}

public static void Main (string[] args)
{
    var shoes = new Article("black shoes", 
           100.00, 
           new HappyHourDiscount());
    var hat = new Article("red hat",     
           30.00, 
           new NewCustomerDiscount());
    var jacket = new Article("blue jacket", 
           80.00, 
           new NoDiscount()); // Now we pass null object insted of just null
}
```

The null object pattern makes the code cleaner and more readable. Null object classes are often implemented as singletons, so only one instance is used throughout the entire application. If Article constructor receives null as the discount, an exception will be raised at the object creation level as it should, so we have no nasty surprises in the later stages of code execution.

##  Summary

These were the some ideas on how to eliminate null references in code, and hopefully improve the code we write by making it more readable and with less bugs.

Thanks for reading and feel free to check out other articles on the Code Freeze!
