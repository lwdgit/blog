import re

def replacer(match):
    return match.group(1).upper()

rawString = "{{prefix_HelloWorld}}   testing this. {{_thiswillNotMatch}} {{prefix_Okay}}"
result = re.sub(r'\{\{prefix_(.+?)\}\}', replacer, rawString)