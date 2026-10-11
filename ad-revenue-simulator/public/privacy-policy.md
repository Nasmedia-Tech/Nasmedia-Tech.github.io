<!doctype html>
<html lang="ko">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <meta name="description" content="AdRevenue Lab 앱 개인정보 고지. 로컬 계산 데이터, 주간 점검 알림, 백업·내보내기 파일, nap mx 광고, 스토어 업데이트 확인과 삭제·선택권을 안내합니다.">
  <title>개인정보 처리방침 | AdRevenue Lab</title>
  <link rel="stylesheet" href="../legal.css">
</head>
<body>
  <a class="skip-link" href="#content">본문 바로가기</a>
  <header class="site-header"><div class="header-inner"><a class="brand" href="../support/">AdRevenue Lab</a><nav aria-label="정책 및 지원"><ul><li><a aria-current="page" href="./">개인정보</a></li><li><a href="../terms-of-service/">이용약관</a></li><li><a href="../support/">고객지원</a></li></ul></nav></div></header>
  <main id="content">
    <section class="hero" aria-labelledby="page-title">
      <p class="eyebrow">PRIVACY</p><h1 id="page-title">개인정보 처리방침</h1>
      <p class="lead">개인정보 처리의 기준은 <strong>케이티나스미디어 개인정보 처리방침</strong>입니다. 이 페이지는 그 방침을 대체하지 않고, AdRevenue Lab의 실제 데이터 흐름과 사용자 선택권을 앱 단위로 설명합니다.</p>
      <p class="meta">시행일·최종 개정일: 2026년 9월 22일 · AdRevenue Lab (<code>kr.co.nasmedia.napyield</code>) · 주식회사 케이티나스미디어</p>
    </section>

    <aside class="notice" aria-label="기준 방침"><strong>기준 방침.</strong> 처리 목적과 법적 근거, 보유·파기, 안전성 확보조치, 정보주체의 권리 행사와 거부 방법, 고충 처리와 구제 절차는 모두 <a href="https://www.nasmedia.co.kr/%EA%B0%9C%EC%9D%B8%EC%A0%95%EB%B3%B4%EC%B2%98%EB%A6%AC%EB%B0%A9%EC%B9%A8/">케이티나스미디어 개인정보 처리방침</a>을 따릅니다. 광고 데이터는 그중 <strong>제8장(행태정보의 수집·이용 및 거부)</strong>이 기준입니다.</aside>

    <section aria-labelledby="local-data"><h2 id="local-data">앱이 직접 처리하는 데이터</h2>
      <ul>
        <li><strong>계산·실적·운영 기록은 회사 서버로 전송하지 않습니다.</strong> MAU·DAU·단가·비용 등 입력값, Revenue Journal 실적, 목표·시나리오, 개선 Action과 메모, 주간 점검 상태, 사용자가 명시적으로 확인한 마지막 분석 기준선, 앱 포트폴리오(앱 이름·플랫폼·월 목표·월 실적), 실험 저널, 학습 진행, 홈 카드 구성, 주간 점검 알림 설정 및 앱 설정은 기기 저장소에서 처리하며 광고 요청에도 넣지 않습니다.</li>
        <li><strong>계정, 자체 분석·크래시 수집 도구가 없습니다.</strong> 회사 서버에 계산 데이터의 복구용 사본도 만들지 않습니다. 알림·백업·내보내기 기능은 새로운 네트워크 전송, SDK 또는 분석 도구를 추가하지 않습니다.</li>
        <li><strong>NAP MX 연결은 선택 사항입니다.</strong> 이용자가 자신의 Report API Key를 입력한 경우에만 NAP MX 공식 HTTPS API로 조회합니다. 키는 Android Keystore 또는 iOS Keychain으로 보호하고 회사 서버에는 보내지 않습니다. 조회한 광고 요청·노출·클릭·수익 데이터는 가져오기 미리보기를 거쳐 사용자가 승인한 항목만 기기에 저장합니다.</li>
        <li><strong>공유·문의는 사용자가 선택한 외부 앱으로 전달됩니다.</strong> 공유 파일·문구와 문의 내용은 운영체제 공유 기능 및 선택한 메일·메신저 등의 정책을 따릅니다. 회사는 이용자가 문의로 직접 보낸 내용만 수신합니다.</li>
      </ul>
    </section>

    <section aria-labelledby="reminder"><h2 id="reminder">주간 점검 알림과 알림 권한</h2>
      <ul>
        <li><strong>기본 꺼짐:</strong> 주간 점검 알림은 사용자가 설정에서 직접 켤 때만 사용합니다. 알림 권한(Android 13 이상 <code>POST_NOTIFICATIONS</code>, iOS 알림 허용)도 이때만 요청하며, 거부해도 알림만 꺼진 상태로 유지되고 다른 기능은 그대로 사용할 수 있습니다.</li>
        <li><strong>기기 안의 로컬 알림:</strong> 사용자가 고른 요일·시각에 주 1회 기기에서 예약됩니다. 서버 푸시와 알림 토큰을 사용하지 않으며 알림 문구에 매출 등 입력 데이터를 넣지 않습니다. 알림을 누르면 분석 화면이 열립니다.</li>
        <li><strong>설정 범위:</strong> 알림 설정(켜짐 여부·요일·시각)은 이 기기에만 저장되고 백업 파일에 포함되지 않습니다. 앱 설정이나 운영체제 알림 설정에서 언제든 끌 수 있습니다.</li>
      </ul>
    </section>

    <section aria-labelledby="files"><h2 id="files">백업·내보내기 파일</h2>
      <ul>
        <li><strong>로컬 백업 파일:</strong> 사용자가 <code>데이터 백업·복원</code>에서 직접 실행할 때만 시나리오·실적·목표/분석 설정·실험 저널·학습 진행·앱 설정 등을 하나의 JSON 파일로 만들어, 운영체제 파일 선택 화면에서 사용자가 고른 위치에 저장합니다. 앱은 이 파일을 회사 서버나 제3자에게 보내지 않습니다.</li>
        <li><strong>암호화하지 않습니다:</strong> 백업 파일은 사용자가 입력한 매출·비용 등 사업 정보를 담습니다. 파일의 SHA-256 값은 손상 여부 확인용일 뿐 암호화나 위·변조 방지 인증이 아닙니다. NAP MX 인증정보와 알림 설정 등 기기 전용 설정은 포함하지 않습니다.</li>
        <li><strong>복원 되돌리기:</strong> 복원 직전 기존 데이터의 안전 사본 1개를 앱 전용 영역(운영체제 백업 제외)에 보관해 한 번 되돌릴 수 있게 하며, 되돌리면 삭제되고 다음 복원 시 새 사본으로 교체됩니다.</li>
        <li><strong>내보내기:</strong> 포트폴리오 CSV, 주간·월간 리뷰 CSV/PDF(완료된 실험 요약 포함), 실적 CSV, 계산 근거 CSV·보고서 PDF는 사용자가 공유·저장을 실행할 때만 만들어 운영체제 공유 기능으로 사용자가 선택한 앱·위치에 전달합니다. 공유용 임시 파일은 앱 캐시에 만들어지며 운영체제가 정리할 때까지 남을 수 있습니다.</li>
        <li><strong>저장 위치는 사용자가 관리합니다:</strong> 앱 밖에 저장·공유한 백업·내보내기 파일은 회사가 접근하거나 삭제할 수 없습니다. 클라우드 드라이브·메일·메신저 등 저장한 곳의 보관·공유·삭제는 사용자가 직접 관리하며, 해당 서비스의 정책이 적용됩니다.</li>
      </ul>
    </section>

    <section aria-labelledby="communications"><h2 id="communications">광고와 스토어 업데이트 통신</h2>
      <ul>
        <li><strong>광고:</strong> Android 광고 ID, iOS는 추적 허용 시 IDFA와 함께 IP 주소, 기기 모델, OS·앱 버전, 언어·네트워크 정보, 광고 요청·노출·클릭·리워드 기록이 nap mx/AdMixer 광고 시스템에서 처리될 수 있습니다. Android 광고 워터폴에는 Google Ad Manager와 Pangle SDK가 포함될 수 있으며, 광고 제공·측정·빈도 제한·부정 이용 방지를 위해 기기 식별자, 대략적 위치로 추정될 수 있는 IP 주소, 기기에 설치된 앱 목록, 앱 상호작용·진단 정보를 처리할 수 있습니다. nap mx SSP 공식 개인정보 안내는 COPPA 아동 대상 또는 GDPR 명시적 거부 상태에서 설치 앱 목록을 조회·저장·전송하지 않는다고 설명합니다. Pangle은 Pangle 광고와 관련된 충돌 로그와 기타 앱 성능 데이터를 추가로 수집할 수 있으며, Pangle의 Google Play 공식 안내는 이 두 항목의 후속 공유를 해당 없음으로 표시합니다. 추적을 거부해도 계산·저장·공유 기능은 그대로 사용할 수 있습니다.</li>
        <li><strong>Android 업데이트:</strong> Google Play 인앱 업데이트 기능이 Play 스토어와 통신합니다. <a href="https://developer.android.com/guide/playcore/in-app-updates">Google 공식 문서</a>에 따르면 업데이트 제공을 위해 기기 메타데이터, 앱 버전, 설치된 모듈·애셋 목록을 처리하며, 전송 중 암호화되고 고정된 보유기간 후 삭제됩니다. 회사의 별도 업데이트 서버로 보내지 않습니다.</li>
        <li><strong>iOS 업데이트:</strong> 사용자가 설정 화면을 열면 Apple 조회 API에 앱 Bundle ID와 국가 코드(<code>kr</code>)로 새 버전을 확인합니다. 현재 앱 버전과의 비교는 기기에서 수행하고 회사 서버에는 저장하지 않습니다.</li>
      </ul>
      <p>Android 앱에는 인터넷·네트워크 상태·광고 ID·광고 측정/주제 API와 광고 SDK 작업을 위한 Wake Lock·Foreground Service 권한이 병합됩니다. 이와 별도로 사용자가 켠 주간 점검 로컬 알림을 표시하기 위한 알림 권한(<code>POST_NOTIFICATIONS</code>)을 선언하며, 사용자가 알림을 켤 때만 요청합니다. 광고 사업자의 실제 처리 항목·보유·제공은 회사의 개인정보 처리방침과 해당 사업자의 정책 및 운영 설정을 함께 따릅니다.</p>
    </section>

    <section aria-labelledby="choices"><h2 id="choices">삭제와 선택권</h2>
      <ul>
        <li><strong>앱 데이터:</strong> <code>더보기 → 설정·개인정보·데이터 관리 → 저장 데이터 초기화</code>에서 입력값·시나리오·실적·포트폴리오·실험 저널·학습 진행·홈 카드 구성·설정·NAP MX 연결 정보를 삭제하고, 예약된 주간 점검 알림을 취소하며 알림 설정을 기본값(꺼짐)으로 되돌립니다. 복원 직전 안전 사본도 삭제하므로 초기화한 데이터를 복원 되돌리기로 되살릴 수 없습니다. 개별 시나리오는 시나리오 화면에서도 삭제할 수 있습니다. 이미 앱 밖에 저장·공유한 백업·내보내기 파일은 초기화로 삭제되지 않으므로 저장한 위치에서 직접 삭제해야 합니다.</li>
        <li><strong>NAP MX 연결 해제:</strong> 분석 화면의 NAP MX 가져오기에서 연결을 해제하면 기기의 API Key와 인증 캐시를 삭제합니다. 이미 가져온 실적은 기본적으로 유지하며, 별도 확인을 거쳐 함께 삭제할 수 있습니다. iOS Keychain 항목은 앱 재설치 후 남을 수 있으므로 연결 해제 또는 저장 데이터 초기화를 사용해야 확실히 제거됩니다.</li>
        <li><strong>삭제 범위:</strong> 앱 삭제 시 현재 기기의 앱 데이터는 제거되지만, 운영체제 백업에 남아 복원될 수 있는 사본, 사용자가 저장한 백업·내보내기 파일, 사용자가 공유한 수신 앱의 사본, 광고 사업자가 법령·정책에 따라 보유하는 기록까지 함께 삭제되는 것은 아닙니다.</li>
        <li><strong>광고 선택:</strong> iOS 추적 선택은 <code>설정 → 개인정보 보호 및 보안 → 추적</code>, Android 광고 선택은 기기의 Google 광고 개인정보 메뉴에서 변경할 수 있습니다. 메뉴 이름은 OS·제조사 버전에 따라 다를 수 있습니다.</li>
      </ul>
      <p>문의 및 권리 행사: <a href="mailto:security@nasmedia.co.kr">security@nasmedia.co.kr</a></p>
    </section>

    <section class="card" aria-labelledby="english-summary"><h2 id="english-summary">English summary</h2><p>Personal data handling is governed by the <a href="https://www.nasmedia.co.kr/%EA%B0%9C%EC%9D%B8%EC%A0%95%EB%B3%B4%EC%B2%98%EB%A6%AC%EB%B0%A9%EC%B9%A8/">Nasmedia privacy policy</a>. Simulation inputs, actual-performance records, goals, scenarios, improvement Actions, weekly-review state, the user-confirmed last-analysis baseline and settings remain in device storage and are not sent to a company server or included in ad requests. Optional NAP MX connection uses the user's own Report API Key, protected by Android Keystore or iOS Keychain, to query the official HTTPS API. Report data is shown in a preview and stored locally only after confirmation. The opt-in weekly review reminder is off by default; notification permission (<code>POST_NOTIFICATIONS</code> on Android 13+, iOS notification authorization) is requested only when the user turns it on, and the reminder is a local notification scheduled on the device with no server push, push token, or entered data in its text. Backup files and portfolio/review CSV/PDF exports are created only when the user saves or shares them, go only to the location or app the user picks, and are not sent to the company. Backup files are not encrypted; their SHA-256 value only detects damage and is not encryption or tamper-proof authentication; NAP MX credentials are excluded. Reset stored data also deletes the pre-restore safety copy, so erased data cannot be recovered by undoing a restore. Files saved or shared outside the app are the user's to manage and are not deleted by Reset stored data. Disconnecting deletes the credential; imported records remain unless separately deleted. The nap mx/AdMixer ad system may process an advertising identifier, device/network data, the list of apps installed on the device and ad events. Official nap mx SSP documentation says that the installed-app list is not queried, stored, or transmitted for a COPPA child-directed user or after an explicit GDPR refusal. Android's GAM and Pangle components may also process approximate location derived from IP, app interactions and diagnostic data; Pangle may collect ad-related crash logs and other app-performance data. There is no account and no first-party analytics or crash-reporting SDK. Contact: <a href="mailto:security@nasmedia.co.kr">security@nasmedia.co.kr</a>.</p></section>
  </main>
  <footer class="site-footer">© 2026 Nasmedia Co., Ltd. · <a href="../terms-of-service/">서비스 이용약관</a> · <a href="../support/">고객지원</a></footer>
</body>
</html>
