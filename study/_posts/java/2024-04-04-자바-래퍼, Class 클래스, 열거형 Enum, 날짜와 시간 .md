---
layout: post
title:  "[자바] 래퍼, Class 클래스, 열거형 Enum, 날짜와 시간"
date:   2024-04-017 20:43:06 +0900
categories: 
            - Study
            - java
published: true
---

---
# 16. 래퍼, Class 클래스

# 17. 열거형 - Enum

# 18. 날짜와 시간

## 기본 날짜와 시간 - LocalDateTime
- 특정 지역의 날짜와 시간만 고려할 때 사용한다.
  - 세계 시간대를 고려하지 않아서 타임존은 적용되지 않는다. 
- <span style="background-color: #fff5b1">모든 날짜 클래스는 불변 ! 변경 발생 시 새로운 객체를 생성해서 반환하므로 반환값을 반드시 받아야 한다!<span>    
<br>
- <strong>LocalDate</strong> : 날짜만 표현할 때 사용한다. 년, 월, 일을 다룬다. 예) 2013-11-21
- <strong>LocalTime</strong> : 시간만 표현할 때 사용한다. 시, 분, 초를 다룬다. 예) 08:20:30.213
  - 초는 밀리초, 나노초 단위도 포함할 수 있다.
- <strong>LocalDateTime</strong> : LocalDate와 LocalTime을 합한 개념이다. 예) 2013-11-21T08:20:30.213

### LocalDate

  ~~~java
  LocalDate nowDate = LocalDate.now();
  LocalDate ofDate = LocalDate.of(2013, 11, 21);
  System.out.println("오늘 날짜= " + nowDate); // 2024-02-09
  System.out.println("지정 날짜= " + ofDate); // 2013-11-21

  // 계산(불변)
  ofDate = ofDate.plusDays(10);
  System.out.println("지정날짜 + 10d = " + ofDate); // 2013-12-01
  ~~~

  - now() : 현재 시간을 기준으로 생성한다.
  - of(...) : 특정 날짜를 기준으로 생성한다. 년, 월, 일을 입력할 수 있다.
  - plusDays() : 특정 일을 더한다.
     

### LocalTime
  ~~~java
  LocalTime nowTime = LocalTime.now();
  LocalTime ofTime = LocalTime.of(9, 10, 30);

  System.out.println("현재 시간 = " + nowTime); // 11:52:51.219602
  System.out.println("지정 시간 = " + ofTime); // 09:10:30

  // 계산(불변)
  LocalTime ofTimePlus = ofTime.plusSeconds(30);
  System.out.println("지정 시간 + 30s = " + ofTimePlus); // 09:11:00
  ~~~

  - plusSeconds() : 특정 초를 더한다. 

### LocalDateTime

  ~~~java
  LocalDateTime nowDt = LocalDateTime.now();
  LocalDateTime ofDt = LocalDateTime.of(2016, 8, 16, 8, 10, 1);

  // 날짜 시간 분리
  LocalDate localDate = ofDt.toLocalDate(); // 2016-08-16
  LocalTime localTime = ofDt.toLocalTime(); // 08:10:01

  // 날짜와 시간 합체
  LocalDateTime localDateTime = LocalDateTime.of(localDate, localTime); // 2016-08-16T08:10:01

  // 계산(불변)
  LocalDateTime ofDtPlus = ofDt.plusDays(1000);
  System.out.println("지정 날짜시간+1000d = " + ofDtPlus); // 2019-05-13T08:10:01
  LocalDateTime ofDtPlus1Year = ofDt.plusYears(1);
  System.out.println("지정 날짜시간+1년 = " + ofDtPlus1Year); // 2017-08-16T08:10:01

  // 비교
  System.out.println("현재 날짜시간이 지정 날짜시간보다 이전인가? " + nowDt.isBefore(ofDt)); // false
  System.out.println("현재 날짜시간이 지정 날짜시간보다 이후인가? " + nowDt.isAfter(ofDt)); // true
  System.out.println("현재 날짜시간과 지정 날짜시간이 같은가? " + nowDt.isEqual(ofDt)); // false
  ~~~

  - LocalDate와 LocalTime을 `toXxx()` 메서드로 분리할 수 있다. 
  - LocalDateTime.of(localDate, localTime) : 날짜와 시간을 사용해서 LocalDateTime을 만든다.
  - isBefore(), isAfter(), isEquals() : 다른 날짜와 시간을 비교한다
    - isEquals() : 단순히 비교 대상이 시간적으로 같으면 true를 반환한다.
    - equals() : 객체의 타입, 타임존 등 내부 데이터의 모든 구성요소가 같아야 true를 반환한다.
