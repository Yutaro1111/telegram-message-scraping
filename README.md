# telegram-message-scraping
# DEMO

![image](https://github.com/Yutaro1111/telegram-message-scraping/assets/136210051/71935b70-6388-4d1a-b9ad-3f3abacca3e4)

# Features
 
telegramのメッセージをwebアプリから簡易にスクレイピングするデスクトップアプリです。
更新内容をtxtに溜め込みます。
実行時は別途実行階層に"last_log.txt"ファイルを作成してください。
また、実行中のchromeを対象にスクレイピングするのでchromeはポート固定して起動してください。
 
# Requirement

* tkinter
* pickle
* selenium
 
# Installation

```bash
pip install selenium
pip install pickle
pip install selenium
```
 
# Code

 ```
import os
import sys
import re
import tkinter as tk
import pickle
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options

# 探索
def scraping():
    xpath = "//div[@id='column-center']/div[contains(@class,'chats-container')]/div[contains(@class,'chat')]/div[contains(@class,'bubbles')]/div[contains(@class,'scrollable')]/div[contains(@class,'bubbles-inner')]//div[@class='bubbles-group']/div[contains(@class,'bubble')]//div[@class='bubble-content']/div[contains(@class,'message')]"
    result = driver.find_elements(by=By.XPATH, value=xpath)
    output =[]
    for element in result:
        output.append(element.text[:element.text.find('\n')])
    return output

#ダンプ
def output_dump(result):
    with open('last_log.txt', 'wb') as file:
        pickle.dump(result, file)

#読み込み
def read_dump():
    if not os.path.exists('last_log.txt'):
        raise Exception('ファイルがありません。')
    if os.path.getsize('last_log.txt') == 0:
        raise Exception('ファイルの中身が空です。')
    with open('last_log.txt', 'rb') as file:
        dump_list = pickle.load(file)
    return dump_list

#CSV差分比較
def list_diff(result, last_result):
    return_list = []
    for tmp in result:
        if tmp not in last_result:
            return_list.append(tmp)
    return return_list

# タイマーイベント関数
def time_update():
    output = scraping()
    diff = list_diff(output, read_dump())
    if diff != []:
        output_dump(output)

    canvas.delete("all")
    canvas.create_text(5, 10, text=output[-1], anchor="nw", fill="white", font=("",10))

    root.after(500, time_update)

# メイン 関数
def main():

   global root
   global canvas
   
   # フォーム表示
   root = tk.Tk()
   root.title("クリプトニュース取得[最新の見出しを表示]")
   root.geometry("500x30")
   root.resizable(False, False)

   canvas = tk.Canvas(master=root, width=500, height=30 ,bg="black")
   canvas.place(x=0, y=0)

   # タイマー
   root.after(10, time_update)

   # Xボタンを押された時の処理
   #root.protocol('WM_DELETE_WINDOW', lambda:root.withdraw())

   # イベント待機
   root.mainloop()


# chromedriverの設定とxpath記述
option = Options()
option.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
driver = webdriver.Chrome(options=option)
# 実行
main()
```
 
# Author

* Yutaro1111
* E-mail
