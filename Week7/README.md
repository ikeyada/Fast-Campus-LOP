7주차입니다.



WEEK7_20년 지역별 월별 PIVOT_3822002019.ipynb

1. **옆 Column 문자(숫자)의 앞에 4글자만 가지고 오기**

```python
category = 3822002019
condition_1 = (df['HS_CODE'] == category)
output= df.loc[condition_1]
output
```

![](https://blogfiles.pstatic.net/MjAyMDEwMjlfMTk4/MDAxNjAzOTY1MDU0NTA2.THv2TyCVIyyFZS13KYTXZyTrhHERUOhbFVUP2S9Ve8Mg.5kNuulFKvPI6iNVpWaDd0t4Izmi_S0hXZOwA1Rfs3Fkg.PNG.ikeyada/201029_df_data%EC%9D%98_%EA%B8%B0%EB%B3%B8_%ED%98%95%ED%83%9C.PNG)



2. **숫자에 천단위로 콤마를 부여하려면 어떻게 하나?**
   * 소숫점 자리수는 round (  숫자 , 자릿수 ) 로 조정함

WEEK6_20년_나라별_ 월별 PIVOT_제놀_바니.ipynb

![](https://blogfiles.pstatic.net/MjAyMDEwMjlfNjIg/MDAxNjAzOTY1NDA0OTg2.iroPrqcVYOveWEXaIY0TJkKQ6YnCESEF_xu0zAbOZzMg.Gk4uDceTvE0asDGdIjUOtyfpFtsZkFDdonjP2PGIyXYg.PNG.ikeyada/201029_%EB%8B%AC%EB%9F%AC%EC%97%90_%EC%B2%9C%EB%8B%A8%EC%9C%84%EB%8B%B9_%EC%BD%A4%EB%B0%94_%EB%B6%80%EC%97%AC%EB%8A%94_%EC%96%B4%EB%96%BB%EA%B2%8C.PNG)



3. **전체에서 특정 열의 숫자 값을 가지고 top 20인 row만 추출하려고 하면 어떻게 하면 될까요?**

   **이를테면 202009열의 값을 가지고 top20에 해당하는 row만 추출하고 싶습니다.**

   

```python
# Sorting
output_송파_P.sort_values(by=202009, inplace=True,ascending=False)
```



4. **중복이 있는 Column 문자열에서 Unique한 문자 List들을 추출하고 싶을때**

   WEEK7_회사 붙이기ipynb

![](https://blogfiles.pstatic.net/MjAyMDEwMjlfMTk2/MDAxNjAzOTY3ODAyNjA5.rY4nIkYgP6nSyHGIJj2fuzPM8lPH6RjrEQHN7PP7bSUg.dfNb-QruU6HCYgpXtXoWKaPXqjLB-e0f9YqW3xiAt4cg.PNG.ikeyada/201029_%EC%A4%91%EB%B3%B5%EB%90%9C_%ED%9A%8C%EC%82%AC_list.PNG)



5. **오류 관련**

WEEK7_회사 붙이기.ipyb