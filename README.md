import requests
from flask import Flask, render_template_string

app = Flask(__name__)

HTML_TEMPLATE = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Random Quote Generator</title>
</head>
<body>
    <h1>Random Quote Generator</h1>
    {% if quote %}
        <blockquote>"{{ quote }}"</blockquote>
        <p>- {{ author }}</p>
    {% endif %}
    <form method="get">
        <button type="submit">Get Another Quote</button>
    </form>
</body>
</html>
"""

@app.route("/", methods=["GET"])
def home():
    quote = None
    author = None
    try:
        response = requests.get("https://api.quotable.io/random", timeout=5)
        if response.status_code == 200:
            data = response.json()
            quote = data.get("content")
            author = data.get("author")
        else:
            quote = "Could not fetch a quote."
            author = ""
    except Exception:
        quote = "Error connecting to quote service."
        author = ""
    return render_template_string(HTML_TEMPLATE, quote=quote, author=author)

if __name__ == "__main__":
    app.run(debug=True)
