# 피싱 웹사이트 탐지 데이터셋 (Phishing Websites)

수행평가용 데이터입니다. 웹사이트의 주소(URL), 페이지 소스, 도메인 정보 등에서 뽑아낸 30가지 특징으로 그 사이트가 **피싱(-1)** 인지 **정상(1)** 인지를 분류하는 데이터셋입니다.

- 출처: UCI Machine Learning Repository — [Phishing Websites](https://archive.ics.uci.edu/dataset/327/phishing+websites) (CC BY 4.0)
- 원작자: Rami M. Mohammad, Fadi Thabtah, Lee McCluskey (University of Huddersfield, 2012)
- 인용: Mohammad, R. & McCluskey, L. (2012). Phishing Websites [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C51W2X

## 파일 구성

| 파일 | 설명 |
|---|---|
| `phishing_websites.csv` | 수행평가용 데이터 (CSV, 헤더 포함) |
| `README.md` | 데이터 및 속성 안내 (이 문서) |

> 수행평가는 **Google Colab**에서 진행합니다. 아래 **데이터 불러오기** 코드로 데이터를 불러온 뒤, 이후 과정은 스스로 해결해 보세요.

## 데이터 개요

| 항목 | 내용 |
|---|---|
| 샘플(행) 수 | 11,055개 |
| 열 수 | 31개 (특성 30 + 판정 결과 1) |
| 결측치 | 없음 |
| 클래스 분포 | 정상(1) 6,157개 (55.7%), 피싱(-1) 4,898개 (44.3%) |

## 데이터는 어떻게 만들어졌나?

피싱 사이트 신고 사이트(PhishTank, MillerSmiles)와 구글 검색 등으로 **피싱 사이트와 정상 사이트**를 모았습니다.
그리고 각 사이트마다 "주소에 IP가 들어 있나?", "HTTPS 인증서가 믿을 만한가?", "도메인이 만들어진 지 얼마나 됐나?" 같은 **30가지 규칙**을 검사해서, 그 결과를 숫자로 기록했습니다.

| 값 | 의미 |
|---|---|
| `1` | 정상(Legitimate) 쪽 특징 |
| `0` | 의심(Suspicious) — 정상과 피싱의 중간 (3단계로 나뉘는 특성에만 있음) |
| `-1` | 피싱(Phishing) 쪽 특징 |

> 원작자의 규칙상 대체로 위와 같이 기록되었지만, **일부 열은 실제 데이터에서 반대 경향을 보이기도 합니다.** 각 특성 값과 `Result`의 관계를 직접 확인해 보세요.
> `Redirect` 열만 예외적으로 `0`(정상)과 `1`(의심) 두 값만 가집니다.

열 이름은 원본 데이터의 이름을 그대로 사용했습니다. (원본의 오타 `Shortining`, `registeration`, `Widnow` 포함)

## 속성(열) 설명

### 정답(타깃)

| 열 이름 | 설명 | 값 |
|---|---|---|
| `Result` | **판정 결과 (예측해야 할 정답)** | `1` = 정상 사이트(Legitimate), `-1` = 피싱 사이트(Phishing) |

### ① 주소창(URL) 기반 특성 — 12개

| 번호 | 열 이름 | 무엇을 검사하나 | 값 |
|---|---|---|---|
| 1 | `having_IP_Address` | 도메인 이름 대신 **IP 주소**를 쓰는가? (예: `http://125.98.3.123/fake.html`) | -1, 1 |
| 2 | `URL_Length` | **URL 길이** — 54자 미만: 정상 / 54~75자: 의심 / 75자 초과: 피싱 | -1, 0, 1 |
| 3 | `Shortining_Service` | bit.ly 같은 **단축 URL 서비스**를 쓰는가? | -1, 1 |
| 4 | `having_At_Symbol` | URL에 **`@` 기호**가 있는가? (`@` 앞부분은 브라우저가 무시함) | -1, 1 |
| 5 | `double_slash_redirecting` | URL 경로 중간에 **`//`** 가 있어 다른 사이트로 넘어가는가? | -1, 1 |
| 6 | `Prefix_Suffix` | 도메인에 **하이픈(`-`)** 이 있는가? (예: `confirme-paypal.com`) | -1, 1 |
| 7 | `having_Sub_Domain` | **서브도메인(점 `.`)** 개수 — 없음: 정상 / 1개: 의심 / 2개 이상: 피싱 | -1, 0, 1 |
| 8 | `SSLfinal_State` | **HTTPS 인증서** — 믿을 만한 발급기관 + 1년 이상: 정상 / 발급기관 불신: 의심 / 그 외: 피싱 | -1, 0, 1 |
| 9 | `Domain_registeration_length` | **도메인 등록 기간**이 1년 이하로 짧은가? | -1, 1 |
| 10 | `Favicon` | 주소창 아이콘(파비콘)을 **다른 도메인**에서 불러오는가? | -1, 1 |
| 11 | `port` | 열려 있으면 안 되는 **포트**(21, 22, 23, 3306 등)가 열려 있는가? | -1, 1 |
| 12 | `HTTPS_token` | 도메인 이름 안에 **"https"라는 글자**를 넣어 속이는가? (예: `https-www-paypal.soft-hair.com`) | -1, 1 |

### ② 페이지 내용 이상(Abnormal) 기반 특성 — 6개

| 번호 | 열 이름 | 무엇을 검사하나 | 값 |
|---|---|---|---|
| 13 | `Request_URL` | 이미지·영상 등 **외부 자료를 다른 도메인**에서 불러오는 비율 (22% 미만: 정상) | -1, 1 |
| 14 | `URL_of_Anchor` | 링크(`<a>` 태그)가 **다른 도메인이거나 아무 데도 연결되지 않는** 비율 — 31% 미만: 정상 / 31~67%: 의심 / 67% 초과: 피싱 | -1, 0, 1 |
| 15 | `Links_in_tags` | `<Meta>`, `<Script>`, `<Link>` 태그가 **다른 도메인**을 가리키는 비율 — 17% 미만: 정상 / 17~81%: 의심 / 81% 초과: 피싱 | -1, 0, 1 |
| 16 | `SFH` | 입력 폼을 처리하는 주소(Server Form Handler) — 비어 있음/`about:blank`: 피싱 / 다른 도메인: 의심 / 그 외: 정상 | -1, 0, 1 |
| 17 | `Submitting_to_email` | 입력한 정보를 **이메일로 전송**(`mailto:`, `mail()`)하는가? | -1, 1 |
| 18 | `Abnormal_URL` | WHOIS(도메인 등록 정보)의 이름이 **URL에 들어 있지 않은가?** | -1, 1 |

### ③ HTML·자바스크립트 기반 특성 — 5개

| 번호 | 열 이름 | 무엇을 검사하나 | 값 |
|---|---|---|---|
| 19 | `Redirect` | 다른 페이지로 **자동 이동(리다이렉트)** 한 횟수 — 1회 이하: 정상(0) / 2회 이상: 의심(1) | 0, 1 |
| 20 | `on_mouseover` | 마우스를 올렸을 때 **상태 표시줄의 주소를 바꿔** 가짜 주소를 보여 주는가? | -1, 1 |
| 21 | `RightClick` | **마우스 오른쪽 클릭을 막아** 소스 보기를 못 하게 하는가? | -1, 1 |
| 22 | `popUpWidnow` | **팝업 창**에서 개인정보 입력을 요구하는가? | -1, 1 |
| 23 | `Iframe` | 보이지 않는 **iframe**(페이지 속 페이지)을 쓰는가? | -1, 1 |

### ④ 도메인 기반 특성 — 7개

| 번호 | 열 이름 | 무엇을 검사하나 | 값 |
|---|---|---|---|
| 24 | `age_of_domain` | 도메인이 만들어진 지 **6개월 이상** 되었는가? | -1, 1 |
| 25 | `DNSRecord` | 도메인의 **DNS 기록**이 존재하는가? | -1, 1 |
| 26 | `web_traffic` | 방문자 수 순위(Alexa) — 10만 위 안: 정상 / 10만 위 밖: 의심 / 기록 없음: 피싱 | -1, 0, 1 |
| 27 | `Page_Rank` | 구글 **페이지랭크**(웹페이지 중요도)가 0.2 이상인가? | -1, 1 |
| 28 | `Google_Index` | **구글 검색**에 등록(색인)되어 있는가? | -1, 1 |
| 29 | `Links_pointing_to_page` | 이 페이지를 가리키는 **외부 링크 수** — 0개: 피싱 / 1~2개: 의심 / 3개 이상: 정상 | -1, 0, 1 |
| 30 | `Statistical_report` | PhishTank 등 **피싱 통계 보고서**의 상위 피싱 IP·도메인 목록에 올라 있는가? | -1, 1 |

## 데이터 불러오기 (Google Colab)

Colab 새 노트북의 코드 셀에 아래 코드를 붙여넣고 실행하세요.

```python
import pandas as pd

url = "https://raw.githubusercontent.com/thiskorea81/Phishing_websites/main/phishing_websites.csv"
df = pd.read_csv(url)

print(df.shape)       # (11055, 31)
df.head()
```

이후 과정은 스스로 해결해 보세요.
