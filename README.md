# Modifying Objects

## Learning Goals

- Review the concept of reference types
- Practice cloning objects
- Practice modifying objects
- Write pure functions that act on objects

## Introduction

So far we've learned how to access information from objects, both directly using
dot or bracket notation, and dynamically using a `for...in` loop. In this
lesson, we'll learn how to copy and modify objects. We'll also learn how to
modify objects _nondestructively_, so we can keep our functions pure.

## Review of Reference Types

You may recall that we learned in a previous lesson that arrays are what is
known in JavaScript as _reference_ types, meaning that a reference to an array
(e.g., a variable name) is pointing to a location in memory rather than the
value of the array itself. This means that, if you copy an array using the
assignment operator (`=`) and make a change to the original array, the same
change will be made to the copy (and vice versa):

```js
myArray = ["one", "two", "three"];
clone = myArray;

console.log(myArray);
// => ["one", "two", "three"]
console.log(clone);
// => ["one", "two", "three"]

myArray.push("four");

console.log(myArray);
// => ["one", "two", "three", "four"];
console.log(clone);
// => ["one", "two", "three", "four"];
```

Objects in JavaScript are also reference types. This isn't surprising given that
in JavaScript, as we learned in an earlier lesson, arrays _are_ objects.

## Cloning Objects

Given that arrays are objects in JavaScript, you may wonder whether we can use
the spread operator to copy objects as well as arrays. Well, we can! Let's take
a look at an example:

```js
const disneyPrincesses = {
  "The Little Mermaid": "Ariel",
  Aladdin: "Jasmine",
  Moana: "Moana",
};
```

> Note that we aren't following JavaScript variable naming rules for our keys
> here. That's okay, but remember the rules regarding using dot notation vs.
> bracket notation. Also, remember that keys are case sensitive!

Using the spread operator to create a copy that points to a different location
in memory than the original is the same as with arrays, except using curly
braces rather than square brackets:

```js
const princessClone = { ...disneyPrincesses };
```

That's it! Now we can make changes to our clone without changing the original
object.

But why is this important?

You may recall that we talked about the advantages of using pure functions when
we learned how to clone arrays. Not surprisingly, the same advantages apply
here. Plus, when you get to React in the next phase, you'll learn about the
concept of _immutability_ and how central it is to developing using React. It's
the same concept: when you write code that changes a variable, you should make a
copy of the variable, and modify and return the copy rather than the original
variable. Following this practice will make your React code more efficient and
robust and make debugging easier.

It is worth spending some time now making sure you're comfortable with cloning
and modifying objects, because you'll use those skills frequently when you
develop apps using React!

## Modifying Objects

Say we want to add a fourth princess, but we don't want to overwrite our
original object. The first step is to clone the object, which we did above:

```js
const princessClone = { ...disneyPrincesses };
```

Let's log the clone to verify:

```js
console.log(princessClone);
// => {The Little Mermaid: 'Ariel', Aladdin: 'Jasmine', Moana: 'Moana'}
```

Next, we'll add our fourth princess to `princessClone` using the assignment
operator:

```js
princessClone.Tangled = "Rapunzel";
console.log(princessClone);
// => {The Little Mermaid: 'Ariel', Aladdin: 'Jasmine', Moana: 'Moana', Tangled: 'Rapunzel'}
```

We can also do both steps at the same time, by "spreading out" the properties in
one object, adding a new property (or multiple properties) after that, and
wrapping the whole thing in curly braces:

```js
const secondClone = { ...princessClone, "The Princess and the Frog": "Tania" };
console.log(secondClone);
// => {The Little Mermaid: 'Ariel', Aladdin: 'Jasmine', Moana: 'Moana', Tangled: 'Rapunzel', 'The Princess and the Frog": 'Tania'}
```

But we just noticed that we spelled our last princess's name wrong: it should be
Tiana, not Tania. How can we fix that?

