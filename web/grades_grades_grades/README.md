# DUCTF 2023 - grades_grades_grades

## Challenge Information

- **Category**: [Category]
- **Points**: [Points]
- **Solves**: [Number of Solves]
- **Description**: [Brief challenge description]
- **Challenge Files**: [List any attached files or resources]

## TL;DR

Sign up and see those grades :D! How well did you do this year's subject? 
Author: donfran

## Approach

In this challenge, you are given access to a website. The website seems to only have a signup option at first glance. 
Intially, I go through the motions as an average user would to see what is available to us. The register page may be susceptible to either SQL or NoSQL however, looking at the provided src code, the auth.py file caught my attention.

This file was the handler for sessions using JWT (JSON Web Tokens). There are a collection of a vulnerabilities for JWT. The other important step I noticed was in routes.py; the flag was present under /grades_flag. 

```
@api.route('/grades_flag', methods=('GET',))
@requires_teacher
def flag():
    return render_template('flag.html', flag="FAKE{real_flag_is_on_the_server}", is_auth=True, is_teacher_role=True)
```

The problem here is, our session had set our user access level to student but to access the flag, we need the teacher role.
```
is_teacher_role=True
```
This being my first time working with JWT, it was great introduction to it. 
The main vulnerability I noticed is that the code does not use the session.validate() function but it didn't really help our situation. 

1. The key was randomly generated, I would not imagine we were expected to brute force the key
```
SECRET_KEY = secrets.token_hex(32)
```
2. The token creation did not have any validation on the data coming from the request. This was the vulnerability.

The POST request for registration was passing these variables.
```
stu_num=testing&stu_email=testing%40te&password=testing
```
Knowing this and that the data from the request not being sanatized. 
```
def create_token(data):
    token = jwt.encode(data, SECRET_KEY, algorithm='HS256')
    return token

``` 
In combination with this website tool, https://jwt.io/.
I wable to see what data the token stored. They were indentical to the variables in the POST request. 
I was able to set the variable for is_teacher=True in the request. 
```
stu_num=testing&stu_email=testing%40te&password=testing&is_teacher=True
```

FLAG: DUCTF{Y0u_Kn0W_M4Ss_A5s1GnM3Nt_c890ne89c3}


### Step 1: [Task Name]

[Explain the first step you took to solve the challenge, including any tools or techniques you used.]

### Step 2: [Task Name]

[Continue describing the steps you followed to solve the challenge. Be sure to include any interesting findings or observations.]

### Step N: [Task Name]

[Repeat the above pattern for each significant step or task you completed during the challenge.]

## Solution

[Provide the solution to the challenge. This could include code snippets, commands, or explanations of the final steps you took to solve the challenge.]

## Flag

[Present the flag you obtained as proof of solving the challenge.]

## Conclusion

[Summarize your overall experience with the challenge, any lessons learned, or interesting aspects of the challenge.]

## Additional Resources

[Include any external links or resources that were helpful in solving the challenge.]

## Acknowledgments

[Mention anyone you want to thank or credit for their assistance or resources during the CTF.]

---

**Note**: Customize this template b