# AiProject

## 1.프로젝트 소개
## 2.범죄자의 직업 비율
## 3.직업별 범죄 발생 분석
## 4.생활정도와 성범죄는 관련이 있는가


# 프로젝트 소개
직업별 전체 범죄의 비율
직업별 많이 발생하는 범죄의 비율
학생에 대한 범행 동기의 비율
직업별 생활정도에 따른 범행의 비율

### 사용한 데이터
* 경찰청_미성년자 범행 동기 현황    
* 경찰청_범죄자 직업    
* 대검찰청_성폭력범죄자의 생활 환경과 직업


### 진행방법
* 데이터 수집 => 공공데이터 포털         
* 데이터 가공 및 정제 => pandas
* 분석 및 시각화 => python               
* 데이터 예측(실패) sklearn

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





[![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=corin2junseo&layout=compact)](https://github.com/corin2junseo/github-readme-stats)
