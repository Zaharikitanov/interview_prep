# QA Interview Questions

---

## Appendix

### Quick Reference by Topic

**Най-чести въпроси (Fundamentals)**

- [Защо тестваме софтуер?](#why-test-software)
- [QA vs QC vs Testing](#qa-vs-qc-vs-testing)
- [Test Case vs Test Scenario](#test-case-vs-scenario)
- [Happy Path & Negative Testing](#happy-path-negative-testing)
- [SDLC & STLC](#sdlc-stlc)
- [Verification vs Validation](#verification-vs-validation)
- [Bug Lifecycle](#bug-lifecycle)
- [Severity vs Priority](#severity-vs-priority)
- [Regression Testing](#regression-testing)
- [Smoke vs Sanity Testing](#smoke-vs-sanity)
- [Re-test vs Regression](#retest-vs-regression)
- [Equivalence Partitioning](#equivalence-partitioning)
- [Boundary Value Analysis](#boundary-value-analysis)

**Bug Reporting**

- [Добър Bug Report](#good-bug-report)
- [Bug Report Полета](#bug-report-fields)
- [Dev казва "не е бъг"](#not-a-bug)
- [Приоритизация на бъгове](#bug-prioritization)
- [Опиши конкретен бъг](#describe-logout-bug)

**Практически казуси**

- [Тестване на Login форма](#test-login-form)
- [Тестване на календар](#test-calendar)
- [Тестване на онлайн магазин](#test-online-shop)

**Инструменти & Процеси**

- [Bug Tracking Systems](#bug-tracking-systems)
- [Agile/Scrum опит](#agile-scrum-experience)
- [HTTP Status Codes](#http-status-codes)
- [HTTP Methods](#http-methods)
- [100% тестван софтуер?](#100-percent-tested)

**Advanced & Gotcha въпроси**

- [Decision Table Testing](#decision-table-testing)
- [State Transition Testing](#state-transition-testing)
- [Exploratory Testing](#exploratory-testing)
- [Automation Test Selection](#automation-test-selection)
- [Performance Testing](#performance-testing)
- [Usability Testing](#usability-testing)
- [Security Testing](#security-testing)
- [Compatibility Testing](#compatibility-testing)
- [Accessibility Testing](#accessibility-testing)
- [SQL Injections](#sql-injections)
- [API Testing](#api-testing)
- [Database Validation](#database-validation)
- [SQL Usage](#sql-usage)
- [Flaky Submit Button](#flaky-submit-button)
- [Mobile-specific Tests](#mobile-specific-tests)
- [Кога е "достатъчно"?](#testing-sufficient)
- [Липса на документация](#no-documentation)
- [Release Deadline Pressure](#release-deadline-pressure)
- [Manual vs Automation](#manual-vs-automation)
- [Bug в Production](#bug-in-production)
- [Не можеш да репликираш бъг](#cannot-replicate-bug)
- [Definition of Done](#definition-of-done)
- [Комуникация с екипа](#team-communication)
- [Конфликт с Developer](#conflict-with-developer)
- [Настояване за критичен бъг](#critical-bug-insistence)
- [Качество без бъгове?](#quality-without-bugs)
- [QA добавена стойност](#qa-added-value)

---

## Най-чести интервю въпроси (Fundamentals)

## Why Test Software?

**Q:** Защо тестваме даден софтуер?

**A:**

- **Намиране на дефекти** преди потребителите
- **Качество** - софтуерът работи според изискванията
- **Намаляване на риска** от критични грешки в production
- **Спестяване на пари** - по-евтино е да фиксваш бъг рано
- **User experience** - потребителят получава работещ продукт
- **Reputation** - защита на бранда и доверие

**Red flags:**

- "За да намерим бъгове" (твърде повърхностно)
- Не споменава бизнес стойност

[↑ Back to Appendix](#appendix)

---

## QA vs QC vs Testing

**Q:** Каква е разликата между QA, QC и Testing?

**A:**

**QA (Quality Assurance):**

- **Процес-ориентирана** дейност
- Превенция на дефекти
- Фокус върху процесите и методологиите
- "Правим ли нещата правилно?"

**QC (Quality Control):**

- **Продукт-ориентирана** дейност
- Откриване на дефекти
- Проверка на финалния продукт
- Включва testing

**Testing:**

- **Част от QC**
- Изпълнение на тестове за намиране на бъгове
- Verification на функционалност

**Аналогия:** QA е превенция (носене на каска), QC е инспекция (проверка на готов продукт)

**Red flags:**

- "QA и Testing са едно и също"
- Не може да обясни разликата с примери

[↑ Back to Appendix](#appendix)

---

## Test Case vs Scenario

**Q:** Каква е разликата между test case и test scenario?

**A:**

**Test Scenario:**

- High-level описание на какво да се тества
- Широко, може да покрива множество функционалности
- Пример: "Тестване на login функционалност"

**Test Case:**

- Детайлни стъпки за изпълнение
- Конкретни входни данни, очакван резултат
- Пример: "Login с валиден email и парола"
  - Стъпки: 1) Отвори login, 2) Въведи email, 3) Въведи парола, 4) Кликни Submit
  - Очакван резултат: Успешен login, redirect към homepage

**Съотношение:** 1 test scenario → multiple test cases

[↑ Back to Appendix](#appendix)

---

## Happy Path Negative Testing

**Q:** Какво е happy path? Какво е negative testing?

**A:**

**Happy Path:**

- Тестване на **стандартния flow** както потребителят трябва да го ползва
- Всичко работи според очакванията
- Валидни данни, нормално поведение
- Пример: Login с правилен username/password

**Negative Testing:**

- Тестване с **невалидни данни** или неочаквано поведение
- Как системата се справя с грешки
- Пример: Login с празно поле, грешна парола, SQL injection опити

**Защо е важно negative testing:**

- Валидация на error handling
- Сигурност
- Предотвратяване на crashes

**Red flags:**

- Само happy path тестване
- "Negative testing не е толкова важно"

[↑ Back to Appendix](#appendix)

---

## SDLC STLC

**Q:** Какво е SDLC & STLC и кои са фазите?

**A:**

**SDLC (Software Development Life Cycle):**

Процесът на създаване на софтуер от начало до край:

1. **Planning** - дефиниране на проекта
2. **Requirements Analysis** - какво трябва да прави софтуерът
3. **Design** - архитектура, UI/UX
4. **Development** - писане на код
5. **Testing** - QA дейности
6. **Deployment** - release в production
7. **Maintenance** - поддръжка, бъг фиксове

**STLC (Software Testing Life Cycle):**

Процесът на тестване (част от SDLC):

1. **Requirement Analysis** - разбиране какво да се тества
2. **Test Planning** - стратегия, ресурси, график
3. **Test Case Development** - писане на test cases
4. **Test Environment Setup** - подготовка на инфраструктура
5. **Test Execution** - изпълнение на тестове
6. **Test Closure** - доклади, ретроспектива

[↑ Back to Appendix](#appendix)

---

## Verification vs Validation

**Q:** Каква е разликата между Verification и Validation?

**A:**

**Verification:**

- "Are we building the product **right**?"
- Проверка дали отговаря на **спецификациите**
- Статични техники (reviews, inspections)
- Пример: Code review, documentation review

**Validation:**

- "Are we building the **right** product?"
- Проверка дали отговаря на **нуждите на потребителя**
- Динамични техники (executing the software)
- Пример: User acceptance testing (UAT)

**Мнемоника:** Verification = спецификации, Validation = потребител

[↑ Back to Appendix](#appendix)

---

## Bug Lifecycle

**Q:** Какво е Bug Lifecycle?

**A:**

Етапите на един бъг от откриване до затваряне:

1. **New** - току-що открит и репортнат
2. **Assigned** - назначен на developer
3. **Open** - developer работи по него
4. **Fixed** - developer заявява, че е фиксиран
5. **Retest** - QA тества фикса
6. **Verified** - QA потвърждава, че е фиксиран
7. **Closed** - финално затваряне
8. **Reopened** - ако бъгът все още съществува след fix

**Алтернативни статуси:**

- **Rejected** - не е бъг, или not reproducible
- **Deferred** - ще се фиксва в бъдеща версия
- **Duplicate** - вече е репортнат

[↑ Back to Appendix](#appendix)

---

## Severity vs Priority

**Q:** Каква е разликата между Severity и Priority? Дай пример.

**A:**

**Severity:**

- **Технически импакт** - колко сериозен е бъгът за системата
- Определен от QA/Tech team
- Нива: Critical, High, Medium, Low

**Priority:**

- **Бизнес импакт** - колко спешно трябва да се фиксне
- Определен от Product Owner/Management
- Нива: High, Medium, Low

**Примери:**

| Сценарий                    | Severity | Priority | Обяснение                                    |
| --------------------------- | -------- | -------- | -------------------------------------------- |
| App crash при login         | High     | High     | Критичен бъг, спешен                         |
| App crash при рядка функция | High     | Low      | Сериозен бъг, но малко потребители засегнати |
| Typo на homepage            | Low      | High     | Несериозен технически, но всички го виждат   |
| Typo в footer               | Low      | Low      | Несериозен и не е спешен                     |

**Red flags:**

- Объркване на двете понятия
- "Severity и Priority са едно и също"

[↑ Back to Appendix](#appendix)

---

## Regression Testing

**Q:** Какво е Regression testing и кога се прави?

**A:**

**Определение:**

- Тестване на **съществуваща функционалност** след промени в кода
- Гарантира, че новите промени не са счупили existing features
- Re-execution на test cases

**Кога се прави:**

- След bug fix
- След добавяне на нова функционалност
- След code refactoring
- Преди всеки release
- При промени в environment/configuration

**Типове:**

- **Full regression** - всички тестове
- **Partial regression** - само засегнатите области
- **Unit regression** - само unit tests

**Best practices:**

- Автоматизация на regression tests
- Приоритизиране на critical paths
- CI/CD integration

**Red flags:**

- "Regression testing се прави само преди release"
- Пълна липса на regression strategy

[↑ Back to Appendix](#appendix)

---

## Smoke vs Sanity

**Q:** Какво е Smoke vs Sanity testing?

**A:**

**Smoke Testing:**

- **Широки, shallow тестове** на основна функционалност
- "Дали приложението работи изобщо?"
- Прави се на **нов build** преди по-детайлно тестване
- Проверява critical paths: login, navigation, основни features
- Ако smoke fails → build се връща на dev без да continue testing

**Sanity Testing:**

- **Тесни, deep тестове** на специфична област
- След **bug fix** или **minor changes**
- Проверява дали фиксът работи и не е засегнал related functionality
- По-фокусирано от regression

**Аналогия:**

- Smoke = "Палим ли кола преди да тръгнем на път?"
- Sanity = "Фиксването на фара не е счупило ли светлините?"

**Red flags:**

- Объркване smoke с sanity
- "Smoke и Sanity са едно и също"

[↑ Back to Appendix](#appendix)

---

## Retest vs Regression

**Q:** Каква е разликата между re-test и regression?

**A:**

**Re-test (Confirmation Testing):**

- Тестване на **конкретен фиксиран бъг**
- Проверка дали фиксът работи
- Изпълняваш **същото**, което е провокирало бъга
- Пример: Бъг при login с празно поле → re-test точно това

**Regression Testing:**

- Тестване на **съществуваща функционалност** след промени
- Проверка дали новите промени не са счупили нещо друго
- Изпълняваш wide range of tests
- Пример: След login fix → тестваш цялата auth функционалност

**Съотношение:** Re-test е specifичен, Regression е broad

[↑ Back to Appendix](#appendix)

---

## Equivalence Partitioning

**Q:** Обясни Equivalence Partitioning с пример

**A:**

**Определение:**

- Разделяне на входните данни на **групи (partitions)** с еднакво поведение
- Тестваш **1 стойност от всяка група** вместо всички възможни
- Намалява броя тестове без да губиш coverage

**Пример:**

Поле за възраст (18-65 allowed):

**Partitions:**

1. **Invalid (under 18):** 0-17 → test с 10
2. **Valid:** 18-65 → test с 30
3. **Invalid (over 65):** 66+ → test с 70

Вместо 100 теста → 3 теста покриват всички случаи

**Ключ:** Всички стойности в един partition трябва да имат еднакво поведение

**Red flags:**

- Тестване на всяка възможна стойност
- Не може да идентифицира валидни/невалидни partitions

[↑ Back to Appendix](#appendix)

---

## Boundary Value Analysis

**Q:** Обясни Boundary Value Analysis с пример

**A:**

**Определение:**

- Тестване на **границите** между partitions
- Бъговете най-често се срещат на границите
- Тестваш: точно на границата, точно преди, точно след

**Пример:**

Поле за възраст (18-65 allowed):

**Boundary values за тест:**

- **Долна граница:** 17 (invalid), 18 (valid), 19 (valid)
- **Горна граница:** 64 (valid), 65 (valid), 66 (invalid)

**Комбинация с Equivalence Partitioning:**

- EP: избираш partition
- BVA: тестваш границите на partition

**Защо границите:**

- Off-by-one errors (`<` vs `<=`)
- Edge cases често са пропуснати от developers

[↑ Back to Appendix](#appendix)

---

## Good Bug Report

**Q:** Какво прави един добър bug report?

**A:**

**Характеристики:**

1. **Clear title** - кратко описание на проблема
2. **Reproducible** - стъпки за повторение
3. **Specific** - точно описание, не assumptions
4. **Complete** - всички необходими данни
5. **Objective** - факти, не емоции

**Съдържание:**

- Summary (заглавие)
- Steps to reproduce (номерирани стъпки)
- Expected result (какво трябва да стане)
- Actual result (какво се случва)
- Environment (browser, OS, version)
- Screenshots/videos (ако е визуален бъг)
- Logs/error messages
- Severity & Priority

**Red flags:**

- "Не работи" без детайли
- Липса на steps to reproduce
- Subjective opinions вместо facts

[↑ Back to Appendix](#appendix)

---

## Bug Report Fields

**Q:** Кои полета винаги попълваш?

**A:**

**Задължителни полета:**

1. **Title/Summary** - кратко описание
2. **Description** - детайлно описание
3. **Steps to Reproduce** - стъпки за повторение
4. **Expected Result** - очаквано поведение
5. **Actual Result** - реално поведение
6. **Environment** - browser, OS, app version
7. **Severity** - технически импакт
8. **Priority** - бизнес спешност
9. **Attachments** - screenshots, logs, videos

**Допълнителни полета:**

- Affected version
- Found in (environment: dev, staging, prod)
- Browser version / Device model
- User role/permissions
- Test data used

[↑ Back to Appendix](#appendix)

---

## Not a Bug

**Q:** Какво правиш, ако dev каже, че не е бъг?

**A:**

**Подход:**

1. **Слушам аргументите** - разбирам защо мисли така
2. **Проверявам документацията/requirements** - потвърждавам очакваното поведение
3. **Събирам evidence** - screenshots, logs, user stories
4. **Дискутирам спокойно** - обяснявам user impact

**Възможни сценарии:**

**Ако наистина не е бъг:**

- Признавам грешката си
- Затварям бъга като "Not a Bug"
- Уча се от случая

**Ако все още вярвам, че е бъг:**

- Ескалирам към Product Owner/BA
- Показвам user perspective
- Предлагам compromise (future enhancement)

**Ключ:** Професионална комуникация, фокус върху потребителя

**Red flags:**

- Агресивно настояване без аргументи
- Затваряне на бъга без дискусия
- Вземане на лична страна

[↑ Back to Appendix](#appendix)

---

## Bug Prioritization

**Q:** Как приоритизираш бъгове, ако имаш 20 открити?

**A:**

**Критерии за приоритизация:**

1. **Severity** - технически импакт
   - Critical/Blocker (app crash, data loss)
   - High (major feature broken)
   - Medium (workaround exists)
   - Low (cosmetic issues)

2. **Priority** - бизнес импакт
   - Брой засегнати потребители
   - Frequency на usage на feature-а
   - Business value

3. **Environment**
   - Production > Staging > Dev

4. **Release timeline**
   - Blockers за release първи

**Подход:**

- **Critical bugs в production** → immediate
- **High severity, high priority** → urgent
- **Low severity, low priority** → backlog

**Комуникация:**

- Споделям приоритизацията с team
- Обяснявам reasoning
- Adaptable към business needs

[↑ Back to Appendix](#appendix)

---

## Describe Logout Bug

**Q:** Да опише бъг с всички необходими неща - Бъгът е, че Logout на Mobile/App крашва като натиснеш бутона

**A:**

**Bug Report:**

**Title:** Mobile app crashes when tapping Logout button

**Environment:**

- Device: iPhone 13, iOS 16.5
- App version: 2.3.1
- Network: WiFi

**Steps to Reproduce:**

1. Open the mobile app
2. Login with valid credentials (user@example.com)
3. Navigate to Profile screen
4. Tap on "Logout" button

**Expected Result:**

- User is logged out successfully
- App redirects to Login screen
- Session is cleared

**Actual Result:**

- App crashes immediately after tapping Logout
- App closes completely
- User remains logged in after reopening the app

**Severity:** High (app crash)  
**Priority:** High (blocks logout functionality)

**Additional Info:**

- Reproducible: 100% (5 out of 5 attempts)
- Crash log attached
- Video recording attached
- Works fine on Android version

**Workaround:** None

[↑ Back to Appendix](#appendix)

---

## Test Login Form

**Q:** Имаш форма за логин. Какви тестове би написал?

**A:**

**Functional Tests:**

**Positive scenarios:**

- Login с valid username + valid password → success
- Remember me checkbox → session persists
- Forgot password link → redirect към reset page

**Negative scenarios:**

- Empty username → error message
- Empty password → error message
- Invalid username format → error
- Wrong password → error message
- Non-existing user → error

**UI/UX Tests:**

- Password field е masked
- Placeholder text е видим
- Error messages са clear and helpful
- Enter key triggers login
- Tab navigation работи

**Security Tests:**

- SQL injection attempts ('; DROP TABLE users--)
- XSS attempts (<script>alert('xss')</script>)
- Password не се показва в network requests plaintext
- Rate limiting (brute force protection)
- CAPTCHA след multiple failed attempts

**Performance:**

- Login response time < 2s
- Multiple concurrent logins

**Compatibility:**

- Различни browsers (Chrome, Firefox, Safari)
- Mobile devices
- Different screen sizes

[↑ Back to Appendix](#appendix)

---

## Test Calendar

**Q:** Как би тествал календар?

**A:**

**Functional Tests:**

- Свичване между месеци (next/previous)
- Свичване между години
- Избор на конкретна дата
- Display на текуща дата (highlighted)
- Leap year handling (29 Feb)
- Различни month lengths (28, 30, 31 days)

**Date Validation:**

- Invalid dates (31 April)
- Past date restrictions (ако e applicable)
- Future date restrictions
- Date range selection

**UI/UX:**

- Weekend highlighting
- Today highlighting
- Holidays marking (ако е applicable)
- Month/year dropdown selection
- Mobile responsive design
- Touch gestures (swipe months)

**Edge Cases:**

- Минимална/максимална година
- DST (Daylight Saving Time) transitions
- Different time zones
- Different date formats (DD/MM/YYYY vs MM/DD/YYYY)
- Different locales (languages, first day of week)

**Integration:**

- Event creation from calendar
- Sync with backend
- Multi-user scenarios

[↑ Back to Appendix](#appendix)

---

## Test Online Shop

**Q:** Как би тествал онлайн магазин?

**A:**

**Core Flows:**

**1. Product Catalog:**

- Search functionality
- Filtering (price, category, brand)
- Sorting (price, popularity)
- Product details page
- Image zoom/gallery

**2. Shopping Cart:**

- Add to cart
- Update quantity
- Remove items
- Cart persistence (logged in/out)
- Cart total calculation

**3. Checkout:**

- Shipping information
- Payment methods
- Order summary
- Promo code application
- Tax calculation

**4. Order Management:**

- Order confirmation
- Email notifications
- Order history
- Order tracking

**Security:**

- Payment data encryption
- PCI compliance
- SQL injection protection
- XSS protection

**Performance:**

- Page load time
- High traffic handling
- Concurrent checkouts

**Edge Cases:**

- Out of stock items
- Product price changes during checkout
- Invalid promo codes
- Payment failures
- Network interruptions

**Usability:**

- Mobile responsiveness
- Accessibility
- Clear error messages
- Intuitive navigation

[↑ Back to Appendix](#appendix)

---

## Bug Tracking Systems

**Q:** С какви bug tracking systems си работил?

**A:**

**Общи системи:**

- **Jira** - най-популярен, Agile workflows
- **Azure DevOps** - Microsoft ecosystem integration
- **Bugzilla** - open source
- **GitHub Issues** - за проекти в GitHub
- **Linear** - модерен, бърз UI

**Ключови функционалности:**

- Bug creation & tracking
- Status management (lifecycle)
- Priority & severity assignment
- Attachment support (screenshots, logs)
- Commenting & collaboration
- Linking to code commits
- Sprint/release planning
- Reporting & analytics

**Red flags:**

- Не е използвал никаква система
- "Excel spreadsheet за bug tracking"

[↑ Back to Appendix](#appendix)

---

## Agile Scrum Experience

**Q:** Работил ли си по Agile / Scrum проект?

**A:**

**Scrum ceremonies участие:**

- **Sprint Planning** - planning на работата за sprint
- **Daily Standup** - статус updates, blockers
- **Sprint Review** - demo на свършена работа
- **Sprint Retrospective** - какво да подобрим

**QA роля в Agile:**

- Участие от requirements фазата
- Continuous testing по време на sprint
- Automation development
- Collaboration с developers
- Definition of Done validation

**Artifacts:**

- User stories разбиране
- Acceptance criteria писане/review
- Bug tracking in sprint backlog

**Benefits за QA:**

- Early involvement → по-качествен продукт
- Continuous feedback loop
- Faster bug detection

**Red flags:**

- "QA тества само в края на sprint"
- Не разбира Agile principles

[↑ Back to Appendix](#appendix)

---

## HTTP Status Codes

**Q:** Какво е HTTP status code 200 / 400 / 401 / 403 / 500?

**A:**

**2xx Success:**

- **200 OK** - request успешен, данни върнати

**4xx Client Errors:**

- **400 Bad Request** - невалиден request (грешен syntax, validation)
- **401 Unauthorized** - липсва или невалидна authentication
- **403 Forbidden** - authenticated, но няма permissions
- **404 Not Found** - ресурсът не съществува

**5xx Server Errors:**

- **500 Internal Server Error** - generic server грешка
- **502 Bad Gateway** - грешка от upstream сървър
- **503 Service Unavailable** - сървърът е временно недостъпен

**QA perspective:**

- Проверка на правилните status codes
- Error messages са ясни и helpful
- Security - не leak sensitive info в errors

[↑ Back to Appendix](#appendix)

---

## HTTP Methods

**Q:** Какво е разликата между GET, POST, PUT, DELETE?

**A:**

**GET:**

- **Retrieve** data от сървър
- Idempotent (множество calls → същия резултат)
- Кешируем
- Параметри в URL
- Пример: Вземи user profile

**POST:**

- **Create** нов ресурс
- Не е idempotent (всяко call създава нов ресурс)
- Данни в request body
- Пример: Регистрация на нов user

**PUT:**

- **Update/Replace** съществуващ ресурс
- Idempotent (multiple calls → същия краен резултат)
- Пример: Update user profile

**DELETE:**

- **Remove** ресурс
- Idempotent
- Пример: Изтрий user account

**PATCH** (bonus):

- **Partial update** на ресурс
- Пример: Update само email field

[↑ Back to Appendix](#appendix)

---

## 100 Percent Tested

**Q:** Може ли софтуер да е 100% тестван?

**A:**

**Отговор: НЕ**

**Причини:**

1. **Безкрайни комбинации**
   - Infinite input combinations
   - Different user behaviors
   - Different environments

2. **Time & resource constraints**
   - Testing всичко е прекалено скъпо
   - Diminishing returns

3. **Changing requirements**
   - Софтуерът еволюира
   - Нови features, нови тестове

4. **Human limitations**
   - Не можем да предвидим всички edge cases
   - Нови бъгове се откриват в production

**Философия:**

- Целта е **adequate testing**, не exhaustive
- Risk-based approach - тестваме най-критичното
- Balance между quality и time-to-market

**Red flags:**

- "Да, можем да тестваме 100%"
- Не разбира trade-offs

[↑ Back to Appendix](#appendix)

---

## По-advanced и "gotcha" въпроси

## Decision Table Testing

**Q:** Какво е Decision Table Testing?

**A:**

**Определение:**

- Тестова техника за **комбинации от conditions** и техните outcomes
- Използва таблица за визуализация
- Полезна за сложна business logic с множество rules

**Кога се използва:**

- Multiple conditions влияят на output
- Complex business rules
- Different combinations of inputs

**Пример:**

Login функционалност с password reset option:

| Condition              | Test 1 | Test 2 | Test 3 | Test 4 |
| ---------------------- | ------ | ------ | ------ | ------ |
| Valid username         | Yes    | Yes    | No     | No     |
| Valid password         | Yes    | No     | Yes    | No     |
| **Action**             |
| Login success          | X      | -      | -      | -      |
| Show error             | -      | X      | X      | X      |
| Lock account (3 fails) | -      | -      | -      | X      |

**Benefits:**

- Comprehensive coverage на combinations
- Clear documentation
- Easy to spot missing test cases

[↑ Back to Appendix](#appendix)

---

## State Transition Testing

**Q:** Какво е State Transition Testing?

**A:**

**Определение:**

- Тестване на системи, които имат **различни states**
- Тестваш transitions между states
- Базирано на state machine diagrams

**Кога се използва:**

- Application има distinct states
- States се променят based on events/inputs
- Пример: order status, login sessions

**Пример - Order Flow:**

**States:** Pending → Confirmed → Shipped → Delivered → Returned

**Tests:**

- Valid transitions (Pending → Confirmed)
- Invalid transitions (Pending → Delivered)
- All possible paths
- Error states
- Boundary conditions

**Elements:**

- **States** - статуси на системата
- **Transitions** - промяна от един state в друг
- **Events** - trigger-и за transition
- **Actions** - какво се случва при transition

**Benefits:**

- Systematic coverage
- Clear визуализация
- Catches missing transitions

[↑ Back to Appendix](#appendix)

---

## Exploratory Testing

**Q:** Кога би използвал Exploratory Testing?

**A:**

**Определение:**

- Simultaneous learning, test design, and execution
- Без предварително писани test cases
- Тестерът учи приложението докато тества

**Кога да го използваш:**

1. **Early stages** - няма пълна документация
2. **Time constraints** - бързо намиране на бъгове
3. **Supplement scripted testing** - допълнение към formal tests
4. **New features** - първоначално проучване
5. **Complex scenarios** - които са трудни за scripting
6. **After bug fixes** - ad-hoc verification

**Типове:**

- **Freestyle** - напълно свободно
- **Scenario-based** - следваш user scenarios
- **Strategy-based** - използваш techniques (boundaries, equivalence partitioning)

**Requirements:**

- Experience & domain knowledge
- Critical thinking
- Creativity
- Good note-taking

**Плюсове:**

- Flexible и adaptive
- Finds unexpected bugs
- Quick feedback
- Тестерска creativity

**Минуси:**

- Hard to reproduce bugs понякога
- Not systematic
- Depends on tester skill
- Difficult to measure coverage

**Red flags:**

- "Exploratory = random clicking"
- Replacing all scripted tests with exploratory
- No documentation of findings

[↑ Back to Appendix](#appendix)

---

## Automation Test Selection

**Q:** Има ли Automation познания как решава кои тестове да автоматизира и кои не?

**A:**

**Кандидати за автоматизация:**

**Да автоматизираш:**

- **Regression tests** - повтарят се често
- **Smoke tests** - изпълняват се на всеки build
- **Data-driven tests** - същата логика, different data
- **Repetitive tasks** - създаване на test data
- **Stable features** - няма чести промени
- **Critical paths** - login, checkout, payment
- **API tests** - бърза и надеждна автоматизация

**Да НЕ автоматизираш:**

- **One-time tests** - не се повтарят
- **UI intensive** - frequent design changes
- **Exploratory testing** - requires human creativity
- **Usability testing** - subjective evaluation
- **Tests with frequent changes** - maintenance overhead
- **Complex visual validation** - color, alignment (unless using visual testing tools)
- **Ad-hoc testing**

**ROI Assessment:**

- **Cost** - време за scripting + maintenance
- **Benefit** - колко често се изпълнява
- **Break-even** - след колко executions си изкарваш инвестицията

**Red flags:**

- "Автоматизирай всичко"
- Автоматизация на нестабилни features
- No maintenance plan
- Игнориране на ROI

[↑ Back to Appendix](#appendix)

---

## Performance Testing

**Q:** Какво е Performance testing?

**A:**

**Определение:**

- Тестване на **speed, scalability, and stability** на системата
- Под различно натоварване

**Типове:**

**1. Load Testing:**

- Expected user load
- Проверка на нормална работа

**2. Stress Testing:**

- Beyond normal load
- Намира breaking point

**3. Spike Testing:**

- Sudden увеличение на users
- Black Friday scenario

**4. Endurance Testing (Soak):**

- Sustained load за дълъг период
- Memory leaks, resource exhaustion

**Metrics:**

- **Response time** - колко бързо отговаря
- **Throughput** - requests per second
- **Resource utilization** - CPU, memory, disk
- **Error rate** - % failed requests

**Tools:**

- JMeter
- Gatling
- LoadRunner
- k6

[↑ Back to Appendix](#appendix)

---

## Usability Testing

**Q:** Какво е Usability testing?

**A:**

**Определение:**

- Тестване на **user experience** и колко лесен за употреба е продуктът
- Focus на потребителя, не на функционалността

**Какво проверяваш:**

- **Learnability** - лесно ли е за научаване
- **Efficiency** - бързо ли се извършват tasks
- **Memorability** - помниш ли как се ползва
- **Errors** - често ли се греши, лесно ли се recovery
- **Satisfaction** - приятен ли е за ползване

**Methods:**

- **User testing** - real users изпълняват tasks
- **A/B testing** - comparison of designs
- **Surveys** - user feedback collection
- **Heatmaps** - tracking user clicks
- **Session recordings** - observe behavior

**Пример checks:**

- Clear navigation
- Intuitive UI elements
- Helpful error messages
- Consistent design
- Reasonable loading times
- Mobile-friendly

[↑ Back to Appendix](#appendix)

---

## Security Testing

**Q:** Какво е Security testing на basic ниво?

**A:**

**Определение:**

- Тестване за **vulnerabilities** и защита на данни
- Предотвратяване на unauthorized access

**Basic security checks:**

**1. Input Validation:**

- SQL Injection ('; DROP TABLE--)
- XSS (Cross-Site Scripting)
- Command injection

**2. Authentication:**

- Strong password requirements
- Password хеширане (не plaintext)
- Session management
- Logout functionality

**3. Authorization:**

- Role-based access control
- Users can access само своите данни
- Horizontal/vertical privilege escalation

**4. Data Protection:**

- HTTPS usage
- Sensitive data encryption
- No sensitive data в URLs
- Secure cookie flags

**5. Common Vulnerabilities:**

- CSRF (Cross-Site Request Forgery)
- Broken authentication
- Sensitive data exposure
- Missing security headers

**6. Error Handling:**

- Generic error messages (не leak technical details)
- No stack traces показани на users

**Tools:**

- OWASP ZAP
- Burp Suite
- Browser DevTools

[↑ Back to Appendix](#appendix)

---

## Compatibility Testing

**Q:** Какво е Compatibility testing?

**A:**

**Определение:**

- Тестване на приложението в **different environments**
- Hardware, software, network configurations

**Types:**

**1. Browser Compatibility:**

- Chrome, Firefox, Safari, Edge
- Different versions
- Browser settings (JavaScript disabled)

**2. OS Compatibility:**

- Windows, macOS, Linux
- Different versions (Windows 10, 11)
- Mobile (iOS, Android)

**3. Device Compatibility:**

- Desktop, laptop, tablet, smartphone
- Different screen sizes/resolutions
- Touch vs mouse input

**4. Network Compatibility:**

- Different connection speeds (4G, 5G, WiFi)
- Offline mode
- Bandwidth limitations

**5. Database Compatibility:**

- PostgreSQL, MySQL, SQL Server
- Different versions

**Tools:**

- BrowserStack
- Sauce Labs
- LambdaTest
- CrossBrowserTesting

**Approach:**

- Test на най-популярните combinations
- Use analytics за prioritization
- Automated testing за scale

[↑ Back to Appendix](#appendix)

---

## Accessibility Testing

**Q:** Какво означава Accessibility и как го тестваш?

**A:**

**Определение:**

- Гарантиране, че приложението е **достъпно за хора с disabilities**
- Visual, auditory, motor, cognitive impairments

**WCAG (Web Content Accessibility Guidelines):**

- Standards за accessibility
- Levels: A (minimum), AA (mid), AAA (highest)

**Key Checks:**

**1. Keyboard Navigation:**

- Tab order е логичен
- Всички функции accessible без mouse
- Focus indicators са visible

**2. Screen Reader Support:**

- Alt text за images
- Semantic HTML (headings, lists)
- ARIA labels където е нужно
- Form labels са правилно linked

**3. Visual:**

- Color contrast ratios (4.5:1 за normal text)
- Text resize до 200% без loss of functionality
- Не разчитай само на color за информация

**4. Forms:**

- Clear labels
- Error messages са descriptive
- Required fields са marked

**5. Multimedia:**

- Captions за videos
- Transcripts за audio

**Testing Tools:**

- **Automated:** WAVE, axe DevTools, Lighthouse
- **Manual:** Keyboard-only navigation
- **Screen readers:** NVDA, JAWS, VoiceOver

**Red flags:**

- "Accessibility e nice-to-have" (в много jurisdiction е legal requirement)
- Testing само със automated tools (manual е също нужен)

[↑ Back to Appendix](#appendix)

---

## SQL Injections

**Q:** SQL Injections

**A:**

**Определение:**

- **Security vulnerability** - injecting malicious SQL код
- Exploitation на improper input validation
- Може да компрометира цялата база данни

**Как работи:**

**Vulnerable code:**

```sql
SELECT * FROM users WHERE username = 'admin' AND password = '$userInput'
```

**Malicious input:**

```
' OR '1'='1
```

**Resulting query:**

```sql
SELECT * FROM users WHERE username = 'admin' AND password = '' OR '1'='1'
```

→ Връща всички users (1=1 е винаги true)

**QA Testing:**

**Test inputs:**

- `' OR '1'='1`
- `'; DROP TABLE users--`
- `admin'--`
- `' UNION SELECT * FROM passwords--`

**Prevention (за awareness):**

- **Parameterized queries** (prepared statements)
- **ORM usage** (TypeORM, Prisma)
- **Input validation**
- **Least privilege** на database users

**Red flags:**

- Не знае какво е SQLinjection
- "Frontend validation е достатъчна"

[↑ Back to Appendix](#appendix)

---

## API Testing

**Q:** Тествал ли си API? Как?

**A:**

**Какво тестваш:**

**1. Functional:**

- Correct response за valid requests
- Correct error handling за invalid requests
- Data accuracy
- Business logic

**2. Status Codes:**

- 200, 201, 400, 401, 403, 404, 500
- Appropriate codes за scenarios

**3. Response Validation:**

- Response structure (JSON schema)
- Data types
- Mandatory fields present
- Response time

**4. Authentication/Authorization:**

- Без token → 401
- Invalid token → 401
- Valid token, no permissions → 403
- Token expiration

**5. CRUD Operations:**

- **POST** - създаване на resource
- **GET** - retrieve data
- **PUT/PATCH** - update existing
- **DELETE** - removal

**6. Edge Cases:**

- Empty payloads
- Large payloads
- Special characters
- Boundary values
- Missing required fields

**Tools:**

- **Postman** - manual & automated testing
- **Newman** - Postman CLI runner
- **REST Assured** - Java library
- **SoapUI** - SOAP & REST
- **curl** - command line testing

**Automation:**

- Collection runner в Postman
- Integration в CI/CD pipeline

[↑ Back to Appendix](#appendix)

---

## Database Validation

**Q:** Как проверяваш дали данните са записани коректно в базата?

**A:**

**Methods:**

**1. Direct Database Queries:**

- SQL заявки за проверка на данни
- Compare with expected values
- После API/UI interaction

**2. Validations:**

- **Data integrity** - правилни стойности
- **Data types** - string, int, date formats
- **Constraints** - NOT NULL, UNIQUE, FOREIGN KEY
- **Relationships** - referential integrity

**3. End-to-End Flow:**

- Create data via UI/API
- Query database
- Verify data е correct
- Update via UI/API
- Query database again
- Delete via UI/API
- Verify data е removed

**4. Checks:**

- Correct table
- All mandatory fields populated
- Default values applied
- Timestamps (created_at, updated_at)
- Related records created (foreign keys)

**Tools:**

- **Database clients:** pgAdmin, MySQL Workbench, DBeaver
- **SQL scripts** - automated queries
- **ORM tools** - query via application layer

**Example:**

```sql
-- After creating user via API
SELECT * FROM users WHERE email = 'test@example.com';

-- Verify related records
SELECT * FROM user_profiles WHERE user_id = 123;
```

[↑ Back to Appendix](#appendix)

---

## SQL Usage

**Q:** Работил ли си със SQL? Дай примерна заявка.

**A:**

**Basic queries:**

**SELECT:**

```sql
-- Get all users
SELECT * FROM users;

-- Get specific columns
SELECT id, username, email FROM users WHERE is_active = true;

-- с условие
SELECT * FROM orders WHERE status = 'pending' AND total > 100;
```

**JOIN:**

```sql
-- Get users с техните orders
SELECT u.username, o.order_id, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id
WHERE o.status = 'completed';
```

**INSERT:**

```sql
INSERT INTO users (username, email, created_at)
VALUES ('testuser', 'test@example.com', NOW());
```

**UPDATE:**

```sql
UPDATE users
SET is_active = false
WHERE last_login < '2023-01-01';
```

**DELETE:**

```sql
DELETE FROM orders WHERE status = 'cancelled';
```

**Aggregation:**

```sql
-- Count orders per user
SELECT user_id, COUNT(*) as order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 5;
```

**QA Usage:**

- Verify data е inserted correctly
- Setup test data
- Clean up test data
- Validate business logic

[↑ Back to Appendix](#appendix)

---

## Flaky Submit Button

**Q:** Как би тествал бутон "Submit", който понякога не работи?

**A:**

**Approach:**

**1. Reproduce the Issue:**

- Опитвам multiple times
- Document frequency (1 in 10, random)
- Identify patterns

**2. Investigate Variables:**

**Environment:**

- Specific browser/device?
- Network conditions?
- Specific time of day?

**User actions:**

- Specific sequence of steps?
- Fast clicks (double-click)?
- Form validation state?

**System state:**

- Logged in/out?
- Concurrent requests?
- Server load?

**3. Debugging Steps:**

- **Browser DevTools:**
  - Console errors
  - Network tab (failed requests?, timing)
  - Disable browser cache
- **Test Scenarios:**
  - Slow network simulation
  - Multiple rapid clicks
  - Incomplete form data
  - Different field combinations

**4. Logging:**

- Add logging to identify failure points
- Backend logs
- Frontend error tracking

**5. Report Details:**

- Reproduction rate (e.g., 30% failure)
- Exact conditions when it fails
- Browser/network info
- Screenshots/videos
- Console logs

**Common causes:**

- Race conditions
- Client-side validation issues
- Event handler conflicts
- Network timeouts
- Double-submit prevention bugs

[↑ Back to Appendix](#appendix)

---

## Mobile Specific Tests

**Q:** Какви тестове би направил за mobile app, които не са релевантни за web?

**A:**

**Mobile-Specific Tests:**

**1. Device-Specific:**

- Different screen sizes/resolutions
- Notch handling (iPhone)
- Orientation changes (portrait ↔ landscape)
- Split-screen mode
- Picture-in-picture mode

**2. Gestures:**

- Swipe (left, right, up, down)
- Pinch to zoom
- Long press
- 3D Touch / Haptic Touch
- Pull to refresh

**3. Hardware Integration:**

- **Camera** - access, permissions, photo/video capture
- **GPS** - location services, permissions
- **Microphone** - voice input
- **Sensors** - accelerometer, gyroscope
- **Biometrics** - Touch ID, Face ID
- **Bluetooth** - connectivity
- **NFC** - payment, data transfer

**4. Network:**

- Switch WiFi ↔ Mobile data
- Offline mode & sync
- Slow/unstable connection (3G, Edge)
- Airplane mode
- Connection loss during operation

**5. Interruptions:**

- Incoming call/SMS
- Notifications
- Low battery warnings
- App sent to background
- Device lock

**6. Permissions:**

- Request/deny permissions (camera, location, contacts)
- Permission changes during app usage
- Restricted permissions impact

**7. App Lifecycle:**

- Fresh install
- App update scenarios
- Background/foreground transitions
- App kill & relaunch
- Deep linking
- Push notifications

**8. Platform-Specific:**

- iOS widgets
- Android back button
- Share functionality
- App shortcuts
- Home screen widgets

**9. Performance:**

- Battery consumption
- Data usage
- Storage usage
- Memory management
- App size

[↑ Back to Appendix](#appendix)

---

## Testing Sufficient

**Q:** Как разбираш, че тестването е „достатъчно"?

**A:**

**Няма абсолютен отговор, но критерии:**

**1. Coverage Metrics:**

- **Requirements coverage** - всички requirements тествани
- **Code coverage** - target (напр. 80%)
- **Risk-based coverage** - critical areas тествани thoroughly

**2. Quality Indicators:**

- **Bug discovery rate** намалява
- **Bug severity** - no critical/high bugs остават
- **Pass rate** на тестове е high & stable

**3. Business Criteria:**

- **Release deadline** - time constraints
- **Budget** - resource allocation
- **Risk tolerance** - acceptable level of risk

**4. Stakeholder Confidence:**

- Product Owner approval
- Team consensus
- UAT completed successfully

**5. Test Exit Criteria (predefined):**

- All planned tests executed
- X% pass rate achieved
- No blocker/critical bugs
- All high priority features tested

**Reality:**

- Testing е винаги incomplete (не може 100%)
- **Trade-off** между thoroughness and time/cost
- Focus на **risk-based testing** - highest value areas

**Red flags:**

- "Никога не е достатъчно" без reasoning
- "100% coverage" unrealistic expectation
- No criteria predefined

[↑ Back to Appendix](#appendix)

---

## No Documentation

**Q:** Какво правиш, ако няма документация?

**A:**

**Approach:**

**1. Gather Information:**

- **Talk to stakeholders:**
  - Product Owner - business requirements
  - Developers - technical implementation
  - Other QAs/team members - existing knowledge
- **Explore the application:**
  - Exploratory testing
  - Understand functionality hands-on
- **Review existing materials:**
  - User stories / Jira tickets
  - Code comments
  - Previous bug reports
  - API contracts (Swagger/OpenAPI)
  - Database schema

**2. Document While Testing:**

- Create documentation за себе си
- Test cases / scenarios
- Feature notes
- Edge cases discovered
- Questions/assumptions log

**3. Ask Questions:**

- Clarify requirements
- Confirm expected behavior
- Validate assumptions
- Don't guess

**4. Contribute:**

- Offer да създадеш documentation
- Share learnings с team
- Update wiki/confluence

**5. Adapt Testing Strategy:**

- More exploratory testing
- Risk-based approach
- Focus на critical paths първи

**Red flags:**

- Complaining without action
- Making assumptions без validation
- Not communicating blockers
- Testing without understanding requirements

[↑ Back to Appendix](#appendix)

---

## Release Deadline Pressure

**Q:** Как реагираш, ако release deadline наближава, а има отворени бъгове?

**A:**

**Approach:**

**1. Assess & Prioritize:**

- **Triage бъговете** по Severity & Priority
- Identify **blockers** vs **non-blockers**
- Risk assessment - impact на release с бъговете

**2. Communicate Clearly:**

- **Stakeholder alignment:**
  - Product Owner
  - Engineering Manager
  - Business stakeholders
- **Present facts:**
  - Number of bugs
  - Severity breakdown
  - Potential user impact
  - Risk assessment

**3. Provide Options:**

**Option A: Fix critical bugs, release**

- Timeline за critical fixes
- Accept known minor bugs
- Plan hotfix if needed

**Option B: Delay release**

- How much time needed
- Business impact of delay

**Option C: Partial release**

- Release stable features
- Feature flag unstable parts

**4. Document Decisions:**

- Known issues log
- Release notes
- Risk acceptance sign-off

**5. Post-Release Plan:**

- Hotfix readiness
- Monitoring план
- Fast rollback strategy

**Key Principles:**

- **Transparency** - don't hide issues
- **Data-driven** - facts, not emotions
- **Professional** - present options, not ultimatums
- **Supportive** - team player mindset

**Red flags:**

- Hiding bugs за да release-неш
- "Not my problem" отношение
- Blame game
- No documentation на known issues

[↑ Back to Appendix](#appendix)

---

## Manual vs Automation

**Q:** Предпочиташ ли manual или automation и защо?

**A:**

**Отговор: И двете са нужни**

**Manual Testing:**

**Кога:**

- Exploratory testing
- Usability testing
- Ad-hoc testing
- New features (първоначално)
- Visual/UX validation
- One-time tests

**Предимства:**

- Human intuition & creativity
- Quick adaptation
- Subjective evaluation (UX)
- No upfront automation cost

**Automation Testing:**

**Кога:**

- Regression tests
- Repetitive tests
- Smoke/sanity tests
- Data-driven tests
- API tests
- Performance tests
- CI/CD integration

**Предимства:**

- Fast execution
- Consistent & reliable
- Reusable
- Parallel execution
- Cost-effective long-term

**Balance:**

- **70-80% automation** - repetitive, regression
- **20-30% manual** - exploratory, usability, new features

**Career perspective:**

- Modern QA трябва да знае **и двете**
- Automation skills са increasingly важни
- Manual testing skills (test design, critical thinking) остават valuable

**Red flags:**

- "Manual testing е obsolete"
- "Automation може да замени QA напълно"
- Избор на едното без context

[↑ Back to Appendix](#appendix)

---

## Bug in Production

**Q:** Какво правиш, ако откриеш бъг в production?

**A:**

**Immediate Actions:**

**1. Assess Severity:**

- **Critical** (data loss, security, app crash)
- **High** (major feature broken)
- **Medium** (workaround exists)
- **Low** (cosmetic)

**2. Immediate Communication:**

**За critical bugs:**

- Alert team immediately (Slack, phone)
- Escalate to on-call engineer
- Notify Product Owner/Manager

**За lower severity:**

- Log bug report
- Normal communication channels

**3. Document:**

- Create detailed bug report:
  - Steps to reproduce
  - User impact
  - Affected users (all? specific segment?)
  - Screenshots/logs
  - Environment details

**4. Mitigation:**

**Temporary:**

- Disable feature (feature flag)
- Add warning message
- Provide workaround to users/support

**Long-term:**

- Hotfix deployment
- Regular fix в next release

**5. Root Cause Analysis:**

- Как бъгътe escaped testing?
- Coverage gaps?
- Process improvements needed?

**6. Learn & Improve:**

- Add regression test
- Update test strategy
- Improve testing process

**Communication Template:**

```
🚨 Production Issue
Severity: High
Impact: 20% of users cannot checkout
Repro: When using promo code XYZ
Workaround: Available - use different promo
ETA: Fix being deployed in 30 min
```

**Red flags:**

- Паника без action
- Не документирам properly
- Blame game
- Липса на follow-up

[↑ Back to Appendix](#appendix)

---

## Cannot Replicate Bug

**Q:** Какво би направил, ако не можеш да репликираш бъг?

**A:**

**Investigation Steps:**

**1. Gather More Details:**

- **Reporter information:**
  - Exact steps
  - Environment (OS, browser, version)
  - User account/role
  - Screenshots/video
  - Logs/error messages
  - Timing (when did it happen?)

**2. Try Different Variables:**

**Environment:**

- Different browser/device
- Different OS version
- Network conditions (slow connection)
- Different data center/region

**User State:**

- Specific user permissions
- Account age/status
- Data conditions
- Cache state (clear/full)

**Timing:**

- Time of day (server load)
- Sequence of actions before bug
- Race conditions

**3. Reproduce Techniques:**

- **Pair with reporter** - watch them reproduce
- **Use their environment** - same browser, same data
- **Check logs** - backend/frontend for clues
- **Database state** - specific data causing issue

**4. If Still Cannot Reproduce:**

**Document everything:**

- All reproduction attempts
- Environments tried
- Variables tested
- Hypothesis

**Label appropriately:**

- "Cannot Reproduce" status
- Keep bug open (don't close immediately)
- Add "need more info" tag

**Monitor:**

- Watch for similar reports
- Set up logging/monitoring
- If it happens again, gather more data

**5. Communicate:**

- Update reporter with findings
- Ask for additional info
- Explain attempts made

**Possible Outcomes:**

- **Intermittent bug** - happens rarely, needs monitoring
- **Environment-specific** - requires specific setup
- **Data-specific** - needs specific test data
- **Invalid bug** - reporter misinformation (rare)

**Red flags:**

- Immediately closing as "cannot reproduce"
- Not trying different scenarios
- Blaming reporter
- Giving up too quickly

[↑ Back to Appendix](#appendix)

---

## Definition of Done

**Q:** Какво е Definition of Done и защо е важно за QA?

**A:**

**Определение:**

- **Checklist** на criteria, които трябва да са completed преди story/task да се счита за "Done"
- Agreement между team members
- Part of Agile/Scrum practices

**Typical DoD включва:**

**Development:**

- Code написан and reviewed
- Unit tests written & passing
- Code merged to main branch
- No code smells/technical debt

**Testing:**

- Functional tests passed
- Regression tests passed
- Integration tests passed
- Bug fixing completed

**Documentation:**

- Code commented
- API documentation updated
- User documentation updated (ако е нужно)

**Deployment:**

- Deployed на staging/test environment
- Smoke tests passed
- Performance check passed

**Sign-off:**

- Product Owner acceptance
- QA approval

**Защо е важно за QA:**

1. **Clear expectations** - QA знае кога да започне testing
2. **Quality gate** - prevents incomplete work being released
3. **Consistency** - same standards across team
4. **Transparency** - всички знаят what "done" means

**Example:**

```
✅ Code reviewed & approved
✅ Unit tests > 80% coverage
✅ QA test cases executed & passed
✅ No critical/high bugs open
✅ Documentation updated
✅ Deployed to staging
✅ PO approval
```

**Red flags:**

- No DoD defined
- DoD exists но не се следва
- QA не е part of DoD definition

[↑ Back to Appendix](#appendix)

---

## Team Communication

**Q:** Как комуникираш проблеми с product owner или dev?

**A:**

**Principles:**

**1. Clear & Specific:**

- Use concrete examples
- Provide evidence (screenshots, logs)
- Avoid vague statements

**2. Timely:**

- Report blockers immediately
- Regular updates за progress
- Don't wait until last minute

**3. Professional:**

- Focus on facts, not emotions
- Constructive tone
- Solution-oriented

**4. Context-Aware:**

- Understand their priorities
- Present business impact
- Provide options, not just problems

**With Developers:**

**Bug Reports:**

- Detailed reproduction steps
- Expected vs actual results
- Environment details
- Logs & screenshots
- Severity & priority

**Communication:**

- Respectful and collaborative
- Ask questions за clarification
- Offer to pair program/debug together
- Acknowledge time constraints

**With Product Owner:**

**Issue Escalation:**

- Business impact focus
- User perspective
- Risk assessment
- Options with trade-offs

**Questions:**

- Clarify requirements
- Confirm expected behavior
- Discuss edge cases
- Propose alternatives

**Channels:**

- **Urgent:** Direct message, call
- **Normal:** Jira comments, Slack
- **Discussion:** Meetings, stand-ups

**Example - Bug Communication:**

```
Hi [Dev],

Found an issue with the checkout flow:

🔍 Issue: Payment fails when using saved cards
📊 Impact: 40% of users use saved cards
🔁 Repro rate: 100% (tested 10 times)
📝 Steps: [detailed steps]
🖼️ Screenshots attached
📋 Console errors: [paste errors]

Environment: Chrome 120, Windows 11, Staging
Priority: High (blocks checkout)

Let me know if you need more details!
```

**Red flags:**

- Aggressive/defensive tone
- Incomplete information
- Blame language
- Не escalate blockers

[↑ Back to Appendix](#appendix)

---

## Conflict with Developer

**Q:** Разкажи за конфликт с developer и как го реши

**A:**

**Използвай STAR метод:**

**Situation:**

- Context на конфликта (напр. disagreement за bug severity)

**Task:**

- Целта (reach agreement, deliver quality)

**Action:**

- Конкретни стъпки за resolution

**Result:**

- Outcome и learning

**Example Answer:**

"Имахме disagreement за бъг, който аз класифицирах като high severity, но developer-ът каза че е low priority.

**Бъгът:** Order confirmation emails не се изпращаха понякога.

**Моята позиция:** High severity - потребителите не получават confirmation, контактуват support.

**Dev позиция:** Low severity - order-ите се създават правилно в системата, email e 'nice to have'.

**Моите действия:**

1. **Изслушах аргументите** - разбрах че from technical perspective, core functionality работи

2. **Събрах данни:**
   - Support tickets - 15% от complaints са за липсващи emails
   - User survey - 80% очакват immediate confirmation
   - Business policy - confirmation email е part of purchase contract

3. **Презентирах evidence** на Product Owner:
   - User impact
   - Support burden
   - Business requirements

4. **Reached agreement:**
   - Upgraded към high priority
   - Planned за next sprint
   - Dev appreciated data-driven approach

**Result:**

- Bug fixed в next release
- Improved relationship с developer (mutual respect)
- Established practice - data-driven priority decisions

**Learning:**

- Evidence > opinions
- Listen first
- Involve appropriate stakeholders
- Professional collaboration"

**Red flags:**

- Personal attacks
- Не listening to other side
- Ego-driven approach
- Unresolved conflict

[↑ Back to Appendix](#appendix)

---

## Critical Bug Insistence

**Q:** Ситуация, в която си настоявал, че бъгът е критичен

**A:**

**STAR Format:**

**Example Answer:**

**Situation:**

"During pre-release testing, открих че logout функцията на mobile app не изчиства authentication token от device storage."

**Technical details:**

- Token оставаше saved locally
- После logout, ако reload-неш app, user was still logged in
- Security vulnerability

**Initial Response:**

- Dev team каза "low priority - users don't logout често"
- Планувано за next sprint

**Task:**

- Demonstrate security risk
- Get it fixed before release

**Action:**

1. **Assessment:**
   - OWASP security guidelines research
   - Token expiration - set за 30 days
   - Public devices risk (family tablets)
   - Compliance requirements (GDPR)

2. **Evidence gathering:**
   - Demo на security team - показах че могаt da access account after logout
   - Documented scenario: User logs out на public device → next person can access account
   - Pointed out - financial/personal data exposed

3. **Escalation:**
   - Presented findings на Security Lead
   - Showed potential PR damage
   - Regulatory compliance risk

4. **Collaboration:**
   - Worked с dev за fast fix
   - Proposed solution review
   - Verified fix thoroughly

**Result:**

- Upgraded към **Critical**
- Fixed before release
- Added tokenклирач като part of DoD
- Security team praised due diligence

**Learning:**

- Security bugs need extra attention
- Data + risk assessment = effective escalation
- Persistence с professionalism pays off

**Red flags:**

- Pure emotion-based escalation
- No evidence
- Backing down без validation
- Not following up

[↑ Back to Appendix](#appendix)

---

## Quality Without Bugs

**Q:** Ако няма бъгове – значи ли, че продуктът е качествен?

**A:**

**Отговор: НЕ**

**Quality ≠ Bug-Free**

**Quality = Product meets user needs & expectations**

**Може да няма бъгове, но:**

**1. Poor Usability:**

- Confusing navigation
- Unintuitive UI
- Frustrating user experience
- Technically correct, но unusable

**2. Slишком Performance:**

- Slow loading times
- High battery drain
- Excessive data usage
- Работи, но poorly

**3. Missing Features:**

- Doesn't solve user problems
- Limited functionality
- Competitor products са по-добри

**4. Bad UX:**

- Too many steps for simple tasks
- Inconsistent design
- Lack of feedback
- Accessibility issues

**5. Poor Documentation:**

- Users can't figure out how to use it
- No help/support

**6. Security Concerns:**

- No bugs, но weak security
- Privacy issues

**Quality Dimensions:**

- **Functionality** - does it work?
- **Reliability** - works consistently?
- **Usability** - easy to use?
- **Performance** - fast enough?
- **Security** - safe to use?
- **Maintainability** - easy to update?

**"It's possible to build a perfect, bug-free product that nobody wants to use."**

**Red flags:**

- "No bugs = quality"
- Focusing само on defect count
- Ignoring user satisfaction

[↑ Back to Appendix](#appendix)

---

## QA Added Value

**Q:** Как QA добавя стойност, освен че намира бъгове?

**A:**

**QA Value Beyond Bug Finding:**

**1. Early Prevention:**

- Requirements review - catch ambiguities early
- Design review - identify issues before coding
- **Cost savings** - cheaper to fix early

**2. User Advocate:**

- Represent user perspective
- Usability feedback
- Accessibility considerations
- Real-world scenario testing

**3. Quality Culture:**

- Promote quality mindset в team
- Best practices sharing
- Knowledge transfer

**4. Risk Mitigation:**

- Identify risks early
- Prioritize testing based on risk
- Provide confidence за release decisions

**5. Process Improvement:**

- Identify bottlenecks
- Suggest automation opportunities
- Improve workflows
- Metrics & reporting

**6. Documentation:**

- Test coverage documentation
- Known issues tracking
- Release notes contribution
- Knowledge base building

**7. Cross-Team Collaboration:**

- Bridge между business & tech
- Facilitate communication
- Clarify requirements

**8. Customer Satisfaction:**

- Better product quality → happy users
- Fewer production issues → less support burden
- Trust & reputation protection

**9. Automation:**

- Build automation frameworks
- Enable fast feedback loops
- CI/CD integration
- Free up time for exploratory testing

**10. Domain Expertise:**

- Deep product knowledge
- Business logic understanding
- Training new team members

**Mindset Shift:**

**Old view:** QA = Bug finder  
**Modern view:Š** QA = Quality enabler, collaborator, advocate

**Red flags:**

- "Само намираме бъгове"
- Reactive вместо proactive
- Isolated от team

[↑ Back to Appendix](#appendix)
