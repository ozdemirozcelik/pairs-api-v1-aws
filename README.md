# Pairs-API V1 for trading stocks (single or pairs), deployed on AWS Elastic Beanstalk

Version 1 of the RESTful API built from the ground-up with Flask-RESTful.
Pairs-API catches and stores webhooks from trading platforms such as Tradingview.

Deployed on AWS for testing purposes:

`http://apipairs-env-1.eba-hxq8uii2.us-east-1.elasticbeanstalk.com/`

Front-end demo for v1 (Javascript):

http://apipairs-env-1.eba-hxq8uii2.us-east-1.elasticbeanstalk.com/apitest


# Use Cases

Pairs-API v1 can be a good starting point for developing trading bots. You can:
- list, save, update and delete stocks and pairs
- enable and disable stocks and pairs for active trading
- catch webhooks from trading platforms or signal generators

# Requirements
* requests~=2.24.0
* flask~=2.0.2
* Flask-RESTful

# Installation
(commands in parentheses for anaconda prompt)

### clone git repository:
```bash
$ git clone https://github.com/ozdemirozcelik/pairs-api-v1-aws.git
````
### create and activate virtual environment:
````bash
$ pip install virtualenv
(conda install virtualenv)

$ mkdir pairs-api
md pairs-api (windows)

$ cd pairs-api

$ python -m venv pairs-api
(conda create --name pairs-api)

$ source pairs-api/bin/activate
.\pairs-api\scripts\activate (windows)
(conda activate pairs-api)
````
### install requirements:

````
$ pip install -r requirements.txt
(conda install --file requirements.txt)

$ rm data.db
delete data.db (windows)

$ python create_db.py
````
### run flask:
````
$ export FLASK_APP=app
$ export FLASK_ENV=development
$ set FLASK_DEBUG=1 
$ flask run

(windows)
set FLASK_APP=app
set FLASK_ENV=development
set FLASK_DEBUG=1 
flask run
````

browse to "http://127.0.0.1:5000/" to see the dashboard.

# Authorization

No authorization needed at the moment, webhooks need a passphrase, by default it is set as 'webhook'.
Check signals.py:

```python
PASSPHRASE = 'webhook'
```

# Resources

Resources defined with flask_restful are:

```python
api.add_resource(SignalWebhook, '/v1/webhook')
api.add_resource(SignalList, '/v1/signals/<string:number_of_items>')
api.add_resource(Signal, '/v1/signal/<string:rowid>')

api.add_resource(PairRegister, '/v1/regpair')
api.add_resource(PairList, '/v1/pairs/<string:number_of_items>')
api.add_resource(Pair, '/v1/pair/<string:name>')

api.add_resource(StockRegister, '/v1/regstock')
api.add_resource(StockList, '/v1/stocks/<string:number_of_items>')
api.add_resource(Stock, '/v1/stock/<string:symbol>')
```

# Request & Response Examples

### POST request to register a single stock:
```python
'http://127.0.0.1:5000/v1/regstock'
```
Request Body:
```json
{
    "symbol": "AAPL",
    "prixch": "SMART",
    "secxch": "NASDAQ",
    "active": 1
}
```

Response:
```json
{
    "message": "Stock created successfully."
}
```

### PUT request to update a single stock:
```python
'http://127.0.0.1:5000/v1/regstock'
```
Request Body:
```json
{
    "symbol": "AAPL",
    "prixch": "SMART",
    "secxch": "NASDAQ",
    "active": 1
}
```

Response:
```json
{
    "symbol": "AAPL",
    "prixch": "ISLAND",
    "secxch": "BYX",
    "active": 0
}
```

### GET request to get all stocks:
```python
'http://127.0.0.1:5000/v1/stocks/0'
```

### GET request to receive certain number of stocks (for exp: 50):
```python
'http://127.0.0.1:5000/v1/stocks/2'
```
Response:
```json
{
    "stocks": [
        {
            "symbol": "AAPL",
            "prixch": "SMART",
            "secxch": "NASDAQ",
            "active": 1
        },
        {
            "symbol": "DRH",
            "prixch": "SMART",
            "secxch": "SMART",
            "active": 1
        }
    ]
}
```

### GET request to get details of a certain stock:
```python
'http://127.0.0.1:5000/v1/stock/AAPL'
```

Response:
```json
{
    "symbol": "AAPL",
    "prixch": "SMART",
    "secxch": "NASDAQ",
    "active": 1
}
```
### DELETE request for a certain stock:
```python
'http://127.0.0.1:5000/v1/stock/AAPL'
```
Response:
```json
{
    "message": "Item deleted"
}
```

### POST request to register a webhook signal:
```python
'http://127.0.0.1:5000/v1/webhook'
```
Request Body:
```json
{
    "passphrase": "webhook",
    "ticker": "AAPL",
    "order_action": "buy",
    "order_contracts": "100",
    "order_price": "400.2",
    "mar_pos": "long",
    "mar_pos_size": "100",
    "pre_mar_pos": "flat",
    "pre_mar_pos_size": "0",
    "order_comment": " Enter Long",
    "order_status": "waiting"
}
```

Response:
```json
{
    "message": "Signal created successfully."
}
```

#### Test the demo application here:

http://apipairs-env-1.eba-hxq8uii2.us-east-1.elasticbeanstalk.com/apitest

# Status Codes

Pairs-API v1 returns the following status codes:

| Status Code | Description             |
| :--- |:------------------------|
| 200 | `OK`                    |
| 201 | `CREATED`               |
| 400 | `BAD REQUEST`           |
| 404 | `NOT FOUND`             |
| 500 | `INTERNAL SERVER ERROR` |


# Deployment:

Install [EB CLI](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install-windows.html):
````
$ pip install awsebcli
````

Clone repository:
````
$ git clone https://github.com/ozdemirozcelik/pairs-api-v1-aws.git
````

Register the application (pairs-api-v1-aws) to AWS Elastic Beanstalk, pay attention to the region (us-east-1):
````
$ eb init -p python-3.4 -r us-east-1 pairs-api-v1-aws
````

Create an environment (Pairsenv-1), used single instance & no load balancer:
````
eb create Pairsenv-1 --single -i t2.micro
````

More information:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-flask.html


# Demo:

http://apipairs-env-1.eba-hxq8uii2.us-east-1.elasticbeanstalk.com/apitest

# Acknowledgements
snippets:
* [Sort a List](https://w3schools.com/howto/howto_js_sort_list.asp)
* [Table Display](http://jsfiddle.net/DaS39)
* [jQuery input filter](https://jsfiddle.net/KarmaProd/hw8j34f2/4/)

# Version Considerations

Considering for the next versions:

- simplify storage with SQLAlchemy
- add PostgreSQL
- add token refreshing and Flask-JWT-Extended
- improve demo with TradingView realtime webhooks
- send real time orders to exchange (possibly via Interactive Brokers)

# Contributing

Pull requests are welcome.


