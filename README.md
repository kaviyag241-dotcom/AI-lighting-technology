# AI-lighting-technology
import os
import json
import random
from flask import Flask, render_template, request

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'static/uploads'

with open('database.json') as f:
    products = json.load(f)

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/upload', methods=['POST'])
def upload():
    file = request.files['image']
    filepath = os.path.join(app.config['UPLOAD_FOLDER'], file.filename)
    file.save(filepath)

    # Simple matching logic
    filename = file.filename.split('.')[0].upper()

    match = None
    for product in products:
        if product["item_no"] in filename:
            match = product
            break

    if not match:
        match = random.choice(products)

    similar = random.sample(products, 3)

    return render_template("result.html", product=match, similar=similar)

if __name__ == "__main__":
    app.run(debug=True)