### Modifying a Property of an Object

One way we can fix our typo is by directly updating the property:

```js
secondClone["The Princess and the Frog"] = "Tiana";
console.log(secondClone);
// =>  {The Little Mermaid: 'Ariel', Aladdin: 'Jasmine', Moana: 'Moana', Tangled: 'Rapunzel', 'The Princess and the Frog": 'Tiana'}
```

Fixed! However, note that this change is _destructive_ — we've overwritten the
value in the original object. As we've discussed, as a general rule, we want to
make our updates nondestructively.

For example, imagine we have an object that keeps track of accounts at a bank.
We might have a `customer` variable that contains all the information about a
particular customer:

```js
customer = {
  name: "Jose Arroyo",
  accountNumber: "0123456789",
  accountType: "Checking",
  balance: 1140.58,
};
```

When the customer makes a deposit, we need a function to adjust the balance.
Specifically, we need to:

1. Create a clone of the variable so our function is pure,
2. update the `balance` property of the clone to reflect the deposit,
3. include the other properties as they are, without changing them, and
4. return the clone.

We can use the spread operator to help us do this. Assuming we've calculated the
new balance in another part of our program, it would look like this:

```js
const updatedCustomer = { ...customer, balance: newBalance };
return updatedCustomer;
```

In the first line, `...customer` "spreads out" all the current properties of
`customer` into the new object we're creating by wrapping it in curly brackets.
Then `balance: newBalance` _updates_ the value of the `balance` property. The
old value gets overwritten in the clone of the object. Finally, we return the
updated customer object in the second line.

Note that we don't need to create a new variable to hold our updated customer
object. The code to the right of the equals sign,
`{ ...customer, balance: newBalance }`, creates the new object, so we can just
return it directly:

```js
return { ...customer, balance: newBalance };
```

Make sure what we've done here makes sense — it will come in handy later!

## Two Examples

### Example 1: Happy Birthday

In an earlier lesson, we saw an example of an _impure_ function that operated on
an object:

```js
const ada = {
  name: "Ada Lovelace",
  age: 202,
};

function happyBirthday(person) {
  console.log(
    `Happy birthday, ${person.name}! You're ${++person.age} years old!`
  );
}
```

The `happyBirthday` function is impure because of part of the code inside the
template literal, `++person.age`. The `++` operator is destructive: it alters
the `person` object that's passed in as an argument.

Let's update our function to make it pure, using the spread operator:

```js
const ada = {
  name: "Ada Lovelace",
  age: 202,
};

function happyBirthday(person) {
  const newPerson = { ...person, age: person.age + 1 };
  console.log(
    `Happy birthday, ${newPerson.name}! You're ${newPerson.age} years old!`
  );
}
```

Here we're using the spread operator to clone the `person` variable, then
updating the value of the `age` property in the cloned variable. By doing it
this way, the original object remains unchanged.

### Example 2: Mapping Flatbook

Let's try something a bit more complex: we'll use `Array.prototype.map()` on an
array that contains objects that we need to modify. Each object represents an
engineer who was recently onboarded at Flatbook. First off, we need to flip each
new engineer's account from a normal user to an admin:

```js
const oldAccounts = [
  { userID: 15, title: "Developer Apprentice", accessLevel: "user" },
  { userID: 63, title: "Developer Apprentice", accessLevel: "user" },
  { userID: 97, title: "Developer Apprentice", accessLevel: "user" },
  { userID: 12, title: "Developer Apprentice", accessLevel: "user" },
  { userID: 44, title: "Developer Apprentice", accessLevel: "user" },
];

const newEngineers = oldAccounts.map(function (account) {
  return { ...account, accessLevel: "admin" };
});

