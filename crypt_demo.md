```
#!/usr/bin/env python3
# coding=utf-8
import os
import sys
import re
import random
import datetime
import subprocess
import json
import string
import tempfile
import pymysql
import base64
import hashlib
import Crypto.Random
from Crypto.Cipher import AES
import logging
import logging.handlers

class Crypt(object):
    def __init__(self):
        self.salt_size = 17
        self.number_of_iterations = 21
        self.aes_multiple = 16

    def generate_key(self, password, salt, iterations):
        assert iterations > 0
        key = password.encode('utf-8') + salt
        for _ in range(iterations):
            key = hashlib.sha256(key).digest()

        return key

    def pad_text(self, text, multiple):
        extra_bytes = len(text) % multiple
        padding_size = multiple - extra_bytes
        padding = chr(padding_size) * padding_size
        padded_text = text + padding

        return padded_text

    def unpad_text(self, padded_text):
        try:
            padding_size = ord(padded_text.decode('utf-8')[-1])
            text = padded_text[:-padding_size]
            return text
        except UnicodeDecodeError:
            logger.error('Salt unmatched')
            raise UnicodeDecodeError('Salt unmatched', b'salt', 1, 2, 'salt')

    def encrypt(self, plaintext, password):
        salt = Crypto.Random.get_random_bytes(self.salt_size)

        key = self.generate_key(password, salt, self.number_of_iterations)

        cipher = AES.new(key, AES.MODE_ECB)

        padded_plaintext = self.pad_text(plaintext, self.aes_multiple)

        ciphertext = cipher.encrypt(padded_plaintext)

        ciphertext_with_salt = salt + ciphertext

        res = str(base64.b64encode(ciphertext_with_salt), 'UTF-8')

        return res

    def decrypt(self, ciphertext, password):
        data = base64.b64decode(ciphertext)

        salt = data[0:self.salt_size]

        ciphertext_sans_salt = data[self.salt_size:]

        key = self.generate_key(password, salt, self.number_of_iterations)

        cipher = AES.new(key, AES.MODE_ECB)

        padded_text = cipher.decrypt(ciphertext_sans_salt)

        plaintext = self.unpad_text(padded_text)

        res = plaintext.decode('utf-8')

        return res

```
