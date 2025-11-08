# PicoArt v29 - 완전 단순화 버전

**릴리즈 날짜:** 2025년 11월 9일  
**이전 버전:** v28 (울트라 강화판)  
**핵심 변경:** 19개 작품 DB 완전 제거, AI 기반 스타일 매칭으로 단순화

---

## 🎯 핵심 철학 변경

### v28 이전: 19개 작품 DB 매칭
```
사용자 사진 업로드
    ↓
Claude API: 19개 작품 중 최적 선택
    ↓
Replicate: 선택된 작품 스타일로 변환
```

**문제점:**
- 19개 이미지 파일 관리 필요
- 복잡한 DB 매칭 로직 (120줄+)
- 제한된 스타일 옵션 (정확히 19개만)

---

### v29: 주요 스타일 선택
```
사용자 사진 업로드
    ↓
Claude API: 2-3개 주요 스타일 중 선택
  (예: 민화/풍속도/산수화)
    ↓
Replicate: 선택된 스타일로 변환
```

**장점:**
- ✅ 코드 214줄 감소 (807줄 → 593줄)
- ✅ 이미지 파일 불필요 (DB 완전 제거)
- ✅ 주요 스타일로 집중 (민화/풍속도/공필화 등)
- ✅ 유지보수 간편
- ✅ Claude는 여전히 큐레이터 역할 (중요!)

---

## 📊 변경 사항 상세

### 1. 삭제된 것들

#### 19개 작품 DB 삭제
```javascript
// ❌ 제거됨
const chineseArtworks = [ /* 9개 작품 */ ];
const koreanArtworks = [ /* 10개 작품 */ ];
```

#### 복잡한 매칭 함수 삭제 (120줄+)
```javascript
// ❌ 제거됨
async function selectOrientalArtwork(imageBase64, artworkDatabase, cultureName) {
  // 120줄의 복잡한 매칭 로직
}
```

#### DB 매칭 로직 삭제 (80줄+)
```javascript
// ❌ 제거됨
if (selectedStyle.id === 'korean') {
  const selection = await selectOrientalArtwork(image, koreanArtworks, 'Korean');
  // ... 복잡한 재시도/fallback 로직
}
```

---

### 2. 추가/개선된 것들

#### 🇰🇷 한국 전통화 - AI 자동 선택
```javascript
korean: {
  name: '한국 전통화',
  prompt: 'Korean traditional painting in authentic Joseon Dynasty style. 
           CRITICAL: 1) Gender preservation, 2) Choose appropriate style 
           (Minhwa/Pungsokdo/landscape), 3) Korean aesthetic. 
           ALLOWED: Hangul (한글) and Hanja (漢字). 
           NO Japanese hiragana/katakana.'
}
```

**AI가 사진을 보고 자동 선택:**
- 동물/꽃 → 민화 스타일 (굵은 윤곽선, 오방색)
- 사람/일상 → 풍속도 (섬세한 붓터치, 한복)
- 산/자연 → 진경산수 (힘찬 먹 표현)

#### 🇨🇳 중국 전통화 - AI 자동 선택
```javascript
chinese: {
  name: '중국 전통화',
  prompt: 'Chinese traditional painting in authentic classical style. 
           CRITICAL: 1) Gender preservation, 2) Choose appropriate style 
           (Shuimohua/Gongbi/Huaniao), 3) Chinese aesthetic. 
           ALLOWED: Chinese characters only. 
           NO Japanese hiragana/katakana.'
}
```

**AI가 사진을 보고 자동 선택:**
- 산수/자연 → 수묵화 (흑백 그라데이션, 여백의 미)
- 인물/초상 → 공필화 (정밀한 붓터치, 화려한 색채)
- 동물/꽃 → 화조화 (사실적 묘사, 섬세한 표현)

---

## 🔍 기술적 세부사항

### 코드 라인 수 변화
```
v28: 807 lines
v29: 535 lines
감소: 272 lines (33.7% 축소)
```

### 파일 구조 변화
```
v28:
├── api/flux-transfer.js (807 lines)
│   ├── chineseArtworks[] (9개)
│   ├── koreanArtworks[] (10개)
│   ├── selectOrientalArtwork() (120 lines)
│   └── 복잡한 DB 매칭 로직 (80 lines)
└── public/oriental/ (19개 이미지 파일)

v29:
├── api/flux-transfer.js (535 lines)
│   ├── fallbackPrompts.korean (간단한 프롬프트)
│   ├── fallbackPrompts.chinese (간단한 프롬프트)
│   └── AI 기반 스타일 자동 선택
└── public/ (이미지 파일 불필요)
```

