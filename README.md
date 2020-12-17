# Chapter 0 - EdgeDB and the book Easy EdgeDB

Thanks for checking out our new book to learn Easy EdgeDB!

We've had quite the journey so far up to the release of this book. It began in spring 2019 with the public release of [EdgeDB Alpha 1](https://www.edgedb.com/blog/edgedb-1-0-alpha-1), showing the possibilies of an open source database built on top of PostgreSQL that combines the simplicity of a NoSQL database with relational model’s powerful querying, strictness, consistency, and performance.

Twenty months and five more Alpha versions later, EdgeDB has now reached beta 1.0. Here are just a few changes that happened along the way:

- [A new JavaScript driver](https://github.com/edgedb/edgedb-js) in Alpha 2.0 and more powerful bindings in Alpha 4.0,
- [We took our cli and Rewrote It In Rust](https://github.com/edgedb/edgedb-cli)! Unsurprisingly, the performance improvements were substantial.
- [Constraints were added to entire types](https://www.edgedb.com/blog/edgedb-1-0-alpha-5-luhman) and JSON casting became as easy as typing `<json>` before a query (Alpha 5.0),
- Native [GraphQL support](https://www.edgedb.com/docs/tutorial/graphql#ref-tutorial-graphql) has been improved including multiple mutations in a single query, and an `exists` filter (Alpha 5.0),
- [Improved UPDATE syntax](https://www.edgedb.com/blog/edgedb-1-0-alpha-6-wolf-359), enums, easier function syntax and cli improvement (Alpha 6.0).

Now that we've reached beta 1.0, we're even more excited to see people give it a try. Up to now our learning and introductory material has been mostly found in the [interactive tutorial](https://tutorial.edgedb.com/), [documentation](https://edgedb.com/docs/) and [blog posts](https://www.edgedb.com/blog/we-can-do-better-than-sql). This time we've put something much longer together: a book large enough (and fun enough) to make any interested beginner into a comfortable intermediate EdgeDB user by the end.

Easy EdgeDB is a bit of a unique book. Here's how it differs from what you might expect from text of its type:

## A story to follow

The book is divided into 20 chapters in which we imagine that we are creating a database for a game. The setting for our imaginary game is the scenes and locations from the book Dracula by Bram Stoker, published in 1897. Our job is to think a little about how to represent items like characters, events, locations, and dates in the book in a database used by a role-playing game built separately. In other words, something like C++/Rust/C# on the frontend, and EdgeDB as the game's database.

Bram Stoker's Dracula was the perfect choice for this textbook for a few reasons:

- It's a fun read. We're excited to see you give EdgeDB a try and hope to make the introduction as painless and immersive as possible. We think that anyone who has spent an hour or so with EdgeDB will walk away excited about its possibilities, but for that to happen it's our job to make the experience a pleasant one. Bram Stoker's novel has really helped out here.
- It's a so-called *epistolary novel*: a novel written through the letters, diaries, and communications written by the main characters. That means that each entry has a date and an author, which makes it a great fit for a database.
- It's copyright free. Our book only scratches the surface of what a real database for a game based on this book would be like, but who knows? Maybe some readers will like it enough to take the concept a bit farther and make it into the real thing. And if that's the case, then an open source database software based on a book completely free of copyright is the best way to start.

## Plain English

We want to give as many people as possible the opportunity to sit down and give EdgeDB a try, and so we've opted for a style of writing that's simple and straightforward. Not baby talk, just plain English. We have three types of people in mind here:

- People unfamiliar with how to build a database but ready to understand how they work if the concepts are explained in a straightforward way,
- People who *are* familiar with databases but maybe aren't in the mood to wade through dense verbiage for a product they've never seen before,
- People with English as a second (or third, fourth...) language who would much prefer reading a book in plain English for the same reason.

## Interactivity and experimentation

Because the book uses the events in the book for the background, we need a database to tie everything together. It will need to show the connections between the characters, locations, dates, and more. It starts with a simple schema (structure) and builds up from there, changing it as we go. The idea is to simulate the mental process for someone new to EdgeDB that has been given the task of putting this all together. That includes sometimes modifying the schema, creating new types, deleting ones that aren't used, and all the tinkering you'd see in real life. 

Going through the book, we will learn how to use queries that are more and more complex. Each chapter contains the schema and inserted data that we've built up so far, and a REPL for you to experiment with. On top of that, each chapter has a number of questions for you to solve if you feel like a small challenge.

## Beauty

We looked far and wide, and didn't see any rule that a text on database software has to be dry and image free. We teamed up with [lyadova.art](https://www.instagram.com/lyadova.art/) on Instagram to put together some beautiful sketches that combine the atmosphere of the book Dracula with the most important schema and query concepts per chapter.

[So let's get started - on to Chapter 1!](../chapter1/index.md)



# Chapter 1 - Jonathan Harker travels to Transylvania

![thththt](Sample_image.png)

In the beginning of the book we see the main character Jonathan Harker, a young lawyer who is going to meet a client. The client is a rich man named Count Dracula who lives somewhere in Eastern Europe. Jonathan still doesn't know that Count Dracula is a vampire, so he's enjoying the trip to a new part of Europe. The book begins with Jonathan writing in his journal as he travels. The parts that are good for a database in **bold**:

>**3 May**. **Bistritz**.—Left **Munich** at **8:35 P.M.**, on **1st May**, arriving at **Vienna** early next morning; should have arrived at 6:46, but train was an hour late. **Buda-Pesth** seems a wonderful place, from the glimpse which I got of it from the train...

## Schema, object types

This is already a lot of information, and it helps us start to think about our database schema. The language used for EdgeDB is called EdgeQL, and is used to define, mutate, and query data. Inside it is [SDL (schema definition language)](https://edgedb.com/docs/edgeql/sdl/index#ref-eql-sdl) that makes migration easy, and which we will learn in this book. So far our schema needs the following:

- Some kind of City or Location type. These types that we can create are called [object types](https://www.edgedb.com/docs/datamodel/objects#object-types), made out of properties and links. What properties should a City type have? Perhaps a name and a location, and sometimes a different name or spelling. Bistritz for example is now called Bistrița (it's in Romania), and Buda-Pesth is now written Budapest.
- Some kind of Person type. We need it to have a name, and also a way to track the places that the person visited.
 
To make a type inside a schema, just use the keyword `type` followed by the type name, then `{}` curly brackets. Our `Person` type will start out like this:

```
type Person {
}
```

That's all you need to create a type, but there's nothing inside there yet. Inside the brackets we add the properties for our `Person` type. Use `required property` if the type needs it, and just `property` if it is optional.

```
type Person {
  required property name -> str;
  property places_visited -> array<str>;
}
```

With `required property name` our `Person` objects are always guaranteed to have a name - you can't make a `Person` object without it. Here's the error message if you try:

```
MissingRequiredError: missing value for required property default::Person.name
```

A `str` is just a string, and goes inside either single quotes: `'Jonathan Harker'` or double quotes: `"Jonathan Harker"`. The `\` escape character before a quote  makes EdgeDB treat it like just another letter: `'Jonathan Harker\'s journal'`.

An `array` is a collection of the same type, and our array here is an array of `str`s. We want it to look like this: `["Bistritz", "Vienna", "Buda-Pesth"]`. The idea is to easily search later and see which character has visited where.

`places_visited` is not a `required` property because we might later add minor characters that don't go anywhere. Maybe one person will be the "innkeeper_in_bistritz" or something, and we won't know or care about `places_visited` for him.

Now for our City type:

```
type City {
  required property name -> str;
  property modern_name -> str;
}
```

This is similar, just properties with strings. The book Dracula was published in 1897 when spelling for cities was sometimes different. All cities have a name in the book (that's why it's `required`), but some won't need a different modern name. Vienna is still Vienna, for example. We are imagining that our game will link the city names to their modern names so we can easily place them on a map.

## Migration

We haven't created our database yet, though. There are two small steps that we need to do first [after installing EdgeDB](https://edgedb.com/download). First we create a database with the `CREATE DATABASE` keyword and our name for it:

```
CREATE DATABASE dracula;
```

Then we type ```\c dracula``` to connect to it.

Lastly, we we need to do a migration. This will give the database the structure we need to start interacting with it. Migrations are not difficult with EdgeDB:

- First you start them with `START MIGRATION TO {}`
- Inside this you add at least one `module`, so your types can be accessed. A module is a namespace, a place where similar types go together. You go up and down a level inside a module with `::`. If you wrote `module default` and then `type Person`, the type `Person` would be at `default::Person`. So when you see a type like `std::bytes` for example, this means the type `bytes` inside `std` (the standard library).
- Then you add the types we mentioned above, and type `POPULATE MIGRATION` to add the data.
- Finally, you type `COMMIT MIGRATION` and the migration is done.

There are naturally a lot of other commands beyond this, though we won't need them for this book. You could bookmark these four pages for later use, however:

- [Admin commands](https://www.edgedb.com/docs/cheatsheet/admin): Creating user roles, setting passwords, configuring ports, etc.
- [CLI commands](https://www.edgedb.com/docs/cheatsheet/cli): Creating databases, roles, setting passwords for roles, connecting to databases, etc.
- [REPL commands](https://www.edgedb.com/docs/cheatsheet/repl): Mostly shortcuts for a lot of the commands we'll be using in this book.
- [Various commands](https://www.edgedb.com/docs/edgeql/statements/tx_rollback#rollback) about rolling back transactions, declaring savepoints, and so on.

There are also a few places to download packages to highlight your syntax if you like. EdgeDB has these packages available for [Atom](https://atom.io/packages/edgedb), [Visual Studio Code](https://marketplace.visualstudio.com/itemdetails?itemName=magicstack.edgedb), [Sublime Text](https://packagecontrol.io/packages/EdgeDB), and [Vim](https://github.com/edgedb/edgedb-vim).

Here's the `City` type we just made with syntax highlighting:

![type City](type_City.png)

## Selecting

Here are three operators in EdgeDB that have the `=` sign: 

- `:=` is used to declare, 
- `=` is used to check equality (not `==`),
- `!=` is the opposite of `=`.

Let's try them out with `SELECT`. `SELECT` is the main query command in EdgeDB, and you use it to see results based on the input that comes after it. 

By the way, keywords in EdgeDB are case insensitive, so `SELECT`, `select` and `SeLeCT` are all the same. But using capital letters is the normal practice for databases so we'll continue to use them that way.

First we'll just select a string:

```
SELECT 'Jonathan Harker\'s journey begins.';
```

This returns `{'Jonathan Harker\'s journey begins.'}`, no surprise there.

Next we'll use `:=` to assign a variable:

```
SELECT jonathans_name := 'Jonathan Harker';
```

This just returns what we gave it: `{'Jonathan Harker'}`. But this time it's a string that we assigned called `jonathans_name` that is being returned.

Now let's do something with this variable. We can do a `SELECT` by first assigning to `jonathans_name` and then comparing it to `'Count Dracula'`:

```
SELECT jonathans_name := 'Jonathan Harker' = 'Count Dracula';
SELECT jonathans_name := 'Jonathan Harker' != 'Count Dracula';
```

The output is `{false}`, then `{true}`. Of course, you can just write `SELECT 'Jonathan Harker' = 'Count Dracula'` and `SELECT 'Jonathan Harker' != 'Count Dracula'` for the same result. Soon we will actually do something with the variables we assign with `:=`.

## Inserting objects

Let's get back to the schema. Later on we can think about adding time zones and locations for the cities for our imaginary game. But in the meantime, we will add some items to the database using `INSERT`. 

Don't forget to separate each property by a comma, and finish the `INSERT` with a semicolon. EdgeDB also prefers two spaces for indentation.

```
INSERT City {
  name := 'Munich',
};

INSERT City {
  name := 'Buda-Pesth',
  modern_name := 'Budapest'
};

INSERT City {
  name := 'Bistritz',
  modern_name := 'Bistrița'
};
```

Note that a comma at the end is optional - you can put it in or leave it out. Here we put a comma at the end sometimes and left it out at other times to show this.

Finally, the `Person` insert would look like this:

```
INSERT Person {
  name := 'Jonathan Harker',
  places_visited := ["Bistritz", "Vienna", "Buda-Pesth"],
};
```

But hold on a second. That insert won't link it to any of the `City` inserts that we already did. Here's where our schema needs some improvement:

- We have a `Person` type and a `City` type,
- The `Person` type has the property `places_visited` with the names of the cities, but they are just strings in an array. It would be better to link this property to the `City` type somehow.

So let's not do that `Person` insert. We'll fix the `Person` type soon by changing `array<str>` from a `property` to something called `multi link` to the `City` type. This will actually join them together.

But first let's look a bit closer at what happens when we use `INSERT`.

As you can see, `str`s are fine with unicode letters like ț. Even emojis and special characters are just fine: you could even create a `City` called '🤠' or '(╯°□°)╯︵ ┻━┻' if you wanted to.

EdgeDB also has a byte literal type that gives you the bytes of a string, but they must be characters that are 1 byte long. You create it by adding a `b` in front of the string:

```
SELECT b'Bistritz';
{b'Bistritz'}
```

And because the characters must be 1 byte, only ASCII works for this type. So the name in `modern_name` as a byte literal will generate an error because of the `ț`:

```
SELECT b'Bistrița';
error: invalid bytes literal: character 'ț' is unexpected, only ascii chars are allowed in bytes literals
```

Every time you `INSERT` an item, EdgeDB gives you a `uuid` back. That's the unique number for each item. It will look like this:

```
{Object {id: d2af670c-f1d6-11ea-a30f-8b40bc5413e0}}
```

It is also what shows up when you use `SELECT` to select a type. Just typing `SELECT` with a type will show you all the `uuid`s for the type. Let's look at all the cities we have so far:

```
SELECT City;
```

This gives us three items:

```
{
  Object {id: d2b64e00-f1d6-11ea-a30f-1f161d0b15ae},
  Object {id: d2c023b2-f1d6-11ea-a30f-e3069a47b57e},
  Object {id: d37bc838-f1d6-11ea-a30f-afb031317264},
}
```

This only tells us that there are three objects of type `City`. To see inside them, we can add property or link names to the query. This is called describing the [shape](https://www.edgedb.com/docs/edgeql/expressions/shapes/#ref-eql-expr-shapes) of the data we want. We'll select all `City` types and display their `modern_name` with this query:

```
SELECT City {
  modern_name,
};
```

Once again, you don't need the comma after `modern_name` because it's at the end of the query.

You will remember that one of our cities (Vienna) doesn't have anything for `modern_name`. But it still shows up as an "empty set", because every value in EdgeDB is a set of elements, even if there's nothing inside. Here is the result:

```
{Object {modern_name: {}}, Object {modern_name: 'Budapest'}, Object {modern_name: 'Bistrița'}}
```

So there is some object with an empty set for `modern_name`, while the other two have a name. This shows us that EdgeDB doesn't have `null` like in some languages: if nothing is there, it will return an empty set.

The first object is a mystery so we'll add `name` to the query so we can see that it's the city of Vienna:

```
SELECT City {
  name,
  modern_name
};
```

This gives the output:


```
{
  Object {name: 'Vienna', modern_name: {}},
  Object {name: 'Bistritz', modern_name: 'Bistrița'},
  Object {name: 'Buda-Pesth', modern_name: 'Budapest'}
}
```

If you just want to return a single part of a type without the object structure, you can use `.` after the type name. For example, `SELECT City.modern_name` will give this output:

```
{'Budapest', 'Bistrița'}
```

This type of expression is called a *path expression* or a *path*, because it is the direct path to the values inside. And each `.` moves on to the next path, if there is another one to follow.

You can also change property names like `modern_name` to any other name if you want by using `:=` after the name you want. Those names you choose become the variable names that are displayed. For example:

```
SELECT City {
  name_in_dracula := .name,
  name_today := .modern_name,
};
```

This prints:

```
{
  Object {name_in_dracula: 'Munich', name_today: {}},
  Object {name_in_dracula: 'Buda-Pesth', name_today: 'Budapest'},
  Object {name_in_dracula: 'Bistritz', name_today: 'Bistrița'},
}
```

This will not change anything inside the schema - it's just a quick variable name to use in a query.

By the way, `.name` is short for `City.name`. You can also write `City.name` each time (that's called the *fully qualified name*), but it's not required.

So if you can make a quick `name_in_dracula` property from `.name`, can we make other things too? Indeed we can. For the moment we'll just keep it simple but here is one example:

```
SELECT City {
  name_in_dracula := .name,
  name_today := .modern_name,
  oh_and_by_the_way := 'This is a city in the book Dracula'
};
```

And here is the output:

```
{
  Object {name_in_dracula: 'Munich', name_today: {}, oh_and_by_the_way: 'This is a city in the book Dracula'},
  Object {name_in_dracula: 'Buda-Pesth', name_today: 'Budapest', oh_and_by_the_way: 'This is a city in the book Dracula'},
  Object {name_in_dracula: 'Bistritz', name_today: 'Bistrița', oh_and_by_the_way: 'This is a city in the book Dracula'},
}
```

Also note that `oh_and_by_the_way` is of type `str` even though we didn't have to tell it. EdgeDB is strongly typed: everything needs a type and it will not try to mix them together. So if you write `SELECT 'Jonathan Harker' + 8;` it will simply refuse with an error: `QueryError: operator '+' cannot be applied to operands of type 'std::str' and 'std::int64'`.

On the other hand, it can use "type inference" to guess the type, and that is what it does here: it knows that we are creating a `str`. We will look at changing types and working with different types soon.

## Links

So now the last thing left to do is to change our `property` in `Person` called `places_visited` to a `link`. Right now, `places_visited` gives us the names we want, but it makes more sense to link `Person` and `City` together. After all, the `City` type has `.name` inside it which is better to link to than rewriting everything inside `Person`. We'll change `Person` to look like this:

```
type Person {
  required property name -> str;
  multi link places_visited -> City;
}
```

We wrote `multi` in front of `link` because one `Person` should be able to link to more than one `City`. The opposite of `multi` is `single`, which only allows one object to link to it. But `single` is the default, so if you just write `link` then EdgeDB will treat it as `single`.

Now when we insert Jonathan Harker, he will be connected to the type `City`. Don't forget that `places_visited` is not `required`, so we can still insert with just his name to create him:

```
INSERT Person {
  name := 'Jonathan Harker',
};
```

But this would only create a `Person` type connected to the `City` type but with nothing in it. Let's see what's inside:

```
SELECT Person {
  name,
  places_visited
};
```

Here is the output: `{Object {name: 'Jonathan Harker', places_visited: {}}}`

But we want to have Jonathan be connected to the cities he has traveled to. We'll change `places_visited` when we `INSERT` to `places_visited := City`:

```
INSERT Person {
  name := 'Jonathan Harker',
  places_visited := City,
};
```

We haven't filtered anything, so it will put all the `City` types in there. Now let's see the places that Jonathan has visited. The code below is almost but not quite what we need: 

```
select Person {
  name,
  places_visited
};
```

Here is the output:

```
  Object {
    name: 'Jonathan Harker',
    places_visited: {
      Object {id: 5ba2c9b2-f64d-11ea-acc7-d3a96742e345},
      Object {id: 5bbb2368-f64d-11ea-acc7-ffb002eabe1a},
      Object {id: 5bc2bba0-f64d-11ea-acc7-8b468bbfae39},
    },
  },
```

Close! But we didn't mention any properties inside `City` so we just got the object id numbers. Now we just need to let EdgeDB know that we want to see the `name` property of the `City` type. To do that, add a colon and then put `name` inside curly brackets.

```
select Person {
  name,
  places_visited: {
    name
  }
};
```

Success! Now we get the output we wanted:

```
  Object {
    name: 'Jonathan Harker',
    places_visited: {Object {name: 'Munich'}, Object {name: 'Buda-Pesth'}, Object {name: 'Bistritz'}},
  },
```

Of course, Jonathan Harker has been inserted with a connection to every city in the database. Right now we only have three `City` objects, so this is no problem yet. But later on we will have more cities and won't be able to just write `places_visited := City` for all the other characters. For that we will need `FILTER`, which we will learn to use in the next chapter.

[Here is all our code so far up to Chapter 1.](code.md)

## Time to practice

1. Entering `SELECT my_name = 'Timothy' != 'Benjamin';` returns an error. Try adding one character to make it return `{true}`.
2. Try inserting a `City` called Constantinople, but now known as İstanbul.
3. Try displaying all the names of the cities in the database. (Hint: you can do it in a single line of code and won't need `{}` to do it)
4. Try selecting all the `City` types along with their `name` and `modern_name` properties, but change `.name` to say `old_name` and change `modern_name` to say `name_now`.
5. Will typing `SelecT City;` produce an error?

[See the answers here.](answers.md)

Up next in Chapter 2: [Jonathan Harker arrives in Romania.](../chapter2/index.md)

# Chapter 2 - At the Hotel in Bistritz

We continue to read the story as we think about the database we need to store the information. The important information is in bold:

>Jonathan Harker has found a hotel in **Bistritz**, called the **Golden Krone Hotel**. He gets a welcome letter there from Dracula, who is waiting in his **castle**. Jonathan Harker will have to take a **horse-driven carriage** to get there tomorrow. We also see that Jonathan Harker is from **London**. The innkeeper at the Golden Krone Hotel seems very afraid of Dracula. He doesn't want Jonathan to leave and says it will be dangerous, but Jonathan doesn't listen. An old lady gives Jonathan a golden crucifix and says it will protect him. Jonathan is embarrassed, and takes it to be polite. Jonathan has no idea how much it will help him later.

Now we are starting to see some detail about the city. Reading the story, we see that we could add another property to `City`, and we will call it `important_places`. That's where places like the **Golden Krone Hotel** could go. We're not sure if the places will be their own types yet, so we'll just make it an array of strings: `property important_places -> array<str>;` We can put the names of important places in there and maybe develop it more later. It will now look like this:

```
type City {
  required property name -> str;
  property modern_name -> str;
  property important_places -> array<str>;
}
```

Now our original insert for Bistritz will look like this:

```
INSERT City {
  name := 'Bistritz',
  modern_name := 'Bistrița',
  important_places := ['Golden Krone Hotel'],
};
```

## Enums, scalar types, and extending

We now have two types of transport in the book: train, and horse-drawn carriage. The book is based in 1887, and our game will let the characters use types of transport that were available that year. Here an `enum` (enumeration) is probably the best choice, because an `enum` is about making one choice between options. The variants of the enum should be written in UpperCamelCase.

Here we see the word `scalar` for the first time: this is a `scalar type` because it only holds a single value at a time. The other types (`City`, `Person`) are `object types` because they can hold multiple values at the same time.

The other keyword we will see for the first time is `extending`, which means to take a type as a base and extend it. This gives you all the power of the type that you are extending, and adds some more options. We will write our `Transport` type like this:

```
scalar type Transport extending enum<Feet, Train, HorseDrawnCarriage>;
```

Did you notice that `scalar type` ends with a semicolon and the other types don't? That's because the other types have a `{}` to make a full expression. But here on a single line we don't have `{}` so we need the semicolon to show that the expression ends here.

This `Transport` type is going to be for player characters in our game, not the people in the book (their stories and choices are already finished). That means that we will need a `PC` type and an `NPC` type, but our `Person` type should stay too - we can use it a base type for both. To do this, we can make `Person` an `abstract type` instead of just a `type`. Then with this abstract type, we can use the keyword `extending` for the other `PC` and `NPC` types.

So now this part of the schema looks like this:

```
abstract type Person {
  required property name -> str;
  multi link places_visited -> City;
}

type PC extending Person {
  required property transport -> Transport;
}

type NPC extending Person {
}
```

Now the characters from the book will be `NPC`s (non-player characters), while `PC` is being made with our game in mind. And because `Person` is now an abstract type, we can't insert it directly anymore. It will give us this error if we try to do something like `INSERT Person {name := 'Mr. HasAName'};`:

```
error: cannot insert into abstract object type 'default::Person'
  ┌─ query:1:8
  │
1 │ INSERT Person {
  │        ^^^^^^^ error
```

No problem - just change `Person` to `NPC` and it will work.

Also, `SELECT` on an abstract type is just fine - it will select all the types that extend from it.

Let's also experiment with a player character. We'll make one called Emil Sinclair who starts out traveling by horse-drawn carriage. We'll also just give him `City` so he'll have all three cities.

```
INSERT PC {
  name := 'Emil Sinclair',
  places_visited := City,
  transport := <Transport>HorseDrawnCarriage,
};
```

Note that we didn't just write `HorseDrawnCarriage`, because we have to choose the enum `Transport` and then make a choice of one of the variants. The `<>` angle brackets do *casting*, meaning to change one type into another. EdgeDB won't try to change one type into another unless you ask it to with casting. That's why this won't give us `true`:

```
SELECT 'feet' IS Transport;
```

We will get an output of `{false}`, because 'feet' is just a `str` and nothing else. But this will work:

```
SELECT <Transport>'feet' IS Transport;
```

Then we get `{true}`.

You can cast more than once at a time if you need to. This example isn't something you will need to do but shows how you can cast over and over again if you want:

```
SELECT <str><int64><str><int32>50 is str; 
```

That also gives us `{true}` because all we did is ask if it is a `str`, which it is. 

Casting works from right to left, with the final cast on the far left. So `<str><int64><str><int32>50` means "50 into an int32 into a string into an int64 into a string". Or you can read it left to right like this: "A string from an int64 from a string from an int32 from the number 50".

## Filter

Finally, let's learn how to `FILTER` before we're done Chapter 2. You can use `FILTER` after the curly brackets in `SELECT` to only show certain results. Let's `FILTER` to only show `Person` types that have the name 'Emil Sinclair':

```
SELECT Person {
  name,
  places_visited: {name},
} FILTER .name = 'Emil Sinclair';
```

`FILTER .name` is short for `FILTER Person.name`. You can write `FILTER Person.name` too if you want - it's the same thing.

The output is this: 

```
{Object {name: 'Emil Sinclair', places_visited: {Object {name: 'Munich'}, Object {name: 'Buda-Pesth'}, Object {name: 'Bistritz'}}}}
```

Let's filter the cities now. One flexible way to search is with `LIKE` or `ILIKE` to match on parts of a string. 

- `LIKE` is case-sensitive: "Bistritz" matches "Bistritz" but "bistritz" does not. 
- `ILIKE` is not case-sensitive (the I in ILIKE means **insensitive**), so "Bistritz" matches "BiStRitz", "bisTRITz", etc.

You can also add `%` on the left and/or right which means match anything before or after. Here are some examples with the matched part **in bold**:

- `LIKE Bistr%` matches "**Bistr**itz" (but not "bistritz"),
- `ILIKE '%IsTRiT%'` matches "B**istrit**z",
- `LIKE %athan Harker` matches "Jon**athan Harker**",
- `ILIKE %n h%` matches "Jonatha**n H**arker".

Let's `FILTER` to get all the cities that start with a capital B. That means we'll need `LIKE` because it's case-sensitive:

```
SELECT City {
  name,
  modern_name,
} FILTER .name LIKE 'B%';
```

Here is the result:

```
  Object {name: 'Buda-Pesth', modern_name: 'Budapest'},
  Object {name: 'Bistritz', modern_name: 'Bistrița'},
```

You can also index a string with `[]` square brackets, starting at 0. For example, the indexes in the string 'Jonathan' look like this:

```
Jonathan
01234567
```

So `'Jonathan'[0]` is 'J' and `'Jonathan'[4]` is 't'.

Let's try it:

```
SELECT City {
  name,
  modern_name,
} FILTER .name[0]; = 'B'; # First character must be 'B'
```

That gives the same result. Careful though: if you set the number too high then it will try to search outside of the string, which is an error. If we change 0 to 18 (`FILTER .name[18]; = 'B';`), we'll get this:

```
ERROR: InvalidValueError: string index 18 is out of bounds
```

Plus, if you have any `City` types with a name of `''`, even a search for index 0 will cause an error. But if you use `LIKE` or `ILIKE` with an empty parameter, it will just give an empty set: `{}` instead of an error. `LIKE` and `ILIKE` are safer than indexing if there is a chance of having no data in a property.

[Here is all our code so far up to Chapter 2.](code.md)

## Time to practice

1. Change the following `SELECT` to display `{100}` by casting: `SELECT '99' + '1'`;
2. Select all the `City` types that start with 'Mu' (case sensitive).
3. Select the third letter (i.e. index number 2) of the name of every `NPC`.
4. Imagine an abstract type called `HasAString`:

```
abstract type HasAString {
  property string -> str
  };
```

How would you change the `Person` type to extend `HasAString`?

5. This query only shows the id numbers of the places visited. How do you show their name?

```
SELECT Person {
  places_visited
};
```

[See the answers here.](answers.md)

Up next in Chapter 3: [Jonathan gets into the carriage and travels into the cold mountains...](../chapter3/index.md)

# Chapter 3 - Jonathan goes to Castle Dracula

In this chapter we are going to start to think about time, as you can see from what Jonathan Harker is doing:

>Jonathan Harker has just arrived at Castle Dracula after a ride in the carriage through the mountains. The ride was terrible: there was snow, strange blue fires and wolves everywhere. It was night when he arrived. He meets with Count Dracula, goes inside, and they talk all night. Dracula leaves before the sun rises though, because vampires are hurt by sunlight. Days go by, and Jonathan still doesn't know that he's a vampire. But he does notice something strange: the castle seems completely empty. If Dracula is so rich, where are his servants? Who is making his meals that he finds on the table? But Jonathan finds Dracula's stories of history very interesting, and so far is enjoying his trip.

Now we are completely inside Dracula's castle, so this is a good time to create a `Vampire` type. We can extend it from `abstract type Person` because that type  has `name` and `places_visited`, which are good for `Vampire` too. But vampires are different from humans because they can live forever. One possibility is adding `age` to `Person` so that all the other types can use it too. Then `Person' would look like this:

```
abstract type Person {
  required property name -> str;
  multi link places_visited -> City;
  property age -> int16;
}
```

`int16` means a 16 bit (2 byte) integer, which has enough space for -32768 to +32767. That's enough for age, so we don't need the bigger `int32` or `int64` types which are much larger. We also don't want it to be a `required property`, because we don't care about everybody's age.

But we don't want `PC`s and `NPC`s to live up to 32767 years, so let's give `age` only to `Vampire` now and think about the other types later. We'll make `Vampire` a type that extends `Person`, and adds age:

```
type Vampire extending Person {            
  property age -> int16;
}
```

Now we can create Count Dracula. We know that he lives in Romania, but that isn't a city. This is a good time to change the `City` type. We'll change the name to `Place` and make it an `abstract type`, and then `City` can extend from it. We'll also add a `Country` type that does the same thing. Now they look like this:

```
abstract type Place {
  required property name -> str;
  property modern_name -> str;
  property important_places -> array<str>;
}

type City extending Place;

type Country extending Place;
```

We will need to change `places_visited` in the `Person` type now to be a `Place` instead of a `City`. After all, characters can visit more places than just cities:

```
abstract type Person {
  required property name -> str;
  multi link places_visited -> Place;
  property age -> int16;
}
```

Now it's easy to make a `Country`, just do an insert and give it a name. We'll quickly insert `Country` objects for Hungary and Romania:

```
INSERT Country {
  name := 'Hungary'
};
INSERT Country {
  name := 'Romania'
};
```

## Capturing a SELECT expression

With these countries added, we are now ready to make Dracula. First we will change `places_visited` in `Person` from `City` to `Place` so that it can include many things: London, Bistritz, Hungary, etc. We only know that Dracula has been in Romania, so we can do a quick `FILTER` when we select it. When doing this, we put the `SELECT` inside `()` brackets. The brackets are necessary to capture the result of the `SELECT`. In other words, EdgeDB will do the operation inside the brackets, and then that completed result is given to `places_visited`.

```
INSERT Vampire {
  name := 'Count Dracula',
  places_visited := (SELECT Place FILTER .name = 'Romania'),
  # .places_visited is the result of this SELECT query.
};
```

The result is `{Object {id: 0a1b83dc-f2aa-11ea-9f40-038d228e2bba}}`.

The `uuid` there is the reply from the server showing that we were successful (otherwise it would show `{}`).

Let's check if `places_visited` worked. We only have one `Vampire` object now, so let's `SELECT` it:

```
SELECT Vampire {
  places_visited: {
    name
  }
};
```

This gives us: `{Object {places_visited: {Object {name: 'Romania'}}}}` 

Perfect.

## Adding constraints

Now let's think about `age` again. It was easy for the `Vampire` type, because they can live forever. But now we want to give `age` to `PC` and `NPC` too, who are humans who don't live forever (we don't want them living up to 32767 years). For this we can add a "constraint" (a limit). Instead of `age`, we'll give them a new type called `HumanAge`. Then we can write `constraint` on it and use [one of the functions](https://edgedb.com/docs/datamodel/constraints) that it can take. We will use `max_value()`. 

Here's the signature for `max_value()`:

`std::max_value(max: anytype)`

The `anytype` part is interesting, because that means it can work on types like strings too. With a constraint `max_value('B')` for example you couldn't use 'C', 'D', etc.

Now let's go back to our constraint for `HumanAge`, which is 120. The `HumanAge` type looks like this:

```
scalar type HumanAge extending int16 {
  constraint max_value(120);
}
```

Remember, it's a scalar type because it can only have one value. Then we'll add it to the `NPC` type. 

```
type NPC extending Person {
  property age -> HumanAge;
}
```

It's our own type with its own name, but underneath it's an `int16` that can't be greater than 120. So if we write this, it won't work:

```
INSERT NPC {
    name := 'The innkeeper',
    age := 130
};
```

Here is the error: `ERROR: ConstraintViolationError: Maximum allowed value for HumanAge is 120.` Perfect.

Now if we change `age` to 30, we get a message showing that it worked: `{Object {id: 72884afc-f2b1-11ea-9f40-97b378dbf5f8}}`. Now no NPCs can be over 120 years old.

## Deleting objects

Deleting in EdgeDB is very easy: just use the `DELETE` keyword. It's similar to `SELECT` in that you write `DELETE` and then the type, which will by default delete them all. And in the same way as `SELECT`, if you `FILTER` then it will only delete the ones that match the filter.

This similarity to `SELECT` might make you nervous, because if you type something like `SELECT City` then it will select all of them. `DELETE` is the same: `DELETE City` deletes every object for the `City` type. That's why a confirmation message pops up if you delete without `FILTER` to make sure that you really want to delete everything. But it won't confirm if you use `FILTER`, because it deletes fewer things and assumes that you know exactly what you want to delete.

So let's give it a try. Remember our two `Country` objects for Hungary and Romania? Let's delete them:

```
DELETE Country;
```

Just like an insert, it gives us the id numbers of the objects that are now deleted:

```
{Object {id: bc9c4766-2898-11eb-b5e8-0bfd25af166c}, Object {id: bd0a6b1a-2898-11eb-b5e8-ef85694d442f}}
```

Okay, insert them again. Now let's delete with a filter:

```
DELETE Country FILTER .name ILIKE '%States%';
```

Nothing matches, so the output is `{}` - we deleted nothing. Let's try again:

```
DELETE Country FILTER .name ILIKE '%ania%';
```

We got a `{Object {id: eaa9b03a-2898-11eb-b5e8-27121e63218a}}`, which is certainly Romania. Only Hungary is left. What if we want to see what we deleted? No problem - just put the `DELETE` inside brackets and `SELECT` it. Let's delete all the `Country` objects again but this time we'll select it:

```
SELECT (DELETE Country) {
  name
};
```

The output is `{Object {name: 'Hungary'}}`, showing us that we deleted Hungary. And now if we do `SELECT Country` we get a `{}`, which confirms that we did delete them all.

(Fun fact: `DELETE` statements in EdgeDB are actually [syntactic sugar](https://www.edgedb.com/docs/edgeql/statements/delete/) for `DELETE (SELECT ...)`. You'll be learning something called `LIMIT` in the next chapter with `SELECT` and as you do so, keep in mind that you can apply the same to `DELETE` too.)

Finally, let's insert Hungary and Romania again to finish the chapter. We'll leave them alone now.

[Here is all our code so far up to Chapter 3.](code.md)

## Time to practice

1. This query is trying to display every `NPC` along with the `name` plus every `City` type for each `NPC`, but it's giving an error. What is it missing?

```
SELECT NPC {
  name,
  cities := SELECT City.name
};
```

2. If the `City` type needed a required property called `population`, what would it look like? What type would 'population' be?
3. This query wants to display `name` twice for some reason but is giving an error. Can you think of a way to do it?

```
SELECT Person {
  name,
  name
};
```

(Hint: the problem is that the name `name` is being used twice)

4. People keep trying to make characters with negative ages. Can you think of a constraint that can stop this?

Hint: the current constraint is `max_value(120);`

5. Can you insert a HumanAge type?

[See the answers here.](answers.md)

Up next in Chapter 4: [Jonathan: "This Count Dracula knows so much about history! I'm glad I came."](../chapter4/index.md)

# Chapter 4 - "What a strange man this Count Dracula is."

>Jonathan Harker wakes up late and is alone in the castle. Dracula appears after nightfall and they talk **through the night**. Dracula is making plans to move to London, and Jonathan gives him some advice about buying houses. Jonathan tells Dracula that a big house called Carfax would be a good house to buy. It's very big and quiet. It's close to an asylum for crazy people, but not too close. Dracula likes the idea. He then tells Jonathan not to go into any of the locked rooms in the castle, because it could be dangerous. Jonathan sees that it's almost morning - they talked through the whole night again. Dracula suddenly stands up and says he must go, and leaves the room. Jonathan thinks about **Mina** back in London, who he is going to marry when he returns. He is beginning to feel that there is something wrong with Dracula, and the castle. Seriously, where are the other people?

First let's create Jonathan's girlfriend, Mina Murray. But we'll also add a new link to the `Person` type in the schema called `lover`:

```
abstract type Person {
  required property name -> str;
  multi link places_visited -> City;
  link lover -> Person;
}
```

With this we can link the two of them together. We will assume that a person can only have one `lover`, so this is a `single link` but we can just write `link`.

Mina is in London, and we don't know if she has been anywhere else. So let's do a quick insert to create the city of London. It couldn't be easier:

```
INSERT City {
    name := 'London',
};
```

To give her the city of London, we can just do a quick `SELECT City FILTER .name = 'London'`. This will give her the `City` that matches `.name = 'London'`, but it won't give an error if the city's not there: it will just return a `{}` empty set.

## DETACHED, LIMIT, and EXISTS

For `lover` it is the same process but a bit more complicated:

```
INSERT NPC {
  name := 'Mina Murray',
  lover := (SELECT DETACHED NPC Filter .name = 'Jonathan Harker' LIMIT 1),
  places_visited := (SELECT City FILTER .name = 'London'),
};
```

You'll notice two things here:

- `DETACHED`. This is because we are inside of an `INSERT` for the `NPC` type, but we want to link to the same type: another `NPC`. We need to add `DETACHED` to specify that we are talking about `NPC` in general, not the `NPC` that we are inserting right now.
- `LIMIT 1`. This is because the link is a `single link`. EdgeDB doesn't know how many results we might get: for all it knows, there might be 2 or 3 or more `Jonathan Harkers`. To guarantee that we are only creating a `single link`, we use `LIMIT 1`. And of course `LIMIT 2`, `LIMIT 3` etc. will work just fine if we want to link to a certain maximum number of objects.

Now we want to make a query to see who is single and who is not. This is easy by using a "computable", where we can create a new variable that we define with `:=`. First here is a normal query:

```
SELECT Person {
  name,
  lover: {
    name
  }
};
```

This gives us:

```
{
  Object {name: 'Jonathan Harker', lover: {}},
  Object {name: 'The innkeeper', lover: {}},
  Object {name: 'Mina Murray', lover: Object {name: 'Jonathan Harker'}},
  Object {name: 'Count Dracula', lover: {}},
}
```

Okay, so Mina Murray has a lover but Jonathan Harker does not yet, because he was inserted first. We'll learn some techniques later in Chapters 6, 14 and 15 to deal with this. In the meantime we'll just leave Jonathan Harker with `{}` for `link lover`.

Back to the query: what if we just want to say `true` or `false` depending on if the character has a lover? To do that we can add a computable to the query, using `EXISTS`. `EXISTS` will return `true` if a set is returned, and `false` if it gets `{}` (if there is nothing). This is once again a result of not having null in EdgeDB. It looks like this:

```
select Person {
  name,
  is_single := NOT EXISTS Person.lover,
};
```

Now this prints:

```
  Object {name: 'Count Dracula', is_single: true},
  Object {name: 'The innkeeper', is_single: true},
  Object {name: 'Mina Murray', is_single: false},
  Object {name: 'Jonathan Harker', is_single: true},
  Object {name: 'Emil Sinclair', is_single: true},
```

This also shows why abstract types are useful. Here we did a quick search on `Person` for data from `Vampire`, `PC` and `NPC`, because they all come from `abstract type Person`.

We can also put the computable in the type itself. Here's the same computable except now it's inside the `Person` type:

```
abstract type Person {
  required property name -> str;
  multi link places_visited -> City;
  property lover -> Person;
  property is_single := not exists .lover;
}
```

We won't keep `is_single` in the type definition though, because it's not useful enough for our game.

You might be curious about how computables are represented in databases on the back end. They are interesting because they [don't show up in the actual database](https://www.edgedb.com/docs/datamodel/computables), and only appear when you query them. And of course you don't specify the type because the computable itself determines the type. You can kind of imagine this when you look at a query with a quick computable like `SELECT country_name := 'Romania'`. Here, `country_name` is computed every time we do a query, and the type is determined to be a string. A computable on a type does the same thing. But nevertheless, they still work in the same way as all other links and properties because the instructions for the computable are part of the type itself and do not change. In other words, they are a bit different on the back but the same up front.

## Ways to tell time

We will now learn about time, because it might be important for our game. Remember, vampires can only go outside at night.

The part of Romania where Jonathan Harker is has an average sunrise of around 7 am and a sunset of 7 pm. This changes by season, but to keep it simple we will just use 7 am and 7 pm to decide if it's day or night.

EdgeDB uses two major types for time.

-`std::datetime`, which is very precise and always has a timezone. Times in `datetime` use the ISO 8601 standard.
-`cal::local_datetime`, which doesn't worry about timezone.

There are two others that are almost the same as `cal::local_datetime`:

-`cal::local_time`, when you only need to know the time of day, and
-`cal::local_date`, when you only need to know the month and the day.

We'll start with `cal::local_time` first.

`cal::local_time` is easy to create, because you can just cast to it from a `str` in the format 'HH:MM:SS':

```
SELECT <cal::local_time>('15:44:56');
```

This gives us the output:

```
{<cal::local_time>'15:44:56'}
```

We will imagine that our game has a clock that gives the time as a `str`, like the '15:44:56' in the example above. We'll make a quick `Date` type that can help. It looks like this:

```
type Date {
  required property date -> str;
  property local_time := <cal::local_time>.date;
  property hour := .date[0:2];
}
```

`.date[0:2]` is an example of ["slicing"](https://www.edgedb.com/docs/edgeql/funcops/array#operator::ARRAYSLICE). [0:2] means start from index 0 (the first index) and stop *before* index 2, which means indexes 0 and 1. This is fine because to cast a `str` to `cal::local_time` you need to write the hour with two numbers (e.g. 09 is okay, but 9 is not).

So this won't work:

```
SELECT <cal::local_time>'9:55:05';
```

It gives this error:

```
ERROR: InvalidValueError: invalid input syntax for type cal::local_time: '9:55:05'
```

Because of that, we are sure that slicing from index 0 to 2 will give us two numbers that indicate the hour of the day.

Now with this `Date` type, we can get the hour by doing this:

```
INSERT Date {
    date := '09:55:05',
};
```

And then we can `SELECT` our `Date` types and everything inside:

```
SELECT Date {
  date,
  local_time,
  hour,
};
```

That gives us a nice output that shows everything, including the hour: 

```{Object {date: '09:55:05', local_time: <cal::local_time>'09:55:05', hour: '09'}}```.

Finally, we can add some logic to the `Date` type to see if vampires are awake or asleep. We could use an `enum` but to be simple, we will just make it a `str`.

```
type Date {
  required property date -> str;
  property local_time := <cal::local_time>.date;
  property hour := .date[0:2];
  property awake := 'asleep' IF <int16>.hour > 7 AND <int16>.hour < 19 ELSE 'awake';
}
```

So `awake` is calculated like this:

- First EdgeDB checks to see if the hour is greater than 7 and less than 19 (7 pm). But it's better to compare with a number than a string, so we write `<int16>.hour` instead of `.hour` so it can compare a number to a number.
- Then it gives us a string saying either 'asleep' or 'awake' depending on that.

Now if we `SELECT` this with all the properties, it will give us this: 

```Object {date: '09:55:05', local_time: <cal::local_time>'09:55:05', hour: '09', awake: 'asleep'}```

## SELECT while you INSERT

Back in Chapter 3, we learned how to select while deleting at the same time. You can do the same thing with `INSERT` by enclosing it in brackets and then selecting that, same as with any other `SELECT`. Because when we insert a new `Date`, all we get is a `uuid`:

```
INSERT Date {
  date := '22.44.10'
};
```

The output is just something like this: `{Object {id: 528941b8-f638-11ea-acc7-2fbb84b361f8}}`

So let's wrap the whole entry in `SELECT ()` so we can display its properties as we insert it. Because it's enclosed in brackets, EdgeDB will do that operation first, and then give it for us to select and do a normal query. Besides the properties to display, we can also add a computable while we are at it. Let's give it a try:

```
SELECT ( # Start a selection
  INSERT Date { # Put the insert inside it
    date := '22.44.10'
}) # The bracket finishes the selection
  { # Now just choose the properties we want
    date,
    hour,
    awake,
    double_hour := <int16>.hour * 2
  };
```

Now the output is more meaningful to us: `{Object {date: '22.44.10', hour: '22', awake: 'awake', double_hour: 44}}` We know the date and the hour, we can see that vampires are awake, and even make a computable from the object we just entered.

[Here is all our code so far up to Chapter 4.](code.md)

## Time to practice

1. This insert is not working.

```
INSERT NPC {
  name := 'I Love Mina',
  lover := (SELECT Person FILTER .name LIKE '%Mina%' LIMIT 1)
};
```

The error is: `invalid reference to default::NPC: self-referencing INSERTs are not allowed`. What keyword can we use to make this insert work?

Bonus: there is another method we could use too to make it work without the keyword. Can you think of another way?

2. How would you display up to 2 `Person` types (and their `name` property) whose names include the letter `a`?

3. How would you display all the `Person` types (and their names) that have never visited anywhere?

Hint: all the `Person` types for which `.places_visited` returns `{}`.

4. Imagine that you have the following `cal::local_time` type:

```
SELECT has_nine_in_it := <cal::local_time>'09:09:09';
```

This displays `{<cal::local_time>'09:09:09'}` but instead you want to display {true} if it has a 9 and {false} otherwise. How could you do that?

5. We are inserting a character called The Innkeeper's Son:

```
INSERT NPC {
  name := "The Innkeeper's Son",
  age := 10
};
```

How would you `SELECT` this insert at the same time to display the `name`, `age`, and `age_ten_years_later` that is made from `age` plus 10?

[See the answers here.](answers.md)

Up next in Chapter 5: [Jonathan decides to explore the castle a bit. Just to be safe...](../chapter5/index.md)

# Chapter 5 - Jonathan tries to leave the castle

Poor Jonathan is not having much luck. Here's what happens to him in this chapter:

>During the day, Jonathan decides to try to explore the castle but too many doors and windows are locked. He doesn't know how to get out, and wishes he could at least send Mina a letter. He pretends that there is no problem, and keeps talking to Dracula during the night. One night he sees Dracula climb out of his window and down the castle wall, like a snake. Now he is very afraid, and knows that Dracula is not human. A few days later he breaks one of the doors and finds another part of the castle. The room is very strange and he feels sleepy. When he opens his eyes, he sees three vampire women next to him. He is attracted to and afraid of them at the same time. He wants to kiss them, but knows that he will die if he does. They come closer, and he can't move...

## std::datetime

Since Jonathan was thinking of Mina back in London, let's learn about `std::datetime` because it uses time zones. To create a datetime, you can just cast a string in ISO 8601 format with `<datetime>`. That format looks like this:

```YYYY-MM-DDTHH:MM:SSZ```

And an actual date looks like this.

`'2020-12-06T22:12:10Z'`

The `T` inside there is just a separator, and the `Z` at the end stands for "zero timeline". That means that it is 0 different (offset) from UTC: in other words, it *is* UTC.

One other way to get a `datetime` is to use the `to_datetime()` function. [Here is its signature](https://edgedb.com/docs/edgeql/funcops/datetime/#function::std::to_datetime), which shows that there are six ways to make a `datetime` with this function depending on how you want to make it. EdgeDB will know which one of the six you have chosen depending on what input you give it.

By the way, you'll notice one unfamiliar type inside called a [`decimal`](https://www.edgedb.com/docs/datamodel/scalars/numeric#type::std::decimal) type. This is a float with "arbitrary precision", meaning that you can give it as many numbers after the decimal point as you want. This is because float types on computers [become imprecise after a while](https://www.youtube.com/watch?v=-3c8G0JMM5Q) thanks to rounding errors. This example shows it:

```
edgedb> SELECT 6.777777777777777; # Good so far
{6.777777777777777}
edgedb> SELECT 6.7777777777777777; # Add one more digit...
{6.777777777777778} # Where did the 8 come from?!
```

If you want to avoid this, add an `n` to the end to get a `decimal` type which will be as precise as it needs to be.

```
edgedb> SELECT 6.7777777777777777n;
{6.7777777777777777n}
edgedb> SELECT 6.7777777777777777777777777777777777777777777777777n;
{6.7777777777777777777777777777777777777777777777777n}
```

Meanwhile, there is a `bigint` type that also uses `n` for an arbitrary size. That's because even int64 has a limit: it's 9223372036854775807.

```
edgedb> SELECT 9223372036854775807; # Good so far...
{9223372036854775807}
edgedb> SELECT 9223372036854775808; # But add 1 and it will fail
ERROR: NumericOutOfRangeError: std::int64 out of range
```

So here you can just add an `n` and it will create a `bigint` that can accommodate any size.

```
edgedb> SELECT 9223372036854775808n;
{9223372036854775808n}
```

Now that we know all the numeric types, let's get back to the six signatures for the `std::to_datetime` function:

```
std::to_datetime(s: str, fmt: OPTIONAL str = {}) -> datetime
std::to_datetime(local: cal::local_datetime, zone: str) -> datetime
std::to_datetime(year: int64, month: int64, day: int64, hour: int64, min: int64, sec: float64, timezone: str) -> datetime
std::to_datetime(epochseconds: decimal) -> datetime
std::to_datetime(epochseconds: float64) -> datetime
std::to_datetime(epochseconds: int64) -> datetime
```

The easiest is probably the third if you find ISO 8601 unfamiliar or you have a bunch of separate numbers to make into a date. With this, our game could have a function that generates integers for times that then use `to_datetime()` to get a proper time stamp. 

Let's imagine that it's May 12. It's a bright morning at 10:35 in Castle Dracula. The sun is up, Dracula is asleep somewhere, and Jonathan is trying to use the time during the day to escape to send Mina a letter. In Romania the time zone is 'EEST' (Eastern European Summer Time). We'll use `to_datetime()` to generate this. We won't worry about the year, because the story takes place in the same year - we'll just use 2020 for convenience. We type this:

`SELECT to_datetime(2020, 5, 12, 10, 35, 0, 'EEST');`

And get the following output:

`{<datetime>'2020-05-12T07:35:00Z'}`

The `07:35:00` part shows that it was automatically converted to UTC, which is London where Mina lives.

We can also use this to see the duration between events. EdgeDB has a `duration` type that you can get by subtracting a datetime from another one. Let's practice by calculating the exact number of seconds between one date in Central Europe and another in Korea:

```
SELECT to_datetime(2020, 5, 12, 6, 10, 0, 'CET') - to_datetime(2000, 5, 12, 6, 10, 0, 'KST');
```

This takes May 12 2020 6:10 am in Central European Time and subtracts May 12 2000 6:10 in Korean Standard Time. The result is: `{631180800s}`.

Now let's try something similar with Jonathan in Castle Dracula again, trying to escape. It's May 12 at 10:35 am. On the same day, Mina is in London at 6:10 am, drinking her morning tea. How many seconds passed between these two events? They are in different time zones but we don't need to calculate it ourselves; we can just specify the time zone and EdgeDB will do the rest:

```
SELECT to_datetime(2020, 5, 12, 10, 35, 0, 'EEST') - to_datetime(2020, 5, 12, 6, 10, 0, 'UTC');
```

The answer is 5100 seconds: `{5100s}`.

To make the query easier for us to read, we can also use the `WITH` keyword to create variables. We can then use the variables in `SELECT` below. We'll make one called `jonathan_wants_to_escape` and another called `mina_has_tea`, and subtract one from another to get a `duration`. With variable names it is now a lot clearer what we are trying to do:

```
WITH 
  jonathan_wants_to_escape := to_datetime(2020, 5, 12, 10, 35, 0, 'EEST'),
  mina_has_tea := to_datetime(2020, 5, 12, 6, 10, 0, 'UTC'),
  SELECT jonathan_wants_to_escape - mina_has_tea;
```

The output is the same: `{5100s}`. As long as we know the timezone, the `datetime` type does the work for us when we need a `duration`.

## Casting to a duration

Besides subtracting a `datetime` from another `datetime`, you can also just cast to make a `duration`. To do this, just write the number followed by the unit: `microseconds`, `milliseconds`, `seconds`, `minutes`, or `hours`. It will return a number of seconds, or a more precise unit if necessary. For example, `SELECT <duration>'2 hours`; will return `{7200s}`, and `SELECT <duration>'2 microseconds';` will return `{2µs}`.

You can include multiple units as well. For example:

```
SELECT <duration>'6 hours 6 minutes 10 milliseconds 678999 microseconds';
```

This will return `{21960.688999s}`.

EdgeDB is pretty forgiving when it comes to inputs when casting to a `duration`, and will ignore plurals and other signs. So even this horrible input will work:

```
SELECT <duration>'1 hours, 8 minute ** 5 second ()()()( //// 6 milliseconds' -
  <duration>'10 microsecond 7 minutes %%%%%%% 10 seconds 5 hour';
```

The result: `{-14344.99401s}`.

## datetime_current()

One convenient function is [datetime_current()](https://www.edgedb.com/docs/edgeql/funcops/datetime/#function::std::datetime_current), which gives the datetime right now. Let's try it out:

```
SELECT datetime_current();
{<datetime>'2020-11-17T06:13:24.418765000Z'}
```

This can be useful if you want a post date when you insert an object. With this you can sort by date, delete the most recent item if you have a duplicate, and so on. If we were to put it into the `Place` type for example it would look like this:

```
abstract type Place {
  required property name -> str;
  property modern_name -> str;
  property important_places -> array<str>;
  property post_date := datetime_current();
}
```

## Required links

Now we need to make a type for the three female vampires. We'll call it `MinorVampire`. These have a link to the `Vampire` type, which needs to be `required`. This is because Dracula controls them and they only exist as `MinorVampire`s because he exists.

```
type MinorVampire extending Person {
  required link master -> Vampire;
}
```

Now that it's required, we can't insert a `MinorVampire` with just a name. It will give us this error: `ERROR: MissingRequiredError: missing value for required link default::MinorVampire.master`. So let's insert one and connect her to Dracula:

```
INSERT MinorVampire {
  name := 'Woman 1',
  master := (SELECT Vampire Filter .name = 'Count Dracula'),
};
```

This works because there is only one 'Count Dracula' (remember, `required link` is short for `required single link`). If there were more than one `Vampire`, we would have to add `LIMIT 1`. Without `LIMIT 1` we would get the following error:

```
error: possibly more than one element returned by an expression for a computable link 'master' declared as 'single'
```

## DESCRIBE to look inside types

Our `MinorVampire` type extends `Person`, and so does `Vampire`. Types can continue to extend other types, and they can extend more than one type at the same time. The more you do this, the more annoying it can be to try to combine it all together in your mind. This is where `DESCRIBE` can help, because it shows exactly what any type is made of. There are three ways to do it:

- `DESCRIBE TYPE MinorVampire` - this will give the [DDL (data definition language)](https://www.edgedb.com/docs/edgeql/ddl/index/) description of a type. DDL is a lower level language than SDL, the language we have been using. It is less convenient for schema, but is more explicit and can be useful for quick changes. We won't be learning any DDL in this course but later on you might find it useful sometimes. For example, with it you can quickly create functions without needing to do a migration. And if you understand SDL it will not be hard to pick up some tricks in DDL. 

Now back to `DESCRIBE TYPE` which gives the results in DDL. Here's what our `Person` type looks like:

```
{
  'CREATE TYPE default::MinorVampire EXTENDING default::Person {
    CREATE REQUIRED SINGLE LINK master -> default::Vampire;
};',
}
```

The `CREATE` keyword shows that it's a series of quick commands, which is why the order is important. Also, because it only shows the DDL commands to create it, it doesn't show us all the `Person` links and properties that it extends. So we don't want that. The next method is:

- `DESCRIBE TYPE MinorVampire AS SDL` - same thing, but in SDL.

The output is almost the same too, just the SDL version of the above. It's also not enough information for what we want now:

```
{
  'type default::MinorVampire extending default::Person {
    required single link master -> default::Vampire;
};',
}
```

The third method is `DESCRIBE TYPE MinorVampire AS TEXT`. This is what we want, because it shows everything inside the type, including from the types that it extends. Here's the output:

```
{
  'type default::MinorVampire extending default::Vampire {
    required single link __type__ -> schema::Type {
        readonly := true;
    };
    optional single link lover -> default::Person;
    required single link master -> default::Vampire;
    optional multi link places_visited -> default::Place;
    optional single property age -> std::int16;
    required single property id -> std::uuid {
        readonly := true;
    };
    required single property name -> std::str;
};',
}
```

The parts that say `readonly := true` we don't need to worry about, as they are automatically generated (and we can't touch them). For everything else, we can see that we need a `name` and a `master`, and could add a `lover`, `age` and `places_visited` for these `MinorVampire`s.

And for a *really* long output, try typing `DESCRIBE MODULE default` (with `AS SDL` or `AS TEXT` if you want). You'll get an output showing the whole module we've built so far.

[Here is all our code so far up to Chapter 5.](code.md)

## Time to practice

1. What do you think `SELECT to_datetime(3600);` will return, and why?

Hint: check the function signatures above and see which one EdgeDB will pick when you enter 3600.

2. Will `SELECT <int16>9 + 1.06n IS decimal;` work? And if it does, will it return `{true}`?

3. How many seconds went by between 5:00 am on Christmas Day 2003 in Turkmenistan (TMT) and 7:00 pm on New Year's Eve for the same year in Uzbekistan (UZT)?

4. How would you write the same query using `WITH` for each of the two times?

5. What's the best way to describe a type if you only want to see how you wrote it?

[See the answers here.](answers.md)

Up next in Chapter 6: [One of the women vampires to her sisters: "He is young and strong; there are kisses for us all."](../chapter6/index.md)

# Chapter 6 - Still no escape

>The women vampires are next to Jonathan and he can't move. Suddenly, Dracula runs into the room and tells the women to leave: "You can have him later, but not tonight!" The women listen to him. Jonathan wakes up in his bed and it feels like a bad dream...but he sees that somebody folded his clothes, and he knows it was not just a dream. The castle has some visitors from Slovakia the next day, so Jonathan has an idea. He writes two letters, one to Mina and one to his boss. He gives the visitors some money and asks them to send the letters. But Dracula finds the letters, and is angry. He burns them in front of Jonathan and tells him not to do that again. Jonathan is still stuck in the castle, and Dracula knows that Jonathan tried to trick him.

## Filtering on sets when doing an insert

There is not much new in this lesson when it comes to types, so let's look at improving our schema. Right now Jonathan Harker is still inserted like this:

```
INSERT NPC {
  name := 'Jonathan Harker',
  places_visited := City,
};
```

This was fine when we only had cities, but now we have the `Place` and `Country` types. First we'll insert two more `Country` types to have some more variety:

```
INSERT Country {
  name := 'France'
};
INSERT Country {
  name := 'Slovakia'
};
```

(In Chapter 9 we'll learn how to do this with just one `INSERT`!)

Then we'll make a new type called `OtherPlace` for places that aren't cities or countries. That's easy: `type OtherPlace extending Place;` and it's done.

Then we'll insert our first `OtherPlace`:

```
INSERT OtherPlace {
  name := 'Castle Dracula'
};
```

That gives us a good number of types from `Place` that aren't of the `City` type.

So back to Jonathan: in our database, he's been to four cities, one country, and one `OtherPlace`...but he hasn't been to Slovakia or France, so we can't just insert him with `places_visited := SELECT Place`. Instead, we can filter on `Place` against a set with the names of the places he has visited. It looks like this:

```
INSERT NPC {
  name := 'Jonathan Harker',
  places_visited := (SELECT Place FILTER .name in {'Munich', 'Buda-Pesth', 'Bistritz', 'London', 'Romania', 'Castle Dracula'})
};
```

You'll notice that we just wrote the names in a set using `{}`, so we didn't need to use an array with `[]` to do it. (This is called a [set constructor](https://www.edgedb.com/docs/edgeql/expressions/overview/#set-constructor), by the way.)

Now what if Jonathan ever escapes Castle Dracula and runs away to a new place? Let's pretend that he escapes and runs away to Slovakia. Of course, we can change his `INSERT` signature to include `'Slovakia'` in the set of names. But what do we do to make a quick update? For that we have the `UPDATE` and `SET` keywords. `UPDATE` selects the type to start the update, and `SET` is for the parts we want to change. It looks like this:

```
UPDATE NPC
  FILTER
  .name = 'Jonathan Harker'
  SET {
    places_visited += (SELECT Place FILTER .name = 'Slovakia')
};
```

You'll know that it succeeded because EdgeDB will return the IDs of the objects that have been updated. In our case, it's just one:

```
{ 
  Object { id: <uuid>"6f436006-3f65-11eb-b6de-a3e7cc8efd4f" } 
}
```

And if we had written something like `FILTER .name = 'SLLLovakia'` then it would return `{}`, letting us know that nothing matched.

And since Jonathan hasn't visited Slovakia, we can use `-=` instead of `+=` with the same `UPDATE` syntax to remove it now.

With that we now know [all three operators](https://www.edgedb.com/docs/edgeql/statements/update) used after `SET`: `:=`, `+=`, and `-=`.

Let's do another update. Remember this?

```
SELECT Person {
  name,
  lover
} FILTER .name = 'Jonathan Harker';
```

Mina Murray has Jonathan Harker as her `lover`, but Jonathan doesn't have her because we inserted him first. We can change that now:

```
UPDATE Person FILTER .name = 'Jonathan Harker'
  SET {
    lover := (SELECT Person FILTER .name = 'Mina Murray' LIMIT 1)
};
```

Now `link lover` for Jonathan finally shows Mina instead of an empty `{}`.

Of course, if you use `UPDATE` without `FILTER` it will do the same change on all the types. This update below for example would give every `Person` type every single `Place` in the database under `places_visited`:

```
UPDATE Person
  SET {
    places_visited := Place
    };
```

## Concatenation with ++

One other operator is `++`, which does concatenation (joining together) instead of adding.

You can do simple operations with it like: ```SELECT 'My name is ' ++ 'Jonathan Harker';``` which gives `{'My name is Jonathan Harker'}`. Or you can do more complicated concatenations as long as you continue to join strings to strings:

```
SELECT 'A character from the book: ' ++ (SELECT NPC.name) ++ ', who is not ' ++ (SELECT Vampire.name);
```

This prints:

```
{
  'A character from the book: Jonathan Harker, who is not Count Dracula',
  'A character from the book: The innkeeper, who is not Count Dracula',
  'A character from the book: Mina Murray, who is not Count Dracula',
}
```

(The concatenation operator works on arrays too, putting them into a single array. So `SELECT ['I', 'am'] ++ ['Jonathan', 'Harker'];` gives `{['I', 'am', 'Jonathan', 'Harker']}`.)

Let's also change the `Vampire` type to link it to `MinorVampire` from that side instead. You'll remember that Count Dracula is the only real vampire, while the others are of type `MinorVampire`. That means we need a `multi link`:

```
type Vampire extending Person {            
  property age -> int16;
  multi link slaves -> MinorVampire;
}
```

Then we can `INSERT` the `MinorVampire` type at the same time as we insert the information for Count Dracula. But first let's remove `link master` from `MinorVampire`, because we don't want two objects linking to each other. There are two reasons for that:

- When we declare a `Vampire` it has `slaves`, but if there are no `MinorVampire`s yet then it will be empty: {}. And if we declare the `MinorVampire` type first it has a `master`, but if we declare them first then their `master` (a `required link`) will not be there.
- If both types link to each other, we won't be able to delete them if we need to. The error looks something like this:

```
ERROR: ConstraintViolationError: deletion of default::Vampire (cc5ee436-fa23-11ea-85e0-e78b548f5a59) is prohibited by link target policy

DETAILS: Object is still referenced in link master of default::MinorVampire (cc87c78e-fa23-11ea-85e0-8f5149329e3a).
```

So first we simply change `MinorVampire` to a type extending `Person`:

```
type MinorVampire extending Person {
}
```

and then we create them all together with Count Dracula like this:

```
INSERT Vampire {
  name := 'Count Dracula',
  age := 800,
  slaves := {
    (INSERT MinorVampire {
      name := 'Woman 1',
  }),
    (INSERT MinorVampire {
     name := 'Woman 2',
  }),
    (INSERT MinorVampire {
     name := 'Woman 3',
  }),
 }
};
```

Note two things: we used `{}` after `slaves` to put everything into a set, and each `INSERT` is inside `()` brackets to capture the insert.

Now we don't have to insert the `MinorVampire` types first and then filter: we can just put them in together with Dracula.

Then when we `select Vampire` like this:

```
SELECT Vampire {
  name,
  slaves: {name}
};
```

We have a nice output that shows them all together:

```
Object {
  name: 'Count Dracula',
  slaves: {Object {name: 'Woman 1'}, Object {name: 'Woman 2'}, Object {name: 'Woman 3'}},
  },
```

This might make you wonder: what if we do want two-way links? There's actually a very convenient way to do it (it's called a **backward link**), but we won't look at it until Chapters 14 and 15. If you're really curious you can skip to those chapters but there's a lot more to learn before then.

## Just type \<json> to generate json

What do we do if we want the same output in JSON? It couldn't be easier: just cast using `<json>`. Any type in EdgeDB (except `bytes`) can be cast to JSON this easily:

```
SELECT <json>Vampire { 
      # <json> is the only difference from the SELECT above
  name,
  slaves: {name}
};
```

The output is:

```
{
  "{\"name\": \"Count Dracula\", \"slaves\": [{\"name\": \"Woman 1\"}, {\"name\": \"Woman 2\"}, {\"name\": \"Woman 3\"}]}",
}
```

## Converting back from JSON

So what about the other way around, namely JSON to an EdgeDB type? You can do this too, but remember to think about the JSON type that you are giving to cast. The EdgeDB philosophy is that casts should be symmetrical: a type cast into JSON should only be cast back into that type. For example, here is the first date in the book Dracula as a string, then cast to JSON and then into a `cal::local_date`:

```
SELECT <cal::local_date><json>'18870503';
```

This is fine because `<json>` turns it into a JSON string, and `cal::local_date` can be created from a string. The result we get is `{<cal::local_date>'1887-05-03'}`. But if we try to turn the JSON value into an `int64`, it won't work:

```
SELECT <int64><json>'18870503';
```

The problem is that it is a conversion from a JSON string to an EdgeDB `int64`. It gives this error: `ERROR: InvalidValueError: expected json number, null; got json string`. To keep things symmetrical, you need to cast a JSON string to an EdgeDB `str` and then cast into an `int64`:

```
SELECT <int64><str><json>'18870503';
```

Now it works: we get `{18870503}` which began as an EdgeDB `str`, turned into a JSON string, then back into an EdgeDB `str`, and finally was cast into an `int64`.

The [documentation on JSON](https://www.edgedb.com/docs/datamodel/scalars/json) explains which JSON types turn into which EdgeDB types and is good to bookmark if you need to convert from JSON a lot.

[Here is all our code so far up to Chapter 6.](code.md)

## Time to practice

1. This select is incomplete. How would you complete it so that it says "Pleased to meet you, I'm " and then the NPC's name?

```
SELECT NPC {
  name,
  greeting := ## Put the rest here
};
```

2. How would you update Mina's `places_visited` to include Romania if she went to Castle Dracula for a visit?

3. With the set `{'W', 'J', 'C'}`, how would you display all the `Person` types with a name that contains any of these capital letters?

Hint: it involves `WITH` and a bit of concatenation.

4. How would you display this same query as JSON?

5. How would you add ' the Great' to every Person type?

Bonus question: what's a quick way to undo this using string indexing?

[See the answers here.](answers.md)

Up next in Chapter 7: [Jonathan climbs the castle wall to try to get into Count Dracula's room.](../chapter7/index.md)
  
# Chapter 7 - Jonathan finally "leaves" the castle

> Jonathan sneaks into Dracula's room during the day and sees him sleeping inside a coffin. Now he knows that he is a vampire. A few days later Count Dracula says that he will leave tomorrow. Jonathan thinks this is a chance, and asks to leave now. Dracula says, "Fine, if you wish..." and opens the door: but there are a lot of wolves outside, howling and making loud sounds. Dracula says, "You are free to leave! Goodbye!" But Jonathan knows that the wolves will kill him if he steps outside. He also knows that Dracula called the wolves, and asks him to please close the door. Dracula smiles and closes the door...he knows that Jonathan is trapped. Later, Jonathan hears Dracula tell the vampire women that they can have him tomorrow after he leaves. Dracula's friends take him away the next day (he is inside a coffin), and Jonathan is alone...and soon it will be night. All the doors are locked. He decides to climb out the window, because it is better to die by falling than to be alone with the vampire women. He writes "Good-bye, all! Mina!" in his journal and begins to climb the wall.

## More constraints

While Jonathan climbs the wall, we can continue to work on our database schema. In our book, no character has the same name so there should only be one Mina Murray, one Count Dracula, and so on. This is a good time to put a [constraint](https://edgedb.com/docs/datamodel/constraints#ref-datamodel-constraints) on `name` in the `Person` type to make sure that we don't have duplicate inserts. A `constraint` is a limitation, which we saw already in `age` for humans that can only go up to 120. For `name` we can give it another one called `constraint exclusive` which prevents two objects of the same type from having the same name. You can put a `constraint` in a block after the property, like this:

```
abstract type Person {
  required property name -> str { ## Add a block
      constraint exclusive;       ## and the constraint
  }
  multi link places_visited -> Place;
  link lover -> Person;
}
```

Now we know that there will only be one `Jonathan Harker`, one `Mina Murray`, and so on. In real life this is often useful for email addresses, User IDs, and other properties that you always want to be unique. In our database we'll also add `constraint exclusive` to `name` inside `Place` because these places are also all unique:

```
abstract type Place {
  required property name -> str {
      constraint exclusive;
  };
  property modern_name -> str;
  property important_places -> array<str>;
}
```

## Passing constraints with delegated

Now that our `Person` type has `constraint exclusive` for the property `name`, no type extending `Person` will be able to have the same name. That's fine for our game in this tutorial, because we already know all the character names in the book and won't be making any real `PC` types. But what if we later on wanted to make a `PC` named Jonathan Harker? Right now it wouldn't be allowed because we have an `NPC` with the same name, and `NPC` takes `name` from `Person`.

Fortunately there's an easy way to get around this: the keyword `delegated` in front of `constraint`. That "delegates" (passes on) the constraint to the subtypes, so the check for exclusivity will be done individually for `PC`, `NPC`, `Vampire`, and so on. So the type is exactly the same except for this keyword:

```
abstract type Place {
  required property name -> str {
      delegated constraint exclusive;
  };
  property modern_name -> str;
  property important_places -> array<str>;
}
```

With that you can have up to one Jonathan Harker the `PC`, the `NPC`, the `Vampire`, and anything else that extends `Place`.

## Using functions in queries

Let's also think about our game mechanics a bit. The book says that the doors inside the castle are too tough for Jonathan to open, but Dracula is strong enough to open them all. In a real game it will be more complicated but we can try something simple to mimic this:

- Doors have a strength, and people have strength as well. 
- If a person has greater strength than the door, then he or she can open it. 

So we'll create a type `Castle` and give it some doors. For now we only want to give it some "strength" numbers, so we'll just make it an `array<int16>`:

```
type Castle extending Place {
    property doors -> array<int16>;
}
```

Then we'll imagine that there are three main doors to enter and leave Castle Dracula, so we `INSERT` them as follows:

```
INSERT Castle {
    name := 'Castle Dracula',
    doors := [6, 19, 10],
};
```

Then we will also add a `property strength -> int16;` to our `Person` type. It won't be required because we don't know the strength of everybody in the book...though later on we could make it required if the game needs it.

Now we'll give Jonathan a strength of 5. That's easy with `UPDATE` and `SET` like before:

```
UPDATE Person FILTER .name = 'Jonathan Harker'
  SET {
    strength := 5
};
```

Great. We know that Jonathan can't break out of the castle, but let's try to show it using a query. To do that, he needs to have a strength greater than that of a door. Or in other words, he needs a greater strength than the weakest door.

Fortunately, there is a function called `min()` that gives the minimum value of a set, so we can use that. If his strength is higher than the door with the smallest number, then he can escape. This query looks like it should work, but not quite:

```
WITH 
  jonathan_strength := (SELECT Person FILTER .name = 'Jonathan Harker').strength,
  castle_doors := (SELECT Castle FILTER .name = 'Castle Dracula').doors,
    SELECT jonathan_strength > min(castle_doors);
```

Here's the error:

```
error: operator '>' cannot be applied to operands of type 'std::int16' and 'array<std::int16>'
```

We can [look at the function signature](https://edgedb.com/docs/edgeql/funcops/set#function::std::min) to see the problem:

```
std::min(values: SET OF anytype) -> OPTIONAL anytype
```

The important part is `SET OF`: it needs a set, so something in curly brackets. We can't just put curly brackets around the array, because then it becomes a set of one item (one array). So `SELECT min({[5, 6]});` just returns `{[5, 6]}`, not `{5}`, because `{[5, 6]}` is the minimum value of the arrays we gave it...because we only gave it one array to look at.

That also means that `SELECT min({[5, 6], [2, 4]});` will give us the output `{[2, 4]}` (instead of 2). That's not what we want.

Instead, what we want to use is the [array_unpack()](https://edgedb.com/docs/edgeql/funcops/array#function::std::array_unpack) function which takes an array and unpacks it into a set. So we'll use that on `weakest_door`:

```
WITH 
  jonathan_strength := (SELECT Person FILTER .name = 'Jonathan Harker').strength,
  doors := (SELECT Castle FILTER .name = 'Castle Dracula').doors,
    SELECT jonathan_strength > min(array_unpack(doors));
```

That gives us `{false}`. Perfect! Now we have shown that Jonathan can't open any doors. He will have to climb out the window to escape.

Along with `min()` there is of course `max()`. `len()` and `count()` are also useful: `len()` gives you the length of an object, and `count()` the number of them. Here is an example of `len()` to get the name length of all the `NPC`  types:

```
SELECT (NPC.name, 'Name length is: ' ++ <str>len(NPC.name));
```

Don't forget that we need to cast with `<str>` because `len()` returns an integer, and EdgeDB won't concatenate a string to an integer. This prints:

```
{
  ('The innkeeper', 'Name length is: 13'),
  ('Mina Murray', 'Name length is: 11'),
  ('Jonathan Harker', 'Name length is: 15'),
}
```

The other example is with `count()`, which also has a cast to a `<str>`:

```
SELECT 'There are ' ++ <str>(SELECT count(Place) - count(Castle)) ++ ' more places than castles';
```

It prints: `{'There are 6 more places than castles'}`.

In a few chapters we will learn how to create our own functions to make queries shorter.

## Using $ to set parameters

Imagine we need to look up `City` types all the time, with this sort of query:

```
SELECT City {
  name,
  population
  } FILTER .name ILIKE '%a%' AND len(.name) > 5 AND .population > 6000;
```

This works fine, returning one city: `{Object {name: 'Buda-Pesth', population: 402706}}`.

But this last line with all the filters can be a little annoying to change: there's a lot of moving about to delete and retype before we can hit enter again.

This could be a good time to add parameters to a query by using `$`. With that we can give them a name, and EdgeDB will ask us for every query what value to give it. Let's start with something very simple:

```
SELECT City {
  name
  } FILTER .name = 'London';
```

Now let's change 'London' to `$name`. Note: this won't work yet. Try to guess why!

```
SELECT City {
  name
  } FILTER .name = $name;
```

The problem is that `$name` could be anything, and EdgeDB doesn't know what type it's going to be. The error tells us too: `error: missing a type cast before the parameter`. So because it's a string, we'll cast with `<str>`:

```
SELECT City {
  name
  } FILTER .name = <str>$name;
```

When we do that, we get a prompt asking us to enter the value: `Parameter <str>$name:` Just type London, with no quotes because it already knows that it's a string. The result: `{Object {name: 'London'}}`

Now let's take that to make a much more complicated (and useful) query, using three parameters. We'll call them `$name`, `$population`, and `$length`. Don't forget to cast them all:

```
SELECT City {
  name,
  population,
  } FILTER
  .name ILIKE '%' ++ <str>$name ++ '%' 
  AND 
  .population > <int64>$population
  AND 
  <int64>len(.name) > <int64>$length;
```

Since there are three of them, EdgeDB will ask us to input three values. Here's one example of what it looks like:

```
Parameter <str>$name: u
Parameter <int64>$population: 2000
Parameter <int64>$length: 5
```

So that will give all `City` types with u in the name, population of more than 2000, and a name longer than 5 characters. The result:

```
{
  Object {name: 'Buda-Pesth', population: 402706},
  Object {name: 'Munich', population: 230023},
}
```

Parameters work just as well in inserts too. Here's a `Date` insert that prompts the user for the hour, minute, and second:

```
SELECT(INSERT Date {
 date := <str>$hour ++ <str>$minute ++ <str>$second
 }) {
 date,
 local_time,
 hour,
 awake
};
Parameter <str>$hour: 10
Parameter <str>$minute: 09
Parameter <str>$second: 09
```

Here's the output:

```
{
  default::Date {
    date: '100909',
    local_time: <cal::local_time>'10:09:09',
    hour: '10',
    awake: 'asleep',
  },
}
```
  
Note that the cast means you can just type 10, not '10'.

So what if you just want to have the *option* of a parameter? No problem, just put `OPTIONAL` before the type name in the cast (inside the `<>` brackets). So the insert above would look like this if you wanted everything optional:

```
SELECT(INSERT Date {
 date := <OPTIONAL str>$hour ++ <OPTIONAL str>$minute ++ <OPTIONAL str>$second
 }) {
 date,
 local_time,
 hour,
 awake
};
```

Of course, the `Date` type needs the proper formatting for the `date` property so this is a bad idea. But that's how you would do it.

The opposite of `OPTIONAL` is `REQUIRED`, but it's the default so you don't need to write it.

The `UPDATE` keyword that we learned last chapter can also take parameters, so that's three in total where you can use them: `SELECT`, `INSERT`, and `UPDATE`.

[Here is all our code so far up to Chapter 7.](code.md)

## Time to practice

1. How would you select each City and the length of its name?

2. How would you select each City and the length of `name` minus the length of `modern_name` if `modern_name` exists, and 0 if `modern_name` does not exist?

3. What if you wanted to write 'Modern name does not exist' instead of 0?

4. How would you insert an NPC with the name 'NPC number 8' if for example there are already seven other NPCs?

5. How would you select only the `Person` types that have the shortest names?

[See the answers here.](answers.md)

Up next in Chapter 8: [Workers in the city of Varna load fifty boxes into a ship. Dracula is inside one of them.](../chapter8/index.md)

# Chapter 8 - Dracula takes the boat to England

We are finally away from Castle Dracula. Here is what happens in this chapter:

> A boat leaves from the city of Varna in Bulgaria, sailing into the Black Sea. It has a **captain, first mate, second mate, cook**, and **five crew**. Inside is Dracula, but they don't know that he's there. Every night Dracula leaves his coffin, and every night one of the men disappears. They become afraid but don't know what is happening or what to do. One of them tells them he saw a strange man walking around the deck, but the others don't believe him. Finally it's the last day before the ship reaches the city of Whitby in England, but the captain is alone - all the others have disappeared. The captain knows the truth now. He ties his hands to the wheel so that the ship will go straight even if Dracula finds him. The next day the people in Whitby see a ship hit the beach, and a wolf jumps off and runs onto the shore - it's Dracula in his wolf form, but they don't know that. People find the dead captain tied to the wheel with a notebook in his hand and start to read the story.

> Meanwhile, Mina and her friend Lucy are in Whitby on vacation...

## Multiple inheritance

While Dracula arrives at Whitby, let's learn about multiple inheritance. We know that you can `extend` a type on another, and we have done this many times: `Person` on `NPC`, `Place` on `City`, etc. Multiple inheritance is doing this with more than one type at the same time. We'll try this with the ship's crew. The book doesn't give them any names, so we will give them numbers instead. Most `Person` types won't need a number, so we'll create an abstract type called `HasNumber` only for types that need a number:

```
abstract type HasNumber {
    required property number -> int16;
}
```

We will also remove `required` from `name` for the `Person` type. Not every `Person` type will have a name now, and we trust ourselves enough to input a name if there is one. We will of course keep it `exclusive`.

Now we can use multiple inheritance for the `Crewman` type. It's very simple: just add a comma between every type you want to extend.

```
type Crewman extending HasNumber, Person {
}
```

Now that we have this type and don't need a name, it's super easy to insert our crewmen thanks to `count()`. We just do this five times:

```
INSERT Crewman {
  number := count(DETACHED Crewman) + 1
};
```

So if there are no `Crewman` types, he will get the number 1. The next will get 2, and so on. So after doing this five times, we can `SELECT Crewman {number};` to see the result. It gives us:

```
{
  Object {number: 1},
  Object {number: 2},
  Object {number: 3},
  Object {number: 4},
  Object {number: 5},
}
```

Next is the `Sailor` type. The sailors have ranks, so first we will make an enum for that:

```
scalar type Rank extending enum<Captain, FirstMate, SecondMate, Cook>;
```

And then we will make a `Sailor` type that uses `Person` and this `Rank` enum:

```
type Sailor extending Person {
    property rank -> Rank;
}
```

Then we will make a `Ship` type to hold them all.

```
type Ship {
  required property name -> str;
  multi link sailors -> Sailor;
  multi link crew -> Crewman;
}
```

Now to insert the sailors we just give them each a name and choose a rank from the enum:

```
INSERT Sailor {
  name := 'The Captain',
  rank := 'Captain'
};

INSERT Sailor {
  name := 'Petrofsky',
  rank := 'FirstMate'
};

INSERT Sailor {
  name := 'The First Mate',
  rank := 'SecondMate'
};

INSERT Sailor {
  name := 'The Cook',
  rank := 'Cook'
};
```

Inserting the `Ship` is easy because right now every `Sailor` and every `Crewman` type is part of this ship - we don't need to `FILTER` anywhere.

```
INSERT Ship {
  name := 'The Demeter',
  sailors := Sailor,
  crew := Crewman
};
```

Then we can look up the `Ship` to make sure that the whole crew is there:

```
SELECT Ship {
  name,
  sailors: {
    name,
    rank,
    },
  crew: {
    number
    },
};
```

The result is:

```
{
  Object {
    name: 'The Demeter',
    sailors: {
      Object {name: 'Petrofsky', rank: FirstMate},
      Object {name: 'The Second Mate', rank: SecondMate},
      Object {name: 'The Cook', rank: Cook},
      Object {name: 'The Captain', rank: Captain},
    },
    crew: {
      Object {number: 1},
      Object {number: 2},
      Object {number: 3},
      Object {number: 4},
      Object {number: 5},
    },
  },
}
```

## The sequence type

On the subject of giving types a number, EdgeDB has a type called [sequence](https://www.edgedb.com/docs/datamodel/scalars/numeric/#type::std::sequence) that you may find useful. This type is defined as an "auto-incrementing sequence of int64", so an `int64` that starts at 1 and goes up every time you use it. Let's imagine a `Townsperson` type for a moment that uses it. Here's the wrong way to do it:

```
type Townsperson extending Person {
  property number -> sequence;
```

This won't work because each `sequence` keeps a record of the most recent number, and if every type just `sequence` then they would share it. So the right way to do it is to extend it to another type that you give a name to, and then that type will start from 1. So our `Townsperson` type would look like this instead:

```
scalar type TownspersonNumber extending sequence;

type Townsperson extending Person {
  property number -> TownspersonNumber;
  }
```

The number for a `sequence` type will continue to increase by 1 even if you delete other items. For example, if you inserted five `Townsperson` objects, they would have the numbers 1 to 5. Then if you deleted them all and then inserted one more `Townsperson`, this one would have the number 6 (not 1). So this is another possible option for our `Crewman` type. It's very convenient and there is no chance of duplication, but the number increments on its own every time you insert. Well, you *could* create duplicate numbers using `UPDATE` and `SET` (EdgeDB won't stop you there) but even then it would still keep track of the next number when you do the next insert.

## Using IS to query multiple types

So now we have quite a few types that extend the `Person` type, many with their own properties. The `Crewman` type has a property `number`, while the `NPC` type has a property called `age`. 

But this gives us a problem if we want to query them all at the same time. They all extend `Person`, but `Person` doesn't have all of their links and properties. So this query won't work:

```
SELECT Person {
  name,
  age,
  number,
};
```

The error is `ERROR: InvalidReferenceError: object type 'default::Person' has no link or property 'age'`. 

Luckily there is an easy fix for this: we can use `IS` inside square brackets to specify the type. Here's how it works:

- `.name`: this stays the same, because `Person` has this property
- `.age`: this belongs to the `NPC` type, so change it to `[IS NPC].age`
- `.number`: this belongs to the `Crewman` type, so change it to `[IS Crewman].number`

Now it will work:

```
SELECT Person {
  name,
  [IS NPC].age,
  [IS Crewman].number,
};
```

The output is now quite large, so here's just a part of it. You'll notice that types that don't have a property or a link will return an empty set: `{}`.

```
{
  Object {name: 'Woman 1', age: {}, number: {}},
  Object {name: 'The innkeeper', age: 30, number: {}},
  Object {name: 'Mina Murray', age: {}, number: {}},
  Object {name: {}, age: {}, number: 1},
  Object {name: {}, age: {}, number: 2},
   # /snip
}
```

This is pretty good, but the output doesn't show us the type for each of them. To refer to self in a query in EdgeDB you can use `__type__`. Calling just `__type__` will just give a `uuid` though, so we need to add `{name}` to indicate that we want the name of the type. All types have this `name` field that you can access if you want to show the object type in a query.

```
SELECT Person {
  __type__: { 
    name      # Name of the type inside module default
  },
  name, # Person.name
  [IS NPC].age,
  [IS Crewman].number,
};
```

Choosing the five objects from before from the output, it now looks like this:

```
{
  Object {__type__: Object {name: 'default::MinorVampire'}, name: 'Woman 1', age: {}, number: {}},
  Object {__type__: Object {name: 'default::NPC'}, name: 'The innkeeper', age: 30, number: {}},
  Object {__type__: Object {name: 'default::NPC'}, name: 'Mina Murray', age: {}, number: {}},
  Object {__type__: Object {name: 'default::Crewman'}, name: {}, age: {}, number: 1},
  Object {__type__: Object {name: 'default::Crewman'}, name: {}, age: {}, number: 2},
}
```

## Supertypes, subtypes, and generic types

The official name for a type that gets extended to another type is a `supertype` (meaning 'above type'). The types that extend them are their `subtypes` ('below types'). Because inheriting a type gives you all of its features, `subtype = supertype` will return `{true}`. And of course, `supertype = subtype` = `{false}` because supertypes do not inherit the features of their subtypes.

In our schema, that means that `SELECT PC IS Person` returns `{true}`, while `SELECT Person IS PC` returns `{false}`.

Now how about the simpler scalar types? We know that EdgeDB is very precise in having different types for integers, floats and so on, but what if you just want to know if a number is an integer for example? Of course this will work, but it's not very satisfying:

```
WITH year := 1887,
SELECT year IS int16 OR year IS int32 OR year IS int64;
```

Output: `{true}`.

But fortunately these types all [extend from abstract types too](https://www.edgedb.com/docs/datamodel/abstract), and we can use them. These abstract types all start with `any`, and are: `anytype`, `anyscalar`, `anyenum`, `anytuple`, `anyint`, `anyfloat`, `anyreal`. The only one that might make you pause is `anyreal`: this one means any real number, so both integers and floats, plus the `decimal` type.

So with that you can change the above input to `SELECT 1887 IS anyint` and get `{true}`.

## Multi in other places

We've seen `multi link` quite a bit already, and you might be wondering if `multi` can appear in other places too. The answer is yes. A `multi property` is like any other property, except that it can have more than one. For example, our `Castle` type has an `array<int16>` for the `doors` property:

```
type Castle extending Place {
  property doors -> array<int16>;
}
```

But it could do something similar like this:

```
type Castle extending Place {
  multi property doors -> int16;
}
```

With that, you would insert using `{}` instead of square brackets for an array:

```
INSERT Castle {
    name := 'Castle Dracula',
    doors := {6, 19, 10},
};
```

The next question of course is which is best to use: `multi property`, `array`, or an object type via a link. The answer is...it depends. But here are some good rules of thumb to help you decide which to choose.

- `multi property` vs. arrays:

How large is the data you are working with? A `multi property` is more efficient when you have a lot of data, while arrays are slower. But if you have small sets, then arrays are faster than `multi property`.

If you want to use indexes and constraints on individual elements, then you should use a `multi property`. We'll look at indexes in Chapter 16, but for now just know that they are a way of making lookups faster.

If order is important, than an array may be better. It's easier to keep the original order of items in an array.

- `multi property` vs. objects

Here we'll start with two areas where `multi property` is better, and then two areas where objects are better.

First negative for objects: objects are always larger, and here's why. Remember `DESCRIBE TYPE as TEXT`? Let's look at one of our types with that again. Here's the `Castle` type:

```
{
  'type default::Castle extending default::Place {
    required single link __type__ -> schema::Type {
        readonly := true;
    };
    optional single property doors -> array<std::int16>;
    required single property id -> std::uuid {
        readonly := true;
    };
    optional single property important_places -> array<std::str>;
    optional single property modern_name -> std::str;
    required single property name -> std::str;
};
```

You'll remember seeing the `readonly := true` types, which are created for each object type you make. The `__type__` link and `id` property together always make up 32 bytes.

The second negative for objects is similar: underneath, they are more work for the computer. EdgeDB runs on top of PostgreSQL, and a `multi link` to an object needs an extra "join" (a link table + object table), but a multi property only has one. Also, a "backward link" or "reverse link" (you'll see those in Chapter 14) takes more work as well.

Okay, now here are two positives for objects in comparison.

Do you have a lot of duplication in property values? If so, then using `constraint exclusive` on an object type is the more efficient way to do it.

Objects are easier to migrate if you need to have more than one value with each.

So hopefully that explanation should help. You can see that you have a lot of choice, so remembering the points above should help you make a decision. Most of the time, you'll probably have a sense for which one you want.

[Here is all our code so far up to Chapter 8.](code.md)

## Time to practice

1. How would you select all the `Place` types and their names, plus the `door` property if it's a `Castle`?

2. How would you select `Place` types with `city_name` for `name` if it's a `City` and `country_name` for `name` if it's a `Country`?

3. How would you do the same but only showing the results of `City` and `Country` types?

4. How would you display all the `Person` types that don't have `lover`s, with their names and their type names?

5. What needs to be fixed in this query? Hint: two things definitely need to be fixed, while one more should probably be changed to make it more readable.

```
SELECT Place {
  __type__,
  name
  [IS Castle]doors
};
```

[See the answers here.](answers.md)

Up next in Chapter 9: [Time to meet Dr. Seward, Arthur Holmwood, Quincey Morris, and the strange Renfield.](../chapter9/index.md)

# Chapter 9 - Strange events in England

For this chapter we've gone back in time a few weeks to when the ship left Varna and Mina and Lucy haven't left for Whitby yet. The introduction is also split into two parts. Here's the first:

> We still don't know where Jonathan is, and the ship The Demeter is on its way to England with Dracula inside. Meanwhile, Mina Harker is in London writing letters to her friend Lucy Westenra. Lucy has three boyfriends (named Dr. John Seward, Quincey Morris, and Arthur Holmwood), and they all want to marry her....

## Working with dates some more

It looks like we have some more people to insert. But first, let's think about the ship a little more. Everyone on the ship was killed by Dracula, but we don't want to delete the crew because they are still part of our game. The book tells us that the ship left on the 6th of July, and the last person (the captain) died on the 4th of August (in 1887). 

This is a good time to add two new properties to the `Person` type to indicate when a character is present. We'll call them `first_appearance` and `last_appearance`. The name `last_appearance` is a bit better than `death`, because for the game it doesn't matter: we just want to know when characters are there or not.

For these two properties we will just use `cal::local_date` for the sake of simplicity. There is also `cal::local_datetime` that includes time, but we should be fine with just the date. (And of course there is the `cal::local_time` type with just the time of day that we have in our `Date` type.)

Doing an insert for the `Crewman` types with the properties `first_appearance` and `last_appearance` will now look something like this:

```
INSERT Crewman {
  number := count(DETACHED Crewman) +1,
  first_appearance := cal::to_local_date(1887, 7, 6),
  last_appearance := cal::to_local_date(1887, 7, 16),
};
```

And since we have a lot of `Crewman` types already inserted, we can easily use the `UPDATE` and `SET` syntax on all of them if we assume they all died at the same time (or if being super precise doesn't matter).

Since `cal::local_date` has a pretty simple YYYYMMDD format, the easiest way to use it in an insert would be just casting from a string:

```
SELECT <cal::local_date>'1887-07-08';
```

But we imagined before that we had a function that gives separate numbers to put into a function, so we will continue to use that method.

Before we used the function `std::to_datetime` which took seven parameters; this time we'll use a similar but shorter [`cal::to_local_date`](https://www.edgedb.com/docs/edgeql/funcops/datetime#function::cal::to_local_date) function. It just takes three integers.

Here are its signatures (we're using the third):

```
cal::to_local_date(s: str, fmt: OPTIONAL str = {}) -> local_date
cal::to_local_date(dt: datetime, zone: str) -> local_date
cal::to_local_date(year: int64, month: int64, day: int64) -> local_date
```

Now we update the `Crewman` types and give them all the same date to keep things simple:

```
UPDATE Crewman
  SET {
  first_appearance := cal::to_local_date(1887, 7, 6),
  last_appearance := cal::to_local_date(1887, 7, 16)
};      
```

This will of course depend on our game. Can a `PC` actually visit the ship when it's sailing to England? Will there be missions to try to save the crew before Dracula kills them? If so, then we will need more precise dates. But we're fine with these approximate dates for now.

## Adding defaults to a type, and the overloaded keyword

Now let's get back to inserting the new characters. First we'll insert Lucy:

```
INSERT NPC {
  name := 'Lucy Westenra',
  places_visited := (SELECT City FILTER .name = 'London')
};
```

Hmm, it looks like we're doing a lot of work to insert 'London' every time we add a character. We have three characters left and they will all be from London too. To save ourselves some work, we can make London the default for `places_visited` for `NPC`. To do this we will need two things: `default` to declare a default, and the keyword `overloaded`. The word `overloaded` indicates that we are using `placed_visited` in a different way than the `Person` type that we got it from.

With `default` and `overloaded` added, it now looks like this:

```
type NPC extending Person {
  property age -> HumanAge;
  overloaded multi link places_visited -> Place {
    default := (SELECT City FILTER .name = 'London');
  }
}
```

## Using FOR and UNION

We're almost ready to insert our three new characters, and now we don't need to add `(SELECT City FILTER .name = 'London')` every time. But wouldn't it be nice if we could use a single insert instead of three?

To do this, we can use a `FOR` loop, followed by the keyword `UNION`. First, here's the `FOR` part:

```
FOR character_name IN {'John Seward', 'Quincey Morris', 'Arthur Holmwood'}
```

In other words: take this set of three strings and do something to each one. `character_name` is the variable name we chose to call each string in this set.

`UNION` comes next, because it is the keyword used to join sets together. For example, this query:

```
WITH city_names := (SELECT City.name),
  castle_names := (SELECT Castle.name),
  SELECT city_names UNION castle_names;
```

joins the names together to give us the output `{'Munich', 'Buda-Pesth', 'Bistritz', 'London', 'Castle Dracula'}`.

Now let's return to the `FOR` loop with the variable name `character_name`, which looks like this:

```
FOR character_name IN {'John Seward', 'Quincey Morris', 'Arthur Holmwood'}
  UNION (
    INSERT NPC {
    name := character_name,
    lover := (SELECT Person FILTER .name = 'Lucy Westenra'),
});
```

We get three `uuid`s as a response to show that they were entered.

Then we can check to make sure that it worked:

```
SELECT NPC {
  name,
  places_visited: {
    name,
  },
  lover: {
  name,
  },
} FILTER .name IN {'John Seward', 'Quincey Morris', 'Arthur Holmwood'};
```

And as we hoped, they are all connected to Lucy now.

```
  Object {
    name: 'John Seward',
    places_visited: {Object {name: 'London'}},
    lover: Object {name: 'Lucy Westenra'},
  },
  Object {
    name: 'Quincey Morris',
    places_visited: {Object {name: 'London'}},
    lover: Object {name: 'Lucy Westenra'},
  },
  Object {
    name: 'Arthur Holmwood',
    places_visited: {Object {name: 'London'}},
    lover: Object {name: 'Lucy Westenra'},
  },
}
```

By the way, now we could use this method to insert our five `Crewman` types inside one `INSERT` instead of doing it five times. We can put their numbers inside a single set, and use the same `FOR` and `UNION` method to insert them. Of course, we already used `UPDATE` to change the inserts but from now on in our code their insert will look like this:

```
FOR n IN {1, 2, 3, 4, 5}
  UNION (
  INSERT Crewman {
  number := n
  first_appearance := cal::to_local_date(1887, 7, 6),
  last_appearance := cal::to_local_date(1887, 7, 16),
});
```

It's a good idea to familiarize yourself with [the order to follow](https://www.edgedb.com/docs/edgeql/statements/for#for) when you use `FOR`:

```
[ WITH with-item [, ...] ]

FOR variable IN "{" iterator-set [, ...]  "}"

UNION output-expr ;
```

The important part is the `{` and `}`, because `FOR` is used on a set. If you try with an array or other type it will generate an error.

Now it's time to update Lucy with three lovers. Lucy has already ruined our plans to have `lover` as just a `link` (which means `single link`). We'll set it to `multi link` instead so we can add all three of the men. Here is our update for her:

```
UPDATE NPC FILTER .name = 'Lucy Westenra'
SET {
  lover := (
    SELECT Person FILTER .name IN {'John Seward', 'Quincey Morris', 'Arthur Holmwood'}
  )
};
```

Now we'll select her to make sure it worked. Let's use `LIKE` this time for fun when doing the filter:

```
SELECT NPC {
  name,
  lover: {
  name
  }
} FILTER .name LIKE 'Lucy%';
```

And this does indeed print her out with her three lovers.

```
{
  Object {
    name: 'Lucy Westenra',
    lover: {
      Object {name: 'John Seward'},
      Object {name: 'Quincey Morris'},
      Object {name: 'Arthur Holmwood'},
    },
  },
}
```

## Overloading instead of making a new type

So now that we know the keyword `overloaded`, we don't need the `HumanAge` type for `NPC` anymore. Right now it looks like this:

```
scalar type HumanAge extending int16 {
      constraint max_value(120);
}
```

You will remember that we made this type because vampires can live forever, but humans only live up to 120. But now we can simplify things. First we move the `age` property over to the `Person` type. Then (inside the `NPC` type) we use `overloaded` to add a constraint on it there. Now `NPC` uses `overloaded` twice:

```
type NPC extending Person {
  overloaded property age {
    constraint max_value(120)
  }
  overloaded multi link places_visited -> Place {
    default := (SELECT City filter .name = 'London');
  }
}
```

This is convenient because we can delete `age` from `Vampire` too:

```
type Vampire extending Person {    
  # property age -> int16; **Delete this one now**
  multi link slaves -> MinorVampire;
}
```

You can see that a good usage of abstract types and the `overloaded` keyword lets you simplify your schema if you do it right.

Okay, let's read the rest of the introduction for this chapter. It continues to explain what Lucy is up to:

>...She chooses to marry Arthur Holmwood, and says sorry to the other two. The other two men are sad, but fortunately the three men become friends with each other. Dr. Seward is depressed and tries to concentrate on his work. He is a psychiatrist who works in an asylum close to a large mansion called Carfax. Inside the asylum is a strange man named Renfield that Dr. Seward finds most interesting. Renfield is sometimes calm, sometimes completely crazy, and Dr. Seward doesn't know why he changes his mood so quickly. Also, Renfield seems to believe that he can get power from living things by eating them. He's not a vampire, but seems to act similar sometimes.

Oops! Looks like Lucy doesn't have three lovers anymore. Now we'll have to update her to only have Arthur:

```
UPDATE NPC FILTER .name = 'Lucy Westenra'
  SET {
    lover := (SELECT NPC FILTER .name = 'Arthur Holmwood'),
};
```

And then remove her from the other two. We'll just give them a sad empty set.

```
UPDATE NPC FILTER .name in {'John Seward', 'Quincey Morris'}
  SET {
    lover := {} # 😢
};
```

Looks like we are mostly up to date now. The only thing left is to insert the mysterious Renfield. He is easy because he has no lover to `FILTER` for:

```
INSERT NPC {
  name := 'Renfield',
  first_appearance := cal::to_local_date(1887, 5, 26),
  strength := 10,
};
```

But he has some sort of relationship to Dracula, similar to the `MinorVampire` type but different. He is also quite strong (as we will see later), so we gave him a `strength` of 10. Later on we'll learn more and more about him and his relationship with Dracula.

[Here is all our code so far up to Chapter 9.](code.md)

## Time to practice

1. Why doesn't this insert work and how can it be fixed?

```
FOR castle IN ['Windsor Castle', 'Neuschwanstein', 'Hohenzollern Castle']
  UNION(
    INSERT Castle {
      name := castle
});
```

2. How would you do the same insert while displaying the castle's name at the same time?
3. How would you change the `Vampire` type if all vampires needed a minimum strength of 10?
4. How would you update all the `Person` types to show that they died on September 11, 1887?

Hint: here's the type again:

```
  abstract type Person {
    required property name -> str {
        constraint exclusive;
    }
    property age -> int16;
    property strength -> int16;
    multi link places_visited -> Place;
    multi link lover -> Person;
    property first_appearance -> cal::local_date;
    property last_appearance -> cal::local_date;
  }
```

5. All the `Person` characters that have an `e` or an `a` in their name have been brought back to life. How would you update to do this?

Hint: "bringing back to life" means that `last_appearance` should return `{}`.

[See the answers here.](answers.md)

Up next in Chapter 10: [Thick fog and a storm hit the city of Whitby.](../chapter10/index.md)

# Chapter 10 - Terrible events in Whitby

> Mina and Lucy are enjoying their time in Whitby. One night there is a huge storm and a ship arrives in the fog - it's the Demeter, carrying Dracula. Lucy later begins to sleepwalk at night and looks very pale, and always says strange things. Mina tries to stop her, but sometimes Lucy gets outside. One night Lucy watches the sun go down and says: "His red eyes again! They are just the same." Mina is worried and asks Dr. Seward for help. Dr. Seward does an examination on Lucy, who is pale and weak but he doesn't know why. Dr. Seward decides to call his old teacher Abraham Van Helsing, who comes from the Netherlands to help. Van Helsing examines Lucy and looks shocked. Then he turns to the others and says, "Listen. We can help this girl, but you are going to find the methods very strange. You are going to have to trust me..."

The city of Whitby is in the northeast of England. Right now our `City` type just extends `Place`, which only gives us the properties `name`, `modern_name` and `important_places`. This could be a good time to give it a `property population` which can help us draw the cities in our game. It will be an `int64` to give us the size we need:

```
type City extending Place {
    property population -> int64;
}
```

By the way, here's the approximate population for our three cities at the time of the book. They are much smaller back in 1887:

- Buda-Pesth (Budapest): 402706
- London: 3500000
- Munich: 230023
- Whitby: 14400
- Bistritz (Bistrița): 9100

Inserting Whitby is easy enough:

```
INSERT City {
  name := 'Whitby',
  population := 14400
};
```

But for the rest of them it would be nice to update everything at the same time. 

## Working with tuples and arrays

If we have all the city data together, we can do a single insert with a `FOR` and `UNION` loop again. Let's imagine that we have some data inside a tuple, which seems similar to an array but is quite different. One big difference is that a tuple can hold different types, so this is okay:

`('Buda-Pesth', 402706), ('London', 3500000), ('Munich', 230023), ('Bistritz', 9100)`

In this case, the type is called a `tuple<str, int64>`.

Before we start using these tuples, let's make sure that we understand the difference between the two. To start, let's look at slicing arrays and strings in a bit more detail.

You'll remember that we use square brackets to access part of an array or a string. So `SELECT ['Mina Murray', 'Lucy Westenra'][1];` will give the output `{'Lucy Westenra'}` (that's index number 1).

You'll also remember that we can separate the starting and ending index with a colon, like in this example:

```
SELECT NPC.name[0:10];
```

This prints the first ten letters of every NPC's name:

```
{
  'Jonathan H',
  'The innkee',
  'Mina Murra',
  'John Sewar',
  'Quincey Mo',
  'Lucy Weste',
  'Arthur Hol',
}
```

But the same can be done with a negative number if you want to start from the index at the end. For example:

```
SELECT NPC.name[2:-2];
```

This prints from index 2 up to 2 indexes away from the end (it'll cut off the first two letters on each side). Here's the output:

```
{
  'nathan Hark',
  'e innkeep',
  'na Murr',
  'hn Sewa',
  'incey Morr',
  'cy Westen',
  'thur Holmwo',
}
```

Tuples are quite different: they behave more like object types with properties that have numbers instead of names. This is why tuples can hold different types together: `string`s with `array`s, `int64`s with `float32`s, anything.

So this is completely fine:

```
SELECT {('Bistritz', 9100, cal::to_local_date(1887, 5, 6)), ('Munich', 230023, cal::to_local_date(1887, 5, 8))};
```

The output is:

```
{
  ('Bistritz', 9100, <cal::local_date>'1887-05-06'),
  ('Munich', 230023, <cal::local_date>'1887-05-08'),
}
```

But now that the type is set (this one is type `tuple<str, int64, cal::local_date>`) you can't mix it up with other tuple types. So this is not allowed:

```
SELECT {(1, 2, 3), (4, 5, '6')};
```

EdgeDB will give an error because it won't try to work with tuples that are of different types. It complains:

```
ERROR: QueryError: operator 'UNION' cannot be applied to operands of type 'tuple<std::int64, std::int64, std::int64>' and 'tuple<std::int64, std::int64, std::str>'
  Hint: Consider using an explicit type cast or a conversion function.
```

In the above example we could easily just cast the last string into an integer and EdgeDB will be happy again: `SELECT {(1, 2, 3), (4, 5, <int64>'6')};`.

To access the fields of a tuple you still start from the number 0, but you write the numbers after a `.` instead of inside a `[]`. Now that we know all this, we can update all our cities at the same time. It looks like this:

```
FOR data in {('Buda-Pesth', 402706), ('London', 3500000), ('Munich', 230023), ('Bistritz', 9100)}
  UNION (
    UPDATE City FILTER .name = data.0
    SET {
    population := data.1
});
```

So it sends each tuple into the `FOR` loop, filters by the string (which is `data.0`) and then updates with the population (which is `data.1`).

## Ordering results and using math

Now that we have some numbers, we can start playing around with ordering and math. Ordering is quite simple: type `ORDER BY` and then indicate the property/link you want to order by. Here we order them by population:

```
SELECT City {
  name,
  population
} ORDER BY .population DESC;
```

This returns:

```
{
  Object {name: 'London', population: 3500000},
  Object {name: 'Buda-Pesth', population: 402706},
  Object {name: 'Munich', population: 230023},
  Object {name: 'Whitby', population: 14400},
  Object {name: 'Bistritz', population: 9100},
}
```

What's `DESC`? It means descending, so largest first and then going down. If we didn't write `DESC` then it would have assumed that we wanted to sort ascending. You can also write `ASC` (to make it clear to somebody reading the code for example), but you don't need to.

For some actual math, you can check out the functions in `std` [here](https://edgedb.com/docs/edgeql/funcops/set#function::std::sum) as well as the `math` module [here](https://edgedb.com/docs/edgeql/funcops/math#function::math::stddev). Instead of looking at each one, let's do a single big query to show some of them all together. To make the output nice, we will write it together with strings explaining the results and then cast them all to `<str>` so we can join them together using `++`.

```
WITH cities := City.population
  SELECT (
   'Number of cities: ' ++ <str>count(cities),
   'All cities have more than 50,000 people: ' ++ <str>all(cities > 50000),
   'Total population: ' ++ <str>sum(cities),
   'Smallest and largest population: ' ++ <str>min(cities) ++ ', ' ++ <str>max(cities),
   'Average population: ' ++ <str>math::mean(cities),
   'At least one city has more than 5 million people: ' ++ <str>any(cities > 5000000),
   'Standard deviation: ' ++ <str>math::stddev(cities)
);
```

This used quite a few functions: 

- `count()` to count the number of items,
- `all()` to return `{true}` if all items match and `{false}` otherwise, 
- `sum()` to add them all together, 
- `max()` to give the highest value, 
- `min()` to give the lowest one, 
- `math::mean()` to give the average, 
- `any()` to return `{true}` if any item matches and `{false}` otherwise, and
- `math::stddev()` for the standard deviation.

The output also makes it clear how they work:

```
{
  (
    'Number of cities: 5',
    'All cities have more than 50,000 people: false',
    'Total population: 4156229',
    'Smallest and largest population: 9100, 3500000',
    'Average population: 831245.8',
    'At least one city has more than 5 million people: false',
    'Standard deviation: 1500876.8248',
  ),
}
```

`any()`, `all()` and `count()` are particularly useful in operations to give you an idea of your data.

## Some more computables for names

We saw in this chapter that Dr. Seward asked his old teacher Dr. Van Helsing to come and help Lucy. Here is how Dr. Van Helsing began his letter to say that he was coming:

```
Letter, Abraham Van Helsing, M. D., D. Ph., D. Lit., etc., etc., to Dr. Seward.

“2 September.

“My good Friend,—
“When I have received your letter I am already coming to you.
```

The `Abraham Van Helsing, M. D., D. Ph., D. Lit., etc., etc.` part is interesting. This might be a good time to think about more about the `name` property inside our `Person` type. Right now our `name` property is just a single string, but we have people with different types of names, in this order:

Title | First name | Last name | Degree

So there is 'Count Dracula' (title and name), 'Dr. Seward' (title and name), 'Dr. Abraham Van Helsing, M.D, Ph. D. Lit.' (title + first name + last name + degrees), and so on.

That would lead us to think that we should have titles like `first_name`, `last_name`, `title` etc. and then join them together using a computable. But then again, not every character has these exact four parts to their name. Some others that don't are 'Woman 1' and 'The Innkeeper', and our game would certainly have a lot more of these. So it's probably not a good idea to get rid of `name` or always build names from separate parts. But in our game we might have characters writing letters or talking to each other, and they will have to use things like titles and degrees.

We could try a middle of the road approach instead. We'll keep `name`, and add some properties to `Person`:

```
property title -> str;
property degrees -> str;
property conversational_name := .title ++ ' ' ++ .name IF EXISTS .title ELSE .name;
property pen_name := .name ++ ', ' ++ .degrees IF EXISTS .degrees ELSE .name;
```

We could try to do something fancier with `degrees` by making it an `array<str>` for each degree, but our game probably doesn't need that much precision. We are just using this for our conversation engine.

Now it's time to insert Van Helsing:

```
INSERT NPC {
  name := 'Abraham Van Helsing',
  title := 'Dr.',
  degrees := 'M.D., Ph. D. Lit., etc.'
};
```

Now we can make use of these properties to liven up our conversation engine in the game. For example:

```
WITH helsing := (SELECT NPC filter .name ILIKE '%helsing%')
  SELECT(
 'There goes ' ++ helsing.name ++ '.',
 'I say! Are you ' ++ helsing.conversational_name ++ '?',
 'Letter from ' ++ helsing.pen_name ++ ',\n\tI am sorry to say that I bring bad news about Lucy.');
```

By the way, the `\n` inside the string creates a new line, while `\t` moves it one tab to the right.

This gives us:

```
{
  (
    'There goes Abraham Van Helsing.',
    'I say! Are you Dr. Abraham Van Helsing?',
    'Letter from Abraham Van Helsing, M.D., Ph. D. Lit., etc.,
        I am sorry to say that I bring bad news about Lucy.',
  ),
}
```

In a standard database with users it's much simpler: get users to enter their first names, last names etc. and make each one a property.

## Other escape characters and raw strings

Besides `\n` and `\t` there are quite a few other escape characters - you can see the complete list [here](https://www.edgedb.com/docs/edgeql/lexical/#strings). Some are rare but hexadecimal with `\x` is a good example of one that might be useful.

If you want to ignore escape characters, put an `r` in front of the quote. Let's try it with the example above. Only the last part has an `r`:

```
WITH helsing := (SELECT NPC filter .name ILIKE '%helsing%')
  SELECT(
 'There goes ' ++ helsing.name ++ '.',
 'I say! Are you ' ++ helsing.conversational_name ++ '?',
 'Letter from ' ++ helsing.pen_name ++ r',\n\tI am sorry to say that I bring bad news about Lucy.');
```

Now we get:

```
{
  (
    'There goes Abraham Van Helsing.',
    'I say! Are you Dr. Abraham Van Helsing?',
    'Letter from Abraham Van Helsing, M.D., Ph. D. Lit., etc.\n\tI am sorry to say that I bring bad news about Lucy.',
  ),
}
```

Finally, there is a raw string literal that uses `$$` on each side. Anything inside this will ignore any and all quotation marks, so you won't have to worry about the string ending in the middle. Here's one example:

```
SELECT $$ "Dr. Van Helsing would like to tell "them" about "vampires" and how to "kill" them." $$;
```

Without the `$$` it will look like three separate strings with three unknown keywords between them, and will generate an error.

## All the scalar types

You now have an understanding of all the EdgeDB scalar types. Summed up, the are: `int16`, `int32`, `int64`, `float32`, `float64`, `bigint`, `decimal`, `sequence`, `str`, `bool`, `datetime`, `duration`, `cal::local_datetime`, `cal::local_date`, `cal::local_time`, `uuid`, `json`, and `enum`. You can see the documentation for them [here](https://www.edgedb.com/docs/datamodel/scalars/index).

## UNLESS CONFLICT ON + ELSE + UPDATE

We put an `exclusive constraint` on `name` so that we won't be able to have two characters with the same name. The idea is that someone might see a character in the book and insert it, and then someone else would try to do the same. So this character named Johnny will work:

```
INSERT NPC {
  name := 'Johnny'
};
```

But if we try again we will get this error: `ERROR: ConstraintViolationError: name violates exclusivity constraint`

But sometimes just generating an error isn't enough - maybe we want something else to happen instead of just giving up. This is where `UNLESS CONFLICT ON` comes in, followed by an `ELSE` to explain what to do. `UNLESS CONFLICT ON` is probably easier to explain through an example. Here's one that shows what we can do to insert an unlimited number of characters named Johnny:

```
WITH johnnies := (SELECT NPC FILTER .name LIKE '%Johnny%'),
 INSERT NPC {
 name := 'Johnny'
 } UNLESS CONFLICT ON .name
 ELSE (
 UPDATE NPC
 SET {
 name := 'Johnny' ++ <str>(count(johnnies))
 });
```
 
Let's look at it step by step:
 
`WITH johnnies := (SELECT NPC FILTER .name LIKE '%Johnny%'),` Here we select all the `NPC` types that have Johnny in their name.
 
Then a normal insert:
 
```
INSERT NPC {
 name := 'Johnny'
 }
```

But it might not work so we add `UNLESS CONFLICT ON .name`. Then follow it with an `ELSE` to give instructions on what to do.

But here's the important part: we don't write `ELSE INSERT`, because we are already in the middle of an `INSERT`. What we write instead is `UPDATE` for the type we are inserting, so `UPDATE NPC`. We are basically taking the failed data from the insert and updating it to try again. So we'll do this instead:

```
UPDATE NPC
 SET {
 name := 'Johnny' ++ ' ' ++ <str>(count(johnnies))
 }
```

With this, the name becomes Johnny plus a number, namely the number of characters with Johnny in their name already. If there's a Johnny already then the next will be 'Johnny 1', then 'Johnny 2', and so on.

[Here is all our code so far up to Chapter 10.](code.md)

## Time to practice

1. Try inserting two `NPC` types in one insert with the following `name`, `first_appearance` and `last_appearance` information.

`{('Jimmy the Bartender', '1887-09-10', '1887-09-11'), ('Some friend of Jonathan Harker', '1887-07-08', '1887-07-09')`

2. Here are two more `NPC`s to insert, except the last one has an empty set (she's not dead). What problem are we going to have?

`{('Dracula\'s Castle visitor', '1887-09-10', '1887-09-11'), ('Old lady from Bistritz', '1887-05-08', {})`

3. How would you order the `Person` types by last letter of their names?

4. Try inserting an `NPC` with the name `''`. Now how would you do the same query in question 3?

Hint: the length of `''` is 0, which may be a problem.

5. How would you insert a `Country` called Slovakia, or Slovak Republic if the name is already taken?

6. How would you insert a character called 'Jonathan Harker', or 'Jonathan Harker 2', 'Jonathan Harker 3' etc. if the name has been taken?

Hint: `LIKE` can help. 

Bonus challenge: give the function [`contains()`](https://www.edgedb.com/docs/edgeql/funcops/generic#function::std::contains) a try to see if you can do the same thing. This function will return `{true}` for example from `SELECT contains('Jonathan Harker', 'Jonathan');`.

[See the answers here.](answers.md)

Up next in Chapter 11: [How can Van Helsing help them without sounding crazy?](../chapter11/index.md)

# Chapter 11 - What's wrong with Lucy?

> Dr. Van Helsing thinks that Lucy is being visited by a vampire. He doesn't tell the others yet because they won't believe him, but says they should close the windows and put garlic everywhere. They are confused, but Dr. Seward tells them to listen: Dr. Van Helsing is the smartest person he knows. It works, and Lucy gets better. But one night Lucy's mother walks into the room and thinks: "This place smells terrible! I'll open the windows for some fresh air." The next day Lucy wakes up pale and sick again. Every time someone makes a mistake like this Dracula gets in her room, and every time the men give Lucy their blood to help her get better. Meanwhile, Renfield continues to try to eat living things and Dr. Seward can't understand him. Then one day he didn't want to talk, only saying: “I don’t want to talk to you: you don’t count now; the Master is at hand.”

We are starting to see more and more events in the book with various characters. Some events have the three men and Dr. Van Helsing together, others have just Lucy and Dracula. Previous events had Jonathan Harker and Dracula, Jonathan Harker and the three women, and so on. In our game, we could use a sort of `Event` type to group everything together: the people, the time, the place, and so on. 

This `Event` type is a bit long, but it would be the main type for our events in the game so it needs to be detailed. We can put it together like this:

```
type Event {
  required property description -> str;
  required property start_time -> cal::local_datetime;
  required property end_time -> cal::local_datetime;
  required multi link place -> Place;
  required multi link people -> Person;
  property exact_location -> tuple<float64, float64>;
  property east -> bool;
  property url := 'https://geohack.toolforge.org/geohack.php?params=' ++ <str>.exact_location.0 ++ ' N ' ++ <str>.exact_location.1 ++ ' ' ++ 'E' if .east = true else 'W';
}
```    

You can see that most of the properties are `required`, because an `Event` type is not useful if it doesn't have all the information we need. It will always need a description, a time, place, and people participating. The interesting part is the `url` property: it's a computable that gives us an exact url for the location if we want. This one is not `required` because not every event in the book is in a perfectly known location.

The url that we are generating needs to know whether a location is east or west of Greenwich, and also whether they are north or south. Here is the url for Bistritz, for example:

```https://geohack.toolforge.org/geohack.php?pagename=Bistri%C8%9Ba&params=47_8_N_24_30_E```

Luckily for us, the events in the book all take place in the north part of the planet. So `N` is always going to be there. But sometimes they are east of Greenwich and sometimes west. To decide between east and west, we can use a simple `bool`. Then in the `url` property we put all the properties together to create a link, and finish it off with 'E' if `east` is `true`, and 'W' otherwise.

Let's insert one of the events in this chapter. It takes place on the night of September 11th when Dr. Van Helsing is trying to help Lucy. You can see that the `description` property is just a string that we write to make it easy to search later on. It can be as long or as short as we like, and we could even just paste in parts of the book.

```
INSERT Event {
  description := "Dr. Seward gives Lucy garlic flowers to help her sleep. She falls asleep and the others leave the room.",
  start_time := cal::to_local_datetime(1887, 9, 11, 18, 0, 0),
  end_time := cal::to_local_datetime(1887, 9, 11, 23, 0, 0),
  place := (SELECT Place FILTER .name = 'Whitby'),
  people := (SELECT Person FILTER .name ILIKE {'%helsing%', '%westenra%', '%seward%'}),
  exact_location := (54.4858, 0.6206),
  east := false
};
```

With all this information we can now find events by description, character, location, etc.

Now let's do a query for all events with the word `garlic flowers` in them:

```
SELECT Event {
  description,
  start_time,
  end_time,
  place: {
  __type__: {
    name
    },
  name
   },
  people: {
  name
    },
  exact_location,
  url
} FILTER .description ILIKE '%garlic flowers%';
```

It generates a nice output that shows us everything about the event:

```
{
  Object {
    description: 'Dr. Seward gives Lucy garlic flowers to help her sleep. She falls asleep and the others leave the room.',
    start_time: <cal::local_datetime>'1857-09-11T18:00:00',
    end_time: <cal::local_datetime>'1857-09-11T23:00:00',
    place: {Object {__type__: Object {name: 'default::City'}, name: 'Whitby'}},
    people: {
      Object {name: 'John Seward'},
      Object {name: 'Lucy Westenra'},
      Object {name: 'Abraham Van Helsing'},
    },
    exact_location: (54.4858, 0.6206),
    url: 'https://geohack.toolforge.org/geohack.php?params=54.4858 N 0.6206 W',
  },
}
```

The url works nicely too. Here it is: https://geohack.toolforge.org/geohack.php?params=54.4858_N_0.6206_W Clicking on it takes you directly to the city of Whitby.

## Writing our own functions

We saw that Renfield is quite strong: he has a strength of 10, compared to Jonathan's 5.

We could use this to experiment with making functions now. Because EdgeQL is strongly typed, you have to indicate both the input type and the return type in the signature. A function that takes an int16 and gives a float64 for example would have this signature:

```
function does_something(input: int16) -> float64
```

The `->` skinny arrow is used to show the return value.

For the body of the function we do the following:

- Write `using` and then follow it up with `()` brackets,
- Write the function inside it,
- Finish with a semicolon.

Here's a very simple function that takes a number and returns a string from it:

```
function make_string(input: int64) -> str
  using (<str>input);
```

That's all there is to it!

Now let's write a function where we have two characters fight. We will make it as simple as possible: the character with more strength wins, and if their strength is the same then the second player wins.

```
function fight(one: Person, two: Person) -> str
  using (
    SELECT one.name ++ ' wins!' IF one.strength > two.strength ELSE two.name ++ ' wins!'
);
```

So far only Jonathan and Renfield have the property `strength`, so let's put them up against each other:

```
WITH
  renfield := (SELECT Person filter .name = 'Renfield'),
  jonathan := (SELECT Person filter .name = 'Jonathan Harker')
    SELECT (
     fight(jonathan, renfield)
     );
```

It prints what we wanted to see: `{'Renfield wins!'}`

It might also be a good idea to add `LIMIT 1` when doing a filter for this function. Because EdgeDB returns sets, if it gets multiple results then it will use the function against each one. For example, let's imagine that we forgot that there were three women in the castle and wrote this:

```
WITH
  the_woman := (SELECT Person filter .name ILIKE '%woman%'),
  jonathan := (SELECT Person filter .name = 'Jonathan Harker')
  SELECT (
    fight(the_woman, jonathan)
    );
```

It would give us this result:

```
{'Jonathan Harker wins!', 'Jonathan Harker wins!', 'Jonathan Harker wins!'}
```

By the way, this result is unbelievable because Jonathan is weaker than all of the vampire women (vampires are very strong, both male and female). But right now their `strength` property just returns `{}`, so even Jonathan's strength of 5 is greater than this.

## Cartesian multiplication

This brings us to the subject of Cartesian multiplication. When you multiply sets in EdgeDB you are given the Cartesian product, which looks like this:

![](Cartesian_product.png)

Source: [user quartl on Wikipedia](https://en.wikipedia.org/wiki/Cartesian_product#/media/File:Cartesian_Product_qtl1.svg)

This means that if we do a `SELECT` on `Person` for our `fight()` function, it will run the function following this formula:

- `{the number of items in the first set}` * `{the number of items in the second set}`

So if there are two in the first set, and three in the second, it will run the function six times.

To demonstrate, let's put three objects in for each side of our function. We'll also make the output a little more clear:

```
WITH
  first_group := (SELECT Person FILTER .name in {'Jonathan Harker', 'Count Dracula', 'Arthur Holmwood'}),
  second_group := (SELECT Person FILTER .name in {'Renfield', 'Mina Murray', 'The innkeeper'}),
  SELECT(first_group.name ++ ' fights against ' ++ second_group.name ++ '. ' ++ fight(first_group, second_group));
```

Here is the output. It's a total of nine fights, where each person in Set 1 fights once against each person in Set 2.

```
{
  'Count Dracula fights against The innkeeper. The innkeeper wins!',
  'Count Dracula fights against Mina Murray. Mina Murray wins!',
  'Count Dracula fights against Renfield. Renfield wins!',
  'Jonathan Harker fights against The innkeeper. The innkeeper wins!',
  'Jonathan Harker fights against Mina Murray. Mina Murray wins!',
  'Jonathan Harker fights against Renfield. Renfield wins!',
  'Arthur Holmwood fights against The innkeeper. The innkeeper wins!',
  'Arthur Holmwood fights against Mina Murray. Mina Murray wins!',
  'Arthur Holmwood fights against Renfield. Renfield wins!',
}
```

And if you take out the filter and just write `SELECT Person` for the function, you will get well over 100 results. EdgeDB by default will only show the first 100, displaying this after showing you 100 results:

```  ... (further results hidden `\set limit 100`)```

[Here is all our code so far up to Chapter 11.](code.md)

## Time to practice

1. How would you write a function called `lucy()` that just returns all the `NPC` types matching the name 'Lucy Westenra'?

2. How would you write a function that takes two strings and returns `Person` types with names that match it?

Hint: try using `SET OF Person` as the return type.

3. What will the output of this be?

`SELECT {'Jonathan', 'Arthur'} ++ {' loves '} ++ {'Mina', 'Lucy'} ++ {' but '} ++ {'Dracula', 'The inkeeper'} ++ {' doesn\'t love '} ++ {'Mina', 'Jonathan'};`

4. How would you make a function that tells you how many times larger one city is than another?

5. Will `SELECT (City.population + City.population)` and `SELECT ((SELECT City.population) + (SELECT City.population))` produce different results?

[See the answers here.](answers.md)

Up next in Chapter 12: [Lucy one night: "Funny, what's that flapping against the window? Sounds like a bat or something..."](../chapter12/index.md)

# Chapter 12 - From bad to worse

There is no good news for our heroes this chapter:

> Dracula continues to break into Lucy's room every time people don't listen to Van Helsing. Dracula always turns into a cloud to sneak in, drinks her blood and sneaks away before morning. Lucy is getting so weak that it's a surprise that she's still alive. Meanwhile, Renfield breaks out of his cell and attacks Dr. Seward with a knife. He cuts him with it, and the moment he sees Dr. Seward's blood he stops and tries to drink it. The asylum security men take Renfield away and Dr. Seward is left confused and trying to understand him. He thinks there is a connection between him and the other events. That night, a wolf controlled by Dracula breaks the windows of Lucy's room and Dracula is able to get in again...

But there is good news for us, because we are going to keep learning about Cartesian products, plus how to overload a function.

## Overloading functions

Last chapter, we used the `fight()` function for some characters, but most only have `{}` for the `strength` property. That's why the Innkeeper defeated Dracula, which is obviously not what would really happen.

Jonathan Harker is just a human but is still quite strong. We'll give him a strength of 5. We'll treat that as the maximum strength for a human, except Renfield who is a bit unique. Every other human should have a strength between 1 and 5. EdgeDB has a random function called `std::rand()` that gives a `float64` in between 0.0 and 1.0. There is another function called `round()` that rounds numbers, so we'll use that too, and finally cast it to an '<int16>'. Our input looks like this:
 
```
SELECT <int16>round((random() * 5)); 
```

So now we'll use this to update our Person types and give them all a random strength.

```
WITH random_5 := (SELECT <int16>round((random() * 5)))
 # WITH isn't necessary - just making the query prettier
 
UPDATE Person
  FILTER NOT EXISTS .strength
  SET {
    strength := random_5 
};
```

And we'll make sure Count Dracula gets 20 strength, because he's Dracula:

```
UPDATE Vampire
FILTER .name = 'Count Dracula'
SET {
  strength := 20
  };
```

Now let's `SELECT Person.strength;` and see if it works:

```
{3, 3, 3, 2, 3, 2, 2, 2, 3, 3, 3, 3, 4, 1, 5, 10, 4, 4, 20, 4, 4, 4, 4}
```

Looks like it worked.

So now let's overload the `fight()` function. Right now it only works for one `Person` vs. another `Person`, but in the book all the characters get together to try to defeat Dracula. We'll need to overload the function so that more than one character can work together to fight. There are a lot of ways to do it, but we'll choose a simple one:

```
    function fight(names: str, one: int16, two: Person) -> str
  using (
    SELECT names ++ ' win!' IF one > two.strength ELSE two.name ++ ' wins!'
);
```

Note that overloading only works if the function signature is different. Here are the two signatures we have now for comparison:

```
fight(one: Person, two: Person) -> str
fight(names: str, one: int16, two: Person) -> str
```

If we tried to overload it with an input of `(Person, Person)`, it wouldn't work because it's the same. That's because EdgeDB uses the input we give it to know which form of the function to use.

So now it's the same function name, but we enter the names of the people together, their strength together, and then the `Person` they are fighting.

Now Jonathan and Renfield are going to try to fight Dracula together. Good luck!

```
WITH
  jon_and_ren_strength := <int16>(SELECT sum(
    (SELECT NPC FILTER .name IN {'Jonathan Harker', 'Renfield'}).strength)
    ),
  dracula := (SELECT Person FILTER .name = 'Count Dracula'),

  SELECT fight('Jon and Ren', jon_and_ren_strength, dracula);
```

So did they...

```
{'Count Dracula wins!'}
```

No, they didn't win. How about four people?

```
WITH
  four_people_strength := <int16>(SELECT sum(
    (SELECT NPC FILTER .name IN {'Jonathan Harker', 'Renfield', 'Arthur Holmwood', 'The innkeeper'}).strength)
    ),
  dracula := (SELECT Person FILTER .name = 'Count Dracula'),

  SELECT fight('The four people', four_people_strength, dracula);
```

Much better:

```
{'The four people win!'}
```

So that's how function overloading works - you can create functions with the same name as long as the signature is different.

You see overloading in a lot of existing functions, such as [sum](https://www.edgedb.com/docs/edgeql/funcops/set#function::std::sum) which takes in all numeric types and returns the sum. [std::to_datetime](https://www.edgedb.com/docs/edgeql/funcops/datetime#function::std::to_datetime) has even more interesting overloading with all sorts of inputs to create a `datetime`.

`fight()` was pretty fun to make, but that sort of function is better done on the gaming side. So let's make a function that we might actually use. Since EdgeQL is a query language, the most useful functions are usually ones that make queries shorter. 

Here is a simple one that tells us if a `Person` type has visited a `Place` or not:

```
function visited(person: str, city: str) -> bool
      using (
        WITH person := (SELECT Person FILTER .name = person LIMIT 1),
        SELECT city IN person.places_visited.name
      );
```

Now our queries are much faster:

```
edgedb> SELECT visited('Mina Murray', 'London');
{true}
edgedb> SELECT visited('Mina Murray', 'Bistritz');
{false}
```

Thanks to the function, even more complicated queries are still quite readable:

```
SELECT(
  'Did Mina visit Bistritz? ' ++ <str>visited('Mina Murray', 'Bistritz'),
  'What about Jonathan and Romania? ' ++ <str>visited('Jonathan Harker', 'Romania')
 );
```

This prints `{('Did Mina visit Bistritz? false', 'What about Jonathan and Romania? true')}`.

The documentation for creating functions [is here](https://www.edgedb.com/docs/edgeql/ddl/functions#create-function). You can see that you can create them with SDL or DDL but there is not much difference between the two. In fact, they are so similar that the only difference is the word `CREATE` that DDL needs. In other words, just add `CREATE` to make a function without touching the schema. For example, here's a function that just says hi:

```
function say_hi() -> str
  using('hi');
```

If you want to create it right now, just do this:

```
CREATE FUNCTION say_hi() -> str
  USING ('hi');
```

(or with lowercase letters, it doesn't matter)

You'll see more or less the same thing when you ask to `DESCRIBE FUNCTION say_hi`:

```
{'CREATE FUNCTION default::say_hi() ->  std::str USING (\'hi\');'}
```

## Deleting (dropping) functions

You can delete a function with the `DROP` keyword and the function signature. You only have to specify the input though, because the input is all that EdgeDB looks at when identifying a function. So in the case of our two `fight()` functions:

```
fight(one: Person, two: Person) -> str
fight(names: str, one: int16, two: Person) -> str
```

You would delete them with `DROP fight(one: Person, two: Person)` and `DROP fight(names: str, one: int16, two: Person)`. The `-> str` part isn't needed.


## More about Cartesian products - the coalescing operator

Now let's learn more about Cartesian products in EdgeDB. You might be surprised to see that even a single `{}` input always results in an output of `{}`, but this is the way that Cartesian products work. Remember, a `{}` has a length of 0 and anything multiplied by 0 is also 0. For example, let's try to add the names of places that start with b and those that start with f.

```
WITH b_places := (SELECT Place FILTER Place.name ILIKE 'b%'),
  f_places := (SELECT Place FILTER Place.name ILIKE 'f%'),
  SELECT b_places.name ++ ' ' ++ f_places.name;
```

The output is....maybe unexpected if you didn't read the previous paragraph.

```
{}
```

!! It's an empty set. But a search for places that start with b gives us `{'Buda-Pesth', 'Bistritz'}`. Let's see if the same works when we concatenate with `++` as well.

```
SELECT {'Buda-Pesth', 'Bistritz'} ++ {};
```

So that should give a `{}`. The output is...

```
error: operator '++' cannot be applied to operands of type 'std::str' and 'anytype'
  ┌─ query:1:8
  │
1 │ SELECT {'Buda-Pesth', 'Bistritz'} ++ {};
  │        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Consider using an explicit type cast or a conversion function.
```

Another surprise! This is an important point though: EdgeDB requires a cast for an empty set, because it won't try to guess at what type it is. There's no way to guess the type of an empty set if all we give it is `{}`, so EdgeDB won't try. You can probably guess that the same is true for array constructors too, so `SELECT [];` returns an error: `QueryError: expression returns value of indeterminate type`.

Okay, one more time, this time making sure that the `{}` empty set is of type `str`:

```
edgedb> SELECT {'Buda-Pesth', 'Bistritz'} ++ <str>{};
{}
edgedb>  
```

Good, so we have manually confirmed that using `{}` with another set always returns `{}`. But what if we want to:

- Concatenate the two strings if they exist, and
- Return what we have if one is an empty set?

In other words, how to add `{'Buda-Peth', 'Bistritz'}` to another set and return the original `{'Buda-Peth', 'Bistritz'}` if the second is empty?

To do that we can use the so-called [coalescing operator](https://www.edgedb.com/docs/edgeql/funcops/set#operator::COALESCE), which is written `??`. The explanation for the operator is nice and simple:

`Evaluate to A for non-empty A, otherwise evaluate to B.`

So if the item on the left is not empty it will return that, and otherwise it will return the one on the right.

Here is a quick example:

```
edgedb> SELECT {'Count Dracula is now in Whitby'} ?? <str>{};
```

Because we used `??` instead of `++`, the result is `{'Count Dracula is now in Whitby'}` and not `{}`.

So let's get back to our original query, this time with the coalescing operator:

```
WITH b_places := (SELECT Place FILTER Place.name ILIKE 'b%'),
     f_places := (SELECT Place FILTER Place.name ILIKE 'f%'),
     SELECT 
       b_places.name ++ ' ' ++ f_places.name IF EXISTS b_places.name AND EXISTS f_places.name 
     ELSE 
       b_places.name ?? f_places.name;
```

This returns:

```
{'Buda-Pesth', 'Bistritz'}
```

That's better.

But now back to Cartesian products. Remember, when we add or concatenate sets we are working with *every item in each set* separately. So if we change the query to search for places that start with b (Buda-Pesth and Bistritz) and m (Munich):

```
WITH b_places := (SELECT Place FILTER Place.name ILIKE 'b%'),
     m_places := (SELECT Place FILTER Place.name ILIKE 'm%'),
     SELECT 
       b_places.name ++ ' ' ++ m_places.name IF EXISTS b_places.name AND EXISTS m_places.name 
     ELSE 
       b_places.name ?? m_places.name;
```

Then we'll get this result:

```
{'Buda-Pesth Munich', 'Bistritz Munich'}
```

instead of something like 'Buda-Peth, Bistritz, Munich'. 

To get the output that we want, we can use two more functions:

- First [array_agg](https://www.edgedb.com/docs/edgeql/funcops/array#function::std::array_agg), which turns the set into an array.
- Next, [array_join](https://www.edgedb.com/docs/edgeql/funcops/array#function::std::array_join) to turn the array into a string.

```
WITH b_places := (SELECT Place FILTER Place.name ILIKE 'b%'),
     m_places := (SELECT Place FILTER Place.name ILIKE 'm%'),
     SELECT
     array_join(array_agg(b_places.name), ', ') ++ ', ' ++ array_join(array_agg(m_places.name), ', ') IF EXISTS b_places.name AND EXISTS m_places.name

      ELSE
        b_places.name ?? m_places.name;
```

Finally! The output is `{'Buda-Pesth, Bistritz, Munich'}`. Now with this more robust query we can use it on anything and don't need to worry about getting {} if we choose a letter like x. Let's look at every place that contains k or e:

```
WITH has_k := (SELECT Place FILTER Place.name ILIKE '%k%'),
     has_e := (SELECT Place FILTER Place.name ILIKE '%e%'),
     SELECT
     array_join(array_agg(has_k.name), ', ') ++ ', ' ++ array_join(array_agg(has_e.name), ', ') IF EXISTS has_k.name AND EXISTS has_e.name
     ELSE
    has_k.name ?? has_e.name;
```

This gives us the result:

```
{'Slovakia, Buda-Pesth, Castle Dracula'}
```

[Here is all our code so far up to Chapter 12.](code.md)

## Time to practice

1. Consider these two functions. Will EdgeDB accept the second one?

First function:

```
function gives_number(input: int64) -> int64
 using(input);
```

Second function:

```
function gives_number(input: int64) -> int32
 using(<int32>input);
```

2. How about these two functions? Will EdgeDB accept the second one?

First function:

```
function make64(input: int16) -> int64
  using(input);
```  

Second function:

```
function make64(input: int32) -> int64
  using(input);
```

3. Will `SELECT {} ?? {3, 4} ?? {5, 6};` work?

4. Will `SELECT <int64>{} ?? <int64>{} ?? {1, 2}` work?

5. Trying to make a single string of everyone's name with `SELECT array_join(array_agg(Person.name));` isn't working. What's the problem?

[See the answers here.](answers.md)

Up next in Chapter 13: [One of the men gives Lucy his blood again to try to save her. Will it be enough?](../chapter13/index.md)

# Chapter 13 - Farewell, Lucy. Meet the new Lucy

> This time it was too late to save Lucy, and she is dying. Suddenly she opens her eyes - they look very strange. She looks at Arthur and says “Arthur! Oh, my love, I am so glad you have come! Kiss me!” He tries to kiss her, but Van Helsing grabs him and says "Don't you dare!" It was not Lucy, but the vampire inside that was talking. She dies, and Van Helsing puts a golden crucifix on her lips to stop her from moving (crucifixes have that power over vampires). Unfortunately, the nurse steals it to sell when nobody is looking. A few days later there is news about a lady who is stealing a biting children - it's Vampire Lucy. Van Helsing tells the other people the truth, but Arthur doesn't believe him and becomes angry that he would say crazy things about his wife. Van Helsing says, "Fine, you don't believe me. Let's go to the graveyard together tonight and see what happens. Maybe then you will."

Looks like Lucy, an `NPC`, has become a `MinorVampire`. How should we show this in the database? Let's look at the types again first.

Right now `MinorVampire` is nothing special, just a type that extends `Person`:

```
type MinorVampire extending Person {
    }
```

Fortunately, according to the book she is a new "type" of person. The old Lucy is gone, and this new Lucy is now one of the `slaves` linked to the `Vampire` named Count Dracula.

So instead of trying to change the `NPC` type, we can just give `MinorVampire` an optional link to `Person`:

```
type MinorVampire extending Person {
  link former_self -> Person;
}
```

It's optional because we don't always know anything people before they were made into vampires. For example, we don't know anything about the three vampire women before Dracula found them so we can't make an `NPC` type for them.

Another way to (informally) link them is to give the same date to `last_appearance` for an `NPC` and `first_appearance` for a `MinorVampire`. First we will update Lucy with her `last_appearance`:

```
UPDATE Person filter .name = 'Lucy Westenra'
  SET {
  last_appearance := cal::to_local_date(1887, 9, 20)
};
```

Then we can add Lucy to the `INSERT` for Dracula. Note the first line where we create a variable called `lucy`. We then use that to bring in all the data to make her a `MinorVampire`, which is much more efficient than manually inserting all the information. It also includes her strength: we add 5 to that, because vampires are stronger.

Here's the insert:

```
WITH lucy := (SELECT Person filter .name = 'Lucy Westenra' LIMIT 1)
INSERT Vampire {
  name := 'Count Dracula',
  age := 800,
  slaves := {
    (INSERT MinorVampire {
      name := 'Woman 1',
  }),
    (INSERT MinorVampire {
     name := 'Woman 2',
  }),
    (INSERT MinorVampire {
     name := 'Woman 3',
  }),
    (INSERT MinorVampire {
     name := lucy.name,
     former_self := lucy,
     first_appearance := lucy.last_appearance,
     strength := lucy.strength + 5,
    }),
 },
 places_visited := (SELECT Place FILTER .name in {'Romania', 'Castle Dracula'})
};
```

With our `MinorVampire` types inserted that way, it's easy to find minor vampires that come from `Person` objects. We'll use two filters to make sure:

```
SELECT MinorVampire {
  name,
  strength,
  first_appearance,
} FILTER .name IN Person.name AND .first_appearance IN Person.last_appearance;
```

This gives us:

```
{
  Object {
    name: 'Lucy Westenra',
    strength: 7,
    first_appearance: <cal::local_date>'1887-09-20',
  },
}
```

We could have just used `FILTER.name IN Person.name` but two filters is better if we have a lot of characters later on. We could also switch to `cal::local_datetime` instead of `cal::local_date` to get the exact time down to the minute. But we won't need to get that precise just yet.

## On target delete

We've decided to keep the old `NPC` type for Lucy, because that Lucy will be in the game until September 1887. Maybe later `PC` types will interact with her, for example. But this might make you wonder about deleting links. What if we had chosen to delete the old type when she became a `MinorVampire`? Or more realistically, what if all `MinorVampire` types connected to a `Vampire` should be deleted when the vampire dies? We won't do that for our game, but you can do it with `on target delete`. `on target delete` means "when the target is deleted", and it goes inside `{}` after the link declaration. For this we have [four options](https://www.edgedb.com/docs/datamodel/links#ref-datamodel-links):

- `restrict`: forbids you from deleting the target object.

So if you declared `MinorVampire` like this:

```
type MinorVampire extending Person {
  link former_self -> Person {
    on target delete restrict;
    }
}
```

then you wouldn't be able to delete Lucy the `NPC` once she was connected to Lucy the `MinorVampire`.

- `delete source`: in this case, deleting Lucy the `Person` (the target of the link) will automatically delete Lucy the `MinorVampire` (the source of the link).

- `allow`: this one simply lets you delete the target (this is the default setting).

- `deferred restrict` - forbids you from deleting the target object, unless it is no longer a target object by the end of a transaction. So this option is like `restrict` but with a bit more flexibility.

So if you wanted to have all the `MinorVampire` types automatically deleted when their `Vampire` dies, you would add a link from `MinorVampire` to the `Vampire` type. Then you would add `on target delete delete source` to this: `Vampire` is the target of the link, and `MinorVampire` is the source that gets deleted.

Now let's look at some tips for making queries.

## Using DISTINCT, \__type__

- `DISTINCT`

`DISTINCT` is easy: just change `SELECT` to `SELECT DISTINCT` to get only unique results. We can see that right now there are quite a few duplicates in our `Person` objects if we `SELECT Person.strength;`. It looks something like this:

```
{5, 4, 4, 4, 4, 4, 10, 2, 2, 2, 2, 2, 2, 2, 7, 5}
```

Change it to `SELECT DISTINCT Person.strength;` and the output will now be `{2, 4, 5, 7, 10}`.

`DISTINCT` works by item and doesn't unpack, so `SELECT DISTINCT {[7, 8], [7, 8], [9]};` will return `{[7, 8], [9]}` and not `{7, 8, 9}`.

## Getting `__type__` all the time

We saw that we can use `__type__` to get object types in a query, and that `__type__` always has `.name` that shows us the type's name (otherwise we will only get the `uuid`). In the same way that we can get all the names with `SELECT Person.name`, we can get all the type names like this:

```
SELECT Person.__type__ {
  name
  };
```

It shows us all the types attached to `Person` so far:

```
{
  Object {name: 'default::Person'},
  Object {name: 'default::MinorVampire'},
  Object {name: 'default::Vampire'},
  Object {name: 'default::NPC'},
  Object {name: 'default::PC'},
  Object {name: 'default::Crewman'},
  Object {name: 'default::Sailor'},
}
```

Or we can use it in a regular query to return the types as well. Let's see what types there are that have the name `Lucy Westenra`:

```
SELECT Person {
  __type__: {
  name
  },
name
} FILTER .name = 'Lucy Westenra';
```

This shows us the objects that match, and of course they are `NPC` and `MinorVampire`.

```
{
  Object {__type__: Object {name: 'default::NPC'}, name: 'Lucy Westenra'},
  Object {__type__: Object {name: 'default::MinorVampire'}, name: 'Lucy Westenra'},
}
```

But there is a settingy you can use to always see the type when you make a query: just type `\set introspect-types on`. Once you do that, you'll always see the type name instead of just `Object`. Now even a simple search like this will give us the type:

```
SELECT Person 
  {
name
} FILTER .name = 'Lucy Westenra';
```

Here's the output:

```
{default::NPC {name: 'Lucy Westenra'}, default::MinorVampire {name: 'Lucy Westenra'}}
```

Because it's so convenient, from now on this book will show results as given with `\set introspect-types on`.

## Being introspective

The word `introspect` we just used in `\set introspect-types on` is also its own keyword: `INTROSPECT`. Every type has the following fields that we can access: `name`, `properties`, `links` and `target`, and `INTROSPECT` lets us see them. Let's give that a try and see what we get. We'll start with this on our `Ship` type, which is fairly small but has all four. Here are the properties and links of `Ship` again so we don't forget:

```
type Ship {
  property name -> str;
  multi link sailors -> Sailor;
  multi link crew -> Crewman;
}
```

First, here is the simplest `INTROSPECT` query: 

```SELECT (INTROSPECT Ship.name);``` 

This query isn't very useful to us but it does show how it works: it returns `{'default::Ship'}`. Note that `INTROSPECT` and the type go inside brackets; it's sort of a `SELECT` expression for types that you then select again to capture.

Now let's put `name`, `properties` and `links` inside the introspection:

```
SELECT (INTROSPECT Ship) {
  name,
  properties,
  links,
};
```

This gives us:

```
{
  schema::ObjectType {
    name: 'default::Ship',
    properties: {
      schema::Property {id: 4332bd76-0134-11eb-b20a-777e9cc80030},
      schema::Property {id: 43379841-0134-11eb-a427-dd28c7eae0ce},
    },
    links: {
      schema::Link {id: 4339bd5f-0134-11eb-bdca-21c31be0f932},
      schema::Link {id: 43360bf2-0134-11eb-b89c-a1211cb5d40e},
      schema::Link {id: 43383b6f-0134-11eb-86db-17f19f35ac2f},
    },
  },
}
```

Just like using `SELECT` on a type, if the output contains another type, property etc. we will just get an id. We will have to specify what we want there as well.

So let's add some more to the query to get the information we want:

```
SELECT (INTROSPECT Ship) {
   name,
   properties: {
     name,
     target: {
       name
       }
 },
 links: {
   name,
 target: {
   name
   },
 },
};
```

So what this will give is: 

1) The type name for `Ship`, 
2) The properties, and their names. But we also use `target`, which is what a property points to (the part after the `->`). For example, the target of `property name -> str` is `std::str`. And we want the target name too; without it we'll get an output like `target: schema::ScalarType {id: 00000000-0000-0000-0000-000000000100}`.
3) The links and their names, and the targets to the links...and the names of *their* targets too.

With all that together, we get something readable and useful. The output looks like this:

```
{
  schema::ObjectType {
    name: 'default::Ship',
    properties: {
      schema::Property {name: 'id', target: schema::ScalarType {name: 'std::uuid'}},
      schema::Property {name: 'name', target: schema::ScalarType {name: 'std::str'}},
    },
    links: {
      schema::Link {name: 'crew', target: schema::ObjectType {name: 'default::Crewman'}},
      schema::Link {name: '__type__', target: schema::ObjectType {name: 'schema::Type'}},
      schema::Link {name: 'sailors', target: schema::ObjectType {name: 'default::Sailor'}},
    },
  },
}
```

This type of query seems complex but it is just built on top of adding things like {name} every time you get output that only a machine can understand.

Plus, if the query isn't too complex (like ours), you might find it easier to read without so many new lines and indentation. Here's the same query written that way, which looks much simpler now:

```
SELECT (INTROSPECT Ship) {
  name,
  properties: {name, target: {name}},
  links: {name, target: {name}},
};             
```

[Here is all our code so far up to Chapter 13.](code.md)

## Time to practice

1. How would you insert an `NPC` named 'Mr. Swales' who has visited the `City` called 'York', the `Country` called 'England', and the `OtherPlace` called 'Whitby Abbey'? Try it in a single insert.

2. How readable is this introspect query?

```
SELECT (INTROSPECT Ship) {
  name,
  properties,
  links
};
```

3. What would be the shortest way to see what links from the `Vampire` type?

4. What do you think the output of `SELECT DISTINCT {1, 2} + {1, 2};` will be?

Hint: don't forget the Cartesian multiplication.

5. What do you think the output of `SELECT DISTINCT {2, 2} + {2, 2};` will be?

[See the answers here.](answers.md)

Up next in Chapter 14: [An old friend returns.](../chapter14/index.md)

# Chapter 14 - Jonathan Harker returns

> Finally there is some good news: Jonathan Harker is alive. After escaping Castle Dracula, he found his way to Budapest in August and then to a hospital, which sent Mina a letter. Mina took a train to the hospital where Jonathan was recovering, and they took a train back to England to the city of Exeter where they got married. Mina sends Lucy a letter from Exeter about the good news...but it arrives too late and Lucy never opens it. Meanwhile, the men visit the graveyard as planned and see vampire Lucy walking around. When Arthur sees her he finally believes Van Helsing, and so do the rest. They now know that vampires are real, and manage to destroy her. Arthur is sad but happy to see that Lucy is no longer forced to be a vampire and can now die in peace.

So we have a new city called Exeter, and adding it is of course easy: 

```
INSERT City {
  name := 'Exeter', 
  population := 40000
};
``` 

That's the population of Exeter at the time, and it doesn't have a `modern_name` that is different from the one in the book.

## Adding annotations to types and using @

Now that we know how to do introspection queries, we can start to give our types `annotations`. An annotation is a string inside the type definition that gives us information about it. By default, annotations can use the titles `title` or `description`.

Let's imagine that in our game a `City` needs at least 50 buildings. Let's use `description` for this:

```
type City extending Place {
  annotation description := 'Anything with 50 or more buildings is a city - anything else is an OtherPlace';
  property population -> int64;
}
```

Now we can do an `INTROSPECT` query on it. We know how to do this from the last chapter - just add `: {name}` everywhere to get the inner details. Ready!

```
SELECT (INTROSPECT City) {
  name,
  properties: {name},
  annotations: {name}
};
```

Uh oh, not quite:

```
{
  schema::ObjectType {
    name: 'default::City',
    properties: {
      schema::Property {name: 'id'},
      schema::Property {name: 'important_places'},
      schema::Property {name: 'modern_name'},
      schema::Property {name: 'name'},
      schema::Property {name: 'population'},
    },
    annotations: {schema::Annotation {name: 'std::description'}},
  },
}
```

Ah, of course: the `annotations: {name}` part returns the name of the *type*, which is `std::description`. This is where `@` comes in. To get the value inside we write something else: `@value`. The `@` is used to directly access the value inside (the string) instead of just the type name. Let's try one more time:

```
SELECT (INTROSPECT City) {
 name,
 properties: {name},
 annotations: {
   name,
   @value
  }
};
```

Now we see the actual annotation:

```
{
  schema::ObjectType {
    name: 'default::City',
    properties: {
      schema::Property {name: 'id'},
      schema::Property {name: 'important_places'},
      schema::Property {name: 'modern_name'},
      schema::Property {name: 'name'},
      schema::Property {name: 'population'},
    },
    annotations: {
      schema::Annotation {
        name: 'std::description',
        @value: 'Anything with 50 or more buildings is a city - anything else is an OtherPlace',
      },
    },
  },
}
```


What if we want an annotation with a different name besides `title` and `description`? That's easy, just declare with `abstract annotation` inside the schema and give it a name. We want to add a warning so that's what we'll call it:

```
abstract annotation warning;
```

We'll imagine that it is important to use `Castle` instead of `OtherPlace` for not just castles, but castle towns too. Thanks to the new abstract annotation, now `OtherPlace` gives that information along with the other annotation:

```
type OtherPlace extending Place {
  annotation description := 'A place with under 50 buildings - hamlets, small villages, etc.';
  annotation warning := 'Castles and castle towns do not count! Use the Castle type for that';
}
```

Now let's do an introspect query on just its name and annotations:

```
SELECT (INTROSPECT OtherPlace) {
  name,
  annotations: {name, @value}
};
```

And here it is:

```
{
  schema::ObjectType {
    name: 'default::OtherPlace',
    annotations: {
      schema::Annotation {name: 'std::description', @value: 'A place with under 50 buildings - hamlets, small villages, etc.'},
      schema::Annotation {name: 'default::warning', @value: 'Castles and castle towns do not count! Use the Castle type for that'},
    },
  },
}
```

## Even more working with dates

A lot of characters are starting to die now, so let's think about that. We could come up with a method to see who is alive and who is dead, depending on a `cal::local_date`. First let's take a look at the `People` objects we have so far. We can easily count them with `SELECT count(Person)`, which gives `{23}`. 

There is also a function called [`enumerate()`](https://www.edgedb.com/docs/edgeql/funcops/set#function::std::enumerate) that gives tuples of the index and the set that we give it. We'll use this to compare to our `count()` function to make sure that our number is right.

First a simple example of how to use `enumerate()`:

```
WITH three_things := {'first', 'second', 'third'},
 SELECT enumerate(three_things);
```

The output is:

```
{(0, 'first'), (1, 'second'), (2, 'third')}
```

So now let's use it with `SELECT enumerate(Person.name);` to make sure that we have 23 results. The last index should be 22:

```
{
  (0, 'Renfield'),
  (1, 'The innkeeper'),
  (2, 'Mina Murray'),
# snip
  (14, 'Count Dracula'),
  (15, 'Woman 1'),
  (16, 'Woman 2'),
  (17, 'Woman 3'),
}
```

There are only 18? Oh, that's right: the `Crewman` objects don't have a name so they don't show up. How can we get them in the query? We could of course try something fancy like this:

```
WITH 
  a := array_agg((SELECT enumerate(Person.name))),
  b:= array_agg((SELECT enumerate(Crewman.number))),
  SELECT (a, b);
```

(`array_agg()` is to avoid multiplying sets by sets, as we saw in Chapter 12)

But the result is less than satisfying:

```
{
  (
    [
      (0, 'Renfield'),
      (1, 'The innkeeper'),
      (2, 'Mina Murray'),
# snip
      (14, 'Count Dracula'),
      (15, 'Woman 1'),
      (16, 'Woman 2'),
      (17, 'Woman 3'),
    ],
    [(0, 1), (1, 2), (2, 3), (3, 4), (4, 5)],
  ),
}
```

The `Crewman` types are now just numbers, which doesn't look good. Let's give up on fancy queries and just update them with names based on the numbers instead. This will be easy:

```
UPDATE Crewman
  SET {
    name := 'Crewman ' ++ <str>.number
};
```

So now that everyone has a name, let's use that to see if they are dead or not. The logic is simple: we input a `cal::local_date`, and if it's greater than the date for `last_appearance` then the character is dead.

```
WITH p := (SELECT Person),
  date := <cal::local_date>'1887-08-16',
  SELECT(p.name, p.last_appearance, 'Dead on ' ++ <str>date ++ '? ' ++ <str>(date > p.last_appearance));  
```

Here is the output:

```
{
  ('Lucy Westenra', <cal::local_date>'1887-09-20', 'Dead on 1888-08-16? true'),
  ('Crewman 1', <cal::local_date>'1887-07-16', 'Dead on 1888-08-16? true'),
  ('Crewman 2', <cal::local_date>'1887-07-16', 'Dead on 1888-08-16? true'),
  ('Crewman 3', <cal::local_date>'1887-07-16', 'Dead on 1888-08-16? true'),
  ('Crewman 4', <cal::local_date>'1887-07-16', 'Dead on 1888-08-16? true'),
  ('Crewman 5', <cal::local_date>'1887-07-16', 'Dead on 1888-08-16? true'),
}
```

We could of course turn this into a function if we use it enough.

## Reverse links

Finally, let's look at how to follow links in reverse direction, one of EdgeDB's most powerful and useful features. Learning to use it can take a bit of effort, but it's well worth it.

We know how to get Count Dracula's `slaves` by name with something like this:

```
SELECT Vampire {
 name,
 slaves: {
   name
   }
};
```

That shows us the following:

```
{
  default::Vampire {
    name: 'Count Dracula',
    slaves: {
      default::MinorVampire {name: 'Woman 1'},
      default::MinorVampire {name: 'Woman 2'},
      default::MinorVampire {name: 'Woman 3'},
      default::MinorVampire {name: 'Lucy Westenra'},
    },
  },
}
```

But what if we are doing the opposite? Namely, starting from `SELECT MinorVampire` and wanting to access the `Vampire` type connected to it. Because right now, we can only bring up the properties that belong to the `MinorVampire` and `Person` type. Consider the following:

```
SELECT MinorVampire {
  name,
  # master... how do we get this?
  # There's no link to Vampire inside MinorVampire...
}
```

Since there's no `link master -> Vampire`, how do we go backwards to see the `Vampire` type that links to it?

This is where reverse links come in, where we use `.<` instead of `.` and specify the type we are looking for: `[IS Vampire]`. 

First let's move out of our `MinorVampire` query and just look at how `.<` works. Here is one example:

```
SELECT MinorVampire.<slaves[IS Vampire] {
  name, 
  age
  };
```

Because it goes in reverse order, it is selecting `Vampire` that has `.slaves` that are of type `MinorVampire`.

You can think of `MinorVampire.<slaves[IS Vampire] {name, age}` as "Select the name and age of the Vampire type with slaves that are of type MinorVampire" - from right to left.

Here is the output:

```
{default::Vampire {name: 'Count Dracula', age: 800}}
```

So far that's the same as just `SELECT Vampire: {name, age}`. But it becomes very useful in our query before, where we wanted to access multiple types. Now we can select all the `MinorVampire` types and their master:

```
SELECT MinorVampire {
  name,
  master := .<slaves[IS Vampire] {name},
};
```

You could read `.<slaves[IS Vampire] {name}` as "the name of the `Vampire` type that links back to `MinorVampire` through `.slaves`".

Here is the output:

```
{
  default::MinorVampire {
    name: 'Lucy Westenra',
    master: {default::Vampire {name: 'Count Dracula'}},
  },
  default::MinorVampire {
    name: 'Woman 1',
    master: {default::Vampire {name: 'Count Dracula'}},
  },
  default::MinorVampire {
    name: 'Woman 2',
    master: {default::Vampire {name: 'Count Dracula'}},
  },
  default::MinorVampire {
    name: 'Woman 3',
    master: {default::Vampire {name: 'Count Dracula'}},
  },
}
```

[Here is all our code so far up to Chapter 14.](code.md)

## Time to practice

1. How would you display just the numbers for all the `Person` types? e.g. if there are 20 of them, displaying `1, 2, 3..., 18, 19, 20`.

2. Using reverse lookup, how would you display 1) all the `Place` types (plus their names) that have an `o` in the name and 2) the names of the people that visited them?

3. Using reverse lookup, how would you display all the Person types that will later become `MinorVampire`s?

Hint: Remember, `MinorVampire` has a link back to the vampire's former self.

4. How would you give the `MinorVampire` type an annotation called `note` that says `'first_appearance for MinorVampire should always match last_appearance for its matching NPC type'`?

5. How would you see this `note` annotation for `MinorVampire` in a query?

[See the answers here.](answers.md)

Up next in Chapter 15: [Time to get revenge.](../chapter15/index.md)

# Chapter 15 - Time to start vampire hunting

> It's good that Jonathan is back, but he is still in shock. He doesn't know if the experience with Dracula was real or not, and thinks he might be crazy. But then he meets Van Helsing who tells him that it was all true. Jonathan hears this and becomes strong and confident again. Now they begin to search for Dracula. The others learn that the Carfax mansion across from Dr. Seward's asylum is the one that Dracula bought. So that's why Renfield was so strongly affected... They search the house when the sun is up and find boxes of earth in which Dracula sleeps. They destroy them all in Carfax, but there are still many left in London. If they don't destroy the other boxes, Dracula will be able to rest in them during the day and terrorize London every night when the sun goes down.

## More abstract types

This chapter they learned something about vampires: they need to sleep in coffins (boxes for dead people) with holy earth during the day. That's why Dracula brought 50 of them over by ship on the Demeter. This is important for the mechanics of our game so we should create a type for this. And if we think about it:

- Each place in the world either has coffins doesn't have them,
- Has coffins = vampires can enter and terrorize the people,
- If a place has coffins, we should know how many of them there are. 

This sounds like a good case for an abstract type. Here it is:

```
abstract type HasCoffins {
  required property coffins -> int16 {
    default := 0;
  }
}
```

Most places will not have a special vampire coffin, so the default is 0. The `coffins` property is just an `int16`, and vampires can remain close to a place if the number is 1 or greater. In the mechanics of our game we would probably give vampires an activity radius of about 100 km from a place with a coffin. That's because of the typical vampire schedule which is usually as follows:

- Wake up refreshed in the coffin after the sun goes down, get ready to leave by 8 pm to find people to terrorize
- Feel free because the night has just begun, start moving away from the safety of the coffins to find victims. May use a horse-driven carriage at 25 kph to do so.
- Around 1 or 2 pm, start to feel nervous. The sun will be up in about 5 hours. Is there enough time to get home?

So the part between 8 pm and 1 am is when the vampire is free to move away, and at 25 kph we get an activity radius of about 100 km around a coffin. At that distance, even the bravest vampire will start running back towards home by 2 am.

With a more complex game we could imagine that vampire terrorism is worse in the winter (activity radius = about 150 km), but we won't get into those details.

With our abstract type done, we will want to have a lot of types `extending` this. First we can have `Place` extend it, and that gives it to all the other location types such as `City`, `OtherPlace`, etc.:

```
abstract type Place extending HasCoffins {
  required property name -> str {
    constraint exclusive;
  };
  property modern_name -> str;
  property important_places -> array<str>;
}
```

Ships are also big enough to have coffins (the Demeter had 50 of them, after all) so we'll extend for `Ship` as well:

```
type Ship extending HasCoffins {
  property name -> str;
  multi link sailors -> Sailor;
  multi link crew -> Crewman;
}
```

If we want, we can now make a quick function to test whether a vampire can enter a place:

```
function can_enter(person_name: str, place: HasCoffins) -> str
  using (
    with vampire := (SELECT Person filter .name = person_name LIMIT 1)
    SELECT vampire.name ++ ' can enter.' IF place.coffins > 0 ELSE vampire.name ++ ' cannot enter.'
        );   
```

You'll notice that `person_name` in this function actually just takes a string that it uses to select a `Person`. So technically it could say something like 'Jonathan Harker cannot enter'. It has a `LIMIT 1` though, and we can probably trust that the user of the function will use it properly. If we can't trust the user of the function, there are some options:

- Overload the function to have two signatures, one for each type of Vampire:

```
function can_enter(vampire: Vampire, place: HasCoffins) -> str
function can_enter(vampire: MinorVampire, place: HasCoffins) -> str
```

- Create an abstract type (like `type IsVampire`) and extend it for `Vampire` and `MinorVampire`. Then `can_enter` can have this signature: `function can_enter(vampire: IsVampire, place: HasCoffins) -> str`

Overloading the function is probably the easier option, because we wouldn't need to touch the schema.

Now let's give London some coffins. According to the book, our heroes destroyed 29 coffins at Carfax that night, which leaves 21 in London.

```
UPDATE City filter .name = 'London'
  SET {
    coffins := 21
 };
```

Now we can finally call up our function and see if it works:

```
SELECT can_enter('Count Dracula', (SELECT City filter .name = 'London'));
``` 

We get `{'Count Dracula can enter.'}`.

Some other possible ideas for improvement later on for `can_enter()` are:

- Move the property `name` from `Place` and `Ship` over to `HasCoffins`. Then the user could just enter a string. The function would then use it to `SELECT` the type and then display its name, giving a result like "Count Dracula can enter London."
- Require a date in the function so that we can check if the vampire is dead or not first. For example, if we entered a date after Lucy died, it would just display something like `vampire.name ++ ' is already dead on ' ++ <str>.date ++ ' and cannot enter ' ++ city.name`.

## More constraints

Let's look at some more constraints. We've seen `exclusive` and `max_value` already, but there are [some others](https://www.edgedb.com/docs/edgeql/sdl/constraints/#constraints) that we can use as well. 

There is one called `max_len_value` that makes sure that a string doesn't go over a certain length. That could be good for our `PC` type, which we created many chapters ago. We only used it once as a test, because we don't have any players yet. We are still just using the book to populate the database with `NPC`s for our imaginary game. `NPC`s won't need this constraint because their names are already decided, but `max_len_value()` is good for `PC`s to make sure that players don't choose crazy names. We'll change it to look like this:

```
type PC extending Person {
  required property transport -> Transport;
  overloaded required property name -> str {
    constraint max_len_value(30);
  }
}
```

Then when we try to insert a `PC` with a name that is too long, it will refuse with `ERROR: ConstraintViolationError: name must be no longer than 30 characters.`

Another convenient constraint is called `one_of`, and is sort of like an enum. One place in our schema where we could use it is `property title -> str;` in our `Person` type. You'll remember that we added that in case we wanted to generate names from various parts (first name, last name, title, degree...). This constraint could work to make sure that people don't just make up their own titles:

```
property title -> str {
  constraint one_of('Mr.', 'Mrs.', 'Ms.', 'Lord')
}
```

For us it's probably not worth it to add a `one_of` constraint though, as there are probably too many titles throughout the book (Count, German *Herr*, etc. etc.).

Another place you could imagine using a `one_of` is in the months, because the book only goes from May to October of the same year. If we had an object type generating a date then you could have this sort of constraint inside it:

```
property month -> int64 {
  constraint one_of(5, 6, 7, 8, 9, 10)
}
```

But that will depend on how the game works.

Now let's learn about perhaps the most interesting constraint in EdgeDB:

## expression on: the most flexible constraint

One particularly flexible constraint is called [`expression on`](https://www.edgedb.com/docs/datamodel/constraints#constraint::std::expression), which lets us add any expression we want. After `expression on` you add the expression (in brackets) that must be true to create the type. In other words: "Create this type *as long as* (insert expression here)".

Let's say we need a type `Lord` for some reason later on, and all `Lord` types must have the word 'Lord' in their name. We can constrain the type to make sure that this is always the case. For this, we will use a function called [contains()](https://www.edgedb.com/docs/edgeql/funcops/generic#function::std::contains) that looks like this:

```
std::contains(haystack: str, needle: str) -> bool
```

It returns `{true}` if the `haystack` (a string) contains the `needle` (usually a shorter string).

We can write the constraint with `expression on` and `contains()` like this:

```
type Lord extending Person {
  constraint expression on (
    contains(__subject__.name, 'Lord') = true
    );
}
```

`__subject__` there refers to the type itself.

Now when we try to insert a `Lord` without it, it won't work:

```
INSERT Lord {
  name := 'Billy'
  # Other stuff..
};
```

But if the `name` is 'Lord Billy' (or 'Lord' anything), it will work.

While we're at it, let's practice doing a `SELECT` and `INSERT` at the same time so we see the output of our `INSERT` right away. We'll change `Billy` to `Lord Billy` and say that Lord Billy (considering his great wealth) has visited every place in our database.

```
SELECT (
 INSERT Lord {
 name := 'Lord Billy',
 places_visited := (SELECT Place),
 }) {
 name,
 places_visited: {
   name
   }
 };
 ```
 
Now that `.name` contains the substring `Lord`, it works like a charm:

```
{
  default::Lord {
    name: 'Lord Billy',
    places_visited: {
      default::Castle {name: 'Castle Dracula'},
      default::City {name: 'Whitby'},
      default::City {name: 'Munich'},
      default::City {name: 'Buda-Pesth'},
      default::City {name: 'Bistritz'},
      default::City {name: 'London'},
      default::Country {name: 'Romania'},
      default::Country {name: 'Slovakia'},
    },
  },
}
```

## Setting your own error messages

Since `expression on` is so flexible, you can use it in almost any way you can imagine. But it's not certain that the user will know about this constraint - there's no message informing the user of this. Meanwhile, the automatically generated error message we have right now is not helping the user at all:

`ERROR: ConstraintViolationError: invalid Lord`

So there's no way to tell that the problem is that `name` needs `'Lord'` inside it. Fortunately, constraints allow you to set your own error message just by using `errmessage`, like this: `errmessage := 'All lords need 'Lord' in their name.`

Now the error becomes:

`ERROR: ConstraintViolationError: All lords need 'Lord' in their name.`

Here's the `Lord` type now:

```
type Lord extending Person {
    constraint expression on (contains(__subject__.name, 'Lord') = true) {
        errmessage := "All lords need \'Lord\' in their name";
    };
};
```

## Links in two directions

Back in Chapter 6 we removed `link master` from `MinorVampire`, because `Vampire` already has `link slaves` to the `MinorVampire` type. One reason was complexity, and the other was because `DELETE` becomes impossible because they both depend on each other. But now that we know how to use reverse links, we can put `master` back in `MinorVampire` if we want.

(Note: we won't actually change the `MinorVampire` type here because we already know how to access `Vampire` with a reverse lookup, but this is how to do it)

First, here is the `MinorVampire` type at present:

```
type MinorVampire extending Person {
    link former_self -> Person;
}
```

To add the `master` link again, the best way is to start with a property called `master_name` that is just a string. Then we can use this in a reverse search to link to the `Vampire` type if the name matches. It's a single link, so we'll add `LIMIT 1` (it won't work otherwise). Here is what the type would look like now:

```
type MinorVampire extending Person {
  link former_self -> Person;
  link master := (SELECT .<slaves[IS Vampire] FILTER .name = MinorVampire.master_name LIMIT 1;
  required single property master_name -> str;
};
```

Now let's test it out. We'll make a vampire named Kain, who has two `MinorVampire` slaves named Billy and Bob.

```
INSERT Vampire {
 name := 'Kain',
 slaves := {
   (INSERT MinorVampire {
     name := 'Billy',
     master_name := 'Kain'
   }),
   (INSERT MinorVampire {
     name := 'Bob',
     master_name := 'Kain'
   })
  }
};
```

Now if the `MinorVampire` type works as it should, we should be able to see Kain via `link master` inside `MinorVampire` and we won't have to do a reverse lookup. Let's check:

```
SELECT MinorVampire {
   name,
   master_name,
   master: {
     name
   }
} FILTER .name IN {'Billy', 'Bob'};
```

And the result:

```
{
  default::MinorVampire {
    name: 'Billy',
    master_name: 'Kain',
    master: default::Vampire {name: 'Kain'},
  },
  default::MinorVampire {
    name: 'Bob',
    master_name: 'Kain',
    master: default::Vampire {name: 'Kain'},
  },
}
```

Beautiful! All the information is right there.

[Here is all our code so far up to Chapter 15.](code.md)

## Time to practice

1. How would you create a type called Horse with a `required property name -> str` that can only be 'Horse'?

2. How would you let the user know that it needs to be called 'Horse'?

3. How would you make sure that `name` for type `PC` is always between 5 and 30 characters in length?

Try it first with `expression on`.

4. How would you make a function called `display_coffins` that pulls up all the `HasCoffins` types with more than 0 coffins?

5. How would you make it without touching the schema?

[See the answers here.](answers.md)

Up next in Chapter 16: [Could Renfield be of help?](../chapter16/index.md)

# Chapter 16 - Is Renfield telling the truth?

> Arthur Holmwood's father has died and now Arthur is the head of the house. His new title is Lord Godalming, and he has a lot of money. With this money he helps the team to find the houses where Dracula has hidden his boxes. 

> Meanwhile, Van Helsing is curious and asks John Seward if he can meet Renfield. He is surprised to see that Renfield is very educated and well-spoken. Renfield talks about Van Helsing's research, politics, history, and so on - he doesn't seem crazy at all! But later, Renfield doesn't want to talk and just calls him an idiot. Very confusing. And one night, Renfield was very serious and asks them to let him leave. He says: “Don’t you know that I am sane and earnest...a sane man fighting for his soul? Oh, hear me! hear me! Let me go! let me go! let me go!” They want to believe him, but can't trust him. Finally Renfield stops and calmly says: “Remember, later on, that I did what I could to convince you tonight.”

## `index on` for quicker lookups

We're getting closer to the end of the book and there is a lot of data that we haven't entered yet. There is also a lot of data from the book that might be useful but we're not ready to organize yet. Fortunately, the original book Dracula is all organized into letters, diaries, etc. that begin with the date and sometimes the time. They all start out in this sort of way:

```
Dr. Seward’s Diary.
1 October, 4 a. m.—Just as we were about to leave the house...

Letter, Van Helsing to Mrs. Harker.
“24 September.
“Dear Madam...

Mina Murray’s Journal.
8 August. — Lucy was very restless all night, and I, too, could not sleep...
```

This is very convenient for us. With this we can make a type that holds a date and a string from the book for us to search through later. Let's call it `BookExcerpt` (excerpt = part of a book).

```
  type BookExcerpt {
    required property date -> cal::local_datetime;
    required property excerpt -> str;
    index on (.excerpt);
    required link author -> Person
}
```

The [`index on (.excerpt)`](https://www.edgedb.com/docs/datamodel/indexes#indexes) part is new, and means to create an index to make future queries faster. Lookups are faster with `index on` because now the database doesn't need to scan the whole set of objects in sequence to find objects that match. 

We could do this for certain other types too - it might be good for types like `Place` and `Person`. 

Note: `index` is good in limited quantities, but you don't want to index everything. Here is why:

- It makes the queries faster, but increases the database size.
- This may make `insert`s and `update`s slower if you have too many.

This is probably not surprising, because you can see that `index` is a choice that the user needs to make. If using `index` was the best idea in every case, then EdgeDB would just do it automatically.

Finally, here are two times when you don't need to create an `index`:

- on links,
- on exclusive constraints for a property.

Indexes are automatically created in these two cases so you don't need to use indexes for them.

So let's insert two book excerpts. The strings in these entries are very long (pages long, sometimes) so we will only show the beginning and the end here:

```
INSERT BookExcerpt {
  date := cal::to_local_datetime(1887, 10, 1, 4, 0, 0),
  author := (SELECT Person FILTER .name = 'John Seward' LIMIT 1),
  excerpt := 'Dr. Seward\'s Diary.\n 1 October, 4 a.m. -- Just as we were about to leave the house, an urgent message was brought to me from Renfield to know if I would see him at once..."You will, I trust, Dr. Seward, do me the justice to bear in mind, later on, that I did what I could to convince you to-night."',
};
```

```
INSERT BookExcerpt {
  date := cal::to_local_datetime(1887, 10, 1, 5, 0, 0),
  author := (SELECT Person FILTER .name = 'Jonathan Harker' LIMIT 1),
  excerpt := '1 October, 5 a.m. -- I went with the party to the search with an easy mind, for I think I never saw Mina so absolutely strong and well...I rest on the sofa, so as not to disturb her.',
};
```

Then later on we could do this sort of query to get all the entries in order and displayed as JSON.

```
SELECT <json>(SELECT BookExcerpt {
  date,
  author: {
    name
    },
  excerpt
} ORDER BY .date);
```

Here's the JSON output with just a small part of the excerpts:

```
{
  "{\"date\": \"1887-10-01T04:00:00\", \"author\": {\"name\": \"John Seward\"}, \"excerpt\": \"Dr. Seward\'s Diary.\\n 1 October, 4 a.m... -- Just as we were about to leave the house...\\\"\"}",
  "{\"date\": \"1887-10-01T05:00:00\", \"author\": {\"name\": \"Jonathan Harker\"}, \"excerpt\": \"1 October, 5 a.m. -- I went with the party to the search...\"}",
}
```

After this, we can add a link to our `Event` type to join it to our new `BookExcerpt` type. `Event` now looks like this:

```
type Event {
  required property description -> str;
  required property start_time -> cal::local_datetime;
  required property end_time -> cal::local_datetime;
  required multi link place -> Place;
  required multi link people -> Person;
  multi link excerpt -> BookExcerpt; # Only this is new
  property exact_location -> tuple<float64, float64>;
  property east_west -> bool;
  property url := 'https://geohack.toolforge.org/geohack.php?params=' ++ <str>.exact_location.0 ++ ' N ' ++ <str>.exact_location.1 ++ ' ' ++ 'E' if .east = true else 'W';
}
```

You can see that `description` is a short string that we write, while `excerpt` links to the longer pieces of text that come directly from the book.

## More functions for strings

The [functions for strings](https://www.edgedb.com/docs/edgeql/funcops/string) can be particularly useful when doing queries on our `BookExcerpt` type (or `BookExcerpt` via `Event`). One is called [`str_lower()`](https://www.edgedb.com/docs/edgeql/funcops/string#function::std::str_lower) and makes strings lowercase:

```
SELECT str_lower('RENFIELD WAS HERE');
{'renfield was here'}
```

Here it is in a longer query:

```
select BookExcerpt {
  excerpt,
  length := (<str>(SELECT len(.excerpt)) ++ ' characters'),
  the_date := (SELECT (<str>.date)[0:10]),
} FILTER contains(str_lower(.excerpt), 'mina');
```

It uses `len()` which is then cast to a string, and `str_lower()` to compare against `.excerpt()` by making it lowercase first. It also slices the `cal::local_datetime` into a string so it can just print indexes 0 to 10. Here is the output:

```
{
  Object {
    excerpt: '1 October, 5 a.m. -- I went with the party to the search with an easy mind, for I think I never saw Mina so absolutely strong and well...I rest on the sofa, so as not to disturb her.',
    length: '182 characters',
    the_date: '1887-10-01',
  },
}
```

Some other functions for strings are:

- `find()` This gives the index of the first match it finds, and returns `-1` if it can't find anything:

`SELECT find(BookExcerpt.excerpt, 'sofa');` produces `{-1, 151}`. That's because first `BookExcerpt.excerpt` doesn't have the word `sofa`, while the second has it at index 151.

- `str_split()` lets you make an array from a string, split however you like. Most common is to split by `' '` to separate words:

```
SELECT str_split('Oh, hear me! hear me! Let me go! let me go! let me go!', ' ');

{
  [
    'Oh,',
    'hear',
    'me!',
    'hear',
    'me!',
    'Let',
    'me',
    'go!',
    'let',
    'me',
    'go!',
    'let',
    'me',
    'go!',
  ],
}
```

But this works too:

```
SELECT MinorVampire {
  names := (SELECT str_split(.name, 'n'))
};
```

Now the `n`s are all gone:

```
{
  default::MinorVampire {names: ['Woma', ' 1']},
  default::MinorVampire {names: ['Woma', ' 2']},
  default::MinorVampire {names: ['Woma', ' 3']},
  default::MinorVampire {names: ['Lucy Weste', 'ra']},
}
```

You can also split by `\n` to split by new line. You can't see it but from the point of view of the computer every new line has a `\n` in it. So this:

```
SELECT str_split('Oh, hear me! 
hear me! 
Let me go! 
let me go! 
let me go!', '\n');
```

will split it by line and give the following array:

```
{['Oh, hear me! ', 'hear me! ', 'Let me go! ', 'let me go! ', 'let me go!']}
```

- Two functions called `re_match()` (for the first match) and `re_match_all()` (for all matches) if you know how to use [regular expressions](https://en.wikipedia.org/wiki/Regular_expression) (regexes) and want to use those. This could be useful because the book Dracula was written over 100 years ago and has different spelling sometimes. The word `tonight` for example is always written with the older `to-night` spelling in Dracula. We can use these functions to take care of that:

```
SELECT re_match_all('[Tt]o-?night', 'Dracula is an old book, so the word tonight is written to-night. Tonight we know how to write both tonight and to-night.');
{['tonight'], ['to-night'], ['Tonight'], ['tonight'], ['to-night']}
```

The function signature is `std::re_match(pattern: str, string: str) -> array<str>`, and as you can see the pattern comes first, then the string. The pattern `[Tt]o-?night` means words that: 

- start with a `T` or a `t`, 
- then have an `o`, 
- maybe have an `-` in between, 
- and end in `night`, 

so it gives: `{['tonight'], ['to-night']}`.

And to match anything, you can use the wildcard character: `.`

## One more note on `index on`

By the way, `index on` can also be used on expressions that you make yourself. This is especially useful now that we know all of these string functions. For example, if we always need to query a `City`'s name along with its population, we could index in this way:

```
type City extending Place {
    annotation description := 'Anything with 50 or more buildings is a city - anything else is an OtherPlace';
    property population -> int64;
    index on (.name ++ ': ' ++ <str>.population);
}
```

[Here is all our code so far up to Chapter 16.](code.md)

## Time to practice

1. How would you split all the `Person` names into two strings if they have two words, and ignore any that don't have exactly two words?

2. How would you display all the `Person` names and where the string 'ma' is in their name?

Hint: this uses the function `find()`.

3. How would you index on the `pen_name` property for type Person?

Hint: try using `describe type Person as SDL` to take a look at it the `pen_name` property again.

4. How would you display the name of every `Person` in uppercase followed by a space and then the same name in lowercase?

Hint: the [str_repeat()](https://www.edgedb.com/docs/edgeql/funcops/string#function::std::str_repeat) function could help (though there is more than one way to do it)

5. How would you use `re_match_all()` to display all the `Person.name`s with `Crewman` in the name? e.g. Crewman 1, Crewman 2, etc.

Hint: [Here are some basic concepts](https://en.wikipedia.org/w/index.php?title=Regular_expression&oldid=988356211#Basic_concepts) if you want a quick read on regular expressions.

[See the answers here.](answers.md)

Up next in Chapter 17: [The truth about Renfield.](../chapter17/index.md)

# Chapter 17 - Poor Renfield. Poor Mina.

> Last chapter Dr. Seward and Dr. Van Helsing wanted to let Renfield out, but couldn't trust him. But it turns out that Renfield was telling the truth! That night, Dracula found out that they were destroying his coffins and decided to attack Mina. He succeeded, and now Mina is slowly turning into a vampire. She is still human, but has a connection with Dracula now. 

> The group finds Renfield in a pool of blood, dying. Renfield is sorry and tells them the truth. He was in communication with Dracula and thought that he would help him become a vampire too, so he let him in the house. But once inside Dracula ignored him, and headed for Mina's room. Renfield attacked Dracula to try to stop him from hurting her, but Dracula was much stronger and won.

> Van Helsing does not give up though, and has a good idea. If Mina is now connected to Dracula, what happens if he uses hypnotism on her? Could that work? He takes out his pocket watch and tells her: "Please concentrate on this watch. You are beginning to feel sleepy...what do you feel? Think about the man who attacked you, try to feel where he is..."


## Named tuples

Remember the function `fight()` that we made? It was overloaded to take either `(Person, Person)` or `(str, Person)` as input. Let's give it Dracula and Renfield:

```
WITH 
  dracula := (SELECT Person FILTER .name = 'Count Dracula'),
  renfield := (SELECT Person FILTER .name = 'Renfield'),
SELECT fight(dracula, renfield);
```

The output is of course `{'Count Dracula wins!'}`. No surprise there.

One other way to do the same query is with a single tuple. Then we can give it to the function with `.0` for Dracula and `.1` for Renfield:

```
WITH fighters := (
  (SELECT Person FILTER .name = 'Count Dracula'), (SELECT Person FILTER .name = 'Renfield')
  ),
  SELECT fight(fighters.0, fighters.1);
```

That's not bad, but there is a way to make it clearer: we can give names to the items in the tuple instead of using `.0` and `.1`. It looks like a regular computable, using `:=`:

```
WITH fighters := (
  dracula := (SELECT Person FILTER .name = 'Count Dracula'), 
  renfield := (SELECT Person FILTER .name = 'Renfield')),
  SELECT fight(fighters.dracula, fighters.renfield);
```

Here's one more example of a named tuple:

```
WITH minor_vampires := (
  women := (SELECT MinorVampire FILTER .name LIKE '%Woman%'), 
  lucy := (SELECT MinorVampire FILTER .name LIKE '%Lucy%')),
SELECT (minor_vampires.women.name, minor_vampires.lucy.name);
```

The output is:

```
{('Woman 1', 'Lucy Westenra'), ('Woman 2', 'Lucy Westenra'), ('Woman 3', 'Lucy Westenra')}
```

Renfield is no longer alive, so we need to use `UPDATE` to give him a `last_appearance`. Let's do a fancy one again where we `SELECT` the update we just made and display that information:

```
SELECT ( # Put the whole update inside
  UPDATE NPC filter .name = 'Renfield'
    SET {
  last_appearance := <cal::local_date>'1887-10-03'
}) # then use it to call up name and last_appearance
  {
  name, 
  last_appearance
  };
```

This gives us: `{default::NPC {name: 'Renfield', last_appearance: <cal::local_date>'1887-10-03'}}`

One last thing: naming an item in a tuple doesn't have any effect on the items inside. So this:

```
SELECT ('Lucy Westenra', 'Renfield') = (character1 := 'Lucy Westenra', character2 := 'Renfield');
```

will return `{true}`.

## Putting abstract types together

Wherever there are vampires, there are vampire hunters. Sometimes they will destroy their coffins, and other times vampires will build more. So it would be cool to create a quick function called `change_coffins()` to change the number of coffins in a place. With this function we could write something like `change_coffins('London', -13)` to reduce it by 13, for example. But the problem right now is this: 

- the `HasCoffins` type is an abstract type, with one property: `coffins`
- places that can have coffins are `Place` and all the types from it, plus `Ship`,
- the best way to filter is by `.name`, but `HasCoffins` doesn't have this property.

So maybe we can turn this type into something else called `HasNameAndCoffins`, and put the `name` and `coffins` properties inside there. This won't be a problem because every place needs a name and a number of coffins in our game. Remember, 0 coffins means that vampires can't stay in a place for long: just quick trips in at night before the sun rises.

Here is the type with its new property. We'll give it two constraints: `exclusive` and `max_len_value` to keep names from being too long.

```
  abstract type HasNameAndCoffins {
    required property coffins -> int16 {
      default := 0;
  }
  required property name -> str {
      constraint exclusive;
      constraint max_len_value(30);
  }
}
```

So now we can change our `Ship` type (notice that we removed `name`)

```
    type Ship extending HasNameAndCoffins {
        multi link sailors -> Sailor;
        multi link crew -> Crewman;
    }
```

And the `Place` type. It's much simpler now.

```
abstract type Place extending HasNameAndCoffins {
  property modern_name -> str;
  property important_places -> array<str>;
}
```

Finally, we can change our `can_enter()` function. This one needed a `HasCoffins` type before: 

```
    function can_enter(person_name: str, place: HasCoffins) -> str
      using (
        with vampire := (SELECT Person FILTER .name = person_name LIMIT 1),
        SELECT vampire.name ++ ' can enter.' IF place.coffins > 0 ELSE vampire.name ++ ' cannot enter.'
            );   
```

But now that `HasNameAndCoffins` holds `name`, the user can now just enter a string. We'll change it to this:

```
function can_enter(person_name: str, place: str) -> str
  using (
  with 
    vampire := (SELECT Person FILTER .name = person_name LIMIT 1),
    place := (SELECT HasNameAndCoffins FILTER .name = place LIMIT 1)
    SELECT vampire.name ++ ' can enter.' IF place.coffins > 0 ELSE vampire.name ++ ' cannot enter.'
    );   
```

And now we can just enter `can_enter('Count Dracula', 'Munich')` to get `'Count Dracula cannot enter.'`. That makes sense: Dracula didn't bring any coffins there.

Finally, we can make our `change_coffins` function. It's easy:

```
    function change_coffins(place_name: str, number: int16) -> HasNameAndCoffins
       using (
         UPDATE HasNameAndCoffins FILTER .name = place_name
         SET {
           coffins := .coffins + number
         }
      );
```

Now let's give the ship `The Demeter` some coffins.

```
SELECT change_coffins('The Demeter', 10);
```

Then we'll make sure that it got them:

```
 SELECT Ship {
  name,
  coffins,
};
```

We get: `{default::Ship {name: 'The Demeter', coffins: 10}}`. The Demeter got its coffins!

## Aliases: creating subtypes when you need them

We've used abstract types a lot in this book. You'll notice that abstract types by themselves are generally made from very general concepts: `Person`, `HasNameAndCoffins`, etc. In databases in real life you'll probably see them in the forms `HasEmail`, `HasID` and so on, which get extended to make subtypes. Aliases also make subtypes, except they use `:=` instead of `extending` and draw from full types.

Let's make an alias for our schema too. Looking at the Demeter again, the ship left from Varna in Bulgaria and reached London. We'll imagine in our game that we have built Varna up into a big port for the characters to explore, and are changing the schema to reflect this. Right now our `Crewman` type just looks like this:

```
type Crewman extending HasNumber, Person {
}
```

Imagine that for some reason we would like a `CrewmanInBulgaria` alias as well, because Bulgarians call each other 'Gospodin' instead of Mr. and our game needs to reflect that. Our Crewman types will get called "Gospodin (name)" whenever they are there. Let's also add a `current_location` computable that makes a link to `Place` types with the name Bulgaria. Here's how to do that:

```
alias CrewmanInBulgaria := Crewman {
  name := 'Gospodin ' ++ .name,
  current_location := (SELECT Place filter .name = 'Bulgaria'),
}
```

You'll notice right away that `name` and `current_location` inside the alias are separated by commas, not semicolons. That's a clue that this isn't creating a new type: it's just creating a *shape* on top of the existing `Crewman` type. For the same reason, you can't do an `INSERT CrewmanInBulgaria`, because there is no such type. It gives this error:

```
error: cannot insert into expression alias 'default::CrewmanInBulgaria'
```

So all inserts are still done through the `Crewman` type. But because an alias is a subtype and a shape, we can select it in the same way as anything else. Let's add Bulgaria now,

```
INSERT Country {
  name := 'Bulgaria'
};
```

And then select this alias to see what we get:

```
SELECT CrewmanInBulgaria {
  name,
  current_location: {
    name
    }
  };
```

And now we see the same `Crewman` types under their `CrewmanInBulgaria` alias: with *Gospodin* added to their name and linked to the `Country` type we just inserted.

```
{
  default::Crewman {
    name: 'Gospodin Crewman 0',
    current_location: default::Country {name: 'Bulgaria'},
  },
  default::Crewman {
    name: 'Gospodin Crewman 1',
    current_location: default::Country {name: 'Bulgaria'},
  },
  default::Crewman {
    name: 'Gospodin Crewman 2',
    current_location: default::Country {name: 'Bulgaria'},
  },
  default::Crewman {
    name: 'Gospodin Crewman 3',
    current_location: default::Country {name: 'Bulgaria'},
  },
  default::Crewman {
    name: 'Gospodin Crewman 4',
    current_location: default::Country {name: 'Bulgaria'},
  },
  default::Crewman {
    name: 'Gospodin Crewman 5',
    current_location: default::Country {name: 'Bulgaria'},
  },
}
```

The [documentation on aliases](https://www.edgedb.com/docs/cheatsheet/aliases/) mentions that they let you use "the full power of EdgeQL (expressions, aggregate functions, backwards link navigation) from GraphQL", so keep aliases in mind if you use GraphQL a lot.

## Creating new names for types in a query (local expression aliases)

It's somewhat interesting that our alias is just declared using a `:=` when we wrote `alias CrewmanInBulgaria := Crewman`. Would it be possible to do something similar inside a query? The answer is yes: we can use `WITH` and then give a new name for an existing type. (In fact, the keyword `WITH` that we have been using the whole time is defined as a "[block used to define aliases](https://www.edgedb.com/docs/edgeql/statements/with)"). Take a simple query like this that shows Count Dracula and the names of his slaves:

```
SELECT Vampire {
  name,
  slaves: {
    name
  }
};
```

If we wanted to use `WITH` to create a new type that is identical to `Vampire`, we would just do this.

```
WITH Drac := Vampire,
SELECT Drac {
  name,
  slaves: {
    name
  }
};
```

So far this is nothing special, because the output is the same:

```
{
  default::Vampire {
    name: 'Count Dracula',
    slaves: {
      default::MinorVampire {name: 'Woman 1'},
      default::MinorVampire {name: 'Woman 2'},
      default::MinorVampire {name: 'Woman 3'},
      default::MinorVampire {name: 'Lucy Westenra'},
    },
  },
}
```

But where it becomes useful is when using this new type to perform operations or comparisons on the type it is created from. It's the same as `DETACHED`, but we give it a name and have some more flexibility to use it.

So let's give this a try. We'll pretend that we are testing out our game engine. Right now our `fight()` function is ridiculously simple, but let's pretend that it's complicated and needs a lot of testing. Here's the variant we would use:

```
function fight(one: Person, two: Person) -> str
  using (
    SELECT one.name ++ ' wins!' IF one.strength > two.strength ELSE two.name ++ ' wins!'
);
```

But for debugging purposes it would be nice to have some more info. Let's create the same function but call it `fight_2()` and add some more information on who is fighting who.

```
CREATE FUNCTION fight_2(one: Person, two: Person) -> str
  USING (
    SELECT one.name ++ ' fights ' ++ two.name ++ '. ' ++ one.name ++ ' wins!' IF one.strength > two.strength 
      ELSE 
    one.name ++ ' fights ' ++ two.name ++ '. ' ++ two.name ++ ' wins!'
);
```

So let's make our `MinorVampire` types fight each other and see what output we get. We have four of them (the three vampire women plus Lucy). First let's just put the `MinorVampire` type into the function and see what we get. Try to imagine what the output will be.

```
SELECT fight_2(MinorVampire, MinorVampire);
```

So the output for this is...

...

```
{
  'Lucy Westenra fights Lucy Westenra. Lucy Westenra wins!',
  'Woman 1 fights Woman 1. Woman 1 wins!',
  'Woman 2 fights Woman 2. Woman 2 wins!',
  'Woman 3 fights Woman 3. Woman 3 wins!',
}
```

The function only gets used four times because a set with only a single object goes in every time...and each `MinorVampire` is fighting itself. That's probably not what we wanted. Now let's try our local type alias again.

```
WITH M := MinorVampire,
  SELECT fight_2(M, MinorVampire);
```

By the way, so far this is exactly the same as:

```
SELECT fight_2(MinorVampire, DETACHED MinorVampire);
```

The output is too long now:

```
{
  'Lucy Westenra fights Lucy Westenra. Lucy Westenra wins!',
  'Woman 1 fights Lucy Westenra. Lucy Westenra wins!',
  'Woman 2 fights Lucy Westenra. Lucy Westenra wins!',
  'Woman 3 fights Lucy Westenra. Lucy Westenra wins!',
  'Lucy Westenra fights Woman 1. Lucy Westenra wins!',
  'Woman 1 fights Woman 1. Woman 1 wins!',
  'Woman 2 fights Woman 1. Woman 1 wins!',
  'Woman 3 fights Woman 1. Woman 1 wins!',
  'Lucy Westenra fights Woman 2. Lucy Westenra wins!',
  'Woman 1 fights Woman 2. Woman 2 wins!',
  'Woman 2 fights Woman 2. Woman 2 wins!',
  'Woman 3 fights Woman 2. Woman 2 wins!',
  'Lucy Westenra fights Woman 3. Lucy Westenra wins!',
  'Woman 1 fights Woman 3. Woman 3 wins!',
  'Woman 2 fights Woman 3. Woman 3 wins!',
  'Woman 3 fights Woman 3. Woman 3 wins!',
}
```

We succeeded at getting each `MinorVampire` type to fight the other one, but there are still `MinorVampire`s fighting themselves (Lucy vs. Lucy, Woman 1 vs. Woman 1, etc.). This is where the convenience of the local type alias comes in: we can filter on it, for example. Now we'll filter to only use `fight_2()` with objects that are not identical to each other:

```
WITH M := MinorVampire,
  SELECT fight_2(M, MinorVampire) FILTER M != MinorVampire;
```

And now we finally have every combination of `MinorVampire` fighting the other one, with no duplicates.

```
{
  'Lucy Westenra fights Woman 1. Lucy Westenra wins!',
  'Lucy Westenra fights Woman 2. Lucy Westenra wins!',
  'Lucy Westenra fights Woman 3. Lucy Westenra wins!',
  'Woman 1 fights Lucy Westenra. Lucy Westenra wins!',
  'Woman 1 fights Woman 2. Woman 2 wins!',
  'Woman 1 fights Woman 3. Woman 3 wins!',
  'Woman 2 fights Lucy Westenra. Lucy Westenra wins!',
  'Woman 2 fights Woman 1. Woman 1 wins!',
  'Woman 2 fights Woman 3. Woman 3 wins!',
  'Woman 3 fights Lucy Westenra. Lucy Westenra wins!',
  'Woman 3 fights Woman 1. Woman 1 wins!',
  'Woman 3 fights Woman 2. Woman 2 wins!',
}
```

Perfect!

With just `DETACHED` this wouldn't work: `SELECT fight_2(MinorVampire, DETACHED MinorVampire) FILTER MinorVampire != DETACHED MinorVampire;` won't do it because the first `DETACHED MinorVampire` isn't a variable name. Without that name to access, the next `DETACHED MinorVampire` is just a new `DETACHED MinorVampire` with no relation to the other one.

So how about adding links and properties in the same way that we did to our `CrewmanInBulgaria` alias? We can do that too by using `SELECT` and then adding any new links and properties you want inside `{}`. Here's a simple example:

```
WITH NPCExtraInfo := (SELECT NPC {
 would_win_against_dracula := .strength > Vampire.strength
 })
 SELECT NPCExtraInfo {
 name,
 would_win_against_dracula
 };
```

And here's the result. Looks like nobody wins:

```
{
  default::NPC {name: 'Jonathan Harker', would_win_against_dracula: {false}},
  default::NPC {name: 'Renfield', would_win_against_dracula: {false}},
  default::NPC {name: 'The innkeeper', would_win_against_dracula: {false}},
  default::NPC {name: 'Mina Murray', would_win_against_dracula: {false}},
  default::NPC {name: 'John Seward', would_win_against_dracula: {false}},
  default::NPC {name: 'Quincey Morris', would_win_against_dracula: {false}},
  default::NPC {name: 'Arthur Holmwood', would_win_against_dracula: {false}},
  default::NPC {name: 'Abraham Van Helsing', would_win_against_dracula: {false}},
  default::NPC {name: 'Lucy Westenra', would_win_against_dracula: {false}},
}
```

Let's create a quick type alias where Dracula has achieved all his goals and now rules London. We'll create a quick new type called `DraculaKingOfLondon` with a better name, and a link to `subjects` (= people under a king) that will be every `Person` that has been to London. Then we'll select this type, and also count how many subjects there are. It looks like this:

```
WITH DraculaKingOfLondon := (SELECT Vampire {
 name := .name ++ ', King of London',
 subjects := (SELECT Person FILTER 'London' in .places_visited.name),
 })
 SELECT DraculaKingOfLondon {
 name,
 subjects: {name},
 number_of_subjects := count(.subjects)
};
```

Here's the output:

```
{
  default::Vampire {
    name: 'Count Dracula, King of London',
    subjects: {
      default::NPC {name: 'Jonathan Harker'},
      default::NPC {name: 'Renfield'},
      default::NPC {name: 'The innkeeper'},
      default::NPC {name: 'Mina Murray'},
      default::NPC {name: 'John Seward'},
      default::NPC {name: 'Quincey Morris'},
      default::NPC {name: 'Arthur Holmwood'},
      default::NPC {name: 'Abraham Van Helsing'},
      default::NPC {name: 'Lucy Westenra'},
    },
    number_of_subjects: 9,
  },
}
```

[Here is all our code so far up to Chapter 17.](code.md)

## Time to practice

1. How would you display every NPC's name, strength, name and population of cities visited, and age (displaying 0 if age = `{}`)? Try it on a single line.

2. The query in 1. showed a lot of numbers without any context. What should we do?

3. Renfield is now dead and needs a `last_appearance`. Try writing a function called `make_dead(person_name: str, date: str) ->  Person` that lets you just write the character name and date to do it.

[See the answers here.](answers.md)

Up next in Chapter 18: [Jonathan the detective.](../chapter18/index.md)

# Chapter 18 - Using Dracula's own weapon against him

> Van Helsing was correct: Mina is connected to Dracula. He continues to use hypnotism to find out more about where he is and what he is doing. Jonathan does a lot of investigation into Dracula's activities in London. He visits all the companies that were involved in selling Dracula's house, and some moving companies who moved his coffins around. Jonathan is becoming more and more confident, and never stops working to find Dracula. They find Dracula's other house in London with all his money. Knowing that he will come to get it, they wait for him to arrive...Suddenly, Dracula runs into the house and attacks. Jonathan hits out with his knife, and cuts Dracula's bag with all his money. Dracula grabs some of the money that fell and jumps out the window. He yells at them: "You shall be sorry yet, each one of you! You think you have left me without a place to rest; but I have more. My revenge is just begun!" Then he disappears.

This is a good reminder that we should probably think about money in our game. The characters have been to countries like England, Romania and Germany, and each of those have their own money. An `abstract type` seems to be a good choice here: we should create an `abstract type Currency` that we can extend for all the other types of money.

Now, there is one difficulty: in the 1800s, monetary systems were more complicated than they are today. In England, for example it wasn't 100 cents to 1 pound, it was as follows:

- 12 pence (the smallest coin) made one shilling,
- 20 shillings made one pound, thus
- 240 pence per pound.

To reflect this, we'll say that `Currency` has three properties: `major`, `minor`, and `sub_minor`. Each one of these will have an amount, and finally there will be a number for the conversion, plus a `link owner -> Person`. So `Currency` will look like this:

```
abstract type Currency {
    required link owner -> Person;

    required property major -> str;
    required property major_amount -> float64 {
        default := 0;
        constraint min_value(0);
    }

    property minor -> str;
    property minor_amount -> float64 {
        default := 0;
        constraint min_value(0);
    }
    property minor_conversion -> int64;

    property sub_minor -> str;
    property sub_minor_amount -> float64 {
        default := 0;
        constraint min_value(0);
    }
    property sub_minor_conversion -> int64;
}
```

You'll notice that only `major` properties are `required`, because some currencies don't even have things like cents. In modern times that includes Japanese yen, Korean won, etc. that are just a single money unit and a number.

We also gave it a constraint of `min_value(0)` so that characters won't be able to buy with money they don't have. And complicated things like credit and negative money we can probably just ignore for now.

Then comes our first currency: the `Pound` type. The `minor` property is called `'shilling'`, and we use `minor_conversion` to get the amount in pounds. The same thing happens with `'pence'`. Then our characters can collect various coins but the final value can still quickly be turned into pounds. Here's the `Pound` type:

```
type Pound extending Currency {
    overloaded required property major {
        default := 'pound'
    }
    overloaded required property minor { 
        default := 'shilling'
    }
    overloaded required property minor_conversion {
        default := 20
    }
    overloaded property sub_minor { 
        default := 'pence'
    }
    overloaded property sub_minor_conversion {
        default := 240
    }
}
```

Now let's give Dracula some money. We'll give him 2500 pounds, 50 shillings, and 200 pence. Maybe that's a lot of money in 1887.

```
INSERT Pound {
 owner := (SELECT Person filter .name = 'Count Dracula'),
   major_amount := 2500,
   minor_amount := 50,
   sub_minor_amount := 200
};
```

Then we can use the conversion rates to display the total amount he owns in pounds:

```
SELECT Currency {
  owner: {name},
  total := .major_amount + (.minor_amount / .minor_conversion) + (.sub_minor_amount / .sub_minor_conversion)
};
```

He has this many:

`{default::Pound {owner: default::Vampire {name: 'Count Dracula'}, total: 2503.3333333333335}}`

We know that Arthur (now called Lord Godalming) has all the money he needs, but the others we aren't sure about. Let's give a few of them a random amount of money, and also `SELECT` it at the same time to display the result. For the random number we'll use the method we used for `strength` before: `round()` on a `random()` number multiplied by the maximum.

Finally, when displaying the total we will cast it to a `decimal` type. With this, we can display the number of pounds as something like 555.76 instead of 555.76545256. For this we use the same `round()` function, but using the last signature:

```
std::round(value: int64) -> float64
std::round(value: float64) -> float64
std::round(value: bigint) -> bigint
std::round(value: decimal) -> decimal
std::round(value: decimal, d: int64) -> decimal
```

That signature has an extra `d: int64` part for the number of decimal places we want to give it.

All together, it looks like this:

```
SELECT (FOR character IN {'Jonathan Harker', 'Mina Murray', 'The innkeeper', 'Emil Sinclair'}
  UNION (
    INSERT Pound {
      owner := (SELECT Person FILTER .name = character LIMIT 1),
      major_amount := (SELECT(round(random() * 500))),
      minor_amount := (SELECT(round(random() * 100))),
      sub_minor_amount := (SELECT(round(random() * 500)))
  })) {
 owner: {
   name
  },
 pounds := .major_amount,
 shillings := .minor_amount,
 pence := .sub_minor_amount,
 total_pounds := (SELECT
    (round(<decimal>(.major_amount + (.minor_amount / .minor_conversion) + (.sub_minor_amount / .sub_minor_conversion)), 2)))
 };
```

And then it will give a result similar to this with our collections of money, each with an owner:

```
{
  default::Pound {owner: default::NPC {name: 'Jonathan Harker'}, pounds: 54, shillings: 100, pence: 256, total_pounds: 60.07n},
  default::Pound {owner: default::NPC {name: 'Mina Murray'}, pounds: 360, shillings: 77, pence: 397, total_pounds: 365.50n},
  default::Pound {owner: default::NPC {name: 'The innkeeper'}, pounds: 87, shillings: 36, pence: 23, total_pounds: 88.90n},
  default::Pound {owner: default::PC {name: 'Emil Sinclair'}, pounds: 427, shillings: 19, pence: 88, total_pounds: 428.32n},
}
```

(If you don't want to see the `n` for the `decimal` type, just cast it into a `<float32>` or `<float64>`.)

You'll notice now that there could be some debate on how to show money. Should it be a `Currency` that links to an owner? Or should it be a `Person` that links to a property called `money`? Our way might be easier for a realistic game, simply because there are many types of `Currency`. If we chose the other method, we would have one `Person` type linked to every type of currency, and most of them would be zero. But with our method, we only have to create 'piles' of money when a character starts owning them.

Of course, if we only had one type of money then it would be simpler to just put it inside the `Person` type. We won't do this in our schema, but let's imagine how to do it. If the game were only inside the United States, it would be easier to just do this without an abstract `Currency` type:

```
type Dollar {
  required property dollars -> int64;
  required property cents -> int64;
  property total_money := .dollars + (.cents / 100)
}
```

The `total_money` type, by the way, will become a `float64` because of the `/ 100` part. We can confirm this with a quick query:

```
SELECT (100 + (55 / 100)) is float64;
```

The output: `{true}`.

We can see the same when we make an insert and use `SELECT` to check the `total_money` property:

```
SELECT(INSERT Dollar {
 dollars := 100,
 cents := 55
 }) {
 total_money
};
```

Here's the output: `{default::Dollar {total_money: 100.55}}`. Perfect! 

Not that we need this `Dollar` type in our game: in our schema it would be `type Dollar extending Currency`.

## Cleaning up the schema

We are nearing the end of the book, and should probably start to clean up the schema and inserts a bit.

First, we have two inserts here where we could only have one.

```
INSERT City {
  name := 'Munich',
};

INSERT City {
    name := 'London',
};
```

We'll change that to an insert with a `UNION`:

```
 FOR city_name IN {'Munich', 'London'}
   UNION (
   INSERT City {
     name := city_name
    }
  );
```

Then we'll do the same for the four `Country` types that we inserted (Hungary, Romania, France, Slovakia). Now they are a single insert:

```
FOR country_name IN {'Hungary', 'Romania', 'France', 'Slovakia'}
  UNION (
    INSERT Country {
      name := country_name
    }
  );
```

The other `City` inserts are a bit different: some have `modern_name` and others have `population`. In a real game we would insert them all in this sort of form, all at once:

```
FOR city IN {
  ('City 1's name', 'City 1's modern name', 800),
  ('City 2's name', 'City 2's modern name', 900),
  ('City 3's name', 'City 3's modern name', 455),
 }
  UNION (
    INSERT City {
      name := city.0,
      modern_name := city.1,
      population := city.2
});
```

And we would do the same with all the `NPC` types, their `first_appearance` data, and so on. But we don't have that many cities and characters to insert in this tutorial so we don't need to be so systematic yet.

We can also turn the inserts for the `Ship` type into a single one. Right now it looks like this:

```
FOR n IN {1, 2, 3, 4, 5}
  UNION (
  INSERT Crewman {
  number := n,
  first_appearance := cal::to_local_date(1887, 7, 6),
  last_appearance := cal::to_local_date(1887, 7, 16),
});

INSERT Sailor {
  name := 'The Captain',
  rank := <Rank>Captain
};

INSERT Sailor {
  name := 'The First Mate',
  rank := <Rank>FirstMate
};

INSERT Sailor {
  name := 'The Second Mate',
  rank := <Rank>SecondMate
};

INSERT Sailor {
  name := 'The Cook',
  rank := <Rank>Cook
};

INSERT Ship {
  name := 'The Demeter',
  sailors := Sailor,
  crew := Crewman
};
```

Let's put that all together:

```
INSERT Ship {
   name := 'The Demeter',
   sailors := {
    (INSERT Sailor {
       name := 'The Captain',
       rank := <Rank>Captain
     }),
    (INSERT Sailor {
       name := 'The First Mate',
       rank := <Rank>FirstMate
     }),
    (INSERT Sailor {
       name := 'The Second Mate',
       rank := <Rank>SecondMate
     }),
    (INSERT Sailor {
       name := 'The Cook',
       rank := <Rank>Cook
     })
 },
   crew := (
 FOR n IN {1, 2, 3, 4, 5}
   UNION (
   INSERT Crewman {
   number := n,
   first_appearance := cal::to_local_date(1887, 7, 6),
   last_appearance := cal::to_local_date(1887, 7, 16),
   })
 )
};
```

Much better!

[Here is all our code so far up to Chapter 18.](code.md)

## Time to practice

1. During the time of Dracula, the Goldmark was used in Germany. One Goldmark had 100 Pfennig. How would you make this type?

2. Try adding two annotations to this type. One should be called `description` and mention that `One mark = 100 Pfennig`. The other should be called `note` and mention the types of coins there are.

[Here are the types of coins](https://en.wikipedia.org/w/index.php?title=German_gold_mark&oldid=972733514#Base_metal_coins): 1, 2, 5, 10, 20, 25 Pfennig coins.

3. A vampire named Godbrand has just attacked a village and turned three villagers into `MinorVampire`s. How would you insert all four of them at once?

Here is their data (name, date of birth (`first_appearance`, date turned into a MinorVampire (`last_appearance`):

('Fritz Frosch', '1850-01-15', '1887-09-11'),
('Levanta Sinyeva', '1862-02-24', '1887-09-11'),
('김훈', '1860-09-09', '1887-09-11'),

[See the answers here.](answers.md)

Up next in Chapter 19: [Only Mina can tell them where Dracula has gone.](../chapter19/index.md)

# Chapter 19 - Dracula escapes

> Van Helsing hypnotises Mina, who is now half vampire and can feel Dracula. He asks her questions:

> “Where are you now?”  
>
> “I do not know. It is all strange to me!”  
>
> “What do you see?”  
>
> “I can see nothing; it is all dark.”  
>
> “What do you hear?”  
>
> “The water... and little waves.”  
>
> “Then you are on a ship?”  
>
> “Oh, yes!”

> Now they know that Dracula has escaped on a ship with his last box and is going back to Transylvania. Van Helsing and Mina go to Castle Dracula, while the others go to Varna to try to catch the ship when it arrives. Jonathan Harker just sharpens his knife, and looks like a different person now. All he wants to do is kill Dracula and save his wife. But where is the ship? Every day they wait...and then one day, they get a message: the ship arrived at Galatz up the river, not Varna! Are they too late? They rush off up the river to try to find Dracula.

## Adding some new types

We have another ship moving around the map in this chapter. Last time, we made a `Ship` type that looks like this:

```
type Ship extending HasNameAndCoffins {
  multi link sailors -> Sailor;
  multi link crew -> Crewman;
}
```

That's not bad, but we can build on it a bit more in this chapter. Right now, it doesn't have any information about visits made by which ship to where. Let's make a quick type that contains all the information on ship visits. Each visit will have a link to a `Ship` and a `Place`, and a `cal::local_date`. It looks like this:

```
type Visit {
  required link ship -> Ship;
  required link place -> Place;
  required property date -> cal::local_date;
}
```

This new ship that Dracula is on is called the `Czarina Catherine` (a Czarina is a Russian queen). Let's use that to insert a few visits from the ships we know. You'll remember that the other ship was called The Demeter and left from Varna towards London. 

But first we'll insert a new `Ship` and two new places (`City`s) so we can link them. We know the name of the ship and that there is one coffin in it: Dracula's last coffin. But we don't know about the crew, so we'll just insert this information:

```
INSERT Ship {
  name := 'Czarina Catherine',
  coffins := 1,
};
```

After that we have the two cities of Varna and Galatz. We'll put them in at the same time:

```
FOR city in {'Varna', 'Galatz'}
 UNION (
 INSERT City {
   name := city
});
```

The captain's book from the Demeter has a lot of other places too, so let's look at a few of them. The Demeter also passed through the Bosphorus. That area is the small bit of ocean in Turkey that divides Europe from Asia, so it's not a city. We can use the `OtherPlace` type for it, which is also the type we added some annotations to in Chapter 14. Remember how to call them up? It looks like this:

```
SELECT (INTROSPECT OtherPlace) {
  annotations: {
     @value
   }
};  
```

Let's look at the output to see what we wrote before to make sure that we should use it:

```
{
  schema::ObjectType {
    annotations: {
      schema::Annotation {
        @value: 'A place with under 50 buildings - hamlets, small villages, etc.',
      },
      schema::Annotation {
        @value: 'Castles and castle towns count! Use the Castle type for that',
      },
    },
  },
}
```

Well, it's not a castle and it isn't actually a place with buildings, so it should work. Soon we will create a `Region` type so that we can have a `Country` -> `Region` -> `City` layout. In that case, `OtherPlace` might be linked to from `Country` or `Region`. But in the meantime we'll add it without being linked to anything:

```
INSERT OtherPlace {
  name := 'Bosphorus'
};
```

That was easy. Now we can put the ship visits in.

```
FOR visit in {
    ('The Demeter', 'Varna', '1887-07-06'),
    ('The Demeter', 'Bosphorus', '1887-07-11'),
    ('The Demeter', 'Whitby', '1887-08-08'),
    ('Czarina Catherine', 'London', '1887-10-05'),
    ('Czarina Catherine', 'Galatz', '1887-10-28')}
    UNION (
 INSERT Visit {
   ship := (SELECT Ship FILTER .name = visit.0),
   place := (SELECT Place FILTER .name = visit.1),
   date := <cal::local_date>visit.2
   });
```

With this data, now our game can have certain ships in cities at certain dates. For example, imagine that a character has entered the city of Galatz. If the date is 28 October 1887, we can see if there are any ships in town:

```
SELECT Visit {
  ship: {
    name
    },
  place: {
    name
    },
  date
  } FILTER .place.name = 'Galatz' AND .date = <cal::local_date>'1887-10-28';
```

And it looks like there is a ship in town! It's the Czarina Catherine.

```
{
  Object {
    ship: default::Ship {name: 'Czarina Catherine'},
    place: default::City {name: 'Galatz'},
    date: <cal::local_date>'1887-10-28',
  },
}
```

While we're doing this, let's practice reverse lookup again on our visits. Here's one:

```
SELECT Ship.<ship[IS Visit] {
  place: {
    name
    },
  ship: {
    name
    },
  date
} FILTER .place.name = 'Galatz';
```

`Ship.<ship[IS Visit]` refers to all the `Visits` with link `ship` to type `Ship`. Because we are selecting `Visit` and not `Ship`, our filter is now on `Visit`'s `.place.name` instead of the properties inside `Ship`.

Here is the output:

```
{
  default::Visit {
    place: default::City {name: 'Galatz'},
    ship: default::Ship {name: 'Czarina Catherine'},
    date: <cal::local_date>'1887-10-28',
  },
}
```

By the way, the heroes of the story found out about the Czarina Catherine thanks to a telegram by a company in the city of Varna that told them. Here's what it said:

```
28 October.—Telegram. Rufus Smith, London, to Lord Godalming, care H. B. M. Vice Consul, Varna.

“Czarina Catherine reported entering Galatz at one o’clock to-day.”
```

Remember our `Date` type? We made it so that we could enter a string and get some helpful information in return. You can see now that it's almost a function:

```
type Date {
 required property date -> str;
 property local_time := <cal::local_time>.date;
 property hour := .date[0:2];
 property awake := 'asleep' IF <int16>.hour > 7 AND <int16>.hour < 19 ELSE 'awake';
}
```

Now that we know that the time was one o'clock, let's put that into the query too - including the `awake` property. Now it looks like this:

```
SELECT Ship.<ship[IS Visit] {
   place: {
     name
     },
   ship: {
     name
     },
   date,
   time := (SELECT(Insert Date {
 date := '13:00:00'
 }) {
   date,
   local_time,
   hour,
   awake
   }),
 } FILTER .place.name = 'Galatz';
 ```
 
 Here's the output, including whether vampires are awake or asleep.
 
 ```
 {
  default::Visit {
    place: default::City {name: 'Galatz'},
    ship: default::Ship {name: 'Czarina Catherine'},
    date: <cal::local_date>'1887-10-28',
    time: default::Date {date: '13:00:00', local_time: <cal::local_time>'13:00:00', hour: '13', awake: 'asleep'},
  },
}
```

## More cleaning up the schema

It is of course cool that we can do a quick insert in a query like this, but it's a bit weird. The problem is that we now have a random `Date` type floating around that is not linked to anything. Instead of that, let's just steal all the properties from `Date` to improve the `Visit` type instead.

```
  type Visit {
    link ship -> Ship;
    link place -> Place;
    required property date -> cal::local_date;
    property time -> str;
    property local_time := <cal::local_time>.time;
    property hour := .time[0:2];
    property awake := 'asleep' IF <int16>.hour > 7 AND <int16>.hour < 19 ELSE 'awake';
}
```

Then update the visit to Galatz to give it a `time`:

```
UPDATE Visit FILTER .place.name = 'Galatz'
  SET {
    time := '13:00:00'
};
```

Then we'll use the reverse query again. Let's add a computable for fun, assuming that it took two hours, five minutes and ten seconds for Arthur to get the telegram. We'll cast the string to a `cal::local_time` and then add a `duration` to it.

```
SELECT Ship.<ship[IS Visit] {
    place: {
      name
      },
    ship: {
      name
      },
    date,
    time,
    hour,
    awake,
    when_arthur_got_the_telegram := (<cal::local_time>.time) + <duration>'2 hours, 5 minutes, 10 seconds'
 } FILTER .place.name = 'Galatz';
```

And now we get all the output that the `Date` type gave us before, plus our extra info about when Arthur got the telegram:

```
{
  default::Visit {
    place: default::City {name: 'Galatz'},
    ship: default::Ship {name: 'Czarina Catherine'},
    date: <cal::local_date>'1887-10-28',
    time: '13:00:00',
    hour: '13',
    awake: 'asleep',
    when_arthur_got_the_telegram: <cal::local_time>'15:05:10',
  },
}
```

Since we are looking at `Place` again, now we can finish up the chapter by filling out the map with the `Region` type that we discussed. That's easy:

```
type Country extending Place {
  multi link regions -> Region;
}

type Region extending Place {
  multi link cities -> City;
  multi link other_places -> OtherPlace;
  multi link castles -> Castle;
}
```

That connects our types based on `Place` quite well.

Now let's do a medium-sized entry that has `Country`, `Region`, and `City` all at the same time. We'll choose Germany in 1887 because Jonathan went through there first. It will have:

- One country: Germany,
- Three regions: Prussia, Hesse, Saxony
- Two cities for each region, for six in total: Berlin and Königsberg, Darmstadt and Mainz, Dresden and Leipzig.

Here is the insert:

```
INSERT Country {
 name := 'Germany',
 regions := {
   (INSERT Region {
     name := 'Prussia',
     cities := {
       (INSERT City {
         name := 'Berlin'
         }),
       (INSERT City {
         name := 'Königsberg'
         }),
    }
 }),
   (INSERT Region {
     name := 'Hesse',
     cities := {
       (INSERT City {
         name := 'Darmstadt'
         }),
       (INSERT City {
         name := 'Mainz'
         }),
 }
 }),
   (INSERT Region {
     name := 'Saxony',
     cities := {
       (INSERT City {
          name := 'Dresden'
          }),
       (INSERT City {
          name := 'Leipzig'
          }),
    }
   })
 }
};
```

With this nice structure set up, we can do things like select a `Region` and see the cities inside it, plus the country it belongs to. To get `Country` from `Region` we need a reverse lookup:

```
SELECT Region {
  name,
  cities: {
    name
    },
    country := .<regions[IS Country] {
      name
      }
};
```

With the reverse lookup at the end we have another link between `Country` and its property `regions`, but now we get the `Country` as the output instead. Here's the output:

```
{
  Object {
    name: 'Prussia',
    cities: {default::City {name: 'Berlin'}, default::City {name: 'Königsberg'}},
    country: {default::Country {name: 'Germany'}},
  },
  Object {
    name: 'Hesse',
    cities: {default::City {name: 'Darmstadt'}, default::City {name: 'Mainz'}},
    country: {default::Country {name: 'Germany'}},
  },
  Object {
    name: 'Saxony',
    cities: {default::City {name: 'Dresden'}, default::City {name: 'Leipzig'}},
    country: {default::Country {name: 'Germany'}},
  },
}
```

[Here is all our code so far up to Chapter 19.](code.md)

## Time to practice

1. How would you display all the `City` names and the names of the `Region` they are in?

2. How about the `City` names plus the names of the `Region` and the name of the `Country` they are in?

[See the answers here.](answers.md)

Up next in Chapter 20: [The race against time.](../chapter20/index.md)

# Chapter 20 - The final battle

You made it to the final chapter - congratulations! Here's the final scene from the last chapter, though we won't spoil the final ending:

> Mina is almost a vampire now, and says she can feel Dracula all the time, no matter what hour of the day. Van Helsing arrives at Castle Dracula and Mina waits outside. Van Helsing then goes inside and destroys the vampire women and Dracula's coffin. Meanwhile, the other men approach from the south and are also close to Castle Dracula. Dracula's friends have him inside his box, and are carrying him on a wagon towards the castle as fast as they can. The sun is almost down, it is snowing, and the need to hurry to catch him. They get closer and closer, and grab the box. They pull the nails back and open it up, and see Dracula lying inside. Jonathan pulls out his knife. But just then the sun goes down. Dracula smiles and opens his eyes, and...

If you're curious about the ending to this scene, just [check out the book on Gutenberg](http://www.gutenberg.org/files/345/345-h/345-h.htm#CHAPTER_XIX) and search for "the look of hate in them turned to triumph".

We are sure that the vampire women have been destroyed, however, so we can do one final change by giving them a `last_appearance`. Van Helsing destroys them on November 5, so we will insert that date. But don't forget to filter Lucy out - she's the only `MinorVampire` that isn't one of the three women at the castle.

```
UPDATE MinorVampire FILTER .name != 'Lucy Westenra'
  SET {
    last_appearance := <cal::local_date>'1887-11-05'
};
```

Depending on what happens in the last battle, we might have to do the same for Dracula or some of the heroes...

## Reviewing the schema

[Here's the schema and inserted data we have up to Chapter 20.](code.md)

Now that you've made it through 20 chapters, you should have a good understanding of the schema that we put together and how to work with it. Let's take a look at it one more time from top to bottom. We'll make sure that we fully understand it and think about which parts are good, and which need improvement, for an actual game.

The first part to a schema is always the command to start the migration:

- `START MIGRATION TO {};`: This is how a schema migration starts. Everything goes inside `{}` curly brackets and ends with a `;` semicolon.
- `module default {}`: We only used one module (namespace) for our schema, but you can make more if you like. You can see the module when you use `DESCRIBE TYPE AS SDL` (or `AS TEXT`).

Here's an example with `Person`, which starts like this and shows us the module it's located in:

`abstract type default::Person`

For a real game our schema would probably be a lot larger with various modules. We might see types in different modules like `abstract type characters::Person` and `abstract type places::Place`, or even modules inside modules like `type characters::PC::Fighter` and `type characters::NPC::Barkeeper`.

Our first type is called `HasNameAndCoffins`, which is abstract because we don't want any actual objects of this type. Instead, it is extended by types like `Place` because every place in our game 

- 1) has a name, and 
- 2) has a number of coffins (which is important because places without coffins are safer from vampires).

```
abstract type HasNameAndCoffins {
  required property coffins -> int16 {
    default := 0;
}
  required property name -> str {
    constraint exclusive;
    constraint max_len_value(30);
  }
}
```

We could have gone with [`int32`, `int64` or `bigint`](https://www.edgedb.com/docs/datamodel/scalars/numeric#numerics) for the `coffins` property but we probably won't see that many coffins so `int16` is fine.

Next is `abstract type Person`. This type is by far the largest, and does most of the work for all of our characters. Fortunately, all vampires used to be people and can have things like `name` and `age`, so they can extend from it too.

```
abstract type Person {
  property first -> str;
  property last -> str;
  property title -> str;
  property degrees -> str;
  required property name -> str {
      constraint exclusive
  }
  property age -> int16;
  property conversational_name := .title ++ ' ' ++ .name IF EXISTS .title ELSE .name;
  property pen_name := .name ++ ', ' ++ .degrees IF EXISTS .degrees ELSE .name;
  property strength -> int16;
  multi link places_visited -> Place;
  multi link lover -> Person;
  property first_appearance -> cal::local_date;
  property last_appearance -> cal::local_date;   

}
```

`exclusive` is probably the most common [constraint](https://www.edgedb.com/docs/datamodel/constraints#constraints), which we use to make sure that each character has a unique name. This works because we already know all the names of all the `NPC` types. But if there is a chance of more than one "Jonathan Harker" or other character, we could give `Person` an `id` property and make that exclusive instead.

Properties like `conversational_name` are [computables](https://www.edgedb.com/docs/datamodel/computables#computables). In our case, we added properties like `first` and `last` later on. It is tempting to remove `name` and only use `first` and `last` for every character, but the book has too many characters with strange names: `Woman 2`, `The innkeeper`, etc. In a standard user database, we would certainly only use `first` and `last` and a field like `email` with `constraint exclusive` to make sure that all users are unique.

Every property has a type (like `str`, `bigint`, etc.). Computables have them too but we don't need to tell EdgeDB the type because the computable itself makes the type. For example, `pen_name` takes `.name` which is a `str` and adds more strings, which will of course produce a `str`. The `++` used to join them together is called [concatenation](https://www.edgedb.com/docs/edgeql/funcops/string#operator::STRPLUS).

The two links are `multi link`s, without which a `link` is to only one object. If you just write `link`, it will be a `single link` and you will have to add `LIMIT 1` when creating a link or it will give this error:

```
error: possibly more than one element returned by an expression for a computable link 'former_self' declared as 'single'
```

For `first_appearance` and `last_appearance` we use `cal::local_date` because our game is only based in one part of Europe inside a certain period. For a modern user database we would prefer [`std::datetime`](https://www.edgedb.com/docs/datamodel/scalars/datetime#type::std::datetime) because it is timezone aware and always ISO8601 compliant. One other reason why we use `cal::local_date` is because if you try to create a `datetime` before 1970 (when [Unix time](https://en.wikipedia.org/wiki/Unix_time) began), it will return an error. So don't write this - it will not be happy.

```
SELECT <datetime>'1887-09-09';
```

But for databases with users around the world, `datetime` is usually the best choice. Then you can use a function like [`std::to_datetime`](https://www.edgedb.com/docs/edgeql/funcops/datetime#function::std::to_datetime) to turn five `int64`s, one `float64` (for the seconds) and one `str` (for [the timezone](https://en.wikipedia.org/wiki/List_of_time_zone_abbreviations)) into a `datetime` that is always returned as UTC:


```
SELECT std::to_datetime(2020, 10, 12, 15, 35, 5.5, 'KST')
# October 12 2020, 3:35 pm and 5.5 seconds in Korea (KST = Korean Standard Time)
;
{<datetime>'2020-10-12T06:35:05.500000000Z'} # The return value is UTC, 6:35 (plus 5.5 seconds) in the morning
```

A similar abstract type to `HasNameAndCoffins` is this one:

```
abstract type HasNumber {
  required property number -> int16;
}
```

We only used this for the `Crewman` type, which only extends two abstract types and nothing else:

```
type Crewman extending HasNumber, Person {
}
```

This `HasNumber` type was used for the five `Crewman` objects, who in the beginning didn't have a name. But later on, we used those numbers to create names based on the numbers:

```
UPDATE Crewman
  SET {
    name := 'Crewman ' ++ <str>.number
};
```

So even though it was rarely used, it could become useful later on. For types later in the game you could imagine this being used for townspeople or random NPCs: 'Shopkeeper 2', 'Carriage Driver 12', etc.

Our vampire types extend `Person`, while `MinorVampire` also has an optional (single) link to `Person`. This is because some characters begin as humans and are "reborn" as vampires. With this format, we can use the properties `first_appearance` and `last_appearance` from `Person` to have them appear in the game. And if one is turned into a `MinorVampire`, we can link the two.

```
type Vampire extending Person {    
  multi link slaves -> MinorVampire;
}

type MinorVampire extending Person {
  link former_self -> Person;
}
```

With this format we can do a query like this one that pulls up all people who have turned into `MinorVampire`s.

```
SELECT Person {
  name,
  vampire_name := .<former_self[IS MinorVampire].name
} FILTER EXISTS .vampire_name;
```

In our case, that's just Lucy: `{default::NPC {name: 'Lucy Westenra', vampire_name: {'Lucy Westenra'}}}` But if we wanted to, we could extend the game back to more historical times and link the vampire women to an `NPC` type. That would become their `former_self`.

Our two enums were used for the `PC` and `Sailor` types:

```
scalar type Rank extending enum<Captain, FirstMate, SecondMate, Cook>;
type Sailor extending Person {
  property rank -> Rank;
}

scalar type Transport extending enum<Feet, HorseDrawnCarriage, Train>;
type PC extending Person {
  required property transport -> Transport;
}
```

The enum `Transport` never really got used, and needs some more transportation types. We didn't look at these in detail, but in the book there are a lot of different types of transport. In the last chapter, Arthur's team that waited at Varna used a boat called a "steam launch" which is smaller than the boat "The Demeter", for example. This enum would probably be used in the game logic itself in this sort of way:

- choosing `Feet` gives the character a certain speed and costs nothing, 
- `HorseDrawnCarriage` increases speed but decreases money, 
- `Train` increases speed the most but decreases money and can only follow railway lines, etc.

`Visit` is one of our two "hackiest" (but most fun) types. We stole most of it from the `Date` type that we created earlier but never used. In it, we have a `time` property that is just a string, but gets used in this way:

- by casting it into a <cal::local_time> to make the `local_time` property,
- by slicing its first two characters to get the `hour` property, which is just a string. This is only possible because we know that even single digit numbers like `1` need to be written with two digits: `01`
- by another computable called `awake` that is either 'asleep' or 'awake' depending on the `hour` property we just made, cast into an `int16`.

```
type Visit {
  required link ship -> Ship;
  required link place -> Place;
  required property date -> cal::local_date;
  property time -> str;
  property local_time := <cal::local_time>.time;
  property hour := .time[0:2];
  property awake := 'asleep' IF <int16>.hour > 7 AND <int16>.hour < 19 ELSE 'awake';
}
```

The NPC type is where we first saw the [`overloaded`](https://www.edgedb.com/docs/edgeql/sdl/links#overloading) keyword, which lets us use properties, links, functions etc. in different ways than the default. Here we wanted to constrain `age` to 120 years, and to use the `places_visited` link in a different way than in `Person` by giving it London as the default.

```
type NPC extending Person {
  overloaded property age {
    constraint max_value(120)
  }
  overloaded multi link places_visited -> Place {
    default := (SELECT City FILTER .name = 'London');
  }
}
```

Our `Place` type shows that you can extend as many times as you want. It's an `abstract type` that extends another `abstract type`, and then gets extended for other types like `City`.

```
abstract type Place extending HasNameAndCoffins {
  property modern_name -> str;
  property important_places -> array<str>;
}
```

The `important_places` property only got used once in this insert: 

```
INSERT City {
  name := 'Bistritz',
  modern_name := 'Bistrița',
  important_places := ['Golden Krone Hotel'],
};
```

and right now it is just an array. We can keep it unchanged for now, because we haven't made a type yet for really small locations like hotels and parks. But if we do make a new type for these places, then we should turn it into a `multi link`. Even our `OtherPlace` type is not quite the right type for this, as the [annotation](https://www.edgedb.com/docs/edgeql/sdl/annotations#annotations) shows:

```
type OtherPlace extending Place {
  annotation description := 'A place with under 50 buildings - hamlets, small villages, etc.';
  annotation warning := 'Castles and castle towns count! Use the Castle type for that';
}
```

So in a real game we would create some other smaller location types and make them a link from the `property important_places` inside `City`. We might also move `important_places` to `Place` so that types like `Region` could link from it too.

Annotations: we used `abstract annotation` to add a new annotation:

```
abstract annotation warning;
```

because by default a type [can only have annotations](https://www.edgedb.com/docs/datamodel/annotations#ref-datamodel-annotations) called `title`, `description`, or `deprecated`. We only used annotations for fun for this one type, because nobody else is working on our database yet. But if we made a real database for a game with many people working on it, we would put annotations everywhere to make sure that they know how to use each type.

Our `Lord` type was only created to show how to use `constraint expression on`, which lets us make our own constraints:

```
type Lord extending Person {
  constraint expression on (contains(__subject__.name, 'Lord') = true) {
    errmessage := "All lords need \'Lord\' in their name";
  };
};
```

(We might remove this in a real game, or maybe it would become type Lord extending PC so player characters could choose to be a lord, thief, detective, etc. etc.)

The `Lord` type uses the function [`contains`](https://www.edgedb.com/docs/edgeql/funcops/generic#function::std::contains) which returns `true` if the item we are searching for is inside the string, array, etc. It also uses `__subject__` which refers to the type itself: `__subject__.name` means `Person.name` in this case. [Here are some more examples](https://www.edgedb.com/docs/datamodel/constraints#constraint::std::expression) from the documentation of using `constraint expression on`.

Another possible way to create a `Lord` is to do it this way, since `Person` has the property called `title`:

```
type Lord extending Person {
  constraint expression on (__subject__.title = 'Lord') {
    errmessage := "All lords need \'Lord\' in their name";
  };
}
```

This will depend on if we want to create `Lord` types with names just as a single string in `.name`, or by using `.first`, `.last`, `.title` etc. with a computable to form the full name.

Our next types extending `Place` including `Country` and `Region` were looked at just last chapter, so we won't review them here. But `Castle` is a bit unique:

```
type Castle extending Place {
  property doors -> array<int16>;
}
```

Back in Chapter 7, we used this in a query to see if Jonathan could break any of the doors and escape the castle. The idea was simple: Jonathan would try to open every door, and if he had more strength then any one of them then he could escape the castle.

```
WITH 
  jonathan_strength := (SELECT Person FILTER .name = 'Jonathan Harker').strength,
  doors := (SELECT Castle FILTER .name = 'Castle Dracula').doors,
    SELECT jonathan_strength > min(array_unpack(doors));
```

However, later on we learned the `any()` function so let's see how we could use it here. With `any()`, we could change the query to this:

```
WITH
  jonathan_strength := (SELECT Person FILTER .name = 'Jonathan Harker').strength,
  doors := (SELECT Castle FILTER .name = 'Castle Dracula').doors,
    SELECT any(array_unpack(doors) < jonathan_strength); # Only this part is different
```

And of course, we could also create a function to do the same now that we know how to write functions and how to use `any()`. Since we are filtering by name (Jonathan Harker and Castle Dracula), the function would also just take two strings and do the same query.

Don't forget, we needed `array_unpack()` because the function [`any()`](https://www.edgedb.com/docs/edgeql/funcops/set#function::std::any) works on sets:

```
std::any(values: SET OF bool) -> bool
```

So this (a set) will work: `SELECT any({5, 6, 7} = 7);`

But this (an array) will not: `SELECT any([5, 6, 7] = 7);`

Here is the error:

```
operator '=' cannot be applied to operands of type 'array<std::int64>' and 'std::int64'
```

Our next type is `BookExcerpt`, which we imagined being useful for the humans creating the database. It would need a lot of inserts from each part of the book, with the text exactly as written. Because of that, we chose to use [`index on`](https://www.edgedb.com/docs/edgeql/sdl/indexes#indexes) for the `excerpt` property, which will then be faster to look up. Remember to use this only where needed: it will increase lookup speed, but make the database larger overall.

```
type BookExcerpt {
    required property date -> cal::local_datetime;
    required link author -> Person;
    required property excerpt -> str;
    index on (.excerpt);
}
```

Next is our other fun and hacky type, `Event`.

```
type Event {
  required property description -> str;
  required property start_time -> cal::local_datetime;
  required property end_time -> cal::local_datetime;
  required multi link place -> Place;
  required multi link people -> Person;
  multi link excerpt -> BookExcerpt;
  property exact_location -> tuple<float64, float64>;
  property east -> bool;
  property url := 'https://geohack.toolforge.org/geohack.php?params=' ++ <str>.exact_location.0 ++ ' N ' ++ <str>.exact_location.1 ++ ' ' ++ 'E' if .east = true else 'W';
}
```

This one is probably closest to an actual usable type for a real game. With `start_time` and `end_time`, `place` and `people` (plus `url`) we can properly arrange which characters are at which locations, and when. The `description` property is for users of the database with descriptions like `'The Demeter arrives at Whitby, crashing on the beach'` that are used to find events when we need to.

The last two types in our schema, `Currency` and `Pound`, were created two chapters ago so we won't review them here.


## Navigating EdgeDB documentation

Now that you have reached the end of the book, you will certainly start looking at the EdgeDB documentation. We'll close the book out with some tips to do so, so that it feels familiar and easy to look through.

### Syntax

This book included a lot of links to EdgeDB documentation, such as types, functions, and so on. If you are trying to create a type, property etc. and are having trouble, a good idea is to start with the section on syntax. This section always shows the order you need to follow, and all the options you have.

For a simple example, [here is the syntax on creating a module](https://www.edgedb.com/docs/edgeql/sdl/modules):

```
module ModuleName "{"
  [ schema-declarations ]
  ...
"}"
```

Looking at that you can see that a module is just a module name, `{}`, and everything inside (the schema declarations). Easy enough.

How about object types? [They look like this](https://www.edgedb.com/docs/edgeql/sdl/objects):

```
[abstract] type TypeName [extending supertype [, ...] ]
[ "{"
    [ annotation-declarations ]
    [ property-declarations ]
    [ link-declarations ]
    [ constraint-declarations ]
    [ index-declarations ]
    ...
  "}" ]
```

This should be familiar to you: you need `type TypeName` to start. You can add `abstract` on the left and `extending` for other types, and then everything else goes inside `{}`.

Meanwhile, the [properties are more complex](https://www.edgedb.com/docs/edgeql/sdl/props) and include three types: concrete, computable, and abstract. We're most familiar with concrete so let's take a look at that:

```
[ overloaded ] [{required | optional}] [{single | multi}]
  property name
  [ extending base [, ...] ] -> type
  [ "{"
      [ default := expression ; ]
      [ readonly := {true | false} ; ]
      [ annotation-declarations ]
      [ constraint-declarations ]
      ...
    "}" ]
```

You can think of the syntax as a helpful guide to keep your declarations in the right order.

### Dipping into DDL

DDL is something you'll see frequently in the documentation. Up to now, we've only mentioned DDL for functions because it's so easy to just add `CREATE` to make a function whenever you need. 

SDL: `function says_hi() -> str using('hi');`

DDL: `CREATE FUNCTION says_hi() -> str USING('hi')`

And even the capitalization doesn't matter.

But for types, DDL requires a lot more typing, using keywords like `CREATE`, `SET`, `ALTER`, and so on. This could still be worth it if you want to make small changes or quick types though. For example, here is a Cat type that just has a name:

```
type Cat {
  property name -> str;
  property sound := 'meow';
};
```

When you enter `DESCRIBE TYPE Cat as SDL`, it will show something similar:

```
type default::Cat {
  optional single property cat_name -> std::str;
};
```

It's the same declaration but includes some information we didn't need to specify:

- That it's in the module default
- That it's optional, as opposed to required,
- That it's a single property, as opposed to multi

So what does it look like as DDL? `DESCRIBE TYPE Cat` will show us:

```
CREATE TYPE default::Cat {
    CREATE OPTIONAL SINGLE PROPERTY name -> std::str;
};
```

Not as bad as you might have thought! The two general rules for experimenting a bit with DDL are:

- You can get a lot done just by adding `CREATE` to every line, `ALTER` to change things and `DROP` to delete them,
- Using `DESCRIBE TYPE` and `DESCRIBE TYPE AS SDL` are very useful to compare the two. If you do this with a type you created and are familiar with, you'll be able to use it as a stepping stone if you are curious about DDL.

## Getting help

Help is always just a message away. The best way to get help is to start a discussion on [our discussion board](https://github.com/edgedb/edgedb/discussions) on GitHub. You can also [start an issue here](https://github.com/edgedb/edgedb/issues/new/choose) on EdgeDB, or do the same for the Easy EdgeDB book on this very page.

## And now it's time to say goodbye

We hope you enjoyed learning EdgeDB through a story, and are now familiar enough with it to implement it for your own projects. Ironically, if we wrote the book with enough detail to answer all your questions then we might never see you on the forums! If that's the case, then we wish you the best of luck with your projects. Let's finish the book up with a poem from another book, the Lord of the Rings, on the endless possibilities of life.

>The Road goes ever on and on  
>Down from the door where it began.  
>Now far ahead the Road has gone,  
>And I must follow, if I can,  
>Pursuing it with eager feet,  
>Until it joins some larger way  
>Where many paths and errands meet.  
>And whither then? I cannot say.  

See you, or not see you, however things turn out! Thanks again for reading.
