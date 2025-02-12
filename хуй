from flask import Flask, render_template, session, redirect, url_for, request
import requests

app = Flask(__name__, template_folder='.')  # Указываем текущую папку как папку для шаблонов
app.secret_key = '933-549-750'  # Этот ключ нужен для работы с сессиями
app.config['SESSION_TYPE'] = 'filesystem'  # Правильная конфигурация сессий

# Главная страница
@app.route('/')
def home():
    return render_template('index.html')  # index.html в корневом каталоге

# Добавление товара в корзину
@app.route('/add_to_cart/<int:id>/<string:name>/<float:price>', methods=['POST'])
def add_to_cart(id, name, price):
    if 'cart' not in session:
        session['cart'] = []
    
    product = {
        'id': id,
        'name': name,
        'price': price,
        'quantity': 1
    }
    
    for item in session['cart']:
        if item['id'] == id:
            item['quantity'] += 1
            break
    else:
        session['cart'].append(product)
    
    session.modified = True
    return redirect(url_for('cart'))

@app.route('/liquids')
def liquids():
    return render_template('liquids.html')  # liquids.html в корневом каталоге

@app.route('/devices')
def devices():
    return render_template('devices.html')  # devices.html в корневом каталоге

@app.route('/nicotine_liquids')
def nicotine_liquids():
    return render_template('nicotine_liquids.html')  # nicotine_liquids.html в корневом каталоге

@app.route('/non_nicotine_liquids')
def non_nicotine_liquids():
    return render_template('non_nicotine_liquids.html')  # non_nicotine_liquids.html в корневом каталоге

@app.route('/premium_liquids')
def premium_liquids():
    return render_template('premium_liquids.html')  # premium_liquids.html в корневом каталоге

@app.route('/flavored_liquids')
def flavored_liquids():
    return render_template('flavored_liquids.html')  # flavored_liquids.html в корневом каталоге

@app.route('/organic_liquids')
def organic_liquids():
    return render_template('organic_liquids.html')  # organic_liquids.html в корневом каталоге

@app.route('/cart')
def cart():
    cart = session.get('cart', [])
    total_sum = sum(item['price'] * item['quantity'] for item in cart)
    return render_template('cart.html', cart=cart, total_sum=total_sum)  # cart.html в корневом каталоге

@app.route('/update_quantity/<int:item_id>/<action>', methods=['POST'])
def update_quantity(item_id, action):
    cart = session.get('cart', [])
    for item in cart:
        if item['id'] == item_id:
            if action == 'increase':
                item['quantity'] += 1
            elif action == 'decrease' and item['quantity'] > 1:
                item['quantity'] -= 1
            break
    session.modified = True
    return redirect(url_for('cart'))

@app.route('/remove_from_cart/<int:item_id>', methods=['POST'])
def remove_from_cart(item_id):
    session['cart'] = [item for item in session['cart'] if item['id'] != item_id]
    session.modified = True
    return redirect(url_for('cart'))

@app.route('/clear_cart', methods=['POST'])
def clear_cart():
    session['cart'] = []
    session.modified = True
    return redirect(url_for('cart'))

@app.route('/checkout')
def checkout():
    if 'cart' not in session or len(session['cart']) == 0:
        return redirect(url_for('home'))

    total_sum = sum(item['price'] * item['quantity'] for item in session['cart'])
    return render_template('checkout.html', total_sum=total_sum)  # checkout.html в корневом каталоге

@app.route('/process_payment', methods=['POST'])
def process_payment():
    if 'cart' not in session or len(session['cart']) == 0:
        return redirect(url_for('home'))

    cart = session['cart']
    total_sum = sum(item['price'] * item['quantity'] for item in cart)

    message = "Новая покупка!\n"
    message += "Товары:\n"
    for item in cart:
        message += f"- {item['name']}: {item['quantity']} x {item['price']} руб.\n"
    message += f"Итого: {total_sum} руб."

    # Отправить сообщение в Telegram
    send_telegram_message(message)

    # Очистить корзину после успешной оплаты
    session['cart'] = []
    session.modified = True

    # Перенаправление на страницу успешной оплаты
    return redirect(url_for('payment_success'))

# Страница успешной оплаты
@app.route('/payment_success')
def payment_success():
    payment_date = "19 января 2025"  # Можете заменить на динамическую дату
    order_id = "1854"  # Номер заказа, можно тоже динамически генерировать
    
    # Получаем корзину из сессии
    cart = session.get('cart', [])
    
    # Рассчитываем общую сумму
    total_amount = sum(item['price'] * item['quantity'] for item in cart)

    return render_template('payment_success.html',
                           payment_date=payment_date,
                           order_id=order_id,
                           cart=cart,
                           total_amount=total_amount)  # payment_success.html в корневом каталоге

# Telegram Bot Token и Chat ID
# Telegram Bot Token и Chat ID
TELEGRAM_BOT_TOKEN = '7791735203:AAFC1TzcCpkgjrvFASLyG5Vhw6X-gVEoHLg'  # Замените на ваш токен
TELEGRAM_CHAT_ID = '771803609'  # Замените на ваш chat_id (можно найти с помощью @userinfobot)

def send_telegram_message(message):
    url = f'https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage'
    payload = {
        'chat_id': TELEGRAM_CHAT_ID,
        'text': message
    }
    response = requests.post(url, data=payload)
    return response.json()

if __name__ == "__main__":
    app.run(debug=True)



