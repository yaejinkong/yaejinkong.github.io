---
layout: post
title:  "[개인 프로젝트] 기온별 옷차림 추천 웹 사이트 'wea(the)r'"
date:   2023-09-14 20:43:06 +0900
categories: Projects
published: true
---

---
# 📍 주제 선정 동기

  이번 프로젝트에서는 현재 사람들이 일상생활에서 종종 겪고 있는 문제점을 해결하고자 하는 목적을 가지고 주제를 선정하였다. 최근 기후변화로 인한 일교차의 심화와 미세먼지로 마스크 착용이 필수적인 상황이 늘어나고 있음에, 사람들이 어떤 옷을 입어야 할지 마스크를 착용해야 할지에 대한 고민이 많아졌다. 이에 오늘의 날씨 정보를 기반으로 적절한 옷차림과 마스크 착용 여부를 추천하는 웹 사이트를 구현함으로써 일상생활의 편의성을 높이고 사람들이 건강하고 멋진 삶을 보낼 수 있도록 돕고자 한다.


# 📍 기대 효과

  이 웹 사이트는 사용자들의 옷차림 선택에 대한 고민을 덜어주면서 일상의 편리함을 도모할 수 있다. 더불어, 팀원들은 프로젝트에서 해당 기술 스택들 을 배우고 이를 활용하면서 개인적인 성장을 이루어낼 수 있다. 또한 실제 사용자들의 요구사항을 파악하고 이에 맞게 개발하는 과정을 거치면서 향후 다양한 프로젝트에서 필요한 역량을 갖추게 될 것이다. 이러한 기대효과를 통해 이번 웹 사이트 개발 프로젝트는 사용자들과 팀원들 모두에게 긍정적 인 영향을 미칠 것이다.


# 📍 최종 목표
  `“기온별 옷차림 추천 웹 사이트 개발”`

  + HTML, CSS, JavaScript로 기능 구현
  + 사용자가 기온별 추천 옷차림 및 마스크 착용 유무를 한눈에 알아볼 수 있도록 시각화된 UI 구현
  + API를 활용하여 사용자의 지역에 따른 실시간 날씨 정보 업데이트 
  + 필요시 추가 기능 제공


# 📍 프로젝트의 내용

사용자가 메인 페이지에서 거주 지역을 선택하면 결과 페이지로 이동하게 된다. 결과 페이지에서 거주 지역에 대한 현재 날씨와 미세먼지 농도 등을 알려주고, 밑으로 스크롤 하면 날씨에 맞는 추천 남/여 옷차림을 보여준다. 또한 현재 비가 오면 우산을 챙기라는 문구, 미세먼지가 농도가 높다면 마스크를 챙기라는 문구를 사용자에게 보여준다. 이후 사용자가 다른 지역을 확인해보고 싶으면 돌아가기 버튼을 통해 웹사이트를 다시 실행시킬 수 있다.


# 📍 주요 코드 및 설명

## OpenAPI 선정 과정
날씨 정보를 받아오기 위해 먼저 기상청 OpenAPI를 받아오려고 했으나, 기상청 API의 경우 데이터를 하는 지역의 좌표로 API call을 해야 했기 때문에 원하는 지역의 상세 좌표를 모두 알고 있어야 했고, 또한 날씨 뿐만이 아닌 강수량, 풍향, 파고 등 우리가 사용하지 않을 데이터들이 많이 포함되어 있었고 시간대별로 방대한 양의 데이터가 있어서 이용하기 까다로웠다.
OpenWeatherMap에서 제공하는 CurrentWeatherAPI는 날씨 데이터를 원하는 지역 이름으로 현재 날씨 데이터를 호출할 수 있었고, 현재 날씨, 최고/최저 기온, 해당지역의 상세 좌표값 등을 기상청에서 제공하는 OpenAPI보다 더 간편하게 받아 올 수 있어 OpenWeatherMap API를 사용하게 되었다. 
추가 기능을 논의하던 중 미세먼지 농도에 따라 마스크를 추천하는 기능도 추가하기로 했다. 같은 OpenWeatherMap에서 AirPollutionAPI를 제공 하였는데 해당 API는 정보를 원하는 지역 이름이 아닌 좌표값으로 호출을 해야 했기 때문에 CurrentWeatherAPI에서 위도 · 경도 정보를 받아와 AirPollutionAPI를 호출하는데 활용하였다.

