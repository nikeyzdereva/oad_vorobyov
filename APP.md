

Основные компоненты
```
Transaction Service - управление транзакциями.
Value Converter Service - конвертация данных.
Transaction Manager Service - управление логами транзакций.
Redis Cache - кэширование.
Kafka - для обмена сообщениями.
```

## Transaction_Service.py

```python
from flask import Flask, request, jsonify
from confluent_kafka import Producer
import redis

app = Flask(__name__)
redis_client = redis.StrictRedis(host='localhost', port=6379, db=0)
producer = Producer({'bootstrap.servers': 'localhost:9092'})

@app.route('/transactions', methods=['POST'])
def create_transaction():
    transaction_data = request.json
    transaction_id = transaction_data.get('id')
    
    producer.produce('transaction-topic', key=transaction_id, value=str(transaction_data))
    producer.flush()

    redis_client.set(transaction_id, str(transaction_data))
    return jsonify({'status': 'Transaction created'}), 201

@app.route('/transactions/<string:transaction_id>', methods=['GET'])
def get_transaction(transaction_id):
    transaction_data = redis_client.get(transaction_id)
    
    if transaction_data:
        return jsonify({'transaction': transaction_data.decode('utf-8')}), 200
    return jsonify({'error': 'Transaction not found'}), 404

if __name__ == '__main__':
    app.run(port=5001)

```
Value Converter Service.py

```python
from flask import Flask, request, jsonify
from confluent_kafka import Producer, Consumer
import json

app = Flask(__name__)
producer = Producer({'bootstrap.servers': 'localhost:9092'})

@app.route('/convert', methods=['POST'])
def convert_value():
    conversion_request = request.json
    request_id = conversion_request.get('id')
    
    # Отправка сообщения в Kafka
    producer.produce('conversion-topic', key=request_id, value=json.dumps(conversion_request))
    producer.flush()
    return jsonify({'status': 'Conversion request sent'}), 200

@app.route('/convert/<string:request_id>', methods=['GET'])
def get_converted_value(request_id):
    # Здесь может быть логика для получения значения, например, из кэша или БД
    return jsonify({'converted_value': 'example_value'}), 200

if __name__ == '__main__':
    app.run(port=5002)

```


Transaction_Manager_Service.py


```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/transactions/log', methods=['GET'])
def get_transaction_logs():
    # Реализация получения логов
    transaction_logs = [
        {"transaction_id": "1", "status": "completed"},
        {"transaction_id": "2", "status": "failed"},
    ]
    return jsonify(transaction_logs), 200

if __name__ == '__main__':
    app.run(port=5003)
```

Consumer.py
```python
from confluent_kafka import Consumer

def start_transaction_consumer():
    consumer = Consumer({
        'bootstrap.servers': 'localhost:9092',
        'group.id': 'transaction_group',
        'auto.offset.reset': 'earliest'
    })
    consumer.subscribe(['transaction-topic'])

    while True:
        message = consumer.poll(1.0)
        if message is None:
            continue
        if message.error():
            print(f"Consumer error: {message.error()}")
            continue

        # Обработка транзакции
        print(f"Received transaction data: {message.value().decode('utf-8')}")

if __name__ == '__main__':
    start_transaction_consumer()
```
каждый сервис является независимым и взаимодействует с другими сервисами через определенные api

