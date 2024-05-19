# INVEX Trading API Documentation

### Introduction

Welcome to Invex, a leading trading platform offering innovative solutions for market makers and other users. Our goal is to empower traders with advanced tools, robust infrastructure, and unparalleled customer service.

At Invex, our mission is to democratize access to financial markets while maintaining rigorous compliance and regulatory oversight. We strive to deliver exceptional performance, reliability, and scalability for cryptocurrencies.

Our platform caters to a diverse range of clients, including institutional investors, proprietary trading firms, and individual traders seeking to optimize returns.

Here's a brief summary of our core offerings:

- **Fast Order Routing:** Leverage our low-latency architecture to execute orders quickly and efficiently.
- **Competitive Pricing**: Enjoy competitive pricing structures tailored to suit your trading strategy.

Ready to take advantage of Invex's powerful capabilities? Get started today by registering for an account and accessing our extensive resources.

### Authentication & Authorization
To use Invex, you must first obtain API credentials. This process involves providing basic contact information, agreeing to our terms and conditions, and completing identity verification procedures. Once approved, you'll receive an API key pair consisting of a public key,an api key to use in header (**X-API-Key-Invex**) and a secret key.

Note: Your API key serves as a secure identifier, allowing you to sign requests and verify your identity. It's essential to keep your API key confidential; never share it with anyone outside of authorized personnel. If you suspect unauthorized activity or compromise, notify our support team immediately or revoke your api key using your dashboard.

Additionally, Invex implements several security measures to protect against malicious activities:

Encryption Protocols: All communication between servers and client applications utilizes industry-standard TLS v1.2+ encryption.
Rate Limits: To prevent abuse, we impose strict rate limits on certain endpoints.
Replay Attacks Prevention: Our systems detect and block attempts to reuse previously sent messages.
How to sign input data?
for each endpoint with input data, you should sign your input data.
to do so, you need to provide an expire_at field in your query params or request body.
take a look at example in following requests.
But how to sign your data??

here a sample code in python:

```python
import binascii
from cryptography.hazmat.primitives.serialization import load_der_private_key
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives import hashes
def sign_using_private_key():
    private_key = ... # secret key of your api key. 
    message =...#json decoded of user input data,including expire_at
    byte_private_key = binascii.unhexlify(private_key)
    rsa_private_key = load_der_private_key(byte_private_key, password=None)
    signature = rsa_private_key.sign(
        message.encode(),
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    return signature.hex()
if __name__ == '__main__':
    print(sign_using_private_key())
```
