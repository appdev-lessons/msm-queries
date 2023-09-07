# Must See Movies Queries: Intro to ActiveRecord

## Walkthrough video

[Here is a video walkthrough for this lesson](https://share.descript.com/view/x1KIlIQgR9l).

[You can see the Git commits I made during the video here.](https://github.com/demostudent24/msm-queries/commits/main)

## Target

In this project, we're going to build the first version of an app that we've already database architected together: Must See Movies.

Recall that the database design we came up with looked something like this (a few columns are left out of this diagram, but this is the gist of it):

![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1690674740/msm-tables_wbuhxm.png)

Now, it's time to actually create a database such as this, populate it with records, and build a web app on top of it.

Here is our target: [msm-queries.matchthetarget.com](https://msm-queries.matchthetarget.com/)

## Refer to the Contact Book lesson

Keep [the Contact Book lesson open in a tab](https://learn.firstdraft.com/lessons/130-contact-book-first-database#retrieving-existing-records) so that you can refer frequently to the ActiveRecord examples within.

## Getting started

This project includes automated tests, so click on this button to get started:

LTI{Load MSM Queries assignment}(https://grades.firstdraft.com/launch)[S9ymPy6WCsn18gLbByVbZQ7k]{vfdtzJb5bLYqYwuqgeRKpc5d}(10)[MSM Queries Project]

Then, fork the repository and create your codespace as usual.

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

## Optional tasks

Here are some optional tasks for you try and tackle:

### Finding a movie by title

How many years ago was "Casablanca" released?

 - Use the [`.where` method](https://learn.firstdraft.com/lessons/130-contact-book-first-database#where). It is everything.
 - Remember that [`.where` always returns a collection, not a single row](https://learn.firstdraft.com/lessons/130-contact-book-first-database#where-always-returns-a-relation-never-a-single-row).
 - Calculate the value dynamically (using e.g. `Time.now.year`), so that the number is always up to date.

### Other queries

 - How many movies in our table are from [before](https://learn.firstdraft.com/lessons/130-contact-book-first-database#less-than-or-greater-than) the year 2000?
    - Displays the titles and years of the films.
 - How many directors in our table are less than 55 years old?
    - Display their names and dates of birth.
 - How many films in our table were directed by Francis Ford Coppola?
    - Display the titles and years of the films.
 - How many films did Morgan Freeman appear in?
    - Display the titles and years of the films.

---

- Approximately how long (in minutes) did this lesson take you to complete?
{: .free_text_number #time_taken title="Time taken" points="1" answer="any" }

---
