Сразу говорю, что я пыталась сделать, но ничего нормального не вышло(как обычно).
Поэтому да.


Структура проекта
```
/payment_service 
- app.py 
- transaction_manager.py
- redis_cache.py
- value_converter.py
```
## app.py

```python
from flask import Flask, request, jsonify
from transaction_manager import TransactionManager
from redis_cache import RedisCache

app = Flask(__name__)
transaction_manager = TransactionManager()
redis_cache = RedisCache()

@app.route('/process_transaction', methods=['POST'])
def process_transaction():
    data = request.json
    transaction_id = transaction_manager.process_transaction(data)
    return jsonify({"transaction_id": transaction_id}), 201

@app.route('/get_transaction/<transaction_id>', methods=['GET'])
def get_transaction(transaction_id):
    transaction = redis_cache.get_transaction(transaction_id)
    if transaction:
        return jsonify(transaction)
    return jsonify({"error": "Transaction not found"}), 404

if __name__ == '__main__':
    app.run(debug=True)
```
transaction_manager.py

```python
import json
from kafka import KafkaProducer

class TransactionManager:
    def __init__(self):
        self.producer = KafkaProducer(bootstrap_servers='localhost:9092',
                                       value_serializer=lambda v: json.dumps(v).encode('utf-8'))

    def process_transaction(self, transaction_data):
        # Логика обработки транзакции
        self.producer.send('transactions', transaction_data)
        return transaction_data.get("id")  # Возвращаем ID транзакции

```


redis_cache.py


```python
import redis
import json

class RedisCache:
    def __init__(self):
        self.client = redis.Redis(host='localhost', port=6379, db=0)

    def cache_transaction(self, transaction_id, transaction_data):
        self.client.set(transaction_id, json.dumps(transaction_data))

    def get_transaction(self, transaction_id):
        transaction = self.client.get(transaction_id)
        if transaction:
            return json.loads(transaction)
        return None
```

value_converter.py
```python
class ValueConverter:
    @staticmethod
    def convert(value):
        # Конвертация значений
        return value * 100  # Например, преобразуем в копейки
```



запуск - 
export FLASK_APP=app.py
flask run