## API 호출 주요 코드 설명

날씨와 미세먼지 API를 받아오기 위한 API Key와 API call URL 
```javascript
const apiKey = "02b49f3f0bfb423b6ec8846c057d4ba4";
const apiUrl = "https://api.openweathermap.org/data/2.5/weather?units=metric&q=";

const apiUrl_dust = "http://api.openweathermap.org/data/2.5/air_pollution?"; //lat={lat}&lon={lon}&appid={API key}
```

q Parameter에 도시 이름을 넣어주면 해당 도시의 Current Weather 정보를 아래와 같은 JSON 형태로 받아온다.
```json
coord	
lon	126.9778
lat	37.5683
weather	
0	
id	800
main	"Clear"
description	"clear sky"
icon	"01d"
base	"stations"
main	
temp	27.56
feels_like	27.48
temp_min	23.69
temp_max	29.66
pressure	1004
humidity	43
visibility	10000
wind	
speed	2.57
deg	220
clouds	
all	0
dt	1686292692
sys	
type	1
id	8105
country	"KR"
sunrise	1686255044
sunset	1686307914
timezone	32400
id	1835848
name	"Seoul"
cod	200
```

받아온 데이터 중 원하는 데이터를 querySelector을 이용해 HTML의 클래스에 반환해준다.
```javascript
async function checkWeather(city){
    const response = await fetch(apiUrl + city + `&appid=${apiKey}`);
    
    if(response.status == 404){
        document.querySelector(".error").style.display= "block";
        document.querySelector(".weather").style.display= "none";
    } else {
        var data = await response.json();
        
        document.querySelector(".city").innerHTML = data.name;
        document.querySelector(".temp").innerHTML = data.main.temp + "°C";
        document.querySelector(".humidity").innerHTML = data.main.humidity + "%";
        document.querySelector(".wind").innerHTML = data.wind.speed + " km/h";
```

Current Weather API에서 받아온 해당 위치의 상세 좌표값을 이용해 AirPollution API call을 해서 미세먼지 데이터를 받아온다.
```javascript
lat_ = data.coord.lat;
lon_ = data.coord.lon;

const response_dust = await fetch(apiUrl_dust + "lat=" + lat_ + "&lon=" + lon_ + "&appid=" + apiKey);
```

날씨와 미세먼지 농도에 따라서 화면에 날씨, 우산, 마스크 정보를 출력하는 case를 나누어 주었다. 
```javascript
if(data_dust.list[0].components.pm10 < 20) {
            degree = "미세먼지 좋음";
            document.querySelector(".mask").innerHTML = "마스크를 챙기지 않아도 됩니다!";
        }
        else if(data_dust.list[0].components.pm10 < 45) {
            degree = "미세먼지 보통";
            document.querySelector(".mask").innerHTML = "마스크를 챙기지 않아도 됩니다!";

        }
        else if(data_dust.list[0].components.pm10 < 75) {
            degree = "미세먼지 나쁨";
            document.querySelector(".mask").innerHTML = "공기가 안좋으니 마스크를 챙기세요!";
        }

        else if(data_dust.list[0].components.pm10 >= 75 ) {
            degree = "미세먼지 매우나쁨";
            document.querySelector(".mask").innerHTML = "공기가 안좋으니 마스크를 챙기세요!";
        }
```
```javascript
if(data.weather[0].main == "Clouds") {
            weatherIcon.src = "./image/clouds.png";
            document.querySelector(".umbrella").innerHTML = "우산을 챙기지 않아도 됩니다!";
        }
        else if(data.weather[0].main == "Clear") {
            weatherIcon.src = "./image/clear.png";
            document.querySelector(".umbrella").innerHTML = "우산을 챙기지 않아도 됩니다!";
        }
        else if(data.weather[0].main == "Rain") {
            weatherIcon.src = "./image/rain.png";
            document.querySelector(".umbrella").innerHTML = "비가오니 우산을 챙기세요!";
        }
        else if(data.weather[0].main == "Drizzle") {
            weatherIcon.src = "./image/drizzle.png";
            document.querySelector(".umbrella").innerHTML = "우산을 챙기지 않아도 됩니다!";
        }
        else if(data.weather[0].main == "Mist") {
            weatherIcon.src = "./image/mist.png";
            document.querySelector(".umbrella").innerHTML = "우산을 챙기지 않아도 됩니다!";
        }
```


