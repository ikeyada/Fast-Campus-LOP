7주차입니다.

Raw Data로부터

기존에는 Item & 지역별로 성장율 구하는 것으로부터

이번주는 회사별로 성장율 구하여보았습니다.



WEEK7_20년 지역별 월별 PIVOT_3822002019.ipynb

1. **옆 Column 문자(숫자)의 앞에 4글자만 가지고 오기**

```python
category = 3822002019
condition_1 = (df['HS_CODE'] == category)
output= df.loc[condition_1]
output
```

![](https://blogfiles.pstatic.net/MjAyMDEwMjlfMTk4/MDAxNjAzOTY1MDU0NTA2.THv2TyCVIyyFZS13KYTXZyTrhHERUOhbFVUP2S9Ve8Mg.5kNuulFKvPI6iNVpWaDd0t4Izmi_S0hXZOwA1Rfs3Fkg.PNG.ikeyada/201029_df_data%EC%9D%98_%EA%B8%B0%EB%B3%B8_%ED%98%95%ED%83%9C.PNG)



**멘토님 꿀팁#1**

① 숫자라면 :  원하는 자릿수로 직접 나눠준다   ex) 나누기 10000

② 문자라면 : **output['지역'].str[:2]**

![](https://blogfiles.pstatic.net/MjAyMDEwMzFfMjUw/MDAxNjA0MTEwNjQ5OTgy.jnicIIwRX4tdknzZZRQ5FbzHoOzJb3bnFdu9wneZklwg.R8ct6LeUcADhGqPOYIjItfzytYmicFE8igFGpJfen-Ug.PNG.ikeyada/201031_%EB%B6%80%EC%82%B0_2%EA%B8%80%EC%9E%90.PNG)

------



2. **숫자에 천단위로 콤마를 부여하려면 어떻게 하나?**
   * 소숫점 자리수는 round (  숫자 , 자릿수 ) 로 조정함

WEEK6_20년_나라별_ 월별 PIVOT_제놀_바니.ipynb

![](https://blogfiles.pstatic.net/MjAyMDEwMjlfNjIg/MDAxNjAzOTY1NDA0OTg2.iroPrqcVYOveWEXaIY0TJkKQ6YnCESEF_xu0zAbOZzMg.Gk4uDceTvE0asDGdIjUOtyfpFtsZkFDdonjP2PGIyXYg.PNG.ikeyada/201029_%EB%8B%AC%EB%9F%AC%EC%97%90_%EC%B2%9C%EB%8B%A8%EC%9C%84%EB%8B%B9_%EC%BD%A4%EB%B0%94_%EB%B6%80%EC%97%AC%EB%8A%94_%EC%96%B4%EB%96%BB%EA%B2%8C.PNG)



**멘토님 꿀팁#2**

**숫자에 천단위로 ' , '를 부여하면 문자로 변해서 오히려 활용하기 어려움**

숫자에 천단위 , 부여는 엑셀로 옮긴 후 부여하자!



------



3. **전체에서 특정 열의 숫자 값을 가지고 top 20인 row만 추출하려고 하면 어떻게 하면 될까요?**

   **이를테면 202009열의 값을 가지고 top20에 해당하는 row만 추출하고 싶습니다.**

   

```python
# Sorting
output_송파_P.sort_values(by=202009, inplace=True,ascending=False)
```



------



4. **중복이 있는 Column 문자열에서 Unique한 문자 List들을 추출하고 싶을때**

   WEEK7_회사 붙이기ipynb

![](https://blogfiles.pstatic.net/MjAyMDEwMjlfMTk2/MDAxNjAzOTY3ODAyNjA5.rY4nIkYgP6nSyHGIJj2fuzPM8lPH6RjrEQHN7PP7bSUg.dfNb-QruU6HCYgpXtXoWKaPXqjLB-e0f9YqW3xiAt4cg.PNG.ikeyada/201029_%EC%A4%91%EB%B3%B5%EB%90%9C_%ED%9A%8C%EC%82%AC_list.PNG)

**멘토님 꿀팁#3**

```python
companys.unique()
```

![](https://blogfiles.pstatic.net/MjAyMDEwMzFfODYg/MDAxNjA0MTExNjczNTA2.fic6FHl01eIsPgmJWdbJzWLGo0lbTOQQhAjusq3DHR0g.B3vrMdpyEBz4kB7QJNbMejmJkNWurWq3IFkbCMRlw9Yg.PNG.ikeyada/201031_%EC%A4%91%EB%B3%B5_%EC%A0%9C%EA%B1%B0_%EB%AC%B8%EC%9E%90_%EB%A6%AC%EC%8A%A4%ED%8A%B8.PNG)

```python
companys.value_counts()
```

![](https://blogfiles.pstatic.net/MjAyMDEwMzFfMTQ2/MDAxNjA0MTExOTU4MjI3.wfi_YBmR6UfzL8H65XGdYyUkUMOtq5a3KMl6EYecbpAg.NoEg_xY9zPuu6_rFax6Y_yG949byqQ_fbSTwPz5sE8Mg.PNG.ikeyada/201031_%ED%9A%8C%EC%82%AC%EB%93%A4_%EA%B0%AF%EC%88%98.PNG)

------



5. **오류 관련**

WEEK7_회사 붙이기.ipyb

**Before (미시 분석) : ITEM & 지역별 Loop - YoY, MoM 구하기**

![](https://blogfiles.pstatic.net/MjAyMDEwMzFfMTAg/MDAxNjA0MTIwMTM3NjE5.1S-r9xkRJ-y5dcZv1JYxeXAS9txdS2abvDZQRFC2E0wg.5JXmYRx91vtbsTNnT88t2VmSdacitLSs28wioKhRE94g.PNG.ikeyada/201031_HSCODE_%EC%A7%80%EC%97%AD%EB%B3%84_YoY_%EA%B5%AC%ED%95%98%EA%B8%B0.PNG)



**After(거시 분석) : 회사별 Loop - YoY, MoM 구하기**

![](https://blogfiles.pstatic.net/MjAyMDEwMzFfMTYy/MDAxNjA0MTIwMjEwOTA3.OjtDnCcHdztQYr6hoe6M0oreegtmS67SPfV6Y5I4ic4g.eCvyOfRCrXXyGZLSyil4xsnJcp7UvXp7UfrsPSgQcHUg.PNG.ikeyada/201031_%ED%9A%8C%EC%82%AC%EB%B3%84_YoY_%EA%B5%AC%ED%95%98%EA%B8%B0.PNG)



