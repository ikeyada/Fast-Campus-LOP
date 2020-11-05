8주차입니다.



1. **나라별 월별 pivot 함수 만들기**

한개의 사례에 대해 할 때는 잘 된다

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



그래서 함수를 만들어 보았다

```python
def country_function(category, place):
    condition_1 = (df['HS_CODE'] == category)
    condition_2 = (df['지역'] == place)
    output= df.loc[condition_1 & condition_2]
    output_P=pd.pivot_table(output, index=['국가'], columns='년월', values='금액($)',aggfunc=np.sum).reset_index()
    output_P = output_P.fillna(value=0)
    output_P.sort_values(by=202010, inplace=True,ascending=False)
```

```python
t=country_function(category=3002150000, place='충북 청주시')
print(t)
```



2. **Multi Index Pivot에서 정렬시키기**

WEEK7_회사 기준 YoY. ipynb

```python
#201105 pivot 돌림
pd.pivot_table(df_c, index=['연도', '월'], columns='회사', values='금액($)',aggfunc=np.sum).reset_index()
```