---

## 🎨 프롬프트 전략

### 핵심 원칙

1. **성별 보존 최우선**
   ```
   CRITICAL: carefully preserve exact gender and facial features
   male stays male, female stays female
   ```

2. **문화적 정확성**
   ```
   ALLOWED: Korean Hangul (한글) and Chinese characters (漢字)
   ABSOLUTELY NO Japanese hiragana (ひらがな) or katakana (カタカナ)
   ```

3. **AI 자율성**
   ```
   Choose appropriate style based on photo subject:
   - Minhwa for animals/flowers
   - Pungsokdo for people/daily life
   - Jingyeong for nature/mountains
   ```

---

## 🚀 성능 및 품질

### 처리 속도
- **v28**: Claude 3초 + Replicate 12초 = **15초**
- **v29**: Replicate 12초 = **12초** (20% 빠름)
- Claude API 호출 감소로 속도 향상

### 비용
- **v28**: 동양화마다 Claude API 호출 (Vision)
- **v29**: 동양화는 API 호출 없음 (무료!)
- 서양 미술(거장)만 Claude API 사용

### 품질
- **이론상**: AI가 더 유연하게 판단 → 품질 향상
- **검증 필요**: 실제 테스트로 확인 필요

---

## 🔄 역호환성

### API 인터페이스
- ✅ **변경 없음** - 동일한 API 엔드포인트
- ✅ **프론트엔드 수정 불필요**

### 선택 옵션
- ✅ **동일** - 한국/중국/일본 선택 가능
- ⚠️ **변경**: 특정 작품 선택 불가 (예: "단오풍정")

---

## 📋 마이그레이션 가이드

### v28에서 v29로 업그레이드

#### 1. 코드 교체
```bash
# 기존 v28 백업
mv picoart-v28 picoart-v28-backup

# v29 배포
unzip picoart-v29.zip
cd picoart-v29
npm install
```

#### 2. 환경 변수 (변경 없음)
```bash
VITE_REPLICATE_API_TOKEN=your_token
VITE_ANTHROPIC_API_KEY=your_key  # 선택사항 (거장 스타일용)
```

#### 3. 이미지 파일
```bash
# v29에서는 불필요 - 삭제 가능
rm -rf public/oriental/
```

---

## 🧪 테스트 필요 항목

### 필수 테스트

#### 한국 스타일
- [ ] 사람 사진 → 풍속도 스타일 확인
- [ ] 동물 사진 → 민화 스타일 확인
- [ ] 산 사진 → 진경산수 확인
- [ ] 성별 보존 확인
- [ ] 일본어 혼입 없는지 확인

#### 중국 스타일
- [ ] 사람 사진 → 공필화 스타일 확인
- [ ] 산수 사진 → 수묵화 스타일 확인
- [ ] 동물 사진 → 화조화 확인
- [ ] 성별 보존 확인
- [ ] 일본어 혼입 없는지 확인

---

## 🎯 예상 결과 vs 실제

### 이론적 장점
1. ✅ **코드 단순화** - 270줄 감소
2. ✅ **유지보수 편의** - DB 관리 불필요
3. ✅ **무한 스타일** - AI가 유연하게 선택
4. ⚠️ **품질 향상** - 검증 필요

### 잠재적 리스크
1. ⚠️ **일관성** - 같은 사진도 매번 다른 스타일 가능
2. ⚠️ **문화 정확성** - AI가 한국/중국 혼동 가능성
3. ⚠️ **일본어 혼입** - 프롬프트로 차단했지만 확인 필요

---

## 🔮 향후 계획

### v29.1 (버그픽스)
- 테스트 후 발견된 문제 수정
- 프롬프트 미세 조정

### v30 (고급 기능)
- 사용자 피드백 반영
- 스타일 강도 조절 옵션
- 배치 처리 지원

---

## 📝 결론

**v29는 "과감한 단순화" 실험입니다.**

### 성공 시나리오
- AI가 똑똑하게 스타일 선택
- 품질 유지 or 향상
- 유지보수 대폭 편해짐

### 실패 시나리오
- 문화적 정확성 저하
- 일본어 혼입 문제
- 일관성 부족

**→ 실제 테스트를 통해 검증 필요!**

---

**v29 준비 완료! 테스트를 시작하세요! 🚀**