<br>

## 타임존 - ZonedDateTime
- "Asia/Seoul" 같은 타임존 안에는 일광 절약 시간제에 대한 정보와 UTC+9:00와 같은 UTC로부터 시간 차이인 오프셋 정보를 모두 포함한다. 

### ZoneId
- <strong>ZoneId</strong> : 내부에 일광 절약 시간 관련 정보, UTC와의 오프셋 정보를 포함한다. 
- ZoneId.systemDefault() : 시스템이 사용하는 기본 ZoneId를 반환한다. 
  - `ZoneId zoneId = ZoneId.systemDefault(); // Asia/Seoul` 
- ZoneId.of() : 타임존을 직접 제공하여 ZoneId를 반환한다. 
  - `ZoneId seoulZoneId = ZoneId.of("Asia/Seoul") // Asia/Seoul`

### ZonedDateTime
- <strong>ZonedDateTime</strong> : LocalDateTime에 시간대 정보인 ZoneId가 합쳐진 것이다. 
- 시간대를 고려한 날짜와 시간을 표현할 때 사용한다. 

  ~~~java
  ZonedDateTime nowZdt = ZonedDateTime.now(); // 2024-04-17T14:37:59.970734+09:00[Asia/Seoul]

  LocalDateTime ldt = LocalDateTime.of(2030, 1, 1, 13, 30, 50);
  ZonedDateTime zdt1 = ZonedDateTime.of(ldt, ZoneId.of("Asia/Seoul"));
  System.out.println("zdt1 = " + zdt1); // 2030-01-01T13:30:50+09:00[Asia/Seoul]

  ZonedDateTime zdt2 = ZonedDateTime.of(2030, 1, 1, 13, 30, 50, 0, ZoneId.of("Asia/Seoul"));
  System.out.println("zdt2 = " + zdt2); // 2030-01-01T13:30:50+09:00[Asia/Seoul]

  ZonedDateTime utcZdt = zdt2.withZoneSameInstant(ZoneId.of("UTC"));
  System.out.println("utcZdt = " + utcZdt); // 2030-01-01T04:30:50Z[UTC]
  ~~~
  
  - withZoneSameInstant(ZoneId) : 타임존을 변경하고, 타임존에 맞추어 시간도 함께 변경된다. 

### OffsetDateTime
- <strong>OffsetDateTime</strong> : LocalDateTime에 UTC 오프셋 정보인 ZoneOffset이 합쳐진 것이다. 
- 시간대를 고려한 날짜와 시간을 표현할 때 사용한다.
- 타임존은 없기 때문에 일광 절약 시간제가 적용되지 않는다.

  ~~~java
  OffsetDateTime nowOdt = OffsetDateTime.now(); // 2024-04-17T14:51:05.917443+09:00
  LocalDateTime ldt = LocalDateTime.of(2030, 1, 1, 13, 30, 50);
  OffsetDateTime odt = OffsetDateTime.of(ldt, ZoneOffset.of("+01:00")); // 2030-01-01T13:30:50+01:00
  ~~~

  - ZoneOffset : +01:00처럼 UTC와의 시간차이인 오프셋 정보만 보관한다.
<br>

