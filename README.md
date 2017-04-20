# Study Guide for [EECS 111, 2017 Spring Quarter][Course Page]

<!--
TODO:
[ ] Recursive functions and recursive data definitions
[ ] Finish writing design recipe section
[ ] Itemizations section
-->

[Course Page]: http://users.eecs.northwestern.edu/~jesse/course/eecs111/
[Textbooks]: http://users.eecs.northwestern.edu/~jesse/course/eecs111/#textbooks
[Racket]: https://racket-lang.org
[HtDP]: http://www.htdp.org

This document will hopefully clear up common confusions about the [Racket programming language][Racket], the [HtDP design recipe][HtDP], and [this course's][Course Page] content.
Please refer to the [textbooks][Textbooks] for more complete content.

## Table of Contents
  - [Basics of Data](#basics-of-data)
    - [Data Values](#data-values)
    - [Data Types](#data-types)
    - [Constants](#constants)
    - [What's the difference between data types, data values, and constants?](#what-s-the-difference-between-data-types-data-values-and-constants-)
    - [Making Our Own Data Definitions](#making-our-own-data-definitions)
      - [Synonyms](#synonyms)
      - [Enumerations](#enumerations)
      - [Structs](#structs)
        - [Constructors](#constructors)
        - [Selectors and Predicates](#selectors-and-predicaes)
        - [Concluding Structs](#concluding-structs)
      - [Itemizations](#itemizations)
  - [Design Recipe](#design-recipe)
    - [Data Definitions](#data-definitions)

I recommend that you read over "Basics of Data" to strengthen your foundations.
Otherwise, [skip to Design Recipe](#design-recipe).

## Basics of Data

### Data Values
**Data Values** in Racket are *things in your code that represent an actual value (something that concretely exists)*.
Here are some examples of data values:
  - `0` — a Number with the value `0`
  - `"faceship"` — a String (a sequence of letters or characers) with the value "faceship"
  - `#true` — a Boolean (a value that represents a logical "yes" or "no") with a value `#true`
  - ![](Square-100-Solid-Red.png) — an Image of a red square

Here are some data values that might be more confusing:
  - `'()` — a value representing nothing (also called null or empty)
  - `""` — an empty String, a value that is not the same as `'()`, but instead represents a string of letters with no content

Side node: The above values are called "literals", meaning you can just type them in your code (or copy-paste them), and Racket knows what data value you mean when you put them in.

There are other examples of data values not represented by literals:
  - `(make-posn 0 0)` — a `Posn` object, representing the position at the origin, with the coordinates (0,0)

### Data Types
**Data types** are *not the same as data values*.
In general, data types are *classes of data values* that group similar data values together.

Here are examples of data types (not data values):
  - `String` — a class of data values that describe all possible sequences of letters and characters within quotation marks:
    - `"faceship"` is a `String`
    - `"EECS 111"` is a `String`
    - `""` is a `String`
    - There is an infinite number of possible `String` values!
  - `Number` — a class of data values that describe all possible numbers:
    - `0` is a `Number`
    - `118999` is a `Number`
    - `-500` is a `Number`
    - `3.14159` is a `Number`
    - There is an infinite number of possible `Number` values!
  - `Image` — a class of data values that describe all possible images:
    - ![](Square-100-Solid-Red.png) is an `Image`
    - There is an infinite number of possible `Image` values!
  - `Boolean` — a class of data values that describe all possible "yes"/"no" values.
    - `#true` is a `Boolean`
    - `#false` is a `Boolean`
    - With this data type, there are only 2 possible `Boolean` values.

These are the main data types we use.

**Important: To avoid confusion, you should always Captalize (CamelCase) the first letters of data type names when you work with them.**
This distinguishes them from other variable names or function names in your code.

## Constants
Constants are how we give names to *data values* inside the code.
We give a name to a specific data value, and we can refer to it later by using that name, *without having to copy-paste the data value around*.

Here are some constants that Racket gave us:
  - `pi` — a `Number` with the value `3.141592653589793`
  - `empty-image` — an `Image` representing an image with nothing in it
  - `empty` — another name for `'()`

We can define our own constants too:

    (define COURSE-NAME "EECS 111")

will define `COURSE-NAME` as a constant, with the value `"EECS 111"`, of type `String`.
You can also make constants of any *data value* that you can think of.

**Important: To avoid confusion, you should name constants with ALL-CAPS-SEPARATED-BY-HYPHENS.**
This distinguishes them from other variable names or function names in your code.

### What's the difference between data types, data values, and constants?
To reiterate:
  - *Data types* are different types of data we want to represent (`String`, `Number`, `Boolean`, etc.).
  - *Data values* are concrete pieces of data, with an associated data type (`"EECS 111"`, `23893724`, `#false`, etc.)
  - *Constants* are names we give to **data values**, so we don't have to type them over and over again, or have to change code in a lot of places if we just want to change a number or something.

### Making Our Own Data Definitions
For our code to make sense, we must define our own **data types**.

There are 4 main categorizations of data definitions:
  - [Synonyms](#synonyms)
  - [Enumerations](#enumerations)
  - [Structs](#structs)
  - [Itemizations](#itemizations)

#### Synonyms
Synonyms are data definitions where you literally call an existing **data type** by another name.

**Why would we want to do this?**
Because it's clearer to read our code if we give a relevant description to all the **data values** we would ever want to consider.

Syntax:

    ; A [DataTypeWeWantToDefine] is a [PreexistingDataType]

Example: We want to make a data type for the first names of people.
The preexisting data type that makes the most sense is `String`.

    ; A FirstName is a String

We can do the same with last names too.

    ; A LastName is a String

Once we make those data definitions, we can make statements about following **data values**:
  - We can call `"Jesse"` a `FirstName`; that is, the data value `"Jesse"` has the data type `FirstName`. Although it's technically a `String`, saying that it has the data type `FirstName` can make more sense to the logic in our programs, and we can forget about the fact that it's actually a `String` if it makes our lives easier.
    We can do this *only once we make the data definitions in the comments of our code*.
  - Similarly we can call `"Tov"` a `LastName`; that is, the data value `"Tov"` has the data type `LastName`.

That's all there is to it for synonyms!

#### Enumerations
Remember when I [said](#data-types) that **data types** are categorizations of data values?
We can make new data types (called "enumerations"), where we can *group relevant data values (not data types) together*.

**Why would we want to do this?**
Because sometimes we want to *list out a finite number of possibilities* and *group them together*.
This is really helpful when we know all the possibilities of something and need a way to categorize them.

Syntax:

    ; A [DataTypeWeWantToDefine] is one of:
    ; - ...
    ; - ...
    ; - ...

Example: We want to make a data type for the eye color of people.
There is a finite number of possibilities:
  - Black
  - Brown
  - Blue
  - Green
  - (there might be more but let's keep it simple)

We can give **data values** to all of these possibilities:
  - Black — `"black"`
  - Brown — `"brown"`
  - Blue — `"blue"`
  - Green — `"green"`

and then group these into a new **data type**.
Let's call ours `EyeColor`.

    ; An EyeColor is one of:
    ; - "black"
    ; - "brown"
    ; - "blue"
    ; - "green"

That's the data definition!

Now, if we say we are given a **data value** with the type `EyeColor` in our code, we know **for sure** that it is one of the four possibilities listed.

#### Structs
A struct (or structure) is a different type of data type than we've seen before.

A struct **takes other data values**, **gives them names**, and **packages them into a single data value**.
Think of it like a box with different compartments, and all the different compartments have names, and you can put other things in there.

**Why would we want to do this?**
It's helpful to package data values into a single "box", instead of treating them as separate.
This makes our coding cleaner, more intuitive, and less prone to mistakes.

Remember when I [wrote about `Posn`](#data-values)?
A `Posn` is actually a struct.
Here's how it would be defined under the hood\*:

    ; A Posn is a (make-posn Number Number)
    (define-struct posn (x y))
    ; interp. `x` is the x-coordinate of the position, and
    ; `y` is the y-coordinate of the position

<sup>\* It's actually more complicated; I just simplified.</sup>

Let's break it down:

    (define-struct [name-of-struct] ([names-of-fields...]))

is a special syntax that makes you a struct.
`[name-of-struct]` is where you put the name of your struct, and `[names-of-fields...]` is where you put the names of the data values inside.

So,

    (define-struct posn (x y))

means we're defining a struct called `posn` and giving it two compartments to put stuff in: `x` and `y`.

However, **our data definition is not complete**.
We need to define what **data types** `x` and `y` are.
Otherwise, the `x` and `y` could have any data type, and it would be harder to do anything useful with this struct without constraining the data types it contains.

We do this by saying

    ; A Posn is a (make-posn Number Number)

and that tells us that we want the `x` and `y` to both have the type `Number`.

Finally, we say

    ; interp. `x` is the x-coordinate of the position, and
    ; `y` is the y-coordinate of the position

so we know what `x` and `y` represent when we use this data type.

Our `Posn` data definition is now complete.

##### Constructors
But wait!
What does `(make-posn ...)` mean in our data definition?

When we said `(define-struct posn (x y))`, it magically gave us this function (called a constructor):

    ; make-posn : Any Any -> ??

`make-posn` is a function that takes 2 inputs of any type, and outputs some data value.
However, **we don't know what the output data type is**, and we can't do anything useful with this unless we **define our data types**.
**A `define-struct` statement is not enough for a data definition.**

Therefore, when we say

    ; A Posn is a (make-posn Number Number)

what we actually mean is that **we're defining the data type `Posn` to be whatever `make-posn` outputs when given 2 inputs `Number`s**.
This lets us generalize and state that `make-posn` is like this instead:

    ; make-posn : Number Number -> Posn

The `make-posn` function, when given 2 input `Number`s, will always output a data value whose type is `Posn`.
To correctly use the `posn` struct, we must always give 2 input `Number`s to `make-posn`.

##### Selectors and Predicates
To retrieve the data inside `Posn` objects, `define-struct` also gives us other functions:

    ; posn-x : Posn -> Number
    ; posn-y : Posn -> Number
    ; posn? : Any -> Boolean

The first two functions (called selectors) will output the `x` and `y` values of the given input `Posn` object.
The last function (called a predicate) takes anything as an input and outputs whether or not the input is a `Posn` object.

##### Concluding Structs
A of a complete struct data definition looks like:

    ; A [DataTypeWeWantToDefine] is a (make-[struct-name] [DataTypesOfFields...])
    (define-struct [name-of-struct] ([names-of-fields...]))
    ; interp. `name-of-field1` is ...
    ; `name-of-field2` is ...
    ; `name-of-field3` is ...
    ; ...

Examples of complete struct data definitions:

`Posn`:

    ; A Posn is a (make-posn Number Number)
    (define-struct posn (x y))
    ; interp. `x` is the x-coordinate of the position, and
    ; `y` is the y-coordinate of the position

gives us the functions

    ; make-posn : Number Number -> Posn
    ; posn-x : Posn -> Number
    ; posn-y : Posn -> Number
    ; posn? : Any -> Boolean

`HouseholdPet`:

    ; A HouseholdPet is a (make-household-pet String String Number)
    (define-struct household-pet (name species age))
    ; interp. `name` is the name of the pet,
    ; `species` is the name of the species, and
    ; `age` is the age of the pet in years

gives us the functions

    ; make-household-pet : String String Number -> HouseholdPet
    ; household-pet-name : HouseholdPet -> String
    ; household-pet-species : HouseholdPet -> String
    ; household-pet-age : HouseholdPet -> Number
    ; household-pet? Any -> Boolean

**Important: As usual, you should name your data types with CamelCase (ByCapitalizingTheFirstLetterOfEveryWord).**
**However, the name of your struct itself should be lowercase-and-separated-with-hyphens.**
This is just so we're consistent with naming functions and data types.

### Itemizations


## Design Recipe
Functions are a thing in Racket that takes input *data values* and gives out an output *data value*.
To receive full credit, you must use the design recipe to write functions.

There are 6 steps in the design recipe:
  1. Data Definitions
  2. Signature, Purpose, and Header
  3. Examples
  4. Strategy
  5. Body
  6. Testing

You should follow these 6 steps when designing functions.

### Data Definitions
