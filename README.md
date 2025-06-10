
### **Guided Learning Activity: AI-Assisted Development & Testing — The Todo API Challenge**

**Project Overview:**
Welcome to the forefront of software development! In this activity, your group will not be given a pre-built application. Instead, you will act as a modern development team, using an AI coding assistant to generate a "Todo" list API. Your primary mission is to then act as the human quality gate: you will write a comprehensive test suite to **verify, validate, and improve** the AI's work. This project emphasizes that AI is a powerful tool, but human-driven testing is what ensures quality and reliability.

**Learning Objectives:**
By the end of this activity, you will be able to:
1.  Effectively prompt an AI assistant to generate a functional Django REST Framework application.
2.  Analyze and critically evaluate AI-generated code for correctness, security, and best practices.
3.  **Use testing as the primary tool to verify, validate, and safely refactor AI-generated code.**
4.  Write unit and integration tests for DRF Views and Serializers using `APITestCase`.
5.  Apply a test-driven workflow to fix bugs and add features to an existing, AI-generated codebase.
6.  Write tests for critical security aspects like authentication and authorization.
7.  Set up a Continuous Integration (CI) pipeline with GitHub Actions to automate your verification process.
8.  Collaborate using Git, branches, and Pull Requests to manage your project.

---

### **Student Guide (`README.md` in the repository)**

Welcome to the AI-Assisted DRF Testing Challenge! Your group will use an AI assistant to bootstrap a Todo API, then use your skills to build a robust test suite that guarantees its quality.

#### **Collaboration Workflow**

* All work should be done on branches. Start with a `develop` branch. For each major task, create a feature branch (e.g., `feature/verify-read-operations`).
* Use Pull Requests to merge feature branches into `develop`. Require at least one other group member to review the PR before merging.

#### **Task 0: The Generation Phase - Vibe Coding Your App**

Your first step is to prompt an AI coding assistant (Gemini, GitHub Copilot, ChatGPT, etc.) to generate the Todo application. The quality of your prompt directly impacts the quality of the output.

1.  **Craft Your Prompt:** As a group, design a detailed prompt. Start with this and refine it:

    > "Generate a complete Django REST Framework application for a 'Todo' list.
    >
    > **Requirements:**
    > 1.  Use Django and Django REST Framework.
    > 2.  Create a single Django app named 'todos'.
    > 3.  The `Todo` model in `todos/models.py` must have the following fields: `title` (CharField), `description` (TextField, optional), `completed` (BooleanField, default False), and `owner` (a ForeignKey to the standard Django User model).
    > 4.  Implement user authentication. Use `djangorestframework-simplejwt` for token-based authentication (provide endpoints for getting and refreshing tokens).
    > 5.  In `todos/views.py`, use DRF's generic views (`ListCreateAPIView` and `RetrieveUpdateDestroyAPIView`).
    > 6.  **Security:** The API must be secure.
    >     - Only authenticated users should be able to access any todo endpoints. Use `IsAuthenticated` permission class.
    >     - A user must only be able to see, update, or delete their *own* todos.
    > 7.  In `todos/serializers.py`, create a `ModelSerializer` for the `Todo` model. The owner should be a read-only field.
    > 8.  Set up the necessary URLs in both the project's `urls.py` and the `todos/urls.py` file.
    >
    > Please provide the full contents for all necessary files: `settings.py` (configured for DRF and JWT), `models.py`, `serializers.py`, `views.py`, and both `urls.py` files."

2.  **Generate and Set Up:**
    * Use the AI to generate the code.
    * Create the Django project and app structure in your repository: `python manage.py startproject todo_project .` and `python manage.py startapp todos`.
    * Copy the AI-generated code into the corresponding files.
    * Install dependencies: `pip install -r requirements.txt`. You may need to add `djangorestframework-simplejwt` to the file if it's not already there.
    * Run migrations: `python manage.py migrate`.

#### **Task 1: Human Review and Initial Baseline**

**DO NOT WRITE TESTS YET.** As a group, read every line of code the AI generated. This is a critical code review step.

* **Discuss:** Does the code make sense? Did the AI fulfill all parts of the prompt?
* **Identify Potential Bugs:** Look for common AI mistakes:
    * Did it actually filter the `get_queryset()` method in the views by `request.user`? Or does it just return `Todo.objects.all()`?
    * Did it implement the `perform_create()` method in the `ListCreateAPIView` to correctly assign the `owner`?
    * Are the permissions set correctly?
* **Commit the Baseline:** Make any immediate, obvious fixes. Then, commit this initial AI-generated code to your `develop` branch. This is your starting point.

#### **Task 2: Verify Read Operations & Authorization**

The AI *claims* a user can only see their own todos. Your job is to **prove it** with tests.

1.  Create `tests/test_api.py` and `tests/factories.py` (you can copy the factory code from the original guide).
2.  **Write a failing test (`test_user_can_only_list_own_todos`)**:
    * Create two users (`user_a`, `user_b`) and several todos owned by each.
    * Authenticate as `user_a`.
    * Make a `GET` request to `/api/todos/`.
    * Assert the status is `200 OK`.
    * Assert that the number of todos in the response is equal to the number of todos `user_a` owns.
    * Assert that the IDs of the todos in the response do not include any of `user_b`'s todos.
3.  **Run the Test:** If the AI made the common mistake, **this test will fail**.
4.  **Fix the Bug:** Go to `views.py` and fix the `get_queryset` method to correctly filter by `owner=self.request.user`. Run the test again until it passes. Congratulations, you've used testing to fix an AI-generated security flaw!

#### **Task 3: Verify Create, Update, and Delete Logic**

Continue using tests to verify the core functionality.

1.  **Test Creation (`test_create_todo_assigns_correct_owner`)**: Write a test that `POST`s a new todo and asserts that the created object's `owner` is the authenticated user. Fix the `perform_create` method if the test fails.
2.  **Test Validation (`test_create_todo_requires_title`)**: Write a test that `POST`s data without a `title` and asserts a `400 BAD REQUEST` response.
3.  **Test Authorization on Detail Views (`test_user_cannot_access_others_todo_detail`)**: Write a test authenticating as `user_b` and trying to `GET`, `PUT`, or `DELETE` a todo owned by `user_a`. Assert you get a `404 NOT FOUND`.

#### **Task 4: Automate Your Verification with CI**

Push your `develop` branch to GitHub. Go to the "Actions" tab. The pre-configured `ci.yml` workflow should run your tests automatically. Your goal is to keep this green. This CI pipeline is your team's automated quality checker.

#### **Final Submission and Reflection**

1.  Ensure all your tests are passing in the GitHub Actions runner.
2.  Create a final Pull Request from `develop` to `main`.
3.  In the Pull Request description, `@mention` your instructor and add a **"Project Reflection"** section answering the following:
    * What was the initial prompt you used to generate the application?
    * How accurate was the AI-generated code? What did it get right?
    * What were the most significant bugs or security flaws you found in the AI's code? How critical was testing in discovering these?
    * How did this process change your view on using AI for coding? What are the benefits and risks your team identified?