## Instant
- <strong>Instant</strong> : UTC(협정 세계시)를 기준으로 하는 시간의 한 지점을 나타낸다.
- 날짜와 시간을 나노초 정밀도로 표현한다.
  - Instant 내부에는 초 데이터만 들어있다.
- 1970년 1월 1일 0시 0분 0초(UTC 기준)를 기준으로 경과한 시간으로 계산된다. 
<br>

### Instant의 특징
- Instant는 Epoch 시간을 다루는 클래스이다.
  - Epoch 시간 : 1970년 1월 1일 00:00:00 이후로 경과한 전체 초의 수로, 시간대에 영향을 받지 않는 절대적인 시간 표현 방식
- 장점
  - UTC 기준으로, 시간대에 영향을 받지 않는다. 전세계 어디서나 동일한 시점을 가리킬 수 있다.
  - 고정된 기준점 (1970년 1월 1일 UTC)을 가져 시간 계산 및 비교가 명확하고 일관된다.
- 단점
  - Instant는 기계적인 시간 처리에는 적합하지만 사람이 읽고 이해하기에는 직관적이지 않다.
  - 시간대 정보가 포함되지 않아, 특정 지역의 날짜와 시간으로 변환하려면 추가 작업이 필요하다.
  - 날짜 계산이 필요하면 LocalDateTime이나 ZonedDateTime을 사용해야한다.
- 사용 예
  - 전 세계적인 시간 기준이 필요할 때 사용한다.
  - 시간대 변환 없이 시간 계산이 필요하다면 적합하다.
  - db에 날짜와 시간 정보를 저장하거나, 다른 시스템과 교환할 때 데이터의 일관성을 유지할 수 있다. 

  ~~~java
  //생성(UTC기준)
  Instant now = Instant.now(); // 2024-04-17T06:10:00.319377Z

  ZonedDateTime zdt = ZonedDateTime.now();
  Instant from = Instant.from(zdt); // 2024-04-17T06:10:00.330523Z

  Instant epochStart = Instant.ofEpochSecond(0); // 1970-01-01T00:00:00Z

  //계산
  Instant later = epochStart.plusSeconds(3600); // 1970-01-01T01:00:00Z

  //조회
  long laterEpochSecond = later.getEpochSecond(); // 3600
  ~~~

  - from() : 다른 타입의 날짜와 시간을 기준으로 Instant를 생성한다. 
    - Instant는 UTC기준이라 LocalDateTime은 사용불가
  - ofEpochSecond() : 에포크 시간 기준으로 Instant를 생성한다.
    - 0초 선택 -> 1970년 1월 1일 0시 0분 0초로 생성된다.
  - getEpochSecond() : 에포크 시간 기준으로 흐른 초를 반환한다.
<br>

## Period, Duration
- 시간의 간격을 표현하는 데 사용된다.
- <strong>Period</strong> : 두 날짜 사이의 간격을 년, 월, 일 단위로 나타낸다.
  - getYears(), getMonths(), getDays()
- <strong>Duration</strong> : 두 시간 사이의 간격을 시, 분, 초(나노초) 단위로 나타낸다.
  - toHours(), toMinutes(), getSeconds(), getNano()

### Period
  ~~~java
  //생성
  Period period = Period.ofDays(10);
  System.out.println("period = " + period);

  LocalDate currentDate = LocalDate.of(2030, 1, 1);
  LocalDate plusDate = currentDate.plus(period); // 2030-01-11

  //기간 차이
  LocalDate startDate = LocalDate.of(2023, 1, 1);
  LocalDate endDate = LocalDate.of(2023, 4, 2);
  Period between = Period.between(startDate, endDate);
  System.out.println("기간: " + between.getMonths() + "개월 " + between.getDays() + "일"); // 3개월 1일
  ~~~

  - of() : 특정 기간을 지정해서 Period를 생성한다.
    - of(년, 월, 일), ofDays(), ofMonths(), ofYears()
  - Period.between(startDate, endDate) : 특정 날짜의 차이를 구하면 Period가 반환된다. 

