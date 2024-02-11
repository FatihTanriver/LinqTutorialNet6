# Course Notes

## Section 2

### 7-LINQ and extension Methods

myList.Where \\ Where is a extension method of IEnumerable

```highlighted
An extension method is a method defined outside of a type that can be called upon this types objects as a regular member method.

Extension methods allow us to add new functionality to a type without modifying it.
```

!!Ex: StringExtension
```csharp
public static class StringExtensions
{
    public static int  GetCountOfLines(this string input)
    {
        return input.Split("\n").Length;
    }
}

```

!!! LinQ methods are Extension methods for type IEnumerable

!!! Both List and an array implement the ``IEnumerable<T>`` interface.


### 8-LINQ,IEnumerable<T> and the method chaining

`IEnumerable<T>`

- Array
- List
- HashSet
- Dictionary

!!! IEnumerable Interface enables foreach loop for collection.

!!! LinQ methods never modify the input collection
    -  GetEnumerator();
    - x Add(Elem), Remove(elem), Clear
    - Append(elem)
        - Original collection does not change, a new collection will be created instead (with Append)

!!! Where, Orderby method will filter or sort and return new IEnumerable collection. Originals are not modified.

!!! - LINQ methods take `IEnumerable<T>` as a parameter.
!!! - LINQ methods return `IEnumerable<T>`.

### 9 Deferred execution in LINQ

!!! Takeaway1: Deferred execution

- !!! Deferred execution means that the evaluation of a LINQ expression is delayed until the value is actually needed.
- It allows us to work on the latest data
- It improves the performance by avoiding unnecessary execution.

!!! words.Where(word => Length <3)  
    \\ this is a *query* and deferred execution action
    \\ !!! output will change even we add an element after the query

!!! words.Where(word => Length <3).ToList();
    \\ with ToList() it is not a *query* anymore, just a list of data
    \\ it is materialized.

!!! Takeaway2:

- Deffered execution improves the performance.

### 10 Method syntax vs Query syntax

```csharp

var numbers =  new [] {4,2,7,10,12,5}

// Method Syntax
var smallOrderedNumbersMethodSyntax = 
    numbers
    .Where(number => number < 10)
    .OrderBy(number => number);

// Query Syntax
var smallOrderedNumbersQuerySyntax = 
    from number in numbers
    where number < 10
    orderby number
    select number;

```

Method Syntax

- Pure C#
- 

Query Syntax

- New "language" to learn
- Easy to learn if you know SQL
- Not all LinQ operations are supported (i.e Distinct)
  - we can combine both syntax
- Some operations are simpler to write (join)


## Section 3 (Any)

### 11 Any

We can use any also to check it has any elements

```csharp
var isNotEmpty = pets.Any();
```


```csharp

myCollection != null && myCollection.Count > 0:

/*
This explicitly checks that the collection is not null and also that it has more than 0 elements using the Count property. This is a clear and straightforward way to ensure that at least one element exists in the collection. However, it may have a minor performance overhead because it fetches the count of all elements in the collection.*/

myCollection != null && myCollection.Any():

/*
This checks that the collection is not null and uses the Any() method, which returns true if at least one element exists. This is also a clear way to ensure at least one element exists and is generally efficient because it doesn't need to count all the elements like Count does.
*/

myCollection?.Any() == true:

/*
This uses the null-conditional operator and checks if myCollection is not null and if it has at least one element. It's concise and will work as expected, but some developers might find it less readable or intuitive compared to the previous options.
*/


```

### Execercise

Exercise description:

Using LINQ, implement the IsAnyNumberNegative method that checks if any number in the collection is smaller than 0.

Exercise solution:

The solution is to simply use the Any method like this:

```csharp
public static bool IsAnyNumberNegative(IEnumerable<int> numbers)
{
    return numbers.Any(n => n <10)
}
```

In the lambda expression, we check if the number is negative.


Second Exercise

```csharp
        /*
        Using LINQ, implement the AreThereAnyBigCats method, which will check if in the collection of Pets there is a cat that weighs over 2 kilos.
        */

        public static bool AreThereAnyBigCats(IEnumerable<Pet> pets)
        {
            return pets.Any(p => p.PetType == PetType.Cat &&  p.Weight > 2);
        }
```


Third Exercise

```csharp
   public static bool AreAllNamesValid_Refactored(string[] names)
        {
            return !names.Any( name => char.IsLower(name[0]) || name.Length > 2 || name.Length < 25);
        }
```


### 14 refactor IsAllValidNames

```csharp
    public class Exercise
    {
        //TODO implement this method
        public static bool AreAllNamesValid_Refactored(string[] names)
        {
            return !names.Any(name => char.IsLower(name[0]) || name.Length < 2 || name.Length > 25);
        }
        
        //do not modify this method
        public static bool AreAllNamesValid(string[] names)
        {
            foreach(var name in names)
            {
                if(char.IsLower(name[0]))
                {
                    return false;
                }
                if(name.Length < 2)
                {
                    return false;
                }
                if(name.Length > 25)
                {
                    return false;
                }
            }
            return true;
        }
    }
```

