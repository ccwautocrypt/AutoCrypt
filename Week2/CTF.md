# CTF? Wargame?

CTF(Capture the flag): 보안을 우회해 FLAG를 탈취하는 방식의 해킹 문제? 대회?
Wargame: 모의 해킹 환경을 구상해 원하는 목표를 달성하는 것. CTF도 이의 한 종류

---

**dreamhack wargame : session-basic**

```
#!/usr/bin/python3
from flask import Flask, request, render_template, make_response, redirect, url_for

app = Flask(__name__)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

users = {
    'guest': 'guest',
    'admin': FLAG
}

@app.route('/')
def index():
    username = request.cookies.get('username', None)
    if username:
        return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not admin"}')
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        try:
            pw = users[username]
        except:
            return '<script>alert("not found user");history.go(-1);</script>'
        if pw == password:
            resp = make_response(redirect(url_for('index')) )
            resp.set_cookie('username', username)
            return resp 
        return '<script>alert("wrong password");history.go(-1);</script>'

app.run(host='0.0.0.0', port=8000)
```

../admin으로 가면

{"2576d97a2b84bace52e5f02cdcb3e5d7e694dfb6ce5b7b28409cf28d5b68d76e":"admin"}

을 확인할 수 있는데, 로그인 후 생긴 sessionid 쿠키를 해당 admin의 쿠키로 변경하면 admin으로 로그인 해 FLAG를 얻을 수 있다.

---

**webhacking.kr: old-01**

![image](https://github.com/user-attachments/assets/5eb4df0d-352c-4e84-a562-e34109483968)

```
<?php
  include "../../config.php";
  if($_GET['view-source'] == 1){ view_source(); }
  if(!$_COOKIE['user_lv']){
    SetCookie("user_lv","1",time()+86400*30,"/challenge/web-01/");
    echo("<meta http-equiv=refresh content=0>");
  }
?>
<html>
<head>
<title>Challenge 1</title>
</head>
<body bgcolor=black>
<center>
<br><br><br><br><br>
<font color=white>
---------------------<br>
<?php
  if(!is_numeric($_COOKIE['user_lv'])) $_COOKIE['user_lv']=1;
  if($_COOKIE['user_lv']>=4) $_COOKIE['user_lv']=1;
  if($_COOKIE['user_lv']>3) solve(1);
  echo "<br>level : {$_COOKIE['user_lv']}";
?>
<br>
<a href=./?view-source=1>view-source</a>
</body>
</html>
```

쿠키를 3보다 크고 4보다는 작은 실수로 바꾸면 해결할 수 있다.

이외에도 netcat을 이용한 시스템 해킹 문제도 시도 했는데, 해당 노트북을 집에 두고 와서 못 적었습니다...


---

**Dgitan Forensic**
2023 암호분석경진대회. PDF 참고.