## 기온에 따른 옷차림 추천 기능 코드

기온에 따른 옷차림을 추천하는 기능을 구현하는 JavaScript 코드를 작성하였다. 먼저, API에서 받아온 날씨 데이터를 활용하기 위해 'todayClothes'라는 함수를 정의하였다. 이 함수는 기온 데이터를 입력으로 받아, 그 값에 따른 적절한 옷차림을 추천한다. 기온을 분석하는 과정에서는 구체적으로 'midWinter', 'winter', 'startWinter', 'fall', 'startFall', 'startSummer', 'summer', 'midSummer'로 총 8개의 범위를 설정하였다. 각 범위는 특정 기온 값을 기준으로 분류되며, 이를 통해 기온에 따른 옷차림을 세분화하여 추천한다. 이후 각 조건문에서는 해당 기온 범위에 맞는 옷차림 이미지와 메시지를 페이지에 출력하도록 하였다. 이를 수행하기 위해 'querySelector' 함수를 활용해 HTML 요소에 접근하였고, 해당 요소의 'src'과 'innerHTML' 속성을 업데이트하여 기온에 따른 옷차림 이미지와 메시지를 제공한다.

```javascript
const clothIcon = document.querySelector(".cloth-icon");

function todayClothes(data) {
    let todayTemp = data.main.temp;

    let midWinter = todayTemp <= 4;
    let winter = todayTemp >= 5 && todayTemp < 9;
    let startWinter = todayTemp >= 9 && todayTemp < 12;
    let fall = todayTemp >= 12 && todayTemp < 17;
    let startFall= todayTemp >= 17 && todayTemp < 20;
    let startSummer = todayTemp >= 20 && todayTemp < 23;
    let summer = todayTemp >= 23 && todayTemp < 28;
    let midSummer = todayTemp >= 28;

    if(midWinter) {
        clothIcon.src = "./image/midwinter.png";
        document.querySelector(".cloth-text1").innerHTML = "얼죽아도 고민하게 만드는 매서운 겨울 날씨!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 패딩, 롱패딩, 두꺼운코트, 누빔옷, 털모자, 목도리, 장갑, 두꺼운 기모제품<br><br>히트텍, 내복, 핫팩 등을 이용해서 체온 조절을 잘 해주세요!";
    }
    else if(winter) {
        clothIcon.src = "./image/winter.png";
        document.querySelector(".cloth-text1").innerHTML = "초겨울 날씨로 히트텍과 두꺼운 겉옷을 꺼내야 할 때!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 경량패딩, 조끼패딩, 가죽자켓, 두꺼운 자켓, 히트텍, 코트,<br> 무스탕, 두꺼운 니트, 기모바지, 청바지";
    }
    else if(startWinter) {
        clothIcon.src = "./image/startwinter.png";
        document.querySelector(".cloth-text1").innerHTML = "트렌치코트가 어울리는 날씨!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 항공점퍼, 트렌치코트, 야상, 두꺼운 가디건, 점퍼, 재킷, 니트,<br>긴바지, 청바지, 면바지, 기모바지, 스타킹";
    }
    else if(fall) {
        clothIcon.src = "./image/fall.png";
        document.querySelector(".cloth-text1").innerHTML = "온도차가 큰 환절기!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 바람막이, 간절기야상, 얇은 코트, 간절기 외투, 재킷, 두꺼운 가디건,<br>자켓, 청자켓, 니트, 후드티, 맨투맨, 청바지, 면바지, 치마, 얇은 스타킹<br><br>얇은 옷을 여러 겹 겹쳐 입는 것을 추천합니다!";
    }
    else if(startFall) {
        clothIcon.src = "./image/startfall.png"
        document.querySelector(".cloth-text1").innerHTML = "활동하기 좋은 봄가을의 날씨!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 얇은 자켓, 얇은 가디건, 바람막이, 후드티, 셔츠, 얇은 니트,<br>맨투맨, 긴팔티, 긴바지, 면바지, 청바지, 슬랙스, 원피스<br><br>일교차가 커서 얇은 외투를 챙기는 것이 좋습니다!";
    }
    else if(startSummer) {
        clothIcon.src = "./image/startsummer.png";
        document.querySelector(".cloth-text1").innerHTML = "점점 더워지는 늦봄 날씨!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 얇은 셔츠, 얇은 가디건, 블라우스, 긴팔티, 청바지, 슬랙스, 원피스";
    }
    else if(summer) {
        clothIcon.src = "./image/summer.png";
        document.querySelector(".cloth-text1").innerHTML = "본격적인 여름 더위의 시작!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 반팔티, 반팔 셔츠, 반팔 니트, 반바지, 얇은 긴바지";
    }
    else if(midSummer) {
        clothIcon.src = "./image/midsummer.png";
        document.querySelector(".cloth-text1").innerHTML = "지금은 한여름! 무더위가 기승, 불쾌지수 폭등!";
        document.querySelector(".cloth-text2").innerHTML = "추천 옷차림: 민소매, 반팔티, 크롭티, 반바지, 치마, 린넨, 민소매 원피스, 마 소재 옷<br><br>외출 시 자외선 차단제를 바르는 것을 잊지 마세요!<br>실내에서는 에어컨을 강하게 틀 수 있으니 얇은 아우터를 챙기는 것도 좋습니다!";
    }
```