oldAccounts;
// => [
//      { userID: 15, title: "Developer Apprentice", accessLevel: "user" },
//      { userID: 63, title: "Developer Apprentice", accessLevel: "user" },
//      { userID: 97, title: "Developer Apprentice", accessLevel: "user" },
//      { userID: 12, title: "Developer Apprentice", accessLevel: "user" },
//      { userID: 44, title: "Developer Apprentice", accessLevel: "user" }
//    ]

newEngineers;
// => [
//      { userID: 15, title: "Developer Apprentice", accessLevel: "admin" },
//      { userID: 63, title: "Developer Apprentice", accessLevel: "admin" },
//      { userID: 97, title: "Developer Apprentice", accessLevel: "admin" },
//      { userID: 12, title: "Developer Apprentice", accessLevel: "admin" },
//      { userID: 44, title: "Developer Apprentice", accessLevel: "admin" }
//    ]
```

Here we are calling `.map()` on the original collection and passing in an
anonymous function as the callback. In the callback, we use the spread operator
to create a **new** object and populate it with the current values. We then
overwrite the `accessLevel` property with the updated value.

Note that both parts of the update are nondestructive: `map()` automatically
creates a new array and returns it at the end, and our callback code creates a
new object for each element in the array rather than updating the existing
objects.

Nondestructive updating is an important concept to practice — destructively
modifying objects at multiple points within a code base is one of the biggest
sources of bugs.

Next, we'll update our `newEngineer` objects to indicate that all the new
engineers have been provided a new work laptop:

```js
const equippedEngineers = newEngineers.map(function (eng) {
  return { ...eng, equipment: "Laptop" };
});

equippedEngineers;
// => [
//      { userID: 15, title: "Developer Apprentice", accessLevel: "admin", equipment: "Laptop" },
//      { userID: 63, title: "Developer Apprentice", accessLevel: "admin", equipment: "Laptop" },
//      { userID: 97, title: "Developer Apprentice", accessLevel: "admin", equipment: "Laptop" },
//      { userID: 12, title: "Developer Apprentice", accessLevel: "admin", equipment: "Laptop" },
//      { userID: 44, title: "Developer Apprentice", accessLevel: "admin", equipment: "Laptop" }
//    ]
```

Challenge: Try rewriting the examples above so that both updates are made at the
same time. To do this, you will only use `.map()` once instead of twice. In the
new array that's returned, the object representing each engineer should contain
both the update to the `accessLevel` property _and_ the new `equipment`
property.

<details><summary><b>Answer</b></summary><p>
<code>const allInOneEngineers = oldAccounts.map(function (account) {</code><br/>
<code>return {...account, accessLevel: "admin", equipment: "Laptop"}</code><br/>
<code>});</code>
</p></details>

## A Final Note About Cloning Objects

There is another way to clone an object, using the built-in `Object.assign()`
method. Let's return to the `happyBirthday` example to see how it works:

```js
// Using the spread operator
const newPerson = { ...person, age: person.age + 1 };
// Using Object.assign()
const newPerson = Object.assign({}, person, { age: person.age + 1 });
```

`Object.assign()` works as follows:

1. Passing an empty object `{}` as the first argument creates a _new_ object
   which gets returned at the end, rather than modifying the original object (in
   this case, `person`).
2. Passing `person` as the second argument populates the new object with the
   properties in the `person` object.
3. The third argument, also an object, is _merged_ into the new object. Since
   the new object contains a property that already exists in `person`, the value
   of that property will be replaced. If the third argument contains a property
   that _isn't_ currently in `person`, that property will be created with the
   value provided.

As you can see, the syntax of `Object.assign()` is complicated and not
especially intuitive. We recommend using the spread operator instead because
it's cleaner and easier to read. However, although the spread operator has been
around for a while now, you may still see examples of `Object.assign()` being
used in existing code so it's important to understand what it's doing.

## Conclusion

In this lesson, we dug deep into copying and modifying objects. We learned how
to modify objects nondestructively using the spread operator, and worked through
some examples with complex nested data structures.

## Resources

- [MDN: Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)
- [MDN: Spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