## Section 4 (All)

### 15 All

Exercise 1: Use LINQ to implement the AreAllNumbersDivisibleBy10 method, which checks if all numbers in the collection are divisible by 10.

```csharp
        public static bool AreAllNumbersDivisibleBy10(int[] numbers)
        {
            return numbers.All(x => (x % 10) == 0);
        }
```

Exercise 2: Using LINQ, implement the AreAllPetsOfTheSameType method that checks if all Pets in the collection are of the same PetType.

```csharp

// MySol
        public static bool AreAllPetsOfTheSameType(IEnumerable<Pet> pets)
        {
            PetType firstPetType = pets.First().PetType;
            return pets.All(x => x.PetType == firstPetType);
        }

// Without First
        public static bool AreAllPetsOfTheSameType(IEnumerable<Pet> pets)
        {
        
            return pets.All(x => x.PetType == PetType.Dog) ||
            pets.All(x => x.PetType == PetType.Cat) ||
            pets.All(x => x.PetType == PetType.Fish);
        }

 // Another way
        public static bool AreAllPetsOfTheSameType(IEnumerable<Pet> pets)
        {
            var allPetTypes = Enum.GetValues(typeof(PetType)).Cast<PetType>();
            return allPetTypes.Any(petType => pets.All(pet => pet.PetType == petType));
        }


```

Refactoring Challange

```csharp
        public static bool AreAllWordsOfTheSameLength(List<string> words)
        {
            if(words.Count == 0 || words.Count == 1)
            {
                return true;
            }
            var length0 = words[0].Length;
            for(int i = 1; i < words.Count; ++i)
            {
                if(words[i].Length != length0)
                {
                    return false;
                }
            }
            return true;
        }

        //Refactored
        public static bool AreAllWordsOfTheSameLength_Refactored(List<string> words)
        {
            return words.All(word => word.Length == words[0].Length);
        }

        /* !!!
        Interestingly, this will also work correctly if the input is an empty List. For empty collections, All return true. Also, the lambda passed as an argument will never be executed (because it's executed for each element of the collection, but there are none), which means no exception will be thrown when calling "words[0]," which would normally happen with an empty list.
        */
```

## Section 5 (Count)

### 19 Count

```csharp

 // Count range is integer
 var countOfDogs = pets.Count(pet => pet.PetType == PetType.Dog);

 // LongCount range is long
  var countOfDogs = pets.LongCount(pet => pet.PetType == PetType.Dog);

// Ex: Implement the CountAllLongWords method, which will count all words longer than 10 letters.

words.Count(w => w.Length > 10);


```

Ex: Using LINQ, implement the AreThereFewerOddThanEvenNumbers method, which will check if in the collection of numbers there are fewer odd than even numbers.

```csharp
        public static bool AreThereFewerOddThanEvenNumbers(IEnumerable<int> numbers)
        {
            return numbers.Count(x => x % 2 == 0) > numbers.Count(x => x % 2 == 1) ;
        } 
```

Refactor Ex:

```csharp
        public static bool IsAnySequenceTooLong(IEnumerable<IEnumerable<int>> numberSequences, int maxLength)
        {
            foreach(var numberSequence in numberSequences)
            {
                var count = 0;
                foreach(var number in numberSequence)
                {
                    ++count;
                }
                if(count > maxLength)
                {
                    return true;
                }
            }
            return false;
        }


        //Refactored
          public static bool IsAnySequenceTooLong_Refactored(IEnumerable<IEnumerable<int>> numberSequences, int maxLength)
        {
            return numberSequences.Any(seq => seq.Count() > maxLength);
        }
```

## Section 6 (Contains)

### 23 Contains

