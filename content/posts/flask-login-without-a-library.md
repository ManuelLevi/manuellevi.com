+++
author = "Manuel Levi"
date = 2023-03-07T00:46:59Z
description = ""
draft = true
slug = "flask-login-without-a-library"
title = "Flask Login WITHOUT a library - Minimal tutorial"

+++


The reason why many of us use flask is that it has less black magic than Django. We want a minimal web framework that we can fully understand nad modify just by looking at the code.

However, today, when you search for "Flask Login" and even "Flask Login without a library" all you get is how to use "Flask-login" module, or how to implement login systems with CORS and JWT and some other fancy stuff.

This article lists how you can create a simple login system with flask, and nothing else. No libraries. No extra features. Not even security. A minimal example of how to do it, not following any of the standard good practices.

Enough for testing and debugging.

The following code does not use external libraries, does not validate anything, it doesn't encrypt the password either. It's as simple (and stupid) as it gets, and yet, beautiful.

```python
@app.route("/login", methods=["GET", "POST"])
def login():
    # check if the user is already logged in
    if session.get("user_id"):
        return redirect(url_for("index"))
    # check if the request method is POST
    if request.method == "POST":
        # get the username and password from the form
        username = request.form.get("username")
        password = request.form.get("password")
        # get the user from the database
        user = User.query.filter_by(name=username).first()
        # check if the username exists and the password is correct
        if user and user.password == password:
            # add the user id to the session
            session["user_id"] = user.id
            # redirect to the home page
            return redirect(url_for("index"))
        else:
            # display an error message
            flash("Wrong username or password")
    # display the login form
    return render_template("login.html")

```

That's it. So when you want to create a simple login function, that's basically what you need. Simple.

