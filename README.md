# Twitter_screenname_list


#目的
 薬剤名のリストが用意してあります。このリストに含まれる薬の名前を含むツイートを検索します。リストに含まれる全ての薬剤について、繰り返し処理します。
検索結果のツイートをしたユーザーのユーザーID(screen_name)を、リストに取得するのがゴールです。

ちなみに、薬のリストはこんな感じです。

```python:medicine_list
medicine_list=['アミトリプチリン', 'イミプラミン', 'スルモンチール', 'アナフラニール', 'アモキサン', 'アンプリット', 'プロチアデン', 'ルジオミール', 'クロンモリン', 'マプロミール', 'テトラミド', ...(中略)...,'リフレックス', 'レメロン', 'スルピリド', 'ドグマチール', 'ミラドール', 'アビリット', 'マーゲノール', 'ベタナミン']
```

また、今回はできるだけbotを避けたいと考え、screen_nameに文字列'bot'を含むアカウントは、取得しないことにしました。

#準備
・ツイッターAPIを登録する。
　　参考：https://qiita.com/kngsym2018/items/2524d21455aac111cdee
　こちらの記事にお世話になりました。

#特定ワードを含むツイートの取得
```python:使用コード
import json
from requests_oauthlib import OAuth1Session


CK = "*******" #API key
CS = "*******" #Consumer_secret
AT = "*******" #Access_token
ATS = "*******" #Access_secret
twitter = OAuth1Session(CK, CS, AT, ATS)

url = 'https://api.twitter.com/1.1/search/tweets.json' #ツイート検索用

users = [] #検索したscreen_nameを格納するリスト

#関数の作成（入力が薬剤名リスト、出力がユーザー名のリスト）
def screen_name_from_medicine(medicine_list):
    for i in range(len(medicine_list)):
        medicine = medicine_list[i] #薬剤名リストから薬剤名を取り出す
        params = {'q': medicine, #この薬剤名を含むツイートを検索
                  'result_type':'mixed', #ツイート検索はランダム（新しい順などではない）
                  'count': 20, #一つの薬剤名あたり取得するツイートの数
                  'lang':'ja' #日本語のツイートを検索
                 }

        req = twitter.get(url, params = params)

        if req.status_code==200: #もしresponseが成功なら、req.status_code=200である
            res = json.loads(req.text)
            for line in res['statuses']:
                screen_name=line['user']['screen_name']
                if ('bot' not in screen_name) and (screen_name not in users):
                 #screen_nameに'bot'を含む物はusersリストに含めない
                 #usersリストに重複がないようにする
                    users.append(screen_name)

#関数の使用
screen_name_from_medicine(medicine_list)
#作成したリストを出力
print(users)
```

#使用環境
・jupyter-notebook
・macOS Catalina

#参考記事
https://qiita.com/tomozo6/items/d7fac0f942f3c4c66daf


