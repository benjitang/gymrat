# Dev Log
I have not tried this before, but i want to keep notes and track my process. Find what I learned and where I can improve. I want to show transparency and limit my usage of AI for coding and use it more for learning.

## Week 1
### 2026-07-27
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

### 2026-07-28

Most important for today, I want to consider what features will go into my app. What makes my app stand out against other popular apps. To do that, I will just combine the best of other apps and also see common complaints. This will just be me throwing stuff I see in bullet form and I can organize later. Just note the important ideas are there.

> Popular apps include Hevy, Strong, Fitbod, and Boostcamp. I personally use RepCount so consider that too.

- Most importantly a polished, modern UI. Take inspiration from Hevy.
- Easy to use, fast logging, extensive exercise libary, progress charts.
- I will not be copying  apple watch support and social features for now. Maybe in the future. It is important to note that according to AI, people complain about too much focus on the social features.
- Common complaints of other apps are a limited free version. Make sure to make this app have generous free version.
- Automatic progression from boostcamp. Make app intelligently recommend the next week's weight. In addition, adjusts rest time based on exercsie type/intensity. Oh in addition how about warm up set calculator. 
  - In addition to this, i need to add the ability to add warm up sets, drop sets, and super sets. This may change in the future, but I want drop sets and warm ups to just be an indicator, I dont think the rest of the weight for a drop set really matters. This may also change, but I want users to only be able to super set and not tri set or any bullshit like that. 
- AI "coach" from Fitbod that notifies the user of stats, ex: "your squat has stalled for three weeks". Also long-term insights that can positively motivate the user. Ex: "Your bench has increase 32 lb over the last 9 months".5
- Better analystics that include strength over time, estimated 1RM trends, muscle volume per week, recovery score, fatigue, consistency streaks, average workout duration, strongest lifts ever, weakest muscle groups, training balance (push, pull, legs). 
- Video analysis. I know that a lot of hackathon submissions seem to do a tracking of person's lift so I will look into that. Helps to prevent injury to track someones form or whatever. 
- Desktop version. Makes it easier for people to visualize their workouts and connect to it on a bigger screen.
- Ability to export on a csv. This just a littl enice thing.
- Gamify the app, how about progress bars for muscle groups. I have to look deep into this though. I'm not sure how to handle if someone isnt working out for a long time and how I would assign points in general.
- Program templates from AI. generate 4 day ppl program or something. Lightweight version of what Boostcamp and fitbod do. 
- Injury flag. If the user is injured, then the app should consider that instead of ridiculing the user for not having the same progress as before.
- I think every workout should have versions. Up to 3. Sometimes cables and weights weighth different in different locations. Important to consider someone can have different results working out at home, at the gym, or another gym. Therea re some design tradeoffs to consider when doing this with analytics though. Perhaps analytics score per version? The gamify point system should stay the same. I think for my app to work I have to trust that the user is giving their all. In fact maybe I should give tips like be 2-3 rpm and do like 10-12 reps. Wait no I will make it variant by physical machine because then it becomes a problem If I am using a cable that weights differently in the same location. I think the limit should be 3 and then upper limits when paid.
- For unilateral exercises, weight and rep count for left and right.
- Log locally, sync when connectivity returns. I will see what other apps do. I understand sometimes like a basement has poor connection and dont want that user to lose their progress. 

Ok I think thats pretty much the big parts of it. I'm going to AI an easier to read md for this in the project called [FEATURES.md](./FEATURES.md).

Based on these features, I'm going to have AI spit out a working database to fit all these based on best practices. I will have to pick at it a little to make sure it fits all my needs but at least the most useful tables and fields will be there. I will save a screenshot and make an md file for how the database works for my features. In the future, if something doesn't work how I want I can just edit those fields. I think after today I can start coding a little bit. 

Nice I have a saved image of [ERD.png](./ERD.png) and I have [SCHEMA.md](./SCHEMA.md) to explain what each table does and can do. I wanted to export my ERD as a draw.io but eraser made it so when i export, it doesnt save the tables, so I dont know what that is about. If I want to look at it, I guess I can always just look at eraser anyway. I'm going to add the coded up schema as a txt file ([ERD.txt](./ERD.txt)) so I can copy and paste if I made a change that broke something. Anytway I think im good to push these changes and work on coding up these tables with `django` tomorrow. 

### 2026-07-29
I'm going to be learning a bit of stuff behind `django` first so I know where I am jumping into. 

It would seem to create feature I need to make a command like `python manage.py startapp users` to add the feature folder into my root directory. I then have to register the app in my `config/settings.py` within "INSTALLED APPS" to make my django project recognize the app. The users will save users data. I'm going to seperate users and authentication so I'm going to make an authentication app too for logins, registering, and other stuff. I'm going to focus on authentication for this week. I note that `django` already provides `Users` app and `Authentication`, but I want control I should be building these apps on top of the preexisting ones because these have been  tested for years for real security. I just want control and maybe try slight modifications.

Here are what the files for each app does:
- migrations/ = Stores database schema changes. Every time you modify a model and run makemigrations, Django creates a migration file here that describes how to update the database.
- migrations/__init__.py = Marks the migrations folder as a Python package so Django can discover and execute migrations.
- admin.py = Registers models with Django's admin site and customizes how they appear to administrators.
- apps.py = Defines configuration for the app, such as its name and any startup behavior.
- models.py = Defines your database models (tables) and the relationships between them.
- tests.py = Contains automated tests that verify your app behaves correctly.
- views.py = Contains the code that handles incoming requests, performs business logic (or calls services), and returns a response.

Apparently later it is good to optionally add these files, so with this information what you will:
- serializers.py = Converts models to and from JSON (commonly used with Django REST Framework).
- urls.py = Defines the URL routes for this app.
- services.py = Contains business logic that doesn't belong in models or views (for example, creating a workout plan or awarding XP).
- permissions.py = Defines who is allowed to access specific endpoints.
signals.py = Runs code automatically when certain events occur (for example, creating a profile whenever a new user is created).
- validators.py = Stores reusable validation logic for fields or input data.
- constants.py = Stores constant values used throughout the app.
- exceptions.py = Defines custom exception classes for your application.
- tasks.py = Contains background jobs (commonly used with task queues like Celery).
- utils.py = Stores small helper functions that don't fit elsewhere.

I note that each app has its own test file so I can remove the tests folder at the root directory.

I want to make a connection with supabase so I'm going to do that. Going to add an .env file and `uv add django-environ` which is the popular way to react .env files in django. Then configure the `settings.py` to read these variables. 

Next I start a new project in `Supabase`. I am only using this for storage so I turn off all security settings since my `django` will ahndle everyting. The architecture I am going for is that frontend connects to my `django` and only `django` connects to supabase for storage of data.

Ok I am finally connected. Just had to configure my .envs for db and then used them to connect in  `settings.json` Databases while also changing the engine from sqlite to postgresql. I had to change supabase connection method to pooler which is IPv4 available. Seems my machine is not iPv6 connection which is what the direct connection protocol needs. I tested connection worked with `python manage.py migrate`. I'm going to just push all my changes. 

I'm going to make another change. I want to add badge rewards to the ERD. The idea is that the levels will show the person's experience while the badges will tell a story of the person's progression. I think leveling up alone won't mean much and won't have them trying to reach anything and I don't really plan on giving rewards so this should be fine. I'm going to push these changes as well. 