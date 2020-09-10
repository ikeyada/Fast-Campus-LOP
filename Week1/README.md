**자기소개**

python을 아래 **기초** 책으로 공부한지 1개월이 채 지나지 않은 김보건입니다.

![img](https://www.wikidocs.net/images//book/j2p_title_new2_S1JyeeE.jpg)

아직 걸음마도 못 떼었는데,  파이썬 LOP 반의 개발을 업으로 하시는 분들과

수업을 같이 듣게 되어 **''왕초보''**인 저로서는 지금 엄청 긴장 상태입니다 ^^;;

------

**파이썬 공부 동기**

1. 엑셀의 반복 작업을 빠르게 줄여줄 수 있다.
2. 엑셀에서는 수백만 row 대용량 Data 핸들링이 어렵다.

------

**파이썬 LOP 수강 동기**

1. 책을 읽어봐도 결국 실무에 어떻게 적용할지 모르겠다.
2. 실무에 적용을 하지 않으니 파이썬 공부가 더 나아가지 못한다
3. 파이썬을 못하니, 세상의 변화를 따라가지 못한다.
4. 결국 멘토가 필요하여 파이썬 LOP 수강 신청을 했습니다.

------

**분석 대상 Dataset 설명**

Data의 형태는 행에 비해 열이 많은 wide data가 아니라, 열은 적고 행이 많은 Data 입니다 



**[Input Data : 전체 Data에서 3행까지만 발췌]**

|   월   | 항목 |    국가    |    지역     |  Value  |
| :----: | :--: | :--------: | :---------: | :-----: |
| 201903 |  22  |  콜롬비아  | 강원 춘천시 | 1.0074  |
| 201903 |  22  |   이라크   | 강원 춘천시 | 16.2323 |
| 201903 |  22  | 카자흐스탄 | 강원 춘천시 | 0.7197  |

1. 월     : 201903 ~ 202006

2. 항목 :  01 ~ 999999 항목 중 일부 항목만 숫자 2자리로 표기

   ​           (Sample Data에서는 6개 항목 Data만 표기, 실제로는 항목 갯수가 수만개임)

3. 국가 : 전세계 국가

4. 지역 : 우리나라 각 지역

5. Value : 국내에서 해당 국가로 연결된 Data 항목의 Volume을 숫자값으로 표현

------

**[YoY, MoM, YTD 산출 대상을 target data로 지정]**

1. 항목 : 6개 항목으로 지정 (11,22,33,44,55,66)
2. 지역 : 2개만 지정(춘천, 용인)
3. YoY, MoM 산출 월 : Data의 가장 최근월 / 전월 / 전년 동월 (202006, 201906, 202005)

------

**[Output Data]**

| 항목 | 지역 |  202006  |  201906  | YoY(%) |  202005  | MoM(%) | 2020_YTD | 2019_YTD | YoY(%) |
| :--: | ---- | :------: | :------: | :----: | :------: | :----: | :------: | :------: | :----: |
|  11  | 춘천 |  3.2269  |    NA    |        |    NA    |        |  5.7929  |  3.2979  |  76%   |
|  22  | 춘천 | 175.0252 | 77.7416  | 125(%) | 250.2582 |  -30%  | 857.5651 | 376.8361 |  128%  |
|  33  | 춘천 | 48.1647  | 27.1325  |  78%   | 62.4561  |  -23%  | 219.6331 | 95.3937  |  130%  |
|  44  | 용인 |  8.8866  |  9.1395  |  -3%   |  8.7396  |   2%   | 58.5109  | 201.1566 |  -71%  |
|  55  | 용인 |  1.0783  |  1.2615  |  -15%  |  0.4194  |  157%  | 88.9573  | 56.4276  |  58%   |
|  66  | 용인 | 38.0396  | 287.8293 |  -87%  | 13.0942  |  191%  | 193.9379 | 2800.967 |  -93%  |

* 2020_YTD(Year To Date) : 2020년 Value 누적값
* 2019_YTD(Year To Date) : 2019년 Value 누적값



첫번째,  **항목 - 지역이 동일한 각 조합에 대해** (모든 나라를 포함 합산하여) 

​               최근 1개월의  **YoY Growth Rate**(전년 동월비, 20년 6월 vs 19년 6월)과

​               **MoM Growth Rate**(동년 전월비, 20년 6월 vs 20년 5월)를  구하고,

두번째,  최근 해당월까지 **2020 누적 Value**와 동월까지의 **2019 누적 Value**의 YoY Growth Rate을 Add하면 됩니다. 

------

그러면 인제 실제로 Data 가공을 해볼까요



**[Data Import]**

```
import io
import pandas as pd

from google.colab import drive
drive.mount('/content/drive')
```

```python
filename = '/content/drive/My Drive/Sample_6 Item.csv'
data = pd.read_csv(filename)
data.head()
```

일단 처음부터 수천여개 항목 - 이백여개 나라 - 백여개 국내 지역의 조합을 분석하는 것은 어려울 것 같고,

오로지 6개 항목만 가지고 와서 Sample Data(Sample_6 Item.csv)를 import한 후,

최근 20년 6월 data에 대해 YoY와 MoM 값을 구해보면서 감을 익히자.



**[Target Data Filtering]**

```python
#isin을 사용하여 특정 조건에 맞는 data만 필터링 한다. 
#loc를 사용하여 지정할수도 있으나, 여기선 isin을 사용함 (https://blog.naver.com/aviyou24/221982852139)

#항목은 6개 항목만, 지역은 춘천과 용인만 대상으로 필터링하여 target으로 지정
target = data[data['항목'].isin(["11","22","33","44","55","66"]) & data['지역'].isin(["강원 춘천시","경기 용인시"])]
```



**[Output 추출을 위한 가공]**

```python
# 월과 지역이 동일한 나라들의 Value sum을 구한다
sum_Value_by_월_항목_지역=target.groupby(['월','항목','지역']).Value.sum()
print(sum_Value_by_월_항목_지역)
```

![](https://blogfiles.pstatic.net/MjAyMDA5MTBfMjIz/MDAxNTk5NzE3NjkyNjg1.QxhQ1wfq45MdPOhgKf0ZfAqQhltAi4D50iC79oAqlzMg.FeA_hyKVn4gSou7wek2ETaB68TGVe7xYaJjVzaUOWSYg.PNG.ikeyada/200910_sample6_groupby.PNG)







```python
# series를 frame으로 변환
df = sum_Value_by_월_항목_지역.to_frame()

#YoY와 MoM Growth Rate은 Pandas 기존 명령어를 사용
df['YoY']=df['Value'].pct_change(12)
df['MoM']=df['Value'].pct_change()
```



![](https://blogfiles.pstatic.net/MjAyMDA5MTBfMSAg/MDAxNTk5NzE3MDM0MjA0.HCRdRVuZrblzNiT6dtevXcffwAGWdyJziSVcKKTPEPsg.634JxZJ8J4uqAfkZ_lblnQKs9e3gb4k6H8J7an-d594g.PNG.ikeyada/200910_sample6_YoY_MoM.PNG)



```
# 패스트캠퍼스 2주차 강의 15. 복합 인덱스와 인덱스 초기화 동영상 강의에서 배운 reset_index를 적용
df2= df.reset_index()
df2
```

![](https://blogfiles.pstatic.net/MjAyMDA5MTBfMTA0/MDAxNTk5NzIyODM1MzE1.mLEd4VdltxLUpLhm87XF6nV4xeAZMT7ITgaMIM_SPBsg.vF3to7j4bWf75ZKEl6cpOer1l2IcnaOfKmCj-l8msYUg.PNG.ikeyada/200910_sample6_reset_index.PNG)



**[본인이 한 부분]**

Sample_6 Item.csv (1개 항목)에 대해 원하는 Data만 필터링하여

Groupby Sum을 구한 후, YoY와 MoM을 구함



**[막혀서 해결이 필요한 부분]**

Output Table을 어떻게 만들어야 할지 감이 안 잡힙니다. 

| 항목 | 지역 |  202006  |  201906  | YoY(%) |  202005  | MoM(%) | 2020_YTD | 2019_YTD | YoY(%) |
| :--: | ---- | :------: | :------: | :----: | :------: | :----: | :------: | :------: | :----: |
|  11  | 춘천 |  3.2269  |    NA    |        |    NA    |        |  5.7929  |  3.2979  |  76%   |
|  22  | 춘천 | 175.0252 | 77.7416  | 125(%) | 250.2582 |  -30%  | 857.5651 | 376.8361 |  128%  |
|  33  | 춘천 | 48.1647  | 27.1325  |  78%   | 62.4561  |  -23%  | 219.6331 | 95.3937  |  130%  |
|  44  | 용인 |  8.8866  |  9.1395  |  -3%   |  8.7396  |   2%   | 58.5109  | 201.1566 |  -71%  |
|  55  | 용인 |  1.0783  |  1.2615  |  -15%  |  0.4194  |  157%  | 88.9573  | 56.4276  |  58%   |
|  66  | 용인 | 38.0396  | 287.8293 |  -87%  | 13.0942  |  191%  | 193.9379 | 2800.967 |  -93%  |



일단 Output의 기준정보를 만든 후 Join으로 풀어나가면 될런지 도움 요청드립니다. 

```python
output=pd.DataFrame ( data={'항목':['11','22','33','44','55','66'],
                            '지역':['강원 춘천시','강원 춘천시','강원 춘천시','경기 용인시','경기 용인시','경기 용인시']}, columns=['항목', '지역'])
output
```

![](https://blogfiles.pstatic.net/MjAyMDA5MTBfNDIg/MDAxNTk5NzI3OTAwMTgz.rPkS1bPBcDoCESWXUGS6FUCymqSLtEh4yy5h8R891Fog.GGRMtnRSk5gJ7b8WSptXmbSIwwh_bA5zHtCluanWbnUg.PNG.ikeyada/200910_output_%EA%B8%B0%EC%A4%80%EC%A0%95%EB%B3%B4_%ED%85%8C%EC%9D%B4%EB%B8%94.PNG)

