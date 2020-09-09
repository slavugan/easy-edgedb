# Welcome!

Welcome to the tutorial to easily learn EdgeDB in plain English. "Plain English" means using simple phrases and shorter sentences so you can quickly get the information you need. It's ideal if English is your second language, or if you just prefer to get the information quickly.

Nevertheless, simple English doesn't mean boring. We will imagine that we are creating the database for a game that is based on the book Bram Stoker's Dracula. Because it will use the events in the book for the background, we need a database to tie everything together. It will need to show the connections between the characters, locations, dates, and more.


# Chapter 1 - Jonathan Harker heads towards Transylvania

In the beginning of the book, we see the main character Jonathan Harker who a young lawyer who is going to meet a client. The client is a rich man named Count Dracula who lives somewhere in Eastern Europe. Jonathan still doesn't know that Count Dracula is a vampire, so he's enjoying the trip to a new part of Europe. Here is how the book begins, with parts that could be good for a database in bold:

>**3 May**. **Bistritz**.—Left **Munich** at **8:35 P.M.**, on **1st May**, arriving at **Vienna** early next morning; should have arrived at 6:46, but train was an hour late. **Buda-Pesth** seems a wonderful place, from the glimpse which I got of it from the train...

This is already a lot of information, and it helps us start to think about our database schema. EdgeQL uses something called [SDL (schema definition language)](https://edgedb.com/docs/edgeql/sdl/index#ref-eql-sdl) that makes migration easy. So far our schema needs the following:

- Some kind of City or Location type. Cities should have a name and a location, and sometimes a different name or spelling. Bistritz for example is now called Bistrița (it's in Romania), and Buda-Pesth is now written Budapest.
- Some kind of Person type. We need it to have a name, and also a way to track the places that the person visited.
 
To make a type in EdgeQL, just use the keyword `type` followed by the type name, then `{}` curly brackets. Our `Person` type will start out like this:

```
type Person {
}
```

Inside this we insert the properties for our `Person` type. Use `required property` if the type needs it, and just `property` if it is optional. 

```
type Person {
  required property name -> str;
  property places_visited -> array<str>;
}
```

A `str` is just a string, and goes inside either single quotes: `'Jonathan Harker'` or double quotes: `"Jonathan Harker"`. An `array` is a collection type, and our array here is an array of `str`s. We want it to look like this: `["Bistritz", "Vienna", "Buda-Pesth"]`. With that we can easily search later and see which character has visited where.

`places_visited` is not a `required` property because we might start adding people that we don't care about. Maybe one person will be the "innkeeper_in_bistritz" or something, and we won't care about `places_visited` for him.

Now for our City type:

```
type City {
  required property name -> str;
  property modern_name -> str;
}
```

This is similar: all cities have a name in the book, but some don't have a different modern name. Vienna is still Vienna, for example. We are imagining that our game will link the city names to their modern names so we can easily place them on a map.

We haven't created our database yet, though. There are two small steps that we need to do first [after installing EdgeDB](https://edgedb.com/download). First we create a database with the `CREATE DATABASE` keyword and our name for it:

```
CREATE DATABASE dracula;
```

Then we type ```\c dracula``` to connect to it.

Lastly, we we need to do a migration. This will give the database the structure we need to start interacting with it. Migrations are not difficult with EdgeDB:

- First you start them with `START MIGRATION TO {}`
- Inside this you add at least one `module`, so your types can be accessed. A module is a namespace, a place where similar types go together. If you wrote `module default` for example and then `type Person`, the type `Person` would be at `default::Person`. And in EdgeQL, `std::bytes` for example is the type `bytes` in the standard library (std).
- Then you add the types we mentioned above, and type `POPULATE MIGRATION` to add the data.
- Finally, you type `COMMIT MIGRATION` and the migration is done.

Later on we can think about adding time zones and locations for the cities for our imaginary game. But in the meantime, we will add some items to the database using `INSERT`. By the way, keywords in EdgeDB are case insensitive, so `INSERT`, `insert` and `InSeRT` are all the same. But using capital letters is the normal practice for databases so it's best to use that.

We use the `:=` operator to declare, while `=` is used for equality (not `==`).

```
INSERT Person {
    name := 'Jonathan Harker',
    places_visited := ["Bistritz", "Vienna", "Buda-Pesth"],
};

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

As you can see, `str`s are fine with unicode letters like ț. Even emojis are just fine: you could create a `City` called '🤠' if you wanted to.

Every time you `INSERT` an item, EdgeDB gives you a `uuid` back. That's the unique number for the item. It will look like this:

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

Now let's add a property to the query. We'll `SELECT` all `City` types with `modern_name`. Here is our query:

```
SELECT City {
....... modern_name,
....... };
```

You will remember that one of our cities doesn't have a modern name. It still shows up though as an "empty set", because every value in EdgeDB is a set of elements. Here is the result:

```
{Object {modern_name: {}}, Object {modern_name: 'Budapest'}, Object {modern_name: 'Bistrița'}}
```

So there is some object with an empty set for `modern_name`, while the other two have a name. This shows us that EdgeDB doesn't have `null` like in some languages: if nothing is there, it will return an empty set.

# Chapter 2 - at the Golden Krone Hotel

We continue to read the story as we think about the database we need to store the information. The important information is in bold:

>Jonathan Harker has found a hotel in **Bistritz**, called the **Golden Krone Hotel**. He gets a welcome letter there from Dracula, who is waiting in his **castle**. Jonathan Harker will have to take a **horse-driven carriage** to get there tomorrow. We also see that Jonathan Harker is from **London**. The innkeeper at the Golden Krone Hotel seems very afraid of Dracula. He doesn't want Jonathan to leave and says it will be dangerous, but Jonathan doesn't listen.

Right away we see that we could add another property to `City`, and will write this: `property important_places -> array<str>;` It will now look like this:

```
type City {
    required property name -> str;
    property modern_name -> str;
    property important_places -> array<str>;
}
```

So we can add it by using SDL when we do the migration. On top of SDL, EdgeQL also has something called [DDL (data definition language)](https://edgedb.com/docs/edgeql/ddl/index) that is more low level. DDL is good for quick changes, but not as good for large migrations. One reason why is that the order matters in DDL, but not in SDL. That means that if you have a type `City` that is connected to a type `Country`, you need to create `Country` first in DDL. In SDL, the other doesn't matter. 

But here we will look at a quick example of DDL. If we've already started our database, we can quickly change it like this:

```
ALTER TYPE City {
    CREATE PROPERTY important places -> array<str>
};
```

And it will say `OK: ALTER` to tell us that it worked. For most of this course we will keep using SDL, so when we change a type we are changing the migration structure. But you can remember that DDL is also an option for quick changes when you need them.

We now have two types of transport in the book: train, and horse-drawn carriage. This book is based in the late 1800s and our game will let the characters use different types of transport too. Here an `enum` is probably the best choice, because an `enum` is about making one choice between options. Here we see the word `scalar` for the first time: this will be a `scalar type` because it only holds a single value at a time. The other types (`City`, `Person`) are `object types` because they hold any number of values at the same time.

The other keyword we will see for the first time is `extending`. This gives you all the power of the type that you are extending. We will write our `Tranport` type like this:

```
scalar type Transport extending enum<'feet', 'train', 'horse-drawn carriage'>;
```

Did you notice that `scalar type` ends with a semicolon and the other types don't? That's because the other types have a `{}` to make a full expression. But here on a single line we don't have `{}` so we need the semicolon to show that the expression ends here.

This `Transport` type, however, is going to be useful for the characters in our game - not the characters in the book. Only characters in our game will be choosing between one type of transport or another. So maybe it's a good time to turn our `Person` type into something else. What we can do is make `Person` an `abstract type` instead. An `abstract type` is a sort of base type that you can use to build other types. Then we can have two other types called `PC` and `NPC` that draw from it with the keyword `extending`.

So now this part of the schema looks like this:

```
        abstract type Person {
            required property name -> str;
            property places_visited -> array<str>;
        }
        type PC extending Person {
            required property transport -> Transport;
        }
        type NPC extending Person {
        }
```

Now the characters from the book will be `NPC`s (non-player characters), while `PC` is being made with our game in mind. Because `Person` is now an abstract type, we can't use it directly anymore. It will give us this error if we try:

```
error: cannot insert into abstract object type 'default::Person'
  ┌─ query:1:8
  │
1 │ INSERT Person {
  │        ^^^^^^^ error
```

No problem - just change `Person` to `NPC` and it will work.

Let's also experiment with a player character. We'll make one called Emil Sinclair who starts out traveling by horse-drawn carriage.

```
INSERT PC {
     name := 'Emil Sinclair',
     places_visited := ['Vienna', 'Germany'],
     transport := <Transport>'horse-drawn carriage',
};
```

Note that we didn't just write `'horse-drawn carriage'`, because transport is an `enum<str>`, not a `str`. The `<>` angle brackets do casting, meaning to change one type into another. EdgeDB won't try to change one type into another unless you ask it to with casting. That's why this won't give us `true`:

```
SELECT 'feet' IS Transport;
```

We will get an output of `{false}`, because 'feet' is just a `str` and nothing else. But this will work:

```
SELECT <Transport>'feet' IS Transport;
```

Then we get `{true}`.

You can cast more than once at a time if you need to. This example isn't something you will ever do but shows how you can cast over and over again if you want:

```
SELECT <str><int64><str><int32>50 is str; 
```

That also gives us `{true}` because all we did is ask if it is a `str`, which it is. 

Casting works from right to left, with the final cast on the far left. So `<str><int64><str><int32>50` means "50 into an int32 into a string into an int64 into a string". Or you can read it left to right like this: "A string from an int64 from a string from an int32 from the number 50".

