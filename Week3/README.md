### **Project 목적 : 대용량 Data에 대한 엑셀 수작업을 Pandas를 이용한 자동화** 



1주차 : 정제된 toy data로 main function 작성

2주차 : 1주차 함수 적용 후 오류에 대한 보완

**3주차 : 정제되지 않은 full data 적용시 Data 전처리 문제 발생**

4주차 : full data를 통한 결과 작성



-3주차-

#### [1번째 문제] 숫자인 dollar 금액 column을 문자열(object)로 인식하는 오류 발생

- **1안) astype을 이용 문자열을 숫자 변경 시도하였으나,** 

  ​        **천단위 구분하는 콤마로 인하여 변경 실패**

1. 정제된 toy data가 아닌 full data를 읽으니 숫자 column을 문자로 인식함

   → df.astype({'중량(Kg)':'float','수량':'float','금액($)':'float'}) 대응함

2. could not convert string to float 

   문자에 쉼표가 있어서 숫자로 변환하지 못하는 오류 발생

------

-  **2안) locale 을 이용 콤마 제거 시도하였으나 변경  실패**

1. locale 을 이용하여  천단위의 쉼표를 숫자로 인식하고자 함

2. locale.atof(df['금액($)'])

   → 오류 메세지 : cannot convert the series to <class 'float'>

------

- **3안) replace 이용해도 쉼표를 제거 불가**

1. **함수 없이 문자열에 바로 적용하였으나 효과 없음**

```python
#aa= "1,000,000,000" #문자로 받음
#aa =int(aa.replace(',','')) #콤마제거 후 정수로 받음
df['금액($)'].replace(',','') 

# 결과에서 천단위를 구분하는 쉼표가 없어지지 않음
```

![](https://blogfiles.pstatic.net/MjAyMDA5MjNfMjI4/MDAxNjAwODU0MjE2NjMz.FxWfZg9PfXRBKwCluGEE1SwueHJJGnpfo7x86uSqC3Yg.6Rnl6KiFSA3AXG721Nh69rNooIumQtBAaD0AUw1BchYg.PNG.ikeyada/200923_%EC%89%BC%ED%91%9C_%EC%A0%9C%EA%B1%B0_%EB%B6%88%EA%B0%80.PNG)

2. **apply 함수로 replace 적용 오류 : 'float' object has no attribute 'replace'**

```python
def remove_comma(x):
  return x.replace(',', '')
  
df['금액($)'].apply(remove_comma)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MjNfMTYg/MDAxNjAwODU1ODU3Mjg5.ArhQ7NJ_ry8CdPE4H8xQNtwHZW5J-Hpa7jv81VfpMwUg.H2hd0PaxcS-98DDxi1IkM3hIn6bPgkHNobPzcd2cuU4g.PNG.ikeyada/200923_float_does_not_have_replace_attribute.PNG)

3. **apply 함수로 replace와 int 적용 오류 : invalid literal for int() with base 10: '11596613.'**

```python
def remove_comma(x):
  return int(x.replace(',', ''))
  
df['금액($)'].apply(remove_comma)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MjNfMTE3/MDAxNjAwODU2MzA0OTk5.Ag_7O-KtmTqiX16ynrqjgqMHARcCJ87U37GDIwIJmQsg.vbf2Ug1HX1QSf5xMgUflOm6ijc3ArkaD1tv_8rCIEpwg.PNG.ikeyada/200923_invalid_int_with_base_10.PNG)

4. **apply 함수로 replace와 float 적용 오류 :  첫번째 오류 다시 발생 'float' object has no attribute 'replace'**