### Duration
  ~~~java
  //생성
  Duration duration = Duration.ofMinutes(30);
  System.out.println("duration = " + duration);

  LocalTime lt = LocalTime.of(1, 0); // 01:00

  //계산에 사용
  LocalTime plusTime = lt.plus(duration); // 01:30

  //시간 차이
  LocalTime start = LocalTime.of(9, 0);
  LocalTime end = LocalTime.of(10, 0);
  Duration between = Duration.between(start, end);
  System.out.println("차이: " + between.getSeconds() + "초"); // 3600초
  System.out.println("근무 시간: " + between.toHours() + "시간 " + between.toMinutesPart() + "분"); // 1시간 0분
  ~~~

## 날짜와 시간의 핵심 인터페이스
<img width="621" alt="image" src="https://github.com/yaejinkong/yaejinkong.github.io/assets/127467781/d085d9a5-20a2-4b6e-9b14-f3b914cd3e71">

- 특정 시점의 시간 : Temporal(TemporalAccessor 포함) 인터페이스를 구현한다.
  - 구현으로는 LocalDateTime, LocalDate, LocalTime, ZonedDateTime, OffsetDateTime, Instant 등
  - TemporalAccessor는 읽기 전용 접근, Temporal은 읽기와 쓰기(조작) 모두를 지원한다.
- 시간의 간격(기간) : TemporalAmount 인터페이스를 구현한다.
  - 구현으로는 Period, Duration이 있다.

### 시간의 단위와 시간 필드
<img width="608" alt="image" src="https://github.com/yaejinkong/yaejinkong.github.io/assets/127467781/4f0bed56-f6ce-4c2a-9e27-f9b8dfc5433d">

- 시간의 단위 - TemporalUnit, ChronoUnit
  - TemporalUnit 인터페이스는 날짜와 시간을 측정하는 단위를 나타내며, `java.time.temporal.ChronoUnit` 열거형으로 구현된다.  
  - ChronoUnit 사용 시 두 날짜 또는 시간 사이의 차이를 해당 단위로 쉽게 계산할 수 있다. 

  ~~~java
  ChronoUnit[] values = ChronoUnit.values();
  for (ChronoUnit value : values) {
      System.out.println("value = " + value);
  }
  System.out.println("HOURS = " + ChronoUnit.HOURS);
  System.out.println("HOURS.duration = " + ChronoUnit.HOURS.getDuration().getSeconds()); // 3600
  System.out.println("DAYS = " + ChronoUnit.DAYS);
  System.out.println("DAYS.duration = " + ChronoUnit.DAYS.getDuration().getSeconds()); // 86400

  //차이 구하기
  LocalTime lt1 = LocalTime.of(1, 10, 0);
  LocalTime lt2 = LocalTime.of(1, 20, 0);

  long secondsBetween = ChronoUnit.SECONDS.between(lt1, lt2); // 600
  long minutesBetween = ChronoUnit.MINUTES.between(lt1, lt2); // 10
  ~~~
<br>

- 시간 필드 - ChronoField
  - TemporalField 인터페이스는 날짜와 시간을 나타내는 데 사용된다. `java.time.temporal.ChronoField` 열거형으로 구현된다.
  - ChronoField는 다양한 필드를 통해 날짜와 시간의 특정 부분을 나타낸다.
  - 2024년 8월 16일에서 `YEAR` : 2024, `MONTH_OF_YEAR` : 8, `DAY_OF_MONTH` : 16
  - 날짜와 시간의 각 필드 중에 원하는 데이터를 조회할 수 있다. 

  ~~~java
  ChronoField[] values = ChronoField.values();
  for (ChronoField value : values) {
      System.out.println(value + ", range = " + value.range());
  }
  System.out.println("MONTH_OF_YEAR.range() = " + ChronoField.MONTH_OF_YEAR.range()); // 1 - 12
  System.out.println("DAY_OF_MONTH.range() = " + ChronoField.DAY_OF_MONTH.range()); // 1 - 28/31
  ~~~
