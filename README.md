# Study Guide for [EECS 111, 2017 Spring Quarter][Course Page]

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
    - [Signature, Purpose, and Header](#signature-purpose-and-header)
    - [Examples](#examples)
    - [Strategy and Body](#strategy-and-body)
      - [Decision Tree](#decision-tree)
      - [Interval Decomposition](#interval-decomposition)
      - [Structural Decomposition](#structural-decomposition)
      - [Function Composition](#function-composition)
    - [Testing](#testing)
  - [Stepwise Evaluation](#stepwise-evaluation)
  - [FAQ](#faq)
    - [How do I add quotation marks inside a `String`?](#how-do-i-add-quotation-marks-inside-a-string)
    - [What are the code style conventions?](#what-are-the-code-style-conventions)

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
```racket
(define COURSE-NAME "EECS 111")
```

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
```racket
; A [DataTypeWeWantToDefine] is a [PreexistingDataType]
```

Example: We want to make a data type for the first names of people.
The preexisting data type that makes the most sense is `String`.
```racket
; A FirstName is a String
```

We can do the same with last names too.
```racket
; A LastName is a String
```

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
```racket
; A [DataTypeWeWantToDefine] is one of:
; - ...
; - ...
; - ...
```

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
```racket
; An EyeColor is one of:
; - "black"
; - "brown"
; - "blue"
; - "green"
```

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
```racket
; A Posn is a (make-posn Number Number)
(define-struct posn (x y))
; interp. `x` is the x-coordinate of the position, and
; `y` is the y-coordinate of the position
```

<sup>\* It's actually more complicated; I just simplified.</sup>

Let's break it down:
```racket
(define-struct [name-of-struct] ([names-of-fields...]))
```
is a special syntax that makes you a struct.
`[name-of-struct]` is where you put the name of your struct, and `[names-of-fields...]` is where you put the names of the data values inside.

So,
```racket
(define-struct posn (x y))
```

means we're defining a struct called `posn` and giving it two compartments to put stuff in: `x` and `y`.

However, **our data definition is not complete**.
We need to define what **data types** `x` and `y` are.
Otherwise, the `x` and `y` could have any data type, and it would be harder to do anything useful with this struct without constraining the data types it contains.

We do this by saying
```racket
; A Posn is a (make-posn Number Number)
```
and that tells us that we want the `x` and `y` to both have the type `Number`.

Finally, we say
```racket
; interp. `x` is the x-coordinate of the position, and
; `y` is the y-coordinate of the position
```
so we know what `x` and `y` represent when we use this data type.

Our `Posn` data definition is now complete.

##### Constructors
But wait!
What does `(make-posn ...)` mean in our data definition?

When we said `(define-struct posn (x y))`, it magically gave us this function (called a constructor), with the [signature](#signature-purpose-and-header):
```racket
; make-posn : Any Any -> ??
```
`make-posn` is a function that takes 2 inputs of any type, and outputs some data value.
However, **we don't know what the output data type is**, and we can't do anything useful with this unless we **define our data types**.
**A `define-struct` statement is not enough for a data definition.**

Therefore, when we say
```racket
; A Posn is a (make-posn Number Number)
```

what we actually mean is that **we're defining the data type `Posn` to be whatever `make-posn` outputs when given 2 input `Number`s**.
This lets us generalize and state that `make-posn` is like this instead:
```racket
; make-posn : Number Number -> Posn
```

The `make-posn` function, when given 2 input `Number`s, will always output a data value whose type is `Posn`.
To correctly use the `posn` struct, we must always give 2 input `Number`s to `make-posn`.

##### Selectors and Predicates
To retrieve the data inside `Posn` objects, `define-struct` also gives us other functions:
```racket
; posn-x : Posn -> Number
; posn-y : Posn -> Number
; posn? : Any -> Boolean
```

The first two functions (called selectors) will output the `x` and `y` values of the given input `Posn` object.
The last function (called a predicate) takes anything as an input and outputs whether or not the input is a `Posn` object.

##### Recursive Data Types
In some cases, we want to reference a struct within itself.

Consider the following data definition, which represents a person who has a best friend:
```racket
; An OptionalFriend is one of:
; - Friend
; - empty

; A Friend is a (make-friend String OptionalFriend)
(define-struct friend (name best-friend))
; interp. `name` is the name of the person we're representing, and
; `best-friend` is another Friend object which represents this person's best
; friend
```
This is a recursive data type, because the `Friend` data type has a field which is another `Friend` object.

Example usage:
```racket
(define SHERLOCK
        (make-friend "Sherlock Holmes" empty)) ; Sherlock has no friends
(define WATSON
        (make-friend "John Watson" SHERLOCK)) ; Watson's best friend is Sherlock

(friend-best-friend WATSON) ; outputs the value of SHERLOCK
(friend-name (friend-best-friend WATSON)) ; outputs "Sherlock Holmes"
```

##### Concluding Structs
A of a complete struct data definition looks like:
```racket
; A [DataTypeWeWantToDefine] is a (make-[struct-name] [DataTypesOfFields...])
(define-struct [name-of-struct] ([names-of-fields...]))
; interp. `name-of-field1` is ...
; `name-of-field2` is ...
; `name-of-field3` is ...
; ...
```

Examples of complete struct data definitions:

`Posn`:
```racket
; A Posn is a (make-posn Number Number)
(define-struct posn (x y))
; interp. `x` is the x-coordinate of the position, and
; `y` is the y-coordinate of the position
```
gives us the functions
```racket
; make-posn : Number Number -> Posn
; posn-x : Posn -> Number
; posn-y : Posn -> Number
; posn? : Any -> Boolean
```

`HouseholdPet`:
```racket
; A HouseholdPet is a (make-household-pet String String Number)
(define-struct household-pet (name species age))
; interp. `name` is the name of the pet,
; `species` is the name of the species, and
; `age` is the age of the pet in years
```
gives us the functions
```racket
; make-household-pet : String String Number -> HouseholdPet
; household-pet-name : HouseholdPet -> String
; household-pet-species : HouseholdPet -> String
; household-pet-age : HouseholdPet -> Number
; household-pet? : Any -> Boolean
```

**Important: As usual, you should name your data types with CamelCase (ByCapitalizingTheFirstLetterOfEveryWord).**
**However, the name of your struct itself should be lowercase-and-separated-with-hyphens.**
This is just so we're consistent with naming functions and data types.

### Itemizations
Itemizations are like [enumerations](#enumerations), but instead of listing out a finite number of *data values*, you can also list **data types**.

**Why would we want to do this?**
Because sometimes you want to use different data types as function outputs, in order to represent different outcomes.

Example: We have a person struct and we need a data type to represent the height of a person.
```racket
; An OptionalHeight is one of:
; - Number
; - #false

; A Person is a (make-person String OptionalHeight)
(define-struct person (name height))
; interp. `name` is the name of the person, and
; `height` is the height of the person in centimeters, and `#false` if not specified
; Examples:
; - (make-person "Justin Jackson" 180)
; - (make-person "Ant Man" #false)
```
Here, we don't know Ant Man's height, so we can't represent it with just a `Number`.
The `OptionalHeight` data type lets us do this by putting in `#false` to indicate this.\*

<sup>\* In practice you'd want to use `'()` or `empty` to represent a nonexsistent value, but I'm just using `#false` because it's more familiar.</sup>

We can also use itemizations to group together similar data types.

Example: We want a data type to represent places where students at Northwestern live.
```racket
; A LivingSpace is one of:
; - ResidentialHall
; - ResidentialCollege
; - "off-campus"

; A ResidentialHall is a (make-residential-hall String String)
(define-struct residential-hall (name address))
; interp. `name` is the name of the res hall, and
; `address` is the street address
; Examples:
; - (make-residential-hall "Bobb-McCollough" "2305 Sheridan Road")

; A ResidentialCollege is a (make-residential-college String String String)
(define-struct residential-college (name address theme))
; interp. `name` is the name of the res college,
; `address` is the street address, and
; `theme` is the theme of the res college
; Examples:
; - (make-residential-college "Slivka" "2332 Campus Drive" "engineering")
```
If we have this data definition, then the data values

- `(make-residential-hall "Bobb-McCollough" "2305 Sheridan Road")`
- `(make-residential-college "Slivka" "2332 Campus Drive" "engineering")`
- `"off-campus"`

can all belong to the data type `LivingSpace`.
If we were to design a function that takes a `LivingSpace` as an input, then it must be able to handle all of these values.
Conversely, if we were to design a function that outputs a `LivingSpace`, it could output all of these values.

**Important: As usual, you should name your data types with CamelCase (ByCapitalizingTheFirstLetterOfEveryWord).**
**However, the name of your struct itself should be lowercase-and-separated-with-hyphens.**
This is just so we're consistent with naming functions and data types.

## Design Recipe
Functions are a thing in Racket that takes input *data values* and gives out an output *data value*.
To receive full credit, you must use the design recipe to write functions.

There are 6 steps in the design recipe:
  1. [Data Definitions](#data-definitions)
  2. [Signature, Purpose, and Header](#signature-purpose-and-header)
  3. [Examples](#examples)
  4. [Strategy](#strategy-and-body)
  5. Body
  6. [Testing](#testing)

You should follow these 6 steps when designing functions.

### Data Definitions
See the section ["Making Our Own Data Definitions"](#making-our-own-data-definitions).
You should define all the data types that you want to use involving this function.

### Signature, Purpose, and Header
Before you even start writing the function itself, consider the input types and output type of the function.
- What do you want to name this function?
- How many input arguments does it take?
- What data types are these arguments?
- What data type does the function output?

Then, you can come up with the **signature** of the function, which looks like:
```racket
; [name-of-function] : [TypesOfInputs...] -> TypeOfOutput
```

Examples of existing functions (simplified):
```racket
;; `+` takes two Numbers and outputs a Numbers

; + : Number Number -> Number
...

;; `rectangle` takes two Numbers, an OutlineMode, and a Color, and outputs an
;; Image
;; where OutlineMode is an enumeration of "solid" or "outline", and
;; Color is a predefined struct data definition

; rectangle : Number Number OutlineMode Color -> Image
...
```
In these cases, the signatures of these functions are:
- `; + : Number Number -> Number`
- `; rectangle : Number Number OutlineMode Color -> Image`

Then, underneath the signature of your function, you should include a **purpose** of your function, a comment on what this function does.

The combined signatures and purposes of the above functions would be:
```racket
; + : Number Number -> Number
; Adds up all numbers.
...

; rectangle : Number Number OutlineMode Color -> Image
; Constructs a rectangle with the given width, height, mode, and color.
...
```

Finally, write the **header** of your function, which simply looks like:
```racket
(define ([name-of-function] [input-arguments...])
  ...)
```
You can just write this first without worrying about what to put inside the function itself, and this in itself will get you partial credit.
```racket
; + : Number Number -> Number
; Adds up all numbers.
(define (+ x y)
  ...)

; rectangle : Number Number OutlineMode Color -> Image
; Constructs a rectangle with the given width, height, mode, and color.
(define (rectangle width height outline-mode color)
  ...)
```

### Examples
Underneath your purpose, you should provide examples of outputs your function will yield, given example inputs.
```racket
; + : Number Number -> Number
; Adds up all numbers.
; Examples:
; - (+ 1 1) -> 2
; - (+ 1 -1) -> 0
; - (+ 0 188999) -> 118999
(define (+ x y)
  ...)

; circle : Number OutlineMode Color -> Image
; Constructs a rectangle with the given width, height, mode, and color.
; Examples:
; - (rectangle 100 100 "solid" "red") ->
```

![](Square-100-Solid-Red.png)

```racket
; - (rectangle 200 100 "outline" "black") -> a 200x100 rectangle with a black
;   outline and no fill
; - ... (it's kinda hard to do image examples here but you get the idea)
(define (rectangle width height outline-mode color)
  ...)
```

### Strategy and Body
A strategy is a method with which your function processes data.

The strategies the professor gives you are:
- [Decision Tree](#decision-tree)
- [Interval Decomposition](#interval-decomposition)
- [Structural Decomposition](#structural-decomposition)
- [Function Composition](#function-composition)
- Domain Knowledge

#### Decision Tree
A function using the decision tree strategy makes decisions on what to do based on the input data.
With Racket, it generally looks like a `cond` statement or `if` statements.

Example: A function that draws a circle either solid or outline.
Below is our signature, purpose, header, and strategy.
```racket
; make-red-circle : Boolean -> Image
; Constructs a red circle with radius 100, solid if the input is `true`, and
; outline if the input is `false`.
; Examples:
; - ...
; Strategy: Decision Tree
(define (make-red-circle is-solid?)
  ...)
```

First, you would want to make a template of this strategy.
You would do this by thinking about what decision we want to make within the function.
In this case, we are deciding what to do between with the input parameter.
I call this template function `make-solid-or-outline-shape`.

```racket
#;
(define (make-solid-or-outline-shape is-solid? ...)
  (if is-solid?
      ... ; yes, it's solid
      ...)) ; no, it's not solid

;; or, equivalently,
#;
(define (make-solid-or-outline-shape is-solid? ...)
  (cond
    [is-solid? ...] ; yes, it's solid
    [else ...])) ; no, it's not solid
```

Then, we can fill out the **body** of our function, by copying and pasting the template, renaming our function, and replacing the `...`s with the appropriate code.

```racket
(define (make-red-circle is-solid?)
  (if is-solid?
      (circle 100 "solid" "red")
      (circle 100 "outline" "red")))

;; or, equivalently,
(define (make-red-circle is-solid?)
  (cond
    [is-solid? (circle 100 "solid" "red")]
    [else (circle 100 "outline" "red")]))
```

#### Interval Decomposition
Interval decomposition is a special case of decision tree, where the conditions you deal with are based on number intervals.

Example: We want a function that turns a number grade into a letter grade.

Data definitions:
```racket
; A NumberGrade falls in the ranges:
; - [0,60)
; - [60,70)
; - [70,80)
; - [80,90)
; - [90,∞)

; A LetterGrade is one of:
; - "A"
; - "B"
; - "C"
; - "D"
; - "F"
```

Signature, purpose, header, examples:
```racket
; grade/number->letter : NumberGrade -> LetterGrade
; Converts a numeric grade to a letter grade.
; Examples:
; - (grade/number->letter 0) -> "F"
; - (grade/number->letter 95) -> "A"
; - (grade/number->letter 60) -> "D"
; Strategy: Interval Decomposition
(define (grade/number->letter number-grade)
  ...)
```

We use the strategy of interval decomposition.
We make a template function to account for all of the intervals that the `NumberGrade` falls into:
```racket
#;
(define (process-number-grade number-grade ...)
  (cond
    [(>= number-grade 90) ...]
    [(>= number-grade 80) ...]
    [(>= number-grade 70) ...]
    [(>= number-grade 60) ...]
    [(>= number-grade 0) ...]))
```

Then, we copy and paste the template, rename our function, and fill in the **body** of our function:
```racket
; grade/number->letter : NumberGrade -> LetterGrade
; Converts a numeric grade to a letter grade.
; Examples:
; - (grade/number->letter 0) -> "F"
; - (grade/number->letter 95) -> "A"
; - (grade/number->letter 60) -> "D"
; Strategy: Interval Decomposition
(define (grade/number->letter number-grade)
  (cond
    [(>= number-grade 90) "A"]
    [(>= number-grade 80) "B"]
    [(>= number-grade 70) "C"]
    [(>= number-grade 60) "D"]
    [(>= number-grade 0) "F"]))
```

Another example:
```racket
; number-grade-passing? : NumberGrade -> Boolean
; Determines whether a NumberGrade is a passing grade.
; Examples:
; - (number-grade-passing? 55) -> #false
; - (number-grade-passing? 70) -> #true
(define (number-grade-passing? number-grade)
  (cond
    [(>= number-grade 60) #true]
    [else #false]))
```
or, more succintly,
```racket
(define (number-grade-passing? number-grade)
  (>= number-grade 60))
```
This would also fall under interval decomposition.

#### Structural Decomposition
Structural decomposition is another special case of decision tree, where the conditions we use are based on the *structure of our data*, or in other words, the **data type** that we are dealing with.

##### Structural Decomposition with Enumerations
In our template, we would want to account for all possible values in the enumeration.

Using our [`LetterGrade` example](#interval-decomposition) as an input to a function, our template would look like:
```racket
#;
(define (process-letter-grade letter-grade ...)
  (cond
    [(string=? letter-grade "A") ...]
    [(string=? letter-grade "B") ...]
    [(string=? letter-grade "C") ...]
    [(string=? letter-grade "D") ...]
    [(string=? letter-grade "F") ...]))
```

Writing a function to convert a `LetterGrade` to the lower bound of the related `NumberGrade`,
```racket
; lower-bound-of-letter-grade : LetterGrade -> NumberGrade
; Takes a LetterGrade and returns the lower bound of its associated NumberGrade.
; Examples:
; - (lower-bound-of-letter-grade "A") -> 90
; - (lower-bound-of-letter-grade "C") -> 70
(define (lower-bound-of-letter-grade letter-grade)
  ...)
```

We copy and paste the template, rename our function, and fill in the **body**:
```racket
; lower-bound-of-letter-grade : LetterGrade -> NumberGrade
; Takes a LetterGrade and returns the lower bound of its associated NumberGrade.
; Examples:
; - (lower-bound-of-letter-grade "A") -> 90
; - (lower-bound-of-letter-grade "C") -> 70
(define (lower-bound-of-letter-grade letter-grade)
  (cond
    [(string=? letter-grade "A") 90]
    [(string=? letter-grade "B") 80]
    [(string=? letter-grade "C") 70]
    [(string=? letter-grade "D") 60]
    [(string=? letter-grade "F") 0]))
```

##### Structural Decomposition with Structs
In our template, we would want to process all the fields of our input structure.
With our [previously used `posn` struct](#structs), our template would look like:
```racket
#;
(define (process-posn posn ...)
  ... (posn-x posn) ...
  ... (posn-y posn) ...)
```

Example: A function that calculates the distance of a `Posn` from the origin:
```racket
; distance-to-origin : Posn -> Number
; Calculates the distance of a Posn from the origin. (sqrt(x^2 + y^2))
; Examples:
; - (distance-to-origin (make-posn 1 0)) -> 1
; - (distance-to-origin (make-posn 1 1)) -> 1.414213562373095
; Strategy: Structural Decomposition
(define (distance-to-origin posn)
  (sqrt (+ (sqr (posn-x posn))
           (sqr (posn-y posn)))))
```

##### Structural Decomposition with Itemizations
In our template, we would want to account for all the different data types associated with our itemization data type.
Using our previous data definitions from ["Itemizations"](#itemizations),
```racket
; A LivingSpace is one of:
; - ResidentialHall
; - ResidentialCollege
; - "off-campus"

; A ResidentialHall is a (make-residential-hall String String)
(define-struct residential-hall (name address))
; interp. `name` is the name of the res hall, and
; `address` is the street address
; Examples:
; - (make-residential-hall "Bobb-McCollough" "2305 Sheridan Road")

; A ResidentialCollege is a (make-residential-college String String String)
(define-struct residential-college (name address theme))
; interp. `name` is the name of the res college,
; `address` is the street address, and
; `theme` is the theme of the res college
; Examples:
; - (make-residential-college "Slivka" "2332 Campus Drive" "engineering")
```

Our template for a function that takes a `LivingSpace` as an input would have to check for the different associated data types, like `ResidentialHall` and `ResidentialCollege`.
Remember the predicate functions that `define-struct` gives us?
We would have to use those to check the type.
```racket
#;
(define (process-living-space living-space ...)
  (cond
    [(residential-hall? living-space) ...]
    [(residential-college? living-space) ...]
    [(string=? living-space "off-campus") ...]))
```
We would also want to structurally decompose of each of the data types we're considering, e.g., the [structural decomposition of structs](#structural-decomposition-with-structs).
```racket
#;
(define (process-living-space living-space ...)
  (cond
    [(residential-hall? living-space) ... (residential-hall-name living-space) ...
                                      ... (residential-hall-address living-space) ...]
    [(residential-college? living-space) ... (residential-college-name living-space) ...
                                         ... (residential-college-address living-space) ...
                                         ... (residential-college-theme living-space) ...]
    [(string=? living-space "off-campus") ...]))
```

Then, to write a function that outputs the name of a `LivingSpace`, we can remove the things we're not considering, and it would look something like this:
```racket
; living-space-name : LivingSpace -> String
; Outputs the name of a LivingSpace.
; Examples:
; - (living-space-name
      (make-residential-hall "Bobb-McCollough" "2305 Sheridan Road"))
      -> "Bobb-McCollough"
; - (living-space-name
      (make-residential-college "Slivka" "2332 Campus Drive" "engineering"))
      -> "Slivka"
; - (living-space-name "off-campus") -> "off-campus"
; Strategy: Structural Decomposition
(define (living-space-name living-space)
  (cond
    [(residential-hall? living-space) (residential-hall-name living-space)]
    [(residential-college? living-space (residential-college-name living-space))
    [(string=? living-space "off-campus") "off-campus"]]))
```

##### Structural Decomposition with [Recursive Data Types](#recursive-data-types)
This is similar to [structural decomposition with structs](#structural-decomposition-with-structs).
However, since our data type references itself, our template function also has to call itself.

Consider our [data defition of the `Friend` data type](#recursive-data-types):
```racket
; An OptionalFriend is one of:
; - Friend
; - empty

; A Friend is a (make-friend String OptionalFriend)
(define-struct friend (name best-friend))
; interp. `name` is the name of the person we're representing, and
; `best-friend` is another Friend object which represents this person's best friend
```
To write a function that outputs a `Friend`, we can start by writing a template that structurally decomposes the struct:
```racket
#;
(define (process-friend friend ...)
  ... (friend-name friend) ...
  ... (friend-best-friend friend) ...)
```
But, if we know that the `(friend-best-friend friend)` has the data type `Friend`, we can make this function call itself to process that value:
```racket
#;
(define (process-friend friend ...)
  ... (friend-name friend) ...
  ... (process-friend (friend-best-friend friend) ...) ...)
```
And finally, we'd want to account for the fact that actually, `(friend-best-friend friend)` is `empty`, so we check for that in our template:
```racket
#;
(define (process-friend friend ...)
  ... (friend-name friend) ...
  ... (if (empty? (friend-best-friend friend))
          ... ; Do something once we know that this person has no best friend
          ; Otherwise, recursively call `process-friend` again to process this
          ; person's best friend too
          ... (process-friend (friend-best-friend friend) ...) ...))
```

Example: Given a person, we want to go through this person's best friend, and then his or her best friend, and so on, until we find a person with no best friend, and output his or her name.
We'll call this function `closest-loner-name`.
```racket
; closest-loner-name : Friend -> String
; Loops through the given person's best friend chain, and outputs the name of
; the person down the list who has no best friend.
; Examples:
; - (closest-loner-name (make-friend "John Watson"
                                     (make-friend "Sherlock Holmes" empty)))
      -> "Sherlock Holmes"
; - Strategy: Structural Decomposition
```
We plug in the template, and rename the function:
```racket
; closest-loner-name : Friend -> String
; Loops through the given person's best friend chain, and outputs the name of
; the person down the list who has no best friend.
; Examples:
; - (closest-loner-name (make-friend "John Watson"
                                     (make-friend "Sherlock Holmes" empty)))
      -> "Sherlock Holmes"
; - Strategy: Structural Decomposition
(define (closest-loner-name friend)
  ... (friend-name friend) ...
  ... (if (empty? (friend-best-friend friend))
          ... ; Do something once we know that this person has no best friend
          ; Otherwise, recursively call `closest-loner-name` again to process
          ; this person's best friend too
          ... (closest-loner-name (friend-best-friend friend)) ...))
```
Now, we just have to think about what to fill in.
Reading the function description again, we want to output the name of this person if we know he or she has no best friend, so we code that in:
```racket
; closest-loner-name : Friend -> String
; Loops through the given person's best friend chain, and outputs the name of the person down the list who has no best friend.
; Examples:
; - (closest-loner-name (make-friend "John Watson"
                                     (make-friend "Sherlock Holmes" empty)))
      -> "Sherlock Holmes"
; - Strategy: Structural Decomposition
(define (closest-loner-name friend)
  ... (friend-name friend) ...
  ... (if (empty? (friend-best-friend friend))
          ; THIS PERSON IS A LONER, so output his or her name
          (friend-name friend)
          ; Otherwise, recursively call `closest-loner-name` again to process
          ; this person's best friend too
          ... (closest-loner-name (friend-best-friend friend)) ...))
```
Finally, looking at all the ellipses, there is nothing else that we need to consider, so we can delete things that we don't need:
```racket
; closest-loner-name : Friend -> String
; Loops through the given person's best friend chain, and outputs the name of
; the person down the list who has no best friend.
; Examples:
; - (closest-loner-name (make-friend "John Watson"
                                     (make-friend "Sherlock Holmes" empty)))
      -> "Sherlock Holmes"
; - Strategy: Structural Decomposition
(define (closest-loner-name friend)
  (if (empty? (friend-best-friend friend))
      ; THIS PERSON IS A LONER, so output his or her name
      (friend-name friend)
      ; Otherwise, PROCESS THE BEST FRIEND to see if they are a loner too
      (closest-loner-name (friend-best-friend friend))))
```

The key to recursion is that we break down our inputs and simplify it, then defer additional logic to subsequent calls of the same function.
Here, if we know that this person has a best friend, we break it down and call `closest-loner-name` again, but with the next friend down the line.
Finally, we know that `closest-loner-name` always outputs a `String`, so it doesn't matter that the function outputs the result of a recursive call to `closest-loner-name`.

#### Function Composition
Function composition simply takes other functions and pieces them together.

Example:
```racket
; mean : Number Number -> Number
; Returns the mean of two numbers.
; Strategy: Function Composition
; (Takes the `+` function and the `/` function
; and composes them into a `mean` function.)
(define (mean a b)
  (/ (+ a b) 2))
```

## Testing
Finally, after you finish writing your function body, you should write tests for that function.

- Use `(check-expect expression expected-expression)` to check for equal values.
- Use `(check-within expression expected-expression delta)` to check that a value is within the expected value, differing by less than `delta`.

Sometimes, if the function is simple enough, you can replace [examples](#examples) with tests.
However, for more complicated functions, you may want to separate examples and tests.
The examples are for helping to see what the expected outputs are for given inputs, while tests are for actually testing whether the function performs as expected.
Once you start writing more complicated functions, tests will become more complicated too, making it harder for you to see what the expected outputs actually are if you use check-expects as examples.

That's it for the design recipe!

# Stepwise Evaluation
Stepwise evaluation is how Racket evaluates functions.
In each step, you take an expression and you manipulate it into something else to get you closer to the final result.
There are a few different types of steps to evaluate the result of a function:

- A plug step is when you replace a function call with the whole function itself, while replacing variables with actual values.
- A cond step is when you look at a boolean and remove branches of code that will not be run.
- An arithmetic step is when you replace predefined Racket functions (like `+`, `equal?`, etc.) with their results.

Here is an example of stepwise evaluation.
Given these function definitions:
```racket
(define (signum x)
  (cond
    [(positive? x) 1]
    [(negative? x) -1]
    [else          0]))

(define (my-abs x)
  (* x (signum x)))
```
Here is how Racket evaluates `(my-abs -8)` to `8`:
```racket
(my-abs -8)
;; -[plug]->
(* -8 (signum -8))
;; -[plug]->
(* -8 (cond
        [(positive? -8) 1]
        [(negative? -8) -1]
        [else          0]))
;; -[arith]->
(* -8 (cond
        [#false         1]
        [(negative? -8) -1]
        [else           0]))
;; -[cond]->
(* -8 (cond
        [(negative? -8) -1]
        [else           0]))
;; -[arith]->
(* -8 (cond
        [#true -1]
        [else  0]))
;; -[cond]->
(* -8 -1)
;; -[arith]->
8
```

**Why would we want to do this?**
It helps us understand how Racket runs functions.

## FAQ
### How do I add quotation marks inside a `String`?
We can use the backslash `\` character to escape characters inside `Strings` by putting them before special characters.
For example, the string of characters `"If you eliminate the impossible," Spock asserted, "whatever remains, however improbable, must be the truth."`, you would just stick backslashes before each quotation:
```racket
(define SMARTASS-QUOTE "\"If you eliminate the impossible,\" Spock asserted, \"whatever remains, however improbable, must be the truth.\"")
```
and this will be a valid `String` in Racket.

### What are the code style conventions?
- All lines are at most 80 characters long.
- All lines are properly indented, including texts in the comments.
- Function names, argument names and constant names (if any) are all meaningful.
- Datatype names are capitalized. constants names are all upper cases. Function names and arguments are all lower cases, using dashes '-' to separate words.