```python
def remove_comma(x):
  return float(x.replace(',', ''))
  
df['금액($)'].apply(remove_comma)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MjNfMTE3/MDAxNjAwODU3MDUyNDkz.PHh3wlPGCLtrbsn3aI8knC_ngdmaezKOqEYIoEubQuMg.xd193Q2Crmmsy26KEXpgQWlqXaWN1qlkgM5HaOtHyakg.PNG.ikeyada/200923_float_does_not_have_replace_attribute2.PNG)

#### **[1번째 문제 해결안]**

- astype을 적용하면 되나 빈칸이나 '-'이 있을때는 먼저 바꿔줘야 함, 이때 **공백** 에 유의해야함

```python
df.loc[df['금액($)']=='-'] #그냥 '-'으로 하면 조회가 안됨
df.loc[df['금액($)']==' - '] # 하이픈에 좌우로 공백이 있어 이부분을 넣어주면 조회 성공
```



```python
df['금액($)'] = df['금액($)'].str.replace(' - ','0') # 이 부분이 핵심, 멘토님이 알려주심
df.sort_values(by='금액($)', ascending=[True]).head() # -이 0으로 바뀌었는지 알기 위해 오름차순으로 숫자를 정렬
```

- 얼핏 보기에 '-'으로 보여도 raw data에 공백이 있는지 없는지 잘 살펴야 함

  ![](https://blogfiles.pstatic.net/MjAyMDA5MjZfMTI5/MDAxNjAxMDgxNDM4MTYz.A_d0JZkvJvcqlooOKJJZPoNL3xE0NApTxzTdxPJJFfkg.cyGZ_FSCXR3NBkeIxAWptYCjQ6YKti7XmWSyWeQFpvgg.PNG.ikeyada/200926_%EA%B8%88%EC%95%A1%EC%97%90_%ED%95%98%EC%9D%B4%ED%94%88%EC%9D%B4_%EC%9E%88%EC%9D%8C_%EA%B7%B8%EB%9E%98%EC%84%9C_astype%EC%9D%B4_%EC%95%88_%EB%A8%B9%EC%9D%8C2.PNG)



- 하이픈을 0으로 변경

![](https://blogfiles.pstatic.net/MjAyMDA5MjZfMjgw/MDAxNjAxMDgzNjI3NjQ3.TOPe-_XhNeVvSFKmauepEMnPr_O_3sFwhZCb97KwBMog.xBM3mPeUdrlH3cHd9VJ7tetqkTz_Nbky0X4E9j7Hatgg.PNG.ikeyada/200926_%ED%95%98%EC%9D%B4%ED%94%88_0_%EB%B3%80%EA%B2%BD_%EC%84%B1%EA%B3%B5_input.PNG)

![](https://blogfiles.pstatic.net/MjAyMDA5MjZfOTIg/MDAxNjAxMDgzNjI3NjQ5.sJjPxcTj_PGHeHG5EOfR7kL0zkQR1bOfGR6MTqkbT74g.P18dql64nd5-XwJEiW3D_cc-RnvuvVs6I5g5BHNi9kcg.PNG.ikeyada/200926_%ED%95%98%EC%9D%B4%ED%94%88_0_%EB%B3%80%EA%B2%BD_%EC%84%B1%EA%B3%B5_output.PNG)



#### **[2번째 문제]  dictionary 형태 data를 frame으로 바꿔주는데 오류 발생**

toy example data 돌린 후 output dictionary 형태 

```python
for i in range(10):
    s = sample_function(category=categorys[i], place=places[i], month=6, year=2020)
    print(s)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MjRfMTUy/MDAxNjAwODc0MTE5OTIz.B7flzHMeHyFpuhruUQt_Vku-HpzcOWyZ_fgeIUjK_5Eg.ZICwlEN6uCy2OEsx3AT-WWnG6jvkYHuwYck0n83yuBcg.PNG.ikeyada/200924_s_output.PNG)

```python
#output = pd.DataFrame(s)
output = pd.DataFrame(s, columns=['항목', '지역', '월', '연도', '당월', '전년동월', 'YoY', '금년전월', 'MoM', '2020_YTD', '2019_YTD', 'YoY_c'])
#type(output)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MjRfMTM0/MDAxNjAwODc0MjYzNTA0.RixpfZvxg7Jj05fWPCSQ_J0DzphkBQAX1uyCKgBVmhog.Vp5nIUYB6WjlygJW1TMIQK2AsnhqLJVIjNsAMKwxx_kg.PNG.ikeyada/200924_dictionary%EB%A5%BC_data_frame%EC%9C%BC%EB%A1%9C_index%EB%A5%BC_%EC%A4%98%EC%95%BC_%ED%95%9C%EB%8B%A4.PNG)



#### [2번째 문제 해결안]

- 먼저 a 라고 list 를 형성함  [ ] 명심할 것

```python
a = []

for i in range(len(info)):
    s = sample_function(category=categorys[i], place=places[i], month=6, year=2020)
    print(s)
    a.append(s)
    
