# BIG_DATA_PROJECT

## 1️⃣주제 : 계절별 직장인 방문 음식점 업종   

## 2️⃣ 선정 배경
현대의 소비 트렌드와 시장 동향은 빠르게 변화하고 있습니다.<br>
이에 따라 기업들은 정확한 데이터를 기반으로 **지역과 업종별로 소비 규모와 트렌드를 파악**하여 비즈니스 전략을 수립해야 합니다.<br>
본 프로젝트는 이러한 동향을 지역과 업종별로 세분화하여 분석함으로써 기업이 시장 변화에 더 신속하게 대응할 수 있도록 도움을 줄 것입니다.<br>
마지막으로 사회적 영향으로는 지역 경제를 활성화하고 지역사회의 발전을 촉진할 수 있는 도구로 활용될 것입니다.

## 3️⃣ 데이터 수집 및 전처리   
#### **1. JSON 데이터를 호출 후 데이터 프레임을 생성 후 컬럼명을 재정의 해줍니다**
```
# 데이터 호출
df = pd.read_json('https://gist.githubusercontent.com/soeun21216/eb79a06eb23e87890500829e7f1530b9/raw/7ba88b76a1623b3a05f1b6e97e3219e44cdfc775/gistfile1.txt')

# 시군구코드, 시도명 제거
df.drop(['CTGG_CD', 'CTPV_CD'], axis=1, inplace=True)

# 컬럼명을 알아보기 쉽게 변경
df.rename(columns={'INDTY_CGR_NM' : 'TYP_FOD', 'CTPV_NM': 'CITY_NM', 'CTGG_NM' : 'GU_NM', 'VST_RAT' : '방문비율'  }, inplace=True)

# 날씨를 코드에서 계절명으로 변경
df['SEASON_SP_CD'] = df['SEASON_SP_CD'].replace({1: '봄'})
df['SEASON_SP_CD'] = df['SEASON_SP_CD'].replace({2: '여름'})
df['SEASON_SP_CD'] = df['SEASON_SP_CD'].replace({3: '가을'})
df['SEASON_SP_CD'] = df['SEASON_SP_CD'].replace({4: '겨울'})
df
```
#### **2. 결측치가 있는 행을 제거**
```
# 결측치가 있는 행 삭제
df.dropna(inplace=True)

df
```


> ### BEFORE
>  ![image](https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/096607a6-49cc-457f-9a75-28bd72d9954a) 
>  ### AFTER
>  ![image](https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/dc34998e-61a6-44a5-a3cb-28dd75cbbd8d)

## 4️⃣ 데이터 분석 및 시각화

### 4-1) 서울에서 가장 유명한 강남구를 분석하기 위해 강남구의 데이터만 추출하겠습니다.
```
# 서울시 강남구의 데이터만 추출
gangnam_data = df[(df['CITY_NM'] == '서울특별시') & (df['GU_NM'] == '강남구')]

# 연도, 업종별로 그룹화 후 순위의 평균 값 추출
df_gangnam_rank = gangnam_data.groupby(['STD_YEAR', 'TYP_FOD'])['RANK'].mean().round().astype(int)

gangnam_data
```
![image](https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/7cbdad9b-476a-4662-82fb-a26db7adbb46)

### 강남구의 데이터 중 2019년 여름의 데이터만 추출 후 순위별로 정렬 하겠습니다.
```
gangnam_2019s_df = gangnam_data[(gangnam_data['STD_YEAR'] == 2019) & (gangnam_data['SEASON_SP_CD'] == '여름')]

# 데이터 정렬
gangnam_df = gangnam_df.sort_values(by='RANK', ascending=False)
```
![image](https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/03d1f7fd-5b04-4542-8597-c297b93d7da3)

### 2019년 여름 강남구 직장인들이 가장 선호하는 음식은 **분식**이었네요
바쁜 직장인들이 가장 빨리 식사를 해결할 수 있어서가 아닐까요 ? 🤔

