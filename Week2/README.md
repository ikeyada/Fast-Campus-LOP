**PLAN**

- STEP1 : Week1 파이썬 코드 실행하여 문제(Error) 여부 확인하기

- STEP2 : Output2 Format 만들기

- STEP3 : Target Information(항목 및 지역) 화일 만들기

- STEP4 : Toy example이 아닌 전체 화일 여러개를 붙인 **Full Data Set**을 만들어

  ​              Week1 파이썬 코드 돌려보기

- STEP5 : YoY가 좋은 Top20 조합 추출하기

- STEP6 : Plotting하기 (Section별, Section 내)



**STEP1 : Week1 코드 실행 문제(Error) 확인**

1.  **Data 불러오기**

```python
# 판다스 라이브러리 불러오기
import pandas as pd
# 구글 드라이브 마운트
from google.colab import drive
drive.mount('/content/drive')

# Toy Data csv 화일 불러와서 df라는 data frame으로 지정하기
df = pd.read_csv('/content/drive/My Drive/Sample_6 Item.csv')

# csv 화일을 불러서 추출 대상 정보를 info 라는 data frame으로 지정하기
info = pd.read_csv('/content/drive/My Drive/target_info.csv')

info
df.head()
```

![](https://blogfiles.pstatic.net/MjAyMDA5MTZfMTU2/MDAxNjAwMjQ4ODA2NTE5.Wcy-8DGmQ191Kpt8O78QieBl8X9MgKtMm3Zi3gnTwqkg.QszOBQr49ECMtVZFRjdIB7nNyMVl5Ax8-zOYdsXJalAg.PNG.ikeyada/200916_info_%ED%99%94%EC%9D%BC_output.PNG)

![](https://blogfiles.pstatic.net/MjAyMDA5MTZfMTAy/MDAxNjAwMjQ5MDExMTM0.ONvq-UrUg9ABFuoWP6pdBCxLEG0RNCL9MvtKJQhd4Psg.ewJ4P_-i535fCZhbb7tygYfjsGa-2818j4Q7FVghGRMg.PNG.ikeyada/200916_df_%ED%99%94%EC%9D%BC.PNG)

2. **Data 가공하기**

```python
# 추출 대상 정보를 각각 변수로 지정하기
category = info['categorys']
place = info['place']

# 아래 부분이 의외로 중요함, 연도는 월 Column의 4번째 자리까지를 정수로 지정
#                          월은 월 Column의 5번째부터 끝까지 자리를 정수로 지정
df['연도'] = df['월'].astype('str').str[:4].astype(int)
df['월'] = df['월'].astype('str').str[4:].astype(int)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MTZfMTAw/MDAxNjAwMjU0NTU4NTA1.n2pkNQHJ5Ywm-SjlQ4DPp-lVpBcCfOTN6RTn8pjSewkg.4jvI2uyuYTdTeRi_5npi9bM1fdvqVkUJsBuKk1eDcMYg.PNG.ikeyada/200916_column_%EB%B6%84%EB%A6%AC_astype_%EC%9D%B4%EC%9A%A9.PNG)

```python
# column 순서 바꾸기
df = df[['연도', '월', '항목', '국가', '지역', 'Value']]
df.head()
```

![](https://blogfiles.pstatic.net/MjAyMDA5MTZfMjc3/MDAxNjAwMjU0NTY0OTY1.XguKTuPE-dSgxeEu9xz1IRyQvcdwPTBB3GOIm9_-IBIg.ujN7uxhQVoVsXr6Ra_38vrIl7HIYd-54MkL4fOYbcWQg.PNG.ikeyada/200916_column_%EC%88%9C%EC%84%9C_%EB%B0%94%EA%BE%B8%EA%B8%B0.PNG)

3. **함수 만들기**

```python
# Mentor 님과 했을때보다 column을 더 추가 완료

def sample_function(category, place, month, year):
    condition_1 = (df['항목'] == category)
    condition_2 = (df['지역'] == place)
    condition_3 = (df['월'] == month)
    condition_4 = (df['월'] == month-1)
    condition_5 = (df['연도'] == year) & (df['월'] <= month)
    condition_6 = (df['연도'] == year-1) & (df['월'] <= month)

    value_2019 = df.loc[condition_1 & condition_2 & condition_3].groupby('연도')['Value'].sum()[year-1]
    value_2020 = df.loc[condition_1 & condition_2 & condition_3].groupby('연도')['Value'].sum()[year]
    value_previous_month = df.loc[condition_1 & condition_2 & condition_4].groupby('연도')['Value'].sum()[year]

    YTD_2020 = df.loc[condition_1 & condition_2 & condition_5]['Value'].sum()
    YTD_2019 = df.loc[condition_1 & condition_2 & condition_6]['Value'].sum()

    전년동월비 = value_2020/value_2019 -1
    전년누적비 = YTD_2020/YTD_2019  - 1
    금년전월비 = value_2020/value_previous_month -1

    ret = {
        '항목': category, 
        '지역': place, 
        '월': month, 
        '연도': year, 
        '당월' : value_2020,
        '전년동월' : value_2019, 
        'YoY'  : 전년동월비,
        '금년전월' : value_previous_month,
        'MoM' : 금년전월비,
        '2020_YTD': YTD_2020, 
        '2019_YTD': YTD_2019, 
        'YoY_c' : 전년누적비,
    }
    return ret
```



4. **함수 실행 및 결과**

```python
for i in range(5):
    s = sample_function(category=categorys[i], place=places[i], month=6, year=2020)
    print(s)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MTZfMTQ5/MDAxNjAwMjU1MDc3NjYz.SpRsclFhL_ZQdBOiA-Qlqsr8qEPxqkOImaFlTqBSBrog.BJ2OOyuWYnIctIk6Wz8vsbDQaAMMgAPoqbwuexDLbLkg.PNG.ikeyada/200916_output.PNG)



# 에러 확인 : 값이 비어 있는 셀은 나누기 오류 발생

                                             **멘토님에게 도움 요청할 부분**

1. 원하는 output format

   | category |    place    | 202006 | 201906 | YoY  |  202005  | MoM  | 2020_YTD | 2019_YTD | YoY  |
   | :------: | :---------: | :----: | :----: | :--: | :------: | :--: | :------: | :------: | :--: |
   |    22    | 강원 춘천시 | 175.02 | 77.74  | 125% |  250.26  | -30% |  857.57  |  376.84  | 128% |
   |    11    | 강원 춘천시 | 3.2269 |   NA   |      |    NA    |      |  5.7929  |   3.30   | 76%  |
   |    11    | 경기 광주시 |   NA   |   NA   |      | 12.39116 |      |    NA    |  12.39   |      |
   |    33    | 경기 고양시 |   NA   |  0.10  |      |    NA    |      |    NA    |   0.10   |      |
   |    33    | 경남 김해시 |  0.13  |   NA   |      |    NA    |      |   0.13   |   0.00   |      |
   |    33    | 서울 중랑구 |  0.56  |   NA   |      |    NA    |      |   1.70   |    NA    |      |

   

2. 문제 확인 : 1행은 계산이 되나 **값이 비어 있는 셀이 있는 경우 나누기가 되지 않아 계산이 되지 않음**

   ​                    즉  **총 5개 셀 (202006, 201906, 202005, 2020_YTD, 2019_YTD ) 중 하나라도 비어 있는 경우 동작하지 않음**

   

**STEP2 : 또 다른 Output Format 만들기** - 항목 * 지역 조합 내 세부 Data 확인

| 연도 |  월  |  11   |  22  |  33  | 합계  | YoY  |  환율   |
| :--: | :--: | :---: | :--: | :--: | :---: | :--: | :-----: |
| 2019 |  1   | 113.4 | 4.4  | 33.1 | 150.9 |      | 1122.48 |
| 2019 |  2   | 110.9 | 0.9  | 31.3 | 143.2 |      | 1123.87 |
| 2019 |  3   | 122.9 | 2.2  | 20.6 | 145.8 |      | 1132.45 |
| 2019 |  4   | 103.5 | 1.2  | 32.0 | 136.7 |      | 1143.05 |
| 2019 |  5   | 78.0  | 4.1  | 15.6 | 97.8  |      | 1185.76 |
| 2019 |  6   | 78.2  | 0.5  | 27.1 | 105.8 |      | 1174.29 |
| 2019 |  7   | 105.2 | 2.7  | 27.9 | 135.9 |      | 1177.54 |
| 2019 |  8   | 101.4 | 0.4  | 25.3 | 127.1 |      | 1211.05 |
| 2019 |  9   | 136.2 | 1.3  | 36.5 | 174.0 |      | 1197.47 |
| 2019 |  10  | 117.4 | 3.6  | 18.3 | 139.2 |      | 1184.0  |
| 2019 |  11  | 112.0 | 1.4  | 25.1 | 138.5 |      | 1168.33 |
| 2019 |  12  | 99.8  | 2.9  | 33.4 | 136.1 |      | 1175.4  |
| 2020 |  1   | 111.0 | 1.3  | 19.7 | 132.0 | -13% | 1167.18 |
| 2020 |  2   | 105.8 | 1.7  | 36.4 | 143.9 |  0%  | 1195.48 |
| 2020 |  3   | 96.0  | 2.6  | 19.4 | 118.0 | -19% | 1220.14 |
| 2020 |  4   | 146.8 | 9.5  | 33.5 | 189.9 | 39%  | 1224.13 |
| 2020 |  5   | 259.1 | 5.6  | 62.5 | 327.2 | 235% | 1230.38 |
| 2020 |  6   | 179.6 | 22.0 | 48.2 | 249.7 | 136% | 1208.42 |

