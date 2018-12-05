# Chrove_protocol
An anonymously pay and autonomous service

# Why anonymous and autonomous is important for proxy/VPN vendor?

In some country, people suffer from internet censorship.
There are many commercial VPN business. But most of them are blocked.
In that country, engineer and small business man build small proxy to make a little money.
But their websites are often blocked and their customer can not contact them. 
They have to accept cash through online payment because there is no other choice.
Some of them are put into jail by authorities because officer can link customer’s account cash flow to their account.


# Why Chrove

Chrove is invented to create an anonymously ordering and autonomous service.
As vendor, you put your service info on a smart contract. Nobody can remove it. 
You won’t lose your customer even if you change domain or IP because people only remember smart contract.
People pay you crypto currency. No bill on bank account or credit card.
Of course , you don’t know your users, too.
Both sides are safe.

# Design principle

Technically, Chrove is based on HTTP, JSON, JWT, JWK, JWE, and RSA encryption and signature.
Chrove don’t force vendor run SSL because it is easy to anonymously run a server, but it is tricky to register a domain and apply a SSL certification even there’s awesome Let’s Encrypt.

Chrove encrypt information by peer’s public key on plain text HTTP protocol. It is strong enough now.

# Let’s talked about code:

Chrove user client:
A javascript client.

Chrove vendor client:
Go lang client
python client

It will be release soon.

## protocol
Server provide a web, two interfaces
### GET /price.json without parameter
This api is used to tell what service it provide, the price of service, and how to pay.

The content is in json format.
```
payload_to_client = {'ContractAddress':'deadbeef', 'Price':{"symbol": "EOS", "amount": "1","unit":"per Day"}, 
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
The 
### GET /cert.info with parameter
Client need to prepare http request with a parameter called code, value should be A compact JWE string.
```
```
Server use the api to tell customer the service information. But client ne
```
 ss_cert_list = [{"type": "ss", "server_name":"hello", "config":{"address": "1.1.1.1", "port": 1984, "method": "aes-cfb-256", "key": "romanholidy3947"}},
{"type": "socks5", "server_name":"world", "config":{"address": "2.2.1.1", "port": 11984, "username": "tomhanks", "password": "pass23hljhsdf"}},
{"type": "https", "server_name":"fish", "config":{"address": "3.3.3.3", "port": 14455, "username": "tomcruse", "password": "pass5767lskljdf"}}
]
```