<br>

### 날짜와 시간 조회
- 날짜와 시간의 필드를 조회하는 ChronoField가 사용된다.

  ~~~java
  LocalDateTime dt = LocalDateTime.of(2030, 1, 1, 13, 30, 59);
  System.out.println("YEAR = " + dt.get(ChronoField.YEAR));
  System.out.println("MONTH_OF_YEAR = " + dt.get(ChronoField.MONTH_OF_YEAR));
  System.out.println("DAY_OF_MONTH = " + dt.get(ChronoField.DAY_OF_MONTH));
  System.out.println("HOUR_OF_DAY = " + dt.get(ChronoField.HOUR_OF_DAY));
  System.out.println("SECOND_OF_MINUTE = " + dt.get(ChronoField.SECOND_OF_MINUTE));

  //편의 메서드 사용
  System.out.println("YEAR = " + dt.getYear());
  System.out.println("MONTH_OF_YEAR = " + dt.getMonthValue());
  System.out.println("DAY_OF_MONTH = " + dt.getDayOfMonth());
  System.out.println("HOUR_OF_DAY = " + dt.getHour());
  System.out.println("MINUTE_OF_HOUR = " + dt.getMinute());
  System.out.println("SECOND_OF_MINUTE = " + dt.getSecond());

  // 편의 메서드에 없음
  System.out.println("MINUTE_OF_DAY = " + dt.get(ChronoField.MINUTE_OF_DAY));
  System.out.println("SECOND_OF_DAY = " + dt.get(ChronoField.SECOND_OF_DAY));
  ~~~

  - TemporalAccessor.get(TemporalField field)
    - TemporalAccessor은 특정 시점의 시간을 조회하는 기능을 제공한다.
    - get(TemporalField field) 호출 시, TemporalField의 구현체인 ChronoField를 인수로 전달하면 된다.
  - 편의 메서드 사용
    - dt.get(ChronoField.DAY_OF_MONTH) -> `dt.getDayOfMonth()`
  <br>
  - 편의 메서드에 없는 경우, get(TemporalField field)를 사용하면 된다. 


### 날짜와 시간 조작
- 날짜와 시간의 단위를 뜻하는 ChronoUnit이 사용된다.

  ~~~java
  LocalDateTime dt = LocalDateTime.of(2018, 1, 1, 13, 30, 59);
  LocalDateTime plusDt1 = dt.plus(10, ChronoUnit.YEARS); // 2028-01-01T13:30:59
  LocalDateTime plusDt2 = dt.plusYears(10); // 2028-01-01T13:30:59
  Period period = Period.ofYears(10);
  LocalDateTime plusDt3 = dt.plus(period); // 2028-01-01T13:30:59
  ~~~

  - Temporal plus(long amountToAdd, TemporalUnit unit) 
    - 호출 시 더하기 할 숫자와 시간의 단위를 전달한다.
    - TemporalUnit의 구현인 ChronoUnit을 인수로 전달하면 된다.
    - 불변이므로 반환 값을 받아야한다.
    - minus()도 존재
  - 편의 메서드
    - dt.plus(10, ChronoUnit.YEARS) -> dt.plusYears(10)
  - Period를 사용하여 특정 시점의 시간에 기간을 더할 수 있다.

### isSupported()

- `boolean isSupported(TemporalField field)`<br>
`boolean isSupported(TemporalUnit unit)` 
- 현재 타입에서 특정 시간 단위나 필드를 사용할 수 있는 지 확인하는 메서드

  ~~~java
  LocalDate now = LocalDate.now();
  boolean supported = now.isSupported(ChronoField.SECOND_OF_MINUTE);
  System.out.println("supported = " + supported);

  if (supported) {
      int minute = now.get(ChronoField.SECOND_OF_MINUTE);
      System.out.println("minute = " + minute);
  }
  ~~~