```highlighted
Contains - additional resources
During the lecture we mentioned the GetHashCode method. This method returns
an integer that is built from the type’s properties and fields. Hashcode is often
considered an identifier of an object, but this is not correct - an identifier must be
unique, and hashcodes can have co-called “conflicts” meaning two different objects
will have the same hashcode. For many types it is unavoidable, because there can
be more different objects of a given type than there are values in int. For example,
consider a Point type, having X and Y of type int. We can have IntRange*IntRange
different points, but we only can have IntRange different hashcodes.
Nevertheless, a good HashCode method should produce as little conflicts as
possible.
Hashcodes are used to insert and identify an object in a hash-based collection, such
as Dictionary or HashSet. You can read more about hashcodes
```

 Details : [GetHashCode Method](https://learn.microsoft.com/en-us/dotnet/api/system.object.gethashcode?redirectedfrom%3DMSDN%26view%3Dnet-5.0#System_Object_GetHashCode)

 !!! Default Contains uses value type check
    - Ref type values will not the way we accepted.
 !!! Exception "String" is ref types
    but it implements the IEquatable(string) interface, providing a custom method for comparing strings.

Example of our custom method
```csharp
    internal class PetEqualityByIdComparer : IEqualityComparer<Pet>
    {
        public bool Equals(Pet x, Pet y)
        {
            return x.Id == y.Id;
        }

        public int GetHashCode([DisallowNull] Pet pet)
        {
            return pet.Id;
        }
    }

    //we can use any EqualityComparer we like
    var containsAnthonyWithEqualityComparer = Data.Pets.Contains(
              new Pet(2, "Anthony", PetType.Cat, 2f), new PetEqualityByIdComparer());
```

#### Exercise1

```csharp
    /*
    Using LINQ, implement the IsAppointmentDateAvailable method. This method takes two
    parameters: date of an appointment, and existingAppointmentDates, which is a
    collection of dates that are already taken. This method should return true only 
    if the date is not amongst the existingAppointmentDates.
    */
    public class Exercise
    {
        public static bool IsAppointmentDateAvailable(DateTime date, IEnumerable<DateTime> existingAppointmentDates)
        {
            return !existingAppointmentDates.Contains(date);
        }
    }
```

#### Exercise2

```csharp
/*
Implement the CountFriendsOf method. This method takes the friend parameter and a
collection of people. We want to count all those people, who have the friend amongst
their friends.
*/
        public static int CountFriendsOf(Friend friend, IEnumerable<Person> people)
        {
            return people.Count(person => person.Friends.Contains(friend));
        }

```

#### Refactor

```csharp
        // Using LINQ, refactor the ContainsBannedWords method.
        public static bool ContainsBannedWords_Refactored(IEnumerable<string> words, IEnumerable<string> bannedWords)
        {
            return words.Any( word => bannedWords.Contains(word)); 
        }
```


## Section 7 (OrderBy)

### 27 OrderBy

```csharp


//we can order by some criteria, and then by other criteria
var petsOrderedByTypeThenName = Data.Pets
    .OrderBy(pet => pet.PetType)
    .ThenBy(pet => pet.Name);
Printer.Print(petsOrderedByTypeThenName, nameof(petsOrderedByTypeThenName));


// Use of comparer overload
public class PetByTypeComparer : IComparer<Pet>
{
    public int Compare(Pet x, Pet y)
    {
        return x.PetType.CompareTo(y.PetType);
    }
}


// here comparer decide order type
var petsOrderedByType = Data.Pets.OrderBy(
    pet => pet, new PetByTypeComparer());

```

### Exercise 1

```csharp
/*
Using LINQ, implement the OrderFromLongestToShortest method, which takes a collection of
strings, and returns those strings ordered from longest to shortest.

For example, for {"bb", "a", "ccc"} the result should be {"ccc", "bb", "a"}
*/
        public static IEnumerable<string> OrderFromLongestToShortest(IEnumerable<string> words)
        {
            //TODO your code goes here
            
            return words.OrderByDescending(word => word.Length);
        }
   
```

### Exercise 2

```csharp
/*
Using LINQ, implement the FirstEvenThenOddDescending method, which orders numbers like this:
first, the even numbers
then, the odd numbers
Then the numbers should be ordered descending. For example, for numbers {1,2,3,4,5,6,7} the result should be: {6,4,2,7,5,3,1}.

!!! The important thing here is that in C# when ordering a collection by a boolean variable, the false values come before true values. For example:
*/


        //Coding Exercise 2
        public static IEnumerable<int> FirstEvenThenOddDescending(
            IEnumerable<int> numbers)
        {
            return numbers
                .OrderBy(number => number % 2 != 0)
                .ThenByDescending(number => number);
        }
```


## Section 8 (MinMax)

### 

## Section 9 (Average)

### 

## Section 10 (Sum)

### 

## Section 11 (ElementAt)

### 

## Section 12 (First and Last)

### 
## Section 13 (Single)

### 
## Section 14 (Where)

### 

## Section 15 (Take)

### 

## Section 16 (Skip)

### 

## Section 17 (OfType)

### 

## Section 18 (Distinct)

### 
## Section 19 (Prepend and Append)

### 
## Section 20 (Concat and Union)

### 

## Section 21 (Collection type change)

### 

## Section 22 (Select)

### 

## Section 23 (SelectMany)

### 

## Section 24 (Generating new collections)

### 

## Section 25 (Grouping)

### 

## Section 26 (Intersect and Except)

### 

## Section 27 (Join)

### 

## Section 28 (Aggregate)

### 

## Section 29 (Zip)

### 

## Section 30 (Introduction to query syntax)

###

## Section 31 (OrderBy (Query Syntax))

### 

## Section 32 (Where (Query Syntax))

### 

## Section 33 (Select (Query Syntax))

### 

## Section 34 (SelectMany (Query Syntax))

### 

## Section 35 (GroupBy (Query Syntax))

### 

## Section 36 (Join (Query Syntax))

### 

## Section 37 (LINQ Updates)

### 

## Section 38 (Bonus)

### 

