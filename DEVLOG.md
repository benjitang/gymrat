# Dev Log
I have not tried this before, but i want to keep notes and track my process. Find what I learned and where I can improve. I want to show transparency and limit my usage of AI for coding and use it more for learning.

## 2026-07-27
Used scaffolds to create this mobile project. I am thinking of starting with backend and databases in this project. It may be abnormal, but I think with most projects I usually get stumped with the frontend and then give up, which has weakened my backend growth and prolonged projects.

I was originally thinking to run the backend with `fastapi` but I recently got stumped in an interview with Amazon because I didn't know one of the popular backend frameworks so I think I'll just stick to `django` for my backend now. It is commonly used in industry, it uses python, and its good to learn something new. Apparently it has built in tools for authentication and session management but I'll have to explore that more as I go.

The `django` project structure is different from what I am used to but I like it more. The project structure uses feature based with a `manage.py` to handle it all. I can use Django's built-in migration system — uv run manage.py makemigrations to generate instructions, and uv run manage.py migrate to apply them — to track and make changes to my database's structure, so it always knows what the database should look like, without me having to write raw SQL myself. I can run the project with just `uv run manage.py runserver`. I'll use this for now before configuring the `start.sh` to start frontend and backend at the same time. I do not have frontend stuff worth talking about yet so I do not care yet.


I installed some packages including:
- `django` = (dull)
- `djangorestframework` = django was meant to render HTML pages but this extends it to build `REST APIs`
- `psycopg2-binary` = translator between `Python` and `PostgreSQL` (which i plan to use)

Apparently I do not need `sqlalchemy` because `django` already has an `ORM` (object-relational mapping that lets you work with databases using objects in programming instead of writing SQL) so that is pretty cool. I'm going to connect this to github and save the changes before I get carried away.