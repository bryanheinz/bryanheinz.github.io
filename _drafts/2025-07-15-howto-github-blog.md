---
layout: post
title:  "How to Github Blog"
published: false
date: 2025-07-15
categories:
    - Meta Knowledge
tags:
    - howto
---

# How to Github Blog

simple shell code

```shell
echo "hello world"
```

simple python script

```python
#!/usr/bin/env python3

"""
The goal of this script is to pull passwords from the macOS keychain.

1. Open the macOS Keychain
2. File -> New Password Item...
3. Add an item name, account username, and a password; click Add
4. Call this function with the input being the keychain item name to get the password
"""

import re
import getpass
import subprocess

def get_credentials(service):
    """
    Retrieves the username and password from an item in the keychain.
    
    Returns an array with the username and password.
    
    e.g. ['username', 'password']
    """
    cmd = ['/usr/bin/security', 'find-generic-password', '-l', service, '-g']
    comp = subprocess.run(cmd, check=True, capture_output=True)
    output = comp.stdout.decode('utf-8')
    errors = comp.stderr.decode('utf-8')
    username_regex = r'\"acct\"<blob>=\"(.+)\"$'
    password_regex = r'password:\s*\"(.+)\"$'
    research_username = re.search(username_regex, output, flags=re.M)
    research_password = re.search(password_regex, errors, flags=re.M)
    return [research_username.group(1), research_password.group(1)]

def get_password(service):
    """Find a keychain item based on its label and return it's password."""
    cmd = ['/usr/bin/security', 'find-generic-password', '-l', service, '-w']
    comp = subprocess.run(cmd, check=True, capture_output=True)
    if comp.returncode == 0: return comp.stdout.decode('utf-8').strip()

def add_password():
    """Create a new keychain item."""
    service = input('Service (Label) > ')
    account = input('Account > ')
    password = getpass.getpass('Password > ')
    cmd = ['/usr/bin/security', 'add-generic-password',
        '-a', account,
        '-s', service,
        '-w', password,
        '-T', '/usr/bin/security'
    ]
    comp = subprocess.run(cmd, check=True, capture_output=True)
    if comp.returncode == 0: print(f"The keychain item {service} was created.")
    return get_password(service)

# add a password, returns the results
print(add_password())
# > 'correct horse battery staple'

# get a password
print(get_password('keychain-item-name'))
# > 'correct horse battery staple'

# alternative to get both the username and password from a Keychain item
print(get_credentials('keychain-item-name'))
# > ['roym', 'correct horse battery staple']
```