## 돌아가기 버튼 주요 코드 설명

만약 사용자가 돌아가기 버튼을 클릭한다면 결과화면의 display를 꺼주고 메인화면의 display를 켜준다.
```javascript
function doaction(){
    main.style.animation="fadeIn 1s";
    setTimeout(() => {
        result.style.animation="fadeOut 1s";
        setTimeout(() => {
            main.style.display = "block";  //display 꺼줌
            result.style.display = "none";  //display 켜줌

            const selected = document.querySelector('.dropdown .selected');
            selected.innerText = "-지역 선택-";
            const options = document.querySelectorAll('.dropdown .menu li');
            options.forEach(option => {
            option.classList.remove('active');
         })
        }, 450)
    }, 450);
}
```


# 📍 결과 화면 및 설명

결과 페이지는 다음과 같이 두 섹션으로 나누어진다. 
<center><img src = "https://github.com/yaejinkong/yaejinkong.github.io/assets/127467781/db67e081-0bd1-449e-8b07-565973c97c45" /></center>
<center><span style='background-color: #f6f8fa'>오늘의 날씨 정보 섹션</span></center>

<center><img src = "https://github.com/yaejinkong/yaejinkong.github.io/assets/127467781/6c09a189-0aa3-477d-a428-e62d47e36a7e" /></center>
<center><span style='background-color: #f6f8fa'>옷차림 추천 섹션</span></center>




---


