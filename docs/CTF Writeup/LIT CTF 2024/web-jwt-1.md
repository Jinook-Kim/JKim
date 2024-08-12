# web/jwt-1

## About
"I just made a website. Since cookies seem to be a thing of the old days, I updated my authentication! With these modern web technologies, I will never have to deal with sessions again. Come try it out at http://litctf.org:31781/ ."

Home Page:
![Home Page](web-jwt-1-asset/home.png)

You can sign up to the website.
![Sign Up](web-jwt-1-asset/signup.png)

However, when you click "GET FLAG", the webpage gives you "Unauthorized".

## Recon
As described in the introduction of the challenge, there is a JWT cookie after signing in.
![cookie](web-jwt-1-asset/cookie.png)

After doing some research, I came across the tool called [JWT_Tool](https://github.com/ticarpi/jwt_tool).
If I put my cookie in, it gives me all the info.
![info](web-jwt-1-asset/jwt_tool.png)

## Exploitation
It seems like I need to change that `admin` value to `True`. You can do that with the `-T` flag:
```bash
python3 jwt_tool.py eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoidGVzdDMzMyIsImFkbWluIjpmYWxzZX0.pexv7afakRnfXaUonh0o0Y96xPDz%2BSOsM453Mn%2F0YD -T
```
![exploit](web-jwt-1-asset/exploit.png)

Then, put the new cookie in the browser and hit "GET FLAG".
![flag](web-jwt-1-asset/flag.png)