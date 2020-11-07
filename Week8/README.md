8주차입니다.

9월과 10월 동안에  엑셀 수작업에서 파이썬 Pandas를 활용한 업무 자동화에 초점을 맞췄고,

크게  아래의 3부분을 요약하였습니다. 



① YoY, MoM, YoY_c 성장율 구하기

② 19~20년 각 월별로 국가별 매출 구하기 (Pivot)

③ 내가 원하는 Data만 필터링 하여 분기별 합계 구하기 (Pivot)



## 1. YoY, MoM, YoY_c 성장율 구하기** - Week2_YoY 산출 첫 성공.ipynb

문자열 "201906"을 연도(2019)와 월(9)로 구분 그래야 전월, 동월에서 숫자가 적용 가능

- 문자와 숫자의 가공

```python
df['연도'] = df['월'].astype('str').str[:4].astype(int)
df['월'] = df['월'].astype('str').str[4:].astype(int)

# column의 순서 바꾸기
df = df[['연도', '월', '항목', '국가', '지역', 'Value']]
```

​		

​          +  내가 원하는 조건으로 필터링 하기

```python
condition_1 = (df['항목'] == category)
condition_2 = (df['지역'] == place)
condition_3 = (df['월'] == month)
condition_4 = (df['월'] == month-1)
condition_5 = (df['연도'] == year) & (df['월'] <= month)
condition_6 = (df['연도'] == year-1) & (df['월'] <= month)

if (year-1) in df.loc[condition_1 & condition_2 & condition_3].groupby('연도')['Value'].sum().keys():
    value_2019 = df.loc[condition_1 & condition_2 & condition_3].groupby('연도')['Value'].sum()[year-1]
else:
    value_2019 = np.NaN
```

```python
a = []

for i in range(len(info)):
    s = sample_function(category=categorys[i], place=places[i], month=6, year=2020)
    print(s)
    a.append(s)
```



Week3. ipynb

```
df.loc[df['금액($)']=='-'] #그냥 '-'으로 하면 조회가 안됨
df.loc[df['금액($)']==' - '] # 하이픈에 좌우로 공백이 있어 이부분을 넣어주면 조회 성공

# 금액($)에 '-'이 있는 부분을 0으로 변경
df['금액($)'] = df['금액($)'].str.replace(' - ','0')

df.sort_values(by='금액($)', ascending=[True]).head()
```



Week4.ipynb

```python

# 숫자에 천단위마다 comma가 들어있어 문자열로 인식하는 문제 그래서 pivot 등이 안 먹음
df['금액($)'] = df['금액($)'].str.replace(',','').astype('float')
df['연도'] = df['년월'].astype('str').str[:4].astype(int)

```

Week4.ipynb

**[결과 output]**

![](https://blogfiles.pstatic.net/MjAyMDExMDdfMTcz/MDAxNjA0NzI0Njc1NzYw.X-gEUSVhicDNUgh4_MWJlxlHK_22Mc8tbGv3Xaa6fqcg.F5T2wrj6lMyVW93_0LOUSJl2IZkUCzumD6epjj7rf3Eg.PNG.ikeyada/201107_4%EC%A3%BC%EC%B0%A8_%EC%A7%80%EC%97%AD_item%EB%B3%84_yoy.PNG)



## 2. 나라별 월별 pivot 함수 만들기**

2-1) 한개의 사례에 대해 할 때는 잘 된다

```python
category = 3002150000
place = '충북 청주시'
condition_3 = (df['HS_CODE'] == category)
condition_4 = (df['지역'] == place)
output_에스디 = df.loc[condition_3 & condition_4]
output_에스디_P=pd.pivot_table(output_에스디, index=['국가'], columns='년월', values='금액($)',aggfunc=np.sum).reset_index()
output_에스디_P = output_에스디_P.fillna(value=0)
output_에스디_P.sort_values(by=202010, inplace=True,ascending=False)
output_에스디_P
```



그래서 함수를 만들어 보았다   WEEK6_20년만_나라별 월별 PIVOT_제놀_바니_바디

```python
def country_function(category, place, monthyear):
    condition_1 = (df['HS_CODE'] == category)
    condition_2 = (df['지역'] == place)
    output= df.loc[condition_1 & condition_2]
    output_P=pd.pivot_table(output, index=['국가'], columns='년월', values='금액($)',aggfunc=np.sum).reset_index()
    output_P = output_P.fillna(value=0)
    return output_P.sort_values(by=monthyear, ascending=False).reset_index(drop=True)
    # sort 할때는 reset_index(drop=True)
```

```python
t=country_function(category=3002150000, place='충북 청주시')
print(t)
```



2-2) Multi Index Pivot에서 정렬시키기**

WEEK7_회사 기준 YoY. ipynb

```python
#201105 pivot 돌림
pd.pivot_table(df_c, index=['연도', '월'], columns='회사', values='금액($)',aggfunc=np.sum).reset_index()
```



multi index에서 data 정렬순서 지정하기

```python
pd.pivot_table(df_c, index=['연도', '월'], columns='회사', values='금액($)',aggfunc=np.sum).reset_index().sort_values(by=["연도","월"], ascending=[False,True])
```



[결과 output]

![](https://blogfiles.pstatic.net/MjAyMDExMDdfMTg1/MDAxNjA0NzI0NTAwMjQ0.coXxcLFzMZ937BSDaXoXte3OLfrvrDEwOcVnIL9I1m4g.VK-bT5RMANa-CTgl36kTgz-f2ifxKzvX9CjvVIeUBa0g.PNG.ikeyada/201107_%ED%9A%8C%EC%82%AC%EB%B3%84_yoy.PNG)





## 3. 내가 원하는 Data만 필터링 하여 분기별 합계 구하기 (Pivot)

WEEK8 샘표 필터링.ipynb

```python
# 숫자를 문자로 변환 후 앞에 6자리 문자만 가져옴
df['HS_CODE']=df['HS_CODE'].astype('str')
df['6자리_CODE']=df['HS_CODE'].str[:6]

# 원하는 특정 list의 문자만 isin을 이용하여 필터링
code = ['190219', '210310', '210320', '210390','210410', '210690', '210111', '210112', '210120', '210130', '200819','200820','200830','200870','200899','160232','160241','160249','160250']
condition_1 = df_10['6자리_CODE'].isin(code)
```

