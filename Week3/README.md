**1~2주차 : 정제된 toy data로 뼈대를 이루는 함수 작성**

**3주차 : 정제되지 않은 full data 적용시 Data 전처리 문제 발생**



**1번째 문제 : [숫자인 금액 column을 문자열(object)로 인식하여 숫자로 변경 시도 중 에러 발생]**

- **1안) astype을 이용 문자열을 숫자 변경 시도하였으나,** 

  ​        **천단위 구분하는 콤마로 인하여 변경 실패**

1. 정제된 toy data가 아닌 full data를 읽으니 숫자 column을 문자로 인식함

   → df.astype({'중량(Kg)':'float','수량':'float','금액($)':'float'}) 대응함

2. could not convert string to float 

   문자에 쉼표가 있어서 숫자로 변환하지 못하는 오류 발생

------

-  **2안) locale 을 이용 콤마 제거 시도하였으나 변경  실패**

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

**2번째 문제 : Sample Function 을 돌린 후에 dictionary 형태 data를 frame으로 바꿔주는데 오류 발생**

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



**3번째 문제 추가 질문**

**금액 숫자에 11596613. 가 있어서 소수점이 붙은 숫자의 처리는?**

**금액($)에서 소수점이 붙은 숫자가 있는지 확인 가능한가?**