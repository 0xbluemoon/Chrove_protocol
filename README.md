# Chrove_protocol
This is a new service model support anonymously pay and autonomous service delivery. Customer and vendor are anonymous to each each other. Service is delivered automatically to customer.
## process
#### setup
* Vendor setup server, insert server URL into smart contract
* User install client
#### query
* User open client to read smart contract, found some URL
* Client visit /price.json of all URLs  and list them all to user
* User select one of service
* Client show service introduction and price and payment method of service

#### pay and visit

* User pay cryptocurrency to payment address, writing client's ID into payment memo.
* Server found purhase record from blockchain, insert purchase record into local storage.
* Client generate request before visit server. Request contains action, it's public key, and double hash256 of client's public key. Sign it and encrypt it using server's public key.
* Client send request to server
* Server decrypt request by it's private key, verify signature by client's public key, accept request, query purchase record with hash of key, return service informaton.

# Why Chrove
Client's information are often leaked because of careless engineer and other reason. We believe real name and bill adderss is not necessary in some cases. So Chrove is invented to support anonymously ordering and automatically delivery service.

The first application is Chrove proxy. It is the first proxy you can anonymously order. Your proxy know nothing about you. This protect you and vendor. Anonymous is more important for vendor located in Iran, Russia, and China.

As vendor, you put your service info your smart contract. Nobody except you can remove it. 
You won’t lose your customer even if you change domain or IP because people still remember smart contract.
People pay you cryptocurrency. No bill on bank account or credit card.
Of course , you don’t know your users, too.
Both sides are safe.

## Chrove service market
As vendor, you may feel hard to let people know your service, you can pay some popular smart contract holder to list your service.

A smart contract holder can list and add service in his smart contract. The smart contract become a market.

Current Chrove client on google chrome
# Design principle

Technically, Chrove is based on HTTP, JSON, JWT, JWK, JWE, and RSA encryption and signature.
Chrove don’t force vendor run SSL because it is easy to anonymously run a server, but it is tricky to register a domain and apply a SSL certification even there’s awesome Let’s Encrypt.

Chrove encrypt information by peer’s public key on plain text HTTP protocol. It is strong enough now.

# User client suppport Chrove:

[Chrove proxy client in Google chrome browser extension](https://chrome.google.com/webstore/detail/chrove/bgbfndcenmhjccmfedpoknepjbfmolib)

# Server client support 
[Chrove proxy server]()


## protocol
Chrove server need to provide a web service and provide two api.

### GET /price.json
No parameter.

This api tell client the service content and price, and how to pay.

The response content is in json format.
#### the process to generate the content
```
payload_to_client = {'ContractAddress':'deadbeefduck', 'Price':{"symbol": "EOS", "amount": "1","unit":"per Day"}, 
'PublicKey':JWK_of_server_public, 'PublicKeyEnc':JWK_of_server_public_enc, "ts":timestamp_in_seconds, 
"brandinfo":{"Logo":"http://www.bbb.zzz/logo.pn",
"title":"flying ghost","description":"a very quickly proxy"}
}
```
Prepare signed content to verify public key
```
signed_by_server = JWS(timestamp_in_seconds_in_string, private_key_server)
```
Put them together and send to http client
```
result_to_client = { 
                                "content": payload_to_client,
                                "ts_signature": signed_by_server}
                        }
```

### GET /cert.info
Client need to prepare http request with a parameter called code, value should be A compact JWE string.
1. calculate kid
```
kid_of_client_public_key = base58_encoding(SHA256(SHA256(public_key_client["n"]))
```
An example of kid_of_client_public_key can be 'HareBAjopJi7GabM5HEX1VWsTHEmu8cFfAQFfSpbMsvT'
                  
2. generate client signature
```
my_signature = JWS(string(current_time_stamp_in_seconds), private_key_client)
```
3. content to server before encrypt
```
clear_payload_to_server = {"key":JWK(public_key_client), "signature": my_signature, "request":"my_subscribe"}
```
4. encrypt content with server's public key
```
encrypted_payload_server = JWE(clear_payload_to_server, public_key_server)
```

parameter to http url should be http:xxx.yy/cert.info?code=encrypted_payload_server

Server will decrypt the request by it's private key, then verify the signature. Then server will hash the RSA public key and use the result as key to match purchase record, because client will pay server and write hash in memo. If server find matching redord, server will use the api to tell customer the service information.
```
 ss_cert_list = [{"type": "ss", "server_name":"hello", "config":{"address": "1.1.1.1", "port": 1984, "method": "aes-cfb-256", "key": "romanholidy3947"}},
{"type": "socks5", "server_name":"world", "config":{"address": "2.2.1.1", "port": 11984, "username": "tomhanks", "password": "pass23hljhsdf"}},
{"type": "https", "server_name":"fish", "config":{"address": "3.3.3.3", "port": 14455, "username": "tomcruse", "password": "pass5767lskljdf"}}
]
```
generate signature
```
signed_payload = JWS(ts_server, server_private_key)
```
Encrypt result to client
```
response_of_server_signed = {'service_type':'proxy', 'service_content':{"server_list": ss_cert_list}, "ts": ts_server, "signature_str_ts": signed_payload})
response_http = JWE(response_of_server_signed, public_key_client)
```
