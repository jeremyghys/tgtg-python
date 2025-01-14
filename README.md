# tgtg-python

Fork depuis tgtg-python

Il faut en plus des dépendences de tgtg-python ajouter:
- pip3 install pytz
- pip3 install cryptography
- pip3 install git+https://github.com/66niko99/py-adyen-encrypt.git

Ce n'est pas un tuto ou autre, si ca peut vous mettre sur une piste pour votre code c'est très bien.


## Exemple: Une fois la commande passé, on initialise un réglement par Paypal et on envoi l'URL pour régler par Pushover (c'est mon cas d'usage)

```
        import time
        import sys
        import os
        import requests
        import json
        import pyshorteners
        
        USER = "USERAPITOKENPUSHOVER"
        API = "APITOKENPUSHOVER"
        
        from datetime import datetime
        from tgtg import TgtgClient
        now = datetime.now()
        type_tiny = pyshorteners.Shortener()
        
        dt_string = now.strftime("%d/%m/%Y %H:%M:%S")
        print("\r\n", dt_string)

        client = TgtgClient(access_token="eXXXXXX", refresh_token="YYYYYYYY", cookie="XFDFDFDFDFD")

        item = client.get_item(item_id=12345678)
        textstr = item
        nompanier = textstr['display_name']
        qtydispo = textstr['items_available']
        if qtydispo > 0:
            order = client.create_order('12345678', 1)
            order_id = order['id']
            payment = client.pay_order(order_id)
            # print(nompanier, " The payment Array is: ", payment)
            payment_id = payment['payment_id']
            # print(f"Payment status: {client.get_payment_status(payment_id)}")
            # print(f"Payment URL: {client.get_payment_paypal_url(payment_id)}")
            long_url = client.get_payment_paypal_url(payment_id)
            short_url = type_tiny.tinyurl.short(long_url)
            text = f"{nompanier} - {short_url}"
            payload = {"message": text, "user": USER, "token": API, "ttl": 240, "expire": 30, "html": 1 }
            requests.post('https://api.pushover.net/1/messages.json', data=payload, headers={'User-Agent': 'Python'})
        else:
            print('Pas de stock', nompanier)
```
