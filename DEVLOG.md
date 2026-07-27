# Dev Log
I have not tried this before, but i want to keep notes and track my process. Find what I learned and where I can improve. I want to show transparency and limit my usage of AI for coding and use it more for learning.

## 2026-07-27
Used my scaffolds repo to create the skeleton for my mobile project. I am thinking of starting with backend and databases in this project. It may be abnormal, but I think with most projects I usually get stumped with the frontend and then give up, which has weakened my backend growth and prolonged projects.

I was originally thinking to run the backend with `fastapi` but I recently got stumped in an interview with Amazon because I didn't know one of the popular backend frameworks so I think I'll just stick to `django` for my backend now. It is commonly used in industry, it uses python, and its good to learn something new. Apparently it has built in tools for authentication and session management but I'll have to explore that more as I go.

The `django` project structure is different from what I am used to but I like it more. The project structure uses feature based with a `manage.py` to handle it all. I can use Django's built-in migration system — uv run manage.py makemigrations to generate instructions, and uv run manage.py migrate to apply them — to track and make changes to my database's structure, so it always knows what the database should look like, without me having to write raw SQL myself. I can run the project with just `uv run manage.py runserver`. I'll use this for now before configuring the `start.sh` to start frontend and backend at the same time. I do not have frontend stuff worth talking about yet so I do not care yet.


I installed some packages including:
- `django` = (dull)
- `djangorestframework` = django was meant to render HTML pages but this extends it to build `REST APIs`
- `psycopg2-binary` = translator between `Python` and `PostgreSQL` (which i plan to use)

Apparently I do not need `sqlalchemy` because `django` already has an `ORM` (object-relational mapping that lets you work with databases using objects in programming instead of writing SQL) so that is pretty cool. I'm going to connect this to github and save the changes before I get carried away.

Ok so in making that push, I actually messed up pushing the url and cut off the "t" in git when adding remote. In the future, you can run `git remote set-url origin <url>` to fix it. Verify with `git remote -v` and you should see the same url for fetch and push.

I think my goal for today is to think of the database tables for my project. I don't really have a firm grasp on what things I should add to make my app unique yet besdies AI features so I plan to work on that the next day. For now I will be super basic.
> There is a difference between models and tables. Models are the table that exists in the database.  So when referring to your project, you would call them models (or objects) and when referring to database, you would call them tables. They represent the same concept


Watching this guy's video to see what tool to use and I will use eraser because I can use code for the tables and table relationships which I think is easier:
- https://www.youtube.com/watch?v=lWX5mk2adrg&t=577s

So I gotta make an entity relationship diagram (ERD) for my project. AI recommends I save this in a `docs` folder so I am going to make one and add the image to it. 

So I wondered when something shoud be its own table or be one long field and the signals to figure this out are:
1. Does it repeat? -> seperate table
2. Is it queries/updated at different frequency or by different code?
3. Does it belong to the user or does the user just reference it?
4. Would grouping these fields ever be reused or queried as a unit?
5. Practical "too many fields" test.

I am going to be referencing AI, spotify's plausible database generated database from (https://databasesample.com/database/spotify-database), and wger repo which is a free open source workout tracker (https://github.com/wger-project/wger).

Ok I procrastinated a lot lol I am going to move on to some other projects today. I can come back here later, but at least I have those basics set up.