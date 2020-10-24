6주차입니다. 



1. **특정 코드 # 1 또는 특정 코드 #2에 해당하는 조건 설정****

제놀 필터링 연습.ipynb



```python
# HSCODE가 하나인 경우에는 잘 사용했는데 코드#1 또는 코드#2 또는 코드 #3에 해당하는 조건은 어떻게 설정하는지 궁금합니다

#category = [3822002019, 8479899099]
#category = 3822002019 or 8479899099
category = 3822002019, 8479899099
condition_1 = (df['HS_CODE'] == category)
df.loc[condition_1]
########################### 이거 멘토님에게 물어봐야 겠다 
#condition_1 = (df['HS_CODE'] == 3822002019) or (df['HS_CODE'] == 8479899099)
```



- **Mentor님 꿀팁 #1**

```
# or에 해당하는 글자는 원화 표시 위에 있는 shift 누르고 '|'임
condition_1 = (df['HS_CODE'] == 3822002019 ) |  (df['HS_CODE']== 8479899099
df.loc[condition_1]
```



```python
# 아무래도 위에 방법은 3개 이상의 조건을 설정하기가 어려움 그래서 꿀팁이 다음임.
code = [3822002019, 8479899099]
condition_1 = df['HS_CODE'].isin(code)
```



2. **json 화일 열기 &  특정 문자와 유사한 row 찾기** (https://open.fda.gov/  API 활용 가능)

   FDA_JSON 열기.ipynb

```python
# PANDAS 책에 있느 Syntax로 안 열린다
#df= pd.read_json('/content/drive/My Drive/device-covid19serology.json')

data = json.load(open('/content/drive/My Drive/device-covid19serology.json'))
df = pd.DataFrame(data["results"])
condition_1 = (df['manufacturer'] == 'Boditech Med Incorporated')
what_I_want = df.loc [condition_1]
# 만약에 boditech와 유사한 문자를 찾고 싶다면 어떻게 찾을까???
```

- **Mentor님 꿀팁 #2**

```
#대,소문자를 구별해야 되는데 우선은 대문자까지 알고 있을때
#condition_1 = (df['manufacturer'].str.contains("Bodi") )

#대,소문자를 정확히 모를때는 아마 거의 모든 경우가 해당되겠지. 모든 문자를 소문자로 바꾼다음에 찾는다
condition_1 = (df['manufacturer'].str.lower().str.contains("bodi") )
```

![](https://blogfiles.pstatic.net/MjAyMDEwMjJfMTcg/MDAxNjAzMzU2MzQ3OTM5.tWQ7mqGNyC0Kaa61h93sM1pvXCW8hhb24PC3frzLs2Eg.3eEVLs2xVxttc6u01EpeR3zpqVLr3osnXJotb_oKLYYg.PNG.ikeyada/201022_OPEN_FDA%ED%99%94%EB%A9%B4.PNG)



3. **무역협회 수출입 통계 크롤링하기** (https://blog.naver.com/jaden-agent/221999057921)

   **Mentor님 꿀팁 #3**

| Python Crawling 공부 순서                                    |
| ------------------------------------------------------------ |
| ① 먼저, 크롤링은 HTML에 대한 충분한 이해 그리고 웹에 대한 이해가 있어야 원하는 방식으로 자유자재로 다뤄보실 수 있습니다.  금방 뚝딱하고 나오면 좋은데 웹사이트 분석과 테스팅 하는 시간이 어느 정도 소요 된다는 점을 미리 말씀드립니다ㅎㅎ |
| ②  selenium 크롤링의 경우 일단 로컬 컴퓨터에서 실행을 해주셔야 합니다. |
| ③ pycharm이나 visual studio 그리고 파이썬을 설치 후 진행해주셔야 하는데, 아마 강의 내용에서 찾아보실 수 있을 것 같습니다. |
| ④ selenium 버전은 크롬 브라우저의 버전과 매칭이 되어야 크롤링 해오실 수 있습니다~ |
| ⑤ 셋업이 되셨다면 일단 가지고 계신 소스코드가 잘 돌아가는지 먼저 실행해 보시고 문제가 없으시다면, 그 때부터 필요 없는 코드들을 하나씩 제거해 나가시면서 원하시는 방향에 맞게 변경해 보시는 것을 추천 드립니다! |

* 패스트 캠퍼스 3주차 크롤링 강의

1) 크롤링 기초 지식 - Chrome 개발자 도구 사용법 -1,2

![](C:\Users\USER\AppData\Roaming\Typora\typora-user-images\image-20201022202958004.png)

2) 크롤링 기초 지식 - 크롤링 주의사항

3)  



**[Coming Soon - 차주에 공부해야할 Code]**

```python
import time
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.wait import WebDriverWait
from selenium.common.exceptions import UnexpectedAlertPresentException
from selenium.webdriver.support.ui import Select
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from bs4 import BeautifulSoup
import xlsxwriter
import xlrd

def excel_column_name(n):
    """Number to Excel-style column name, e.g., 1 = A, 26 = Z, 27 = AA, 703 = AAA."""
    name = ''
    while n > 0:
        n, r = divmod (n - 1, 26)
        name = chr(r + ord('A')) + name
    return name
    
def crawling_all_trade(read_data, month_select_op):

    options = Options()
    #options.headless = True
    options.headless = False
    browser = webdriver.Chrome(executable_path="./chromedriver.exe", options=options)

    print(read_data)

    #url = "http://stat.kita.net/stat/kts/prod/ProdWholeDetailPopup.screen#none"
    #browser.get("http://stat.kita.net/stat/kts/prod/ProdWholeList.screen")
    browser.get("http://stat.kita.net/stat/kts/prod/ProdWholeDetailPopup.screen#none")
    print("Get URL")
    #browser.wait_for_and_accept_alert()

    #browser.get(url)
    try:
    #    browser.get(url)
        #browser.get("http://stat.kita.net/stat/kts/prod/ProdWholeDetailPopup.screen#none")
        result = browser.switch_to_alert()
        print(result.text)
        result.accept()
        result.dismiss()
    except:
    #except UnexpectedAlertPresentException:
        #alert = browser.switch_to_alert()
        #alert.dismiss()
        print("There is no alert")


    # browser.get("", data)

    # s_term_gb
    # s_monthsum_gb
    # s_measure
    # s_prod_name
    # s_year

    crawling_list = []

    #prod_code = "460"
    #item_value = "382200"

    wait = WebDriverWait(browser, 40)

    for n in range(len(read_data)):

        prod_code = read_data[n][0]
        item_value = read_data[n][2]
        
        print("data index : ", n, prod_code, item_value)

        time.sleep(0.5)

        s_prod_code = browser.find_element_by_css_selector("#s_prod_code")
        s_prod_code.clear()
        s_prod_code.send_keys(prod_code)
       

        time.sleep(0.5)

        s_item_value = browser.find_element_by_css_selector("#s_item_value")
        #for i in range(5):
        #    s_item_value.send_keys(Keys.BACKSPACE)
        #    time.sleep(0.5)
        s_item_value.clear()
        s_item_value.send_keys(item_value)

        time.sleep(0.5)

        monthsum_select = Select(browser.find_element_by_css_selector("body > form > div > div > fieldset > div:nth-child(2) > div:nth-child(2) > select"))
        monthsum_select.select_by_value('1')

        time.sleep(0.5)

        scale_select = Select(browser.find_element_by_css_selector("body > form > div > div > fieldset > div:nth-child(2) > div:nth-child(3) > select"))
        scale_select.select_by_value('1000')

        browser.find_element_by_xpath("/html/body/form/div/div/fieldset/div[3]/a/img").click()

        time.sleep(1)
        wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR, '#mySheet1 > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody > tr:nth-child(2) > td.GMClassReadOnly.GMCell.GMNoRight.GMEmpty.GME.IBSheetFont0')))

        if month_select_op == 1:

            ## Scroll DOWN
            try:
                browser.find_element_by_css_selector("#mySheet1 > tbody > tr:nth-child(2) > td:nth-child(2) > div > div").click()
                actions = ActionChains(browser)
                for i in range(3):
                    actions.send_keys(Keys.PAGE_DOWN).perform()
                    time.sleep(0.5)
            except:
                print("Scroll Bar error")

            #mySheet1 > tbody > tr:nth-child(2) > td:nth-child(2) > div > div

            click_interval = 3
            

            tag_names = browser.find_element_by_css_selector("#mySheet1 > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table").find_elements_by_tag_name("tr")
            #for tag in tag_names:
            #    print (tag.text.split("\n"))
            
            tr_cnt = len(tag_names)
            print(tr_cnt)

            print("Expand")

            browser.find_element_by_css_selector("#mySheet1 > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody > tr:nth-child(" + str(tr_cnt)+") > td.GMClassReadOnly.GMCell.GMNoRight.GMEmpty.GMEL.IBSheetFont0").click()
            #try:
            #    wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR, "#mySheet1 > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody > tr:nth-child(" + str(tr_cnt+1) + ") > td > table > tbody > tr:nth-child(2) > td.GMClassReadOnly.GMWrap0.GMAlignLeft.GMText.GMCell.IBSheetFont0.GMNoLeft.HideCol0C1")))         
            #except:
            #    print("First Expand Error")
            time.sleep(3.0)    
            for idx in range(tr_cnt-2):
                print("Expand Index : ", idx, "str(tr_cnt-2-idx) ", tr_cnt-2-idx)
                browser.find_element_by_css_selector("#mySheet1 > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody > tr:nth-child(" + str(tr_cnt-1-idx) + ") > td.GMClassReadOnly.GMCell.GMNoRight.GMEmpty.GME.IBSheetFont0").click()
                wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR, "#mySheet1 > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody > tr:nth-child(" + str(tr_cnt-idx) + ") > td > table > tbody > tr:nth-child(2) > td.GMClassReadOnly.GMWrap0.GMAlignLeft.GMText.GMCell.IBSheetFont0.GMNoLeft.HideCol0C1")))         
                #time.sleep(click_interval)

                if idx == 12:            
                    #Scroll UP
                    actions = ActionChains(browser)
                    for i in range(3):
                        actions.send_keys(Keys.PAGE_UP).perform()
                        time.sleep(0.5)

        html = browser.page_source
        soup = BeautifulSoup(html, 'html.parser', from_encoding='utf-8')
        #print(soup)
        trs = soup.find_all('tr', {"class":"GMDataRow"})

        # Write BS to TXT file
        #f = open("./test.txt", "w", encoding='utf-8')
        #f.write(str(soup))
        #f.close()


        # len 17
        # ['', '\xa0', '2020년', '36,631,568', '1,934.1', '21,961,664', '169.9', '0', '0.0', '32,455,714', '-11.0', '239,694,362', '-4.8', '0', '0.0', '4,175,854', '2020']
        tr_list = []

        str_year = "2030년"
        
        for tr in trs:
            
            pass_tr = 0
            
            #print(tr.text.split("\n"))
            #print(tr.text)
            tds = tr.find_all('td')
            td_list = []

            #년월           td[2]
            #수출 금액      td[3]
            #수출 증감률    td[4]
            #수출 중량      td[5]
            #수출 증감률    td[6]
            #수입 금액      td[9]
            #수입 증감률    td[10]
            #수입 중량      td[11]
            #수입 증감률    td[12]
            #수지           td[15]

            for k in range(len(tds)):
                if k==2:
                    if tds[k].text.find("년") == -1:
                        #print(tds[k].text)
                        td_list.append(str_year + tds[k].text)            
                    else:
                        str_year = tds[k].text
                        td_list.append(tds[k].text)
                        if month_select_op == 1:
                            pass_tr = 1;
                elif k==4 or k==6 or k==10 or k==12:
                    td_list.append(float(tds[k].text.replace(',','')))
                elif k==3 or k==5 or k==9 or k==11 or k==15:
                    td_list.append(int(tds[k].text.replace(',','')))
                else:
                    td_list.append(tds[k].text)

            #for td in tds:
            #    td_list.append(td.text)
            #    #print(td.text)

            #rint("len", len(td_list))
            if pass_tr == 0:
                tr_list.append(td_list)

     
        #print(tr_list)

        #print("$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$")
        #browser.find_element_by_css_selector("#mySheet1 > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody > tr:nth-child(5) > td > table > tbody > tr:nth-child(10) > td.GMClassReadOnly.GMWrap0.GMAlignLeft.GMText.GMCell.IBSheetFont0.GMNoLeft.HideCol0C1").click()

        '''
        for k in range(3):
            print("=================================================")
            for j in range(15):
                actions.send_keys(Keys.ARROW_DOWN).perform()
                
            print(browser.find_element_by_css_selector("#mySheet1").text)
            time.sleep(1)


        time.sleep(3)
        tag_names = browser.find_element_by_css_selector("#mySheet1").find_elements_by_tag_name("tr")
        for tag in tag_names:
            print (tag.text.split("\n"))

        '''

        #print(browser.find_element_by_css_selector("#mySheet1 > tbody > tr:nth-child(3)").text)
        
        if month_select_op == 1:
            tr_list.reverse()
            new_tr_list = []
            idx = 0
            
            while idx < len(tr_list):
                if idx + 12 > len(tr_list):
                    temp_list = tr_list[idx:]
                    temp_list.reverse()
                    new_tr_list = new_tr_list + temp_list
                    #print(new_tr_list)
                else:
                    #print(type(new_tr_list), type(tr_list[idx:idx+12]), type(tr_list[idx:idx+12].reverse()))
                    temp_list = tr_list[idx:idx+12]
                    temp_list.reverse()
                    new_tr_list = new_tr_list + temp_list
                    #print(new_tr_list)
                idx = idx + 12

            crawling_list.append(new_tr_list)      
        else:
            tr_list.reverse()
            crawling_list.append(tr_list)

    browser.close()

    return crawling_list

```

