# AiProject

## 1.프로젝트 소개
## 2.범죄자의 직업 비율
## 3.직업별 범죄 발생 분석
## 4.생활정도와 성범죄는 관련이 있는가


# 프로젝트 소개
* 직업별 전체 범죄의 비율
* 직업별 많이 발생하는 범죄의 비율
* 학생에 대한 범행 동기의 비율
* 직업별 생활정도에 따른 범행의 비율

### 사용한 데이터
* 경찰청_미성년자 범행 동기 현황    
* 경찰청_범죄자 직업    
* 대검찰청_성폭력범죄자의 생활 환경과 직업


### 진행방법
* 데이터 수집 => 공공데이터 포털         
* 데이터 가공 및 정제 => pandas
* 분석 및 시각화 => python               
* 데이터 예측(실패) sklearn

# 범죄자의 직업 비율
### 단계 1: 데이터 처리 전 폰트 설치
```
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm

!apt-get -qq -y install fonts-nanum > /dev/null
#fontpath = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'

#font = fm.FontProperties(fname=fontpath, size=9)

#fm._rebuild()



fe = fm.FontEntry(
    fname=r'/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf', # ttf 파일이 저장되어 있는 경로
    name='NanumGothic')                         # 이 폰트의 원하는 이름 설정
fm.fontManager.ttflist.insert(0, fe)              # Matplotlib에 폰트 추가
plt.rcParams.update({'font.size': 18, 'font.family': 'NanumGothic'}) # 폰트 설치
```
### 단계 2: 런타임 재시작
```
import os
os.kill(os.getpid(), 9)  
```
### 단계 3: 한글 폰트 설정
```
import matplotlib.pyplot as plt
import matplotlib as mpl
import matplotlib.font_manager as fm

# 마이너스 표시 문제
mpl.rcParams['axes.unicode_minus'] = False

# 한글 폰트 설정
fe = fm.FontEntry(
    fname=r'/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf', # ttf 파일이 저장되어 있는 경로
    name='NanumGothic')                        # 이 폰트의 원하는 이름 설정
fm.fontManager.ttflist.insert(0, fe)              # Matplotlib에 폰트 추가
plt.rcParams.update({'font.size': 18, 'font.family': 'NanumGothic'}) # 폰트 설
```

### 경찰청 범죄자 직업 데이터 프레임
```
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

filename = '/content/경찰청_범죄자 직업_12_31_2020.csv'
dforiginal = pd.read_csv(filename, encoding='cp949')

dforiginal.head()
범죄대분류	범죄중분류	자영업(소계)	자영업(농.임.수산업)	자영업(광업)	자영업(제조업)	자영업(건설업)	자영업(도.소매업)	자영업(무역업)	자영업(요식업)	...	전문직(기타전문직)	공무원	기타(소계)	기타(학생)	기타(주부)	기타(전경.의경)	기타(공익요원)	기타(기타)	무직자	미상
0	강력범죄	살인기수	34	4	0	0	4	0	0	1	...	2	4	39	5	10	0	0	24	155	28
1	강력범죄	살인미수등	50	6	0	0	7	0	1	4	...	3	4	61	14	9	0	1	37	184	47
2	강력범죄	강도	87	2	0	0	8	0	1	0	...	3	1	355	239	6	0	0	110	509	66
3	강력범죄	강간	761	77	0	7	57	7	1	32	...	115	44	1543	699	4	2	9	829	1296	638
4	강력범죄	유사강간	115	11	0	2	7	1	0	6	...	21	3	269	135	1	2	3	128	204	94
```

### 직업별 범죄 발생 건수 데이터 프레임
```
filename = '/content/경찰청_범죄자 직업_12_31_2020.csv'
dforiginal = pd.read_csv(filename, encoding='cp949')
df = dforiginal.drop(['범죄대분류'],axis=1)
df = df.groupby('범죄중분류').sum()

df.head()
	자영업(소계)	자영업(농.임.수산업)	자영업(광업)	자영업(제조업)	자영업(건설업)	자영업(도.소매업)	자영업(무역업)	자영업(요식업)	자영업(숙박업)	자영업(유흥업)	...	전문직(기타전문직)	공무원	기타(소계)	기타(학생)	기타(주부)	기타(전경.의경)	기타(공익요원)	기타(기타)	무직자	미상
범죄중분류																					
강간	761	77	0	7	57	7	1	32	17	19	...	115	44	1543	699	4	2	9	829	1296	638
강도	87	2	0	0	8	0	1	0	2	7	...	3	1	355	239	6	0	0	110	509	66
강제추행	2246	198	0	42	160	15	11	102	21	45	...	436	263	3590	1294	25	2	13	2256	3321	1513
공갈	398	30	0	12	43	1	3	16	2	7	...	38	7	1105	735	30	0	0	340	913	428
기타 강간 강제추행등	39	10	0	0	6	0	0	2	2	1	...	1	0	119	68	2	0	1	48	90	18
```

### 직업별 범죄 비율 원형 그래프 생성
```
#나누기 2를 해서 소계만 구함
self_employment = (df['자영업(소계)'].sum().sum())/2
employee = df['피고용자(소계)'].sum().sum()/2
professional = df['전문직(소계)'].sum().sum()/2
student = df['기타(학생)'].sum().sum()/2
unemployed = df['무직자'].sum().sum()/2

all_sum = self_employment+employee+professional+student+unemployed

print(self_employment)
print(employee)
print(professional)
#print(public_official)
print(student)
print(unemployed)
print(all_sum)
print()
ratio = [158124.0,239445.5,25037.5,43688.5,187727.0]
labels=['자영업','피고용자','전문직','학생','무직자']
explode = [0,0,0, 0.1,0.0]

plt.pie(ratio, labels=labels, explode=explode,autopct='%.1f%%', textprops={'fontsize': 10})
plt.show()
```
![image](https://github.com/corin2junseo/AiProject/assets/96821559/dbbb8b92-0e20-44e6-9078-9ef9a8ef8486)


# 직업별 범죄 분석 
* 자영업자의 범죄 분석
```
# '자영업'에 해당하는 칼럼들만 선택하여 각 칼럼의 합 계산
self_employment_columns = ['자영업(소계)', '자영업(농.임.수산업)', '자영업(광업)', '자영업(제조업)', '자영업(건설업)',
                           '자영업(도.소매업)', '자영업(무역업)', '자영업(요식업)', '자영업(숙박업)', '자영업(유흥업)']

# '자영업'에 해당하는 칼럼들 중 상위 5개 직업 열 선택
top_5_self_employment = df[self_employment_columns].sum().nlargest(6).index[1:].tolist()

# 상위 5개 직업 열을 이용하여 그래프 그리기
df.loc['강간':'기타 강간 강제추행등', top_5_self_employment].plot(kind='bar', stacked=True, figsize=(12, 8))

plt.title('범죄자 직업별 범죄 중분류에 따른 분포')
plt.xlabel('범죄 중분류')
plt.ylabel('범죄자 직업 합계')

plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))

plt.show()
```
![image](https://github.com/corin2junseo/AiProject/assets/96821559/cb70026a-1bf9-435b-bca9-6bc1b4643393)

* 피고용자의 범죄 분석
```
# '피고용자'에 해당하는 칼럼들만 선택하여 각 칼럼의 합 계산
employee_columns = ['피고용자(교원(사립))', '피고용자(사무원)', '피고용자(기술자)', '피고용자(점원)',
                    '피고용자(공원)', '피고용자(운전사)', '피고용자(경비원)', '피고용자(외판원)', '피고용자(국공영기업체직원)',
                    '피고용자(일반회사원)', '피고용자(금융기관직원)', '피고용자(유흥업종사자)', '피고용자(요식업종사자)', '피고용자(일용노동자)']

# '피고용자'에 해당하는 칼럼들 중 상위 5개 직업 열 선택
top_5_employee = df[employee_columns].sum().nlargest(5).index.tolist()

# 상위 5개 직업 열을 이용하여 그래프 그리기
df.loc['강간':'기타 강간 강제추행등', top_5_employee].plot(kind='bar', stacked=True, figsize=(12, 8))

plt.title('범죄자 직업별 범죄 중분류에 따른 분포')
plt.xlabel('범죄 중분류')
plt.ylabel('범죄자 직업 합계')

plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))

plt.show()
```
![image](https://github.com/corin2junseo/AiProject/assets/96821559/cf383870-858b-42e3-a42c-44466bf1ec4a)

* 전문직의 범죄 분석
```
# '전문직'에 해당하는 칼럼들만 선택하여 각 칼럼의 합 계산
professional_columns = ['전문직(의사)', '전문직(변호사)', '전문직(교수)', '전문직(종교가)', '전문직(언론인)', '전문직(예술인)']

# '전문직'에 해당하는 칼럼들 중 상위 5개 직업 열 선택
top_5_professional = df[professional_columns].sum().nlargest(5).index.tolist()

# 상위 5개 직업 열을 이용하여 그래프 그리기
df.loc['강간':'기타 강간 강제추행등', top_5_professional].plot(kind='bar', stacked=True, figsize=(12, 8))

plt.title('범죄자 직업별 범죄 중분류에 따른 분포')
plt.xlabel('범죄 중분류')
plt.ylabel('범죄자 직업 합계')

plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))

plt.show()
```
![image](https://github.com/corin2junseo/AiProject/assets/96821559/17ddf1f2-ea9b-4b98-a75a-cf6bfa412b75)

* 학생의 범죄 분석
```
# '기타(학생)' 열의 상위 2위부터 6위까지 데이터 가져오기
top_2_to_6_students = df['기타(학생)'].nlargest(6).iloc[1:]

# 색상
colors = ['gold', 'lightcoral', 'lightskyblue', 'lightgreen', 'orange']

# 원형 그래프 그리기
plt.figure(figsize=(8, 8))
plt.pie(top_2_to_6_students, labels=top_2_to_6_students.index, colors=colors, autopct='%1.1f%%', startangle=140)
plt.axis('equal')  # 원을 원형으로 보이기 위해
plt.title('기타(학생) 상위 2위부터 6위까지의 비율',pad=20)
plt.show()
```
![image](https://github.com/corin2junseo/AiProject/assets/96821559/24fb3a30-d3e9-47b0-832a-931155e6e6d8)
* 학생의 범죄 동기
```
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

filename = '/content/경찰청_미성년자 범행 동기 현황_20201231.csv'
dforiginal2 = pd.read_csv(filename, encoding='cp949')
df2 = dforiginal2.drop(['기타','총합'], axis=1)

# '연도' 열을 인덱스로 설정하여 플로팅
df2.set_index('연도', inplace=True)

df2.plot(kind='bar', stacked=True, figsize=(10, 10))

plt.title('미성년자 범행 동기')
plt.xlabel('연도')
plt.ylabel('범죄 동기')

plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))

plt.show()
```
![image](https://github.com/corin2junseo/AiProject/assets/96821559/9c244d83-a336-40ab-86fe-21dc30eda2a3)

# 생활정도와 성범죄 관련성
### 검찰청 성폭력 범죄자의 생활환경과 직업 데이터셋
```
import pandas as pd
import matplotlib.pyplot as plt

# 데이터 불러오기
filename = '/content/대검찰청_성폭력범죄자의 생활환경과 직업_20171231.csv'
df3 = pd.read_csv(filename, encoding='cp949')
df3.head()


직업별	생활정도_하류	생활정도_중류	생활정도_상류	생활정도_미상	유배우자	동거	이혼	사별	혼인관계_미상	실(양)부모	계부모	실부계모	실부무모	실모계부	실모무부	계부무모	계모무부	무부모	미혼자부모관계_미상
0	자영업(농.임.수산업)	177	143	5	76	194	13	53	11	76	24	0	2	1	1	14	0	0	12	0
1	자영업(제조업)	47	41	10	17	61	2	6	2	17	23	0	0	0	0	2	0	0	1	1
2	자영업(건설업)	112	124	11	93	140	6	48	2	95	25	0	0	3	1	12	0	0	8	0
3	자영업(도.소매업)	22	28	3	17	32	1	3	0	17	12	0	0	0	1	2	0	0	2	0
4	자영업(무역업)	8	16	1	10	14	0	3	0	10	7	0	0	0	0	1	0	0	0	0
```

### 직업별 발생 건수 계산 데이터셋
```
# 직업별 전체 발생 건수 계산
job_counts = df3.groupby('직업별').size().sort_values(ascending=False)

# 상위 1위부터 10위까지의 직업 리스트
top_10_jobs = job_counts.head(10).index.tolist()

# 전체 발생 건수 상위 1위부터 10위까지의 직업만 필터링하여 가져오기
top_10_jobs_data = df3[df3['직업별'].isin(top_10_jobs)]
```
### 해당 데이터셋을 이용해 피벗으로 그래프 생성
```
# 필터링한 데이터를 피벗하여 그래프 생성
pivot_df = top_10_jobs_data.pivot_table(index='직업별', values=['생활정도_하류', '생활정도_중류', '생활정도_상류'])

# 그래프 생성
pivot_df.plot(kind='bar', stacked=True, figsize=(12, 8))
plt.title('상위 직업별 생활정도 분포')
plt.xlabel('직업별')
plt.ylabel('생활정도')
plt.legend(loc='center left', bbox_to_anchor=(1, 0.5))
plt.show()

```
![image](https://github.com/corin2junseo/AiProject/assets/96821559/0fcccce0-0274-4051-87c9-434ad68aee12)









[![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=corin2junseo&layout=compact)](https://github.com/corin2junseo/github-readme-stats)