### with() 메서드
- 날짜와 시간을 조작한다.

  ~~~java
  LocalDateTime dt = LocalDateTime.of(2018, 1, 1, 13, 30, 59);
  System.out.println("dt = " + dt); // 2018-01-01T13:30:59

  LocalDateTime chagedDt1 = dt.with(ChronoField.YEAR, 2020); // 2020-01-01T13:30:59
  LocalDateTime chagedDt2 = dt.withYear(2020); // 2020-01-01T13:30:59

  // TemporalAdjuster 사용
  // 다음 주 금요일
  LocalDateTime with1 = dt.with(TemporalAdjusters.next(DayOfWeek.FRIDAY));
  System.out.println("기준 날짜: " + dt); // 2018-01-01T13:30:59
  System.out.println("다음 금요일: " + with1); // 2018-01-05T13:30:59

  // 이번 달의 마지막 금요일
  LocalDateTime with2 = dt.with(TemporalAdjusters.lastInMonth(DayOfWeek.SUNDAY));
  System.out.println("같은 달의 마지막 일요일 = " + with2); // 2018-01-28T13:30:59
  ~~~

  - Temporal with(TemporalField field, long newValue)
    - 날짜와 시간의 특정 필드의 값만 변경 가능하다.
    - 불변이므로 반환 값 필수
  - 편의 메서드
    - with()는 단순한 날짜만 변경 가능하다.
    - TemporalAdjusters를 사용하면 이번 달의 마지막 일요일과 같은 복잡한 날짜를 계산할 수 있다.
    - `TemporalAdjusters.next(DayOfWeek.FRIDAY)` : 다음 주 금요일
    - `TemporalAdjusters.lastInMonth(DayOfWeek.SUNDAY)` : 이번 달의 마지막 일요일
  - DayOfWeek : 월, 화, 수, 목, 금, 토, 일을 나타내는 열거형
<br>

## 날짜와 시간 문자열 파싱과 포맷팅
- 포맷팅 : 날짜와 시간 데이터를 원하는 포맷의 문자열로 변경하는 것. Date -> String
- 파싱 : 문자열을 날짜와 시간 데이터로 변경하는 것. String -> Date

  ~~~java
  //포맷팅 : 날짜를 문자로
  LocalDate date = LocalDate.of(2024, 12, 31);
  DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy년 MM월 dd일");
  String formattedDate = date.format(formatter);
  System.out.println("날짜와 시간 포맷팅: " + formattedDate); // 날짜와 시간 포맷팅: 2024년 12월 31일

  //파싱: 문자를 날짜로
  String input = "2030년 01월 01일";
  LocalDate parsedDate = LocalDate.parse(input, formatter);
  System.out.println("문자열 파싱 날짜와 시간: " + parsedDate); // 2030-01-01
  ~~~

  - DateTimeFormatter : 날짜 객체를 원하는 형태의 문자로 변경한다. 
  - ofPattern() : 원하는 포맷을 지정한다. 

### 문자열을 날짜와 시간으로 파싱

  ~~~java
  //포맷팅 : 날짜와 시간을 문자로
  LocalDateTime now = LocalDateTime.of(2024, 12, 31, 13, 30, 59);
  DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyy-MM-dd HH:mm:ss");
  String formattedDateTime = now.format(formatter);
  System.out.println("날짜와 시간 포맷팅: " + formattedDateTime); // 2024-12-31 13:30:59

  //파싱: 문자를 날짜와 시간으로
  String dateTimeString = "2030-01-01 11:30:00";
  LocalDateTime parsedDateTime = LocalDateTime.parse(dateTimeString, formatter);
  System.out.println("문자열 파싱 날짜와 시간: " + parsedDateTime); // 2030-01-01T11:30
  ~~~


<br>
<br>
<br>

<blockquote>김영한의 실전 자바 기본편을 참고하였습니다</blockquote>










  

  


