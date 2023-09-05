# MSM Queries

## Target

In this project, we're going to build the first version of an app that we've already database architected together: Must See Movies.

Recall that the database design we came up with looked like this:

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1690674740/msm-tables_wbuhxm.png)

Now, it's time to actually create a database such as this, populate it with records, and build a web app on top of it.

[Here is our target.](https://msm-queries.matchthetarget.com/)

## Refer to the Contact Book lesson

Keep [the Contact Book lesson open in a tab](https://learn.firstdraft.com/lessons/130#retrieving-existing-records) so that you can refer frequently to the ActiveRecord examples within.

## Setup

- `bin/dev` to start your live app preview.
- In the live app preview, notice that there's already a root route and RCAV defined. That's all that there is in this app, as far as starter code.
- In another tab, navigate to `/rails/db`. Verify that there are currently no tables in this app's database.

## Create your tables and models

In the Contact Book project, we created our `contacts` table with a raw SQL command in `psql`:

```sql
CREATE TABLE contacts (
  id SERIAL PRIMARY KEY,
  first_name TEXT,
  last_name TEXT,
  date_of_birth DATE,
  street_address_1 TEXT,
  street_address_2 TEXT,
  city TEXT,
  state TEXT,
  zip TEXT,
  phone TEXT,
  notes TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

Moving forward, we're not going to use raw SQL anymore. Instead, we'll use another feature of ActiveRecord: **migrations**.

As we saw in the Contact Book project, _models_ are Ruby classes that generate the SQL we need to _CRUD rows in tables_.

_Migrations_ are Ruby classes that generate the SQL we need to _create the tables in the first place_.

Rails can automatically write most of our migrations for us, if we tell it:

- The name of the table we want.
- The columns we want in that table, along with their datatypes.

We can give Rails this information using a new tool: a Rails **generator**.

In order to avoid typos, it's okay to copy-paste this command to the bash prompt:

```
rails generate model movie title:string year:integer duration:integer description:text image:string director_id:integer
```

When you execute this command, you'll see some output indicating that two files were created.

- One is a migration file located in the `db/migrations` folder.
- One is a model file located in the `app/models` folder.

Take a peek at both files. Then, run the migration file with the following command:

```
rake db:migrate
```

You will see some output indicating that the `movies` table has been created. Verify in `/rails/db`.

Then, copy-paste the commands to create the rest of our tables:

```
rails generate model director name:string dob:date bio:text image:string
```

```
rails generate model actor name:string dob:date bio:text image:string
```

```
rails generate model character name:string actor_id:integer movie_id:integer
```

To execute the new migrations, run `rake db:migrate` again. The `rake db:migrate` command looks in the `db/migrations` folder, looks for any migrations that haven't yet been executed, and executes them. So we don't need to worry about the movies table being duplicated — the `rake db:migrate` command is smart, and knows that it already ran that migration.

Verify in `/rails/db` that all four tables have been created.

Since the model files to interact with these tables have also been generated, we're ready to go! We can start adding sample data.

To make your life easier, I already scraped [the iMDB Top 250](https://www.imdb.com/chart/top/) list and wrote a sample data rake task. You can simply run it with:

```
rake sample_data
```

In `/rails/db`, you should now see that all four tables have been populated with data.

Now, let's use our ActiveRecord querying skills, along with our RCAV skills, to start displaying the information in our database on our dynamically generated web pages.

---

[Here is a video for this lesson](https://share.descript.com/view/wJx6iADlVfp). **You should watch the video _and_ pause it frequently to read the notes below.** Work on your own project in parallel with the video and lesson.

---

## Getting started

In this project, we'll build the first version of Must See Movies, which is a simplified version of [iMDB's Top 250 page](https://www.imdb.com/chart/top/).

Here is your target: [msm-queries.matchthetarget.com](https://msm-queries.matchthetarget.com/)

This project includes automated tests, so click on this button to get started:

LTI{Load MSM Queries assignment}(https://grades.firstdraft.com/launch)[S9ymPy6WCsn18gLbByVbZQ7k]{vfdtzJb5bLYqYwuqgeRKpc5d}(10)[MSM Queries Project]

Then, fork the repository and create your codespace as usual.

## Objective



In this project, we'll finally use our first database tables! Mostly, we'll practice using `.where` and other **ActiveRecord** methods. `ActiveRecord` is the name of the class that Rails provides from which we inherit lots of database-related goodies. (There is [more on `ActiveRecord` in the next reading](https://learn.firstdraft.com/lessons/127), but wait until you finish this project to visit that.)

We're going to practice in the context of our familiar movie-related domain — Directors, Movies, Characters, and Actors. As you're watching the video, you'll probably (hopefully) have lots of questions — **write them down** for us to discuss the next time we're together!

## Two new visual development URLs

We have two new visual development tools that we can visit in our live app preview:

 - `/rails/info`: shows all visitable routes in the applications
 - `/rails/db`: shows a visual interface for the database

Start your server with `bin/dev` and open the live app preview in the Ports tab.

## Our database

In the live preview, we already have a homepage `/` with the links for directors, movies, etc., but if we try any of the links, then we get a "no route matches" error. 

Navigate to `/rails/db` in the browser. This route was added by the `gem "rails_db"` line in the `Gemfile`. This is not included in Rails by default, it's rather a third party gem from the community. 

Rails includes a development database out-of-the-box called *SQLite* (often pronounced as *Sequel Light*). `/rails/db` gives you a visual way of looking at your database. 

If you visit `/rails/db`, you'll see that in this project, I've already created our database and added four tables: the familiar directors, movies, characters, and actors. (In future projects, you'll learn how to add your own tables with whatever columns you want; it's just one bash prompt command per table.)

### SQL

At the `/rails/db` URL, if you click on `directors`, you will be brought to the URL `/rails/db/tables/directors/data`, and you will see a blank table with columns *Name*, *Dob*, *Bio*, *Image*, *Created at*, and *Updated at*.

Normally, to add some records into a relational database, we'd have to learn Structured Query Language (SQL). You can click on the "SQL Editor" tab at the top of the `rail/db/tables/directors/data` interface to bring up a terminal for entering SQL commands.

Here are some examples of SQL, if you want to give it a try in that SQL Editor.

Adding a director:

```sql
INSERT INTO "directors" ("name", "dob", "bio", "image", "created_at", "updated_at")
VALUES ("Greta Gerwig", "1983-08-04", "Greta Celeste Gerwig /ˈɡɜːrwɪɡ/; born August 4, 1983) is an American actress and filmmaker. She first garnered attention after working on and appearing in several mumblecore films. Between 2006 and 2009, she appeared in a number of films by Joe Swanberg, some of which she co-wrote or co-directed, including Hannah Takes the Stairs 2007) and Nights and Weekends 2008).", "https://upload.wikimedia.org/wikipedia/commons/thumb/1/1c/Greta_Gerwig_Berlinale_2018.jpg/330px-Greta_Gerwig_Berlinale_2018.jpg", "2020-05-19 17:47:04.103354", "2020-05-19 17:47:04.103354")
```

Adding a movie:

```sql
INSERT INTO "movies" ("title", "year", "duration", "description", "image", "director_id", "created_at", "updated_at")
VALUES ("Little Women", 2019, 135, "Jo March reflects back and forth on her life, telling the beloved story of the March sisters - four young women, each determined to live life on her own terms.", "https://upload.wikimedia.org/wikipedia/en/9/9d/Little_Women_%282019_film%29.jpeg", 35, "2020-05-19 17:31:22.333798", "2020-05-19 17:31:22.333798")
```

Go ahead and paste these in exactly as-is if you'd like to add a row to each table.

Relational databases are extremely powerful, and SQL is a very sharp tool. But, not a lot of fun to type out by hand.

Wouldn't it be nicer to write Ruby?

## Add Ruby classes

Let's create some Ruby classes that will help us interact with our database. In the `app/models` folder, create the following four files:

```ruby
# app/models/actor.rb
class Actor < ApplicationRecord
end
```

```ruby
# app/models/character.rb
class Character < ApplicationRecord
end
```

```ruby
# app/models/director.rb
class Director < ApplicationRecord
end
```

```ruby
# app/models/movie.rb
class Movie < ApplicationRecord
end
```

Now, let's try these Ruby classes out. We could create a rake task, but a quicker way to run one-line Ruby programs is to use the `rails console`.

## The `rails console`

Run:

```
rails console
```

at a bash prompt, and you'll get a REPL where you can run Ruby interactively. (A shortcut for that is just running `rails c`.)

This will change the prompt from:

```
%
```

to

```
[1] pry(main)>
```

The former is the **bash prompt** and the latter is the **rails console**. _It's important to always know which one you are in._

`rails console` is an interactive way to try out Ruby with immediate feedback. It's similar to `irb`, but it also gives us access to our database tables.

If you want to get out of rails console and back to the bash prompt, which is like quitting an app to get back to the desktop of your computer, then type `exit` — or just open a new bash prompt with the `+` icon.

### Important notes about `rails console`

 1. Sometimes when the output of a Ruby expression is very long, `rails console` is going to paginate it for you. You will have a `:` prompt when this is true, and you can hit <kbd>return</kbd> to scroll through line by line, or <kbd>space</kbd> to scroll through page by page.

    When you reach the end of the output, you'll see `(END)`.

    **To get back to the regular bash prompt so that you can enter your next command, just hit <kbd>Q</kbd> at any time.**

 2. If you are in `rails console` and then make a change to a model (for example, you define a new method or fix a syntax error), then, annoyingly, **you have to `exit` and then start a new `rails console`** to pick up the new logic. Or, you can use the `reload!` method.

## Exploring the tables

Open a bash prompt, launch a `rails console` session, and then try the following:

```ruby
Director.count
Movie.count
Character.count
Actor.count
```

You'll see that the `.count` method already works, even though we didn't define it; but right now there are no rows in any of the tables. You can see what columns are in each table by typing just the class name into `rails console`, e.g.

```
[2] pry(main)> Character
=> Character(id: integer, movie_id: integer, actor_id: integer, name: string, created_at: datetime, updated_at: datetime)
```

## Interacting with our database: `ActiveRecord`

`ActiveRecord` comprises a set of classes within Ruby on Rails that help us interact with our database tables, and is one of the most powerful reasons to choose Rails.

Assuming that we have a database and a table called `directors` in it, we would normally have to write SQL to interact with it.

Instead, we can define a class, e.g. `Director`, and [inherit](https://learn.firstdraft.com/lessons/78#inheritance) from `ApplicationRecord`:

```ruby
class Director < ApplicationRecord
end
```

Then _boom_ — our class `Director` has now inherited a _tremendous_ number of powerful methods to interact with the `directors` table, most of them related to **C**reating, **R**eading, **U**pdating, and **D**eleting records.

<aside markdown="1">
Actually, these methods mostly come from our grandparent class `ActiveRecord::Base` rather than our immediate parent class `ApplicationRecord`. We'll go into these details later.
</aside>

We refer to these database-related classes as **models**, and we place them in the `app/models` folder within our Rails app. These classes talk to the database for us, contain most of our business logic, and are, in many ways, the heart of our applications.

## Adding some database entries

Okay, now that we have `ActiveRecord` Ruby classes to translate for us, we can generate the same SQL as above (better actually) by just using familiar Ruby methods. Let's create some records.

In `rails console`, try the following:

```ruby
Director.all
```

You'll see that we get back an empty array. Right now, we have no rows in our table. We can confirm this with `Director.all.count`. We do `Director.all.count` so often that Rails lets us take a shortcut — we can just do `Director.count` directly.

A crucial thing to remember: when you are talking to the **whole table**, you are referencing the _class_ `Director`, so **use a capital letter**. If you did `director.count`, what error message would you expect? Try it and see.

Like any Ruby class we instantiate a new, blank object with the `.new` method:

<aside markdown="1">
Remember Array, Hash, or even String, before we got to their [literal](https://learn.firstdraft.com/lessons/73#array-literals) [shorthand](https://learn.firstdraft.com/lessons/77#hash-literals) [syntaxes](https://learn.firstdraft.com/lessons/69#string-literals)? We always started with `.new`, and then built up from scratch.
</aside>

```ruby
d = Director.new
```

As usual we store the new object in a variable so that we can continue to work with it; in this case, we named the variable `d`. This object has attribute setter methods for every column in the table:

```ruby
d.name = "Greta Gerwig"
d.dob = "August 4, 1983"
```

We didn't have to declare the `attr_accessor`s like we did for pure Ruby classes — by inheriting from `ApplicationRecord`, our `Director` class gains superpowers. It connects to the database, figures out what columns are in the "contacts" table, and automatically defines methods to access each column that exists.

So far, this is just like a pure Ruby class with `attr_accessor`s. But, crucially, we have the `.save` method now. Try it:

```ruby
d.save
```

And voila! `ActiveRecord` generates a fully-formed SQL statement to transact with the database.

Now you can just type `d` and it should show you that `d` has been inserted into the database **and it has been assigned an ID number**.

You can check out your newly saved director directly in the database (rather than through our temporary variable `d`) with:

```ruby
Director.all.at(-1)
```

or, for short,

```ruby
Director.last
```

Similarly, we can add a new movie (this assumes it is directed by someone who was assigned the ID number `42` in the directors table):

```ruby
m = Movie.new
m.title = "Little Women"
m.year = 2019
m.duration = 135
m.director_id = 42
m.save
```

Add a couple more directors and movies, an actor and a character, to get some practice instantiating objects, assigning values to their attributes, and saving them.

## Hydrate with sample data

We could enter a bunch of movies — perhaps even [the entire IMDB Top 250](https://www.imdb.com/chart/top) — manually in `rails console` this way; adding directors and actors first, then adding movies, and finally adding characters to join movies and actors.

Go ahead and add the IMDB Top 250 by hand with `.new`, `.save`, etc..... just kidding! That would take forever. In the real world, _someone_ would initially have to add all of our data, whether it's us, or our employees, or our users (through forms in their browser, obviously, not through `rails console`).

But, to make life easy for developers working on this app, I've included a program that will populate your tables for you quickly. I named the program `sample_data`, and you can run it from the bash prompt with:

```
rake sample_data
```

We'll talk more about how to write these programs in a later lesson, but they are just Ruby scripts like the ones you've written before. In this case, the Ruby script automates what you've been doing in `rails console` — using `Director`, `Movie`, `Character`, and `Actor` to CRUD records.

When you run `rake sample_data`, you should see output similar to this:

```bash
There are 34 directors in the database
There are 50 movies in the database
There are 652 actors in the database
There are 722 characters in the database
```

You can verify this yourself by `.count`ing each table in `rails console`.

---

**At this point in the lesson, follow along with the video to build out your app. There are collected notes in [the `ActiveRecord` reading](https://learn.firstdraft.com/lessons/127), but you don't need to read through it all now.**

---

## Optional tasks

Here are some optional tasks for you try and tackle. You should [visit the `ActiveRecord` lesson](https://learn.firstdraft.com/lessons/127), read through it, and keep it handy while you try and accomplish these.

### Finding a movie by title

How many years ago was "Casablanca" released?

 - Use the [`.where` method](https://learn.firstdraft.com/lessons/127#where). It is everything.
 - Remember that [`.where` always returns a collection, not a single row](https://learn.firstdraft.com/lessons/127#where-always-returns-a-collection-not-a-single-row).
 - Calculate the value dynamically (using e.g. `Time.now.year`), so that the number is always up to date.

### Other queries

 - How many movies in our table are from [before](https://learn.firstdraft.com/lessons/127#less-than-or-greater-than) the year 2000?
    - Displays the titles and years of the films.
 - How many directors in our table are less than 55 years old?
    - Display their names and dates of birth.
 - How many films in our table were directed by Francis Ford Coppola?
    - Display the titles and years of the films.
 - How many films did Morgan Freeman appear in?
    - Display the titles and years of the films.
