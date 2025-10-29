# Lab: Authorizing Requests ✅ COMPLETED

## Scenario

In this lab, we'll continue working on the blog site, and add some features that
only logged in users have access to.

## 🎉 Implementation Status

**✅ COMPLETED**: All authorization features have been successfully implemented and tested.

## Tools & Resources

- [GitHub Repo](https://github.com/learn-co-curriculum/flask-authorizing-requests-lab)
- [What is Authentication? - auth0](https://auth0.com/intro-to-iam/what-is-authentication)
- [API - Flask: class flask.session](https://flask.palletsprojects.com/en/2.2.x/api/#flask.session)

## Set Up

There is some starter code in place for a Flask API backend and a React frontend.
To get set up, run:

```bash
pipenv install; pipenv shell
npm install --prefix client
cd server
flask db upgrade
python seed.py
```

You can work on this lab by running the tests with `pytest -x`. It will also be
helpful to see what's happening during the request/response cycle by running the
app in the browser. You can run the Flask server with:

```bash
python app.py
```

And you can run React in another terminal from the root project directory with:

```bash
npm start --prefix client
```

You don't have to make any changes to the React code to get this lab working.

## Instructions

### Task 1: Define the Problem

Now that we've got the basic login feature working, we need to reward our logged
in users with some bonus content that only users who have logged in will be able
to access.

### Task 2: Determine the Design

We added a new attribute to our articles, `is_member_only`, to reflect whether
the article should only be available to authorized users of the site. We also
created two new views: `MemberOnlyIndex` and `MemberOnlyArticle`.

Your goal is to add the following functionality to the new views:

- If a user is not signed in, the `get()` methods in each view should return a
  status code of 401 unauthorized, along with an error message.
- If the user is signed in, the `get()` methods in each view should return the
  JSON data for the members-only articles and the members-only article by ID, respectively.

### Task 3: Develop, Test, and Refine the Code

#### Step 1: Check Current User for Authorization

Use the `session` to find if the user is logged in or not.

#### Step 2: Update Logic and Response if User is Authorized

If a user is not signed in, the `get()` methods in each view should return a
status code of 401 unauthorized, along with an error message.

#### Step 3: Update Logic and Response if User is not Authorized

If a user is not signed in, the `get()` methods in each view should return a
status code of 401 unauthorized, along with an error message.

#### Step 4: Test and Refine the Code

Run the test suite:

```bash
pytest
```

If any tests aren't passing, refine your code using each error message.

View the app in browser and test login, logout, and session persistence. Refine code if needed.

Feel free to also take a look at how the frontend logic is set up to use these endpoints.

#### Step 5: Commit and Push Git History

* Commit and push your code:

```bash
git add .
git commit -m "final solution"
git push
```

* If you created a separate feature branch, remember to open a PR on main and merge.

### Task 4: Document and Maintain
Best Practice documentation steps:
* Add comments to the code to explain purpose and logic, clarifying intent and functionality of your code to other developers.
* Update README text to reflect the functionality of the application following https://makeareadme.com. 
  * Add screenshot of completed work included in Markdown in README.
* Delete any stale branches on GitHub
* Remove unnecessary/commented out code
* If needed, update git ignore to remove sensitive data

## 🚀 Solution Summary

### Features Implemented

**1. MemberOnlyIndex Resource (`/members_only_articles`)**
- ✅ Returns 401 Unauthorized if user not logged in
- ✅ Returns only member-only articles for authenticated users
- ✅ Uses Flask session for authentication checking

**2. MemberOnlyArticle Resource (`/members_only_articles/<int:id>`)**
- ✅ Returns 401 Unauthorized if user not logged in
- ✅ Returns specific article by ID for authenticated users
- ✅ Includes 404 error handling for non-existent articles

### Key Implementation Details

```python
class MemberOnlyIndex(Resource):
    def get(self):
        # Check if user is logged in
        if not session.get('user_id'):
            return {'message': 'Unauthorized'}, 401
        
        # Return only member-only articles
        member_articles = Article.query.filter(Article.is_member_only == True).all()
        articles_json = [ArticleSchema().dump(article) for article in member_articles]
        return make_response(articles_json, 200)

class MemberOnlyArticle(Resource):
    def get(self, id):
        # Check if user is logged in
        if not session.get('user_id'):
            return {'message': 'Unauthorized'}, 401
        
        # Find and return the specific article
        article = Article.query.filter(Article.id == id).first()
        if not article:
            return {'message': 'Article not found'}, 404
        
        article_json = ArticleSchema().dump(article)
        return make_response(article_json, 200)
```

### Test Results
All tests passing ✅:
- `test_can_only_access_member_only_while_logged_in`
- `test_member_only_articles_shows_member_only_articles`  
- `test_can_only_access_member_only_article_while_logged_in`

### API Endpoints

| Endpoint | Method | Authentication Required | Description |
|----------|--------|------------------------|-------------|
| `/members_only_articles` | GET | ✅ Yes | Returns all member-only articles |
| `/members_only_articles/<int:id>` | GET | ✅ Yes | Returns specific member-only article |

## Important Submission Note

Before you submit your solution, you need to save your progress with git.

1. Add your changes to the staging area by executing `git add .`.
2. Create a commit by executing `git commit -m "Your commit message"`.
3. Push your commits to GitHub by executing `git push origin main`.

CodeGrade will grade your lab using the same tests as are provided in the `testing/` directory.