### 그렇다면 작년인 2022년 겨울엔 어떤 변화가 이루어 졌을까요 ?    
#### 4-2) 이번엔 순위가 아닌 방문 비율로 확인 하고 방문 비율이 가장 높은 곳과 낮은곳의 비율을 확인해 보겠습니다.

```
2022년 강남구 겨울 데이터 추출
gangnam_2022w_df = gangnam_data[(gangnam_data['STD_YEAR'] == 2022) & (gangnam_data['SEASON_SP_CD'] == '겨울')]

# 방문 비율이 가장 높은 값
max_point = gangnam_2022w_df[gangnam_2022w_df['방문비율'] == gangnam_2022w_df['방문비율'].max()]
# 방문 비율이 가장 낮은 값
min_point = gangnam_2022w_df[gangnam_2022w_df['방문비율'] == gangnam_2022w_df['방문비율'].min()]
```
|방문비율 Max|방문비율 Min|
|--|--|
|<img src="https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/4f315bc7-1f41-4e6b-828a-ecbe3bef6e36" width="400">|<img src="https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/c2956d39-dd39-48bc-9b03-c26916f0bc18" width="400">|

### 4-3) 이번엔 2022년 구로구의 겨울 음식점 방문률을 확인해 보겠습니다.
```
# 구로구 데이터만 추출
guro_2022_winter_df = df[(df['STD_YEAR'] == 2022) & (df['SEASON_SP_CD'] == '겨울') & (df['GU_NM'] == '구로구')]
plt.figure(figsize=(8, 8))
plt.pie(guro_2022_winter_df['방문비율'], labels=guro_2022_winter_df['TYP_FOD'], autopct='%1.1f%%', startangle=140, colors=sns.color_palette('viridis'))
plt.title('2022 서울 구로구 음식점 방문률')
plt.show()
```
![image](https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/729aa4eb-9570-46ab-96b8-8519c7324810)

### 구로구에선 **한식**의 비율이 압도적으로 나왔네요! 😮

### 4-4) 서울시 전체를 기준으로 살펴보겠습니다
```
# Count Plot 생성
plt.figure(figsize=(12, 8))

# 빈도순으로 정렬한 DataFrame 생성
sorted_df = df['TYP_FOD'].value_counts().reset_index()
sorted_df.columns = ['TYP_FOD', '빈도']

# Countplot 그리기
sns.barplot(x='TYP_FOD', y='빈도', data=sorted_df, palette='viridis')
plt.title('음식점 종류별 빈도 Count Plot')
plt.xlabel('음식 종류')
plt.ylabel('빈도')
plt.xticks(rotation=45)  # X축 눈금 레이블 회전
plt.show()
```

![image](https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/6fb5c6ee-7570-49b3-8f6b-8e4dfa8cb889)

#### 서울 전체에서도 **한식**이 1등을 차지했네요 !

### 4-5) # 계절별 음식 업종 방문 비율을 살펴보겠습니다
```
# 계절별로 그룹화,
season_df = pd.DataFrame({
    x_label: group['TYP_FOD'].value_counts()
    for x_label, group in df.groupby('SEASON_SP_CD')
})

# 시각화를 위한 figure와 axes 설정
fig, ax = plt.subplots(figsize=(10, 8))

# 히트맵 생성(annot=True: 비율을 텍스트로 생성)
sns.heatmap(season_df, cmap='viridis', annot=True, fmt='d', linewidths=.5, ax=ax)

# X축 제목
plt.xlabel('계절', fontsize=14)
# Y축 제목
plt.ylabel('음식 업종', fontsize=14)
# 타이틀
plt.title('계절별 음식 업종 방문 비율', fontsize=16)

plt.show()
```
![image](https://github.com/soeun21216/BIG_DATA_PROJECT/assets/113682873/426364e4-1f9c-47a6-afe7-da65163a411a)






