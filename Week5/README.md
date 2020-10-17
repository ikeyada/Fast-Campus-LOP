엑셀 업무의 판다스화에 초점을 맞추고 있는 김보건입니다.



**9월**에는  수백만개 Item들의 매출 금액의 전년 동월 대비 성장율(YoY), 동년 전월 대비 성장율(MoM)  등을

판다스를 활용하여 산출하였고, 원하는 조건에 맞는 Item들만 필터링하여 추출함



**10월**에는 추출된 Item들에 대해  Micro 분석(국내 지역, 해외 나라 별)을 하는 부분을 진행하려 했으나,

우선 금주에는 매출 금액은 있는데 내가 정확한 Item No. 를 모르는 경우에 해당 Item을 찾아가는 과정을 다루고자함

(Item No.를 전임자가 알려주지 않은 경우 또는 문서 상에서 지워진 경우)



- 문제의 정의  - Y의 시계열 추이와 가장 잘 매칭되는 X의 시계열 추이를 찾기

  

Output 변수 Y : 해당 제품의 매출 금액 분기별 추이

![](https://blogfiles.pstatic.net/MjAyMDEwMTdfMjcw/MDAxNjAyOTAyMzM4Njkw.4s7lix93xQ4gsYgSUlAEmuQ2McQOdqLYPiXXPadt1qwg.08GcJ_t_-TMsTvbLpNJ3qgQL8sNbxyNaAKsQT0zMN_4g.PNG.ikeyada/y_%ED%8C%A8%ED%84%B4.PNG)



Input 변수 X : 여러개 Item No 들의 매출 금액 분기별 추이

​                         Raw Data 상태로 분기별로 가공할 수 있음 (금주 Pandas로 자동 산출 진행)

![](https://blogfiles.pstatic.net/MjAyMDEwMTdfMTQz/MDAxNjAyOTAyNTcxNTQ3.UJ6dIrp_tYx8nmCtg1GuAKsm2ej2mArxL5vGH52nvcog.eK1vWj5kQh_r35_6BN06JbcI3uq7VZdctw8bEADNahog.PNG.ikeyada/x%ED%8C%A8%ED%84%B4.PNG)



* **숫자값은 정확히 일치하는 것이 아니기에, 시계열 추이(패턴?)를 비교하여 유사한 패턴을 가지는 X를 찾고자 함**



 멘토님께 상의한 결과 

어려운 부분 : 각 Row별 Sample Size가 최소한 100여개 이상은 되어야 Machine Learning Tool의 좋은 성능이 나올 수 있는데

​                       Sample Size가 9개 수준이라 매우 적음. 우선  활용가능한 방법을 research 예정.

                       

제가 의미를 가지는 부분 :  활용 가능한 Tool을 알아도 추후 Data 확보시에 활용 가능하며,

                                             X의 분기별 매출 Raw Data를 만들어 가는 과정 자체가 저 자신에게는 의미가 있음 *^^* 



- Data의 Size :  대략 7백만 Row * 7개 Column

                           영상이나 이런 Data Size에는 훨씬 못 미치겠지만, 엑셀을 가공하는 저에게는 부담스러운 Data Size임



-     X를 만들어 가는 과정                                        

1) 특정 조건으로 Data 추출 및 가공

```python
place = '인천 남동구'
country = '중국'
condition_2 = (df['지역'] == place)
condition_4 = (df['국가'] == country)
```

```python
#인천 남동구와 중국 조건으로 데이타 추출
output_중국 = df.loc[condition_2 & condition_4]
output_중국.head()

#column header만 바꾸고 싶다면
output_중국.rename(columns={output_중국.columns[6]:"금액(중국)"},inplace=True)
output_중국

# 중량, 수량, 연도, 월 네 column은 제거한다
output_중국.drop(['중량(Kg)', '수량', '연도', '월'],axis=1, inplace=True)
```



2) 다른 Data와 Merge

① 다른 나라 Data도 추출

```python
# 대부분 inner join에서는 한개의 조건만 사용하는데, 여기선 두개의 조건 "년월"과 "HS_CODE" 두개를 활용해야 함
pd.merge(output_베트남, output_중국, how='outer', on= ['년월' , 'HS_CODE'])
# https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html
```

```python
# 결측치 NaN은 0으로 변환 
output = output.fillna(value=0)
```

② 매출 금액 (달러)를 원화 환산하기 위해 월별 환율과 분기 Data을 merge

```python
info_time = pd.read_csv('/content/drive/My Drive/info_time.csv')
output2 = pd.merge(output, info_time, how='left', on='년월')
output2["금액(백만원)"]= output2["금액_Total"]*output2["월 환율"]/1000000
```

③ 개별 Item들의 분기별 매출 합계를 구함(Pivot)

```python
output2_P = pd.pivot_table(output2, index=['HS_CODE'], columns='분기', values='금액(백만원)',aggfunc=np.sum).reset_index()
output2_P = output2_P.fillna(value=0)
# 여전히 Item들이 많아 특정 조건으로 대상을 줄여야 함, 20년 1Q,2Q 매출 금액에 조건을 걸어줌
output2_P = output2_P[(output2_P["20_2Q"] > 50) & (output2_P["20_1Q"] > 20)]
```

