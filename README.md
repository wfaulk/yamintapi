# yamintapi: Yet Another Mint.com API

A minimalistic wrapper to Mint.com API, designed to be a lean but functional module that other Python applications can use.

## Requirements

- Python 3+
- Only real dependency is `requests`
- `selenium` and `phantomjs` are needed for automated login. Alternatively, login information can be updated using information from a browser session.

## Usage

### Log in
```python
from yamintapi import Mint

# will prompt for 2-factor login code that will be sent to email
mint = Mint().login(email, password)

# optional: will initiate account refresh and wait until all accounts are refreshed
mint.refresh_accounts()
```

Alternatively, logging in using an existent browser session with no selenium/phantomjs dependency
```python
mint = Mint()
mint._js_token = ... # find the attribute value for element id=javascript-user from any page in Mint after logging in
# for each cookie in mint session
mint.session.cookies[...] = ... 
```
### Get information
```python
mint.get_accounts()
mint.get_categories()
mint.get_tags()
```
The results of these calls are cached. To clear the cache, just call `mint.login(...)` again.

### Get transactions
```python
# newest 100 transactions
mint.get_transactions(limit=100)

# all transactions (slow!)
mint.get_transactions()
```

Another option to get all transactions, much faster but with less details:
```python
import io
csv_string = mint.get_transaction_csv()

# then parse with 
import csv
transactions = [row for row in csv.DictReader(io.StringIO(csv_string))]

# OR with pandas
import pandas as pd
transactions = pd.read_csv(io.StringIO(csv_string))
```

### Change/add transactions
Change description, add `tag1` and remove `tag2` to the newest transaction
```python
trans_id = mint.get_transactions(limit=1)[0]['id']
mint.update_transaction(trans_id, description='new description', tags={'tag1': True, 'tag2: False'})
```
Tags should already exist. If not, you can create with `mint.add_tag(tag_name)`.

Add a cash transaction (default to today)
```python
mint.add_cash_transaction('dinner', amount=-10.0, category_name='Restaurants', tags=['tag1', 'tag2'])
```

## See also

[mintapi](https://github.com/mrooney/mintapi)