output = pd.DataFrame(a)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MjZfMjg0/MDAxNjAxMDkyNjcyOTU3.YtOmzceGFs7vThPSOVzwYv5Ua6eqRBJljS6NICH0cpsg.XJuO1vP1C8r3tSPSEXFIK9eZe7-D_a8w4jZaaF0Uu2cg.PNG.ikeyada/200926_%EC%B5%9C%EC%A2%85_output_%EC%A4%91%EA%B0%84%ED%98%95%ED%83%9C.PNG)





#### **[3번째 문제] ****여러 개의 월 data를 연속적으로 붙였는데 원본 data가 누락된 것을 확인함**



**①  concat하는 data의 column name이 미세하게 다르면 누락 발생 risk**

- 원본 데이타 : 금액 부분에 44,099라는 숫자가 표기됨

  ![](https://blogfiles.pstatic.net/MjAyMDA5MjZfMTc3/MDAxNjAxMDgyMDU3ODY5.luJo_ZhWLBaYXJEdznzDrLeiIDkfCcN9ShU_3tEcGZ8g.rxX8u2PXoXfmrwVGpm4r4vxQtnxCnvPyFB5NIbI9Ex8g.PNG.ikeyada/200924_%EC%97%91%EC%85%80_%EC%9B%90%EB%B3%B8_%EB%8D%B0%EC%9D%B4%ED%83%80.PNG)

  

- 파이썬에서 붙인 데이타 : 금액 부분에 숫자가 없고 NaN 표기됨

![](https://blogfiles.pstatic.net/MjAyMDA5MjZfMTE1/MDAxNjAxMDgyMjQwNjU1.WY3a07JbJnuOjlxLNYQkzcgy7atQutmTMLhSn_Wo-Ocg.9hJiET6__wziypVo65G15fjt8WfKD3WOua75NMrzK7og.PNG.ikeyada/200924_python_%EB%B6%99%EC%9D%B8_%EB%8D%B0%EC%9D%B4%ED%83%80_NaN.PNG)



- **(특정 조건에 해당하는 row를 추출)** 

  각 월별 data를 합치기 전에는 금액 data가 있었는지를 확인한 결과, 금액 data는 있었음

  **즉 각 월별 data를 합친 후에 금액 data가 숫자에서 숫자가 없는 NaN으로 변함**

  **[data를 합치기 전 금액 column 값 : 숫자 존재]**

  ![](https://blogfiles.pstatic.net/MjAyMDA5MjZfMjE4/MDAxNjAxMDc0NTE1ODY4.G_bV0XozojVqnkeIcgqI-pb9seXjvZbxsK0VLawcd10g.sAO5_oql1UH65Fsm08qJpeVUyZng-t_thzEPb-1psKQg.PNG.ikeyada/200926_%ED%95%A9%EC%B9%98%EA%B8%B0_%EC%A0%84%EC%97%90%EB%8A%94_data%EA%B0%80_%EC%9E%88%EC%97%88%EB%8B%A4.PNG)



**② column name이 엑셀이나 육안상으로 동일하게 보이지만 잘 합쳐지지 않음**

- data concat 오류 

  ![](https://blogfiles.pstatic.net/MjAyMDA5MjZfODIg/MDAxNjAxMDgxODI5NjAz.cmVw3URblgIVovaNWP4ispVOXt_4SC-_QBKm3q2TeAAg.APeKxY9QMxQcTWaiGSIZd2j9jhfqxumSN-dQzdRzZXsg.PNG.ikeyada/200926_data_concat_%EC%98%A4%EB%A5%98.PNG)



#### **[3번째 문제 해결안]**

#### **첫째, 중량(Kg)과 중량(KG)로 Column Name이 미세하게 다름,**

**둘째, 엑셀 상의 column name이 같아도 눈에 보이지 않는 공백이 차이나서**

####          **column header를 일괄적으로 복사 하였더니 data가 잘 붙음**



- column name을 철자가 같아도 다시 hard copy 함. data 붙이기 성공

  ![](https://blogfiles.pstatic.net/MjAyMDA5MjZfMTg0/MDAxNjAxMDgxNzA0OTk4.8JWHvzV_a8U_i5Aa6byq-LUpqBxGjvV4noR3viECPKcg.Zb2UEkZYSw-FamrFVY06X9aQYmrMbyYGNwUw_CCRnMQg.PNG.ikeyada/200926_data_concat_%EC%84%B1%EA%B3%B5.PNG)





