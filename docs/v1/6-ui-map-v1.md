### ================================= UI MAP =================================
## Abbreviation 
- CTA - Call to Action (кнопка/ссылка/баннер)
- RBAC - Role-based access control 

## Роли
- `Public` (неавторизованный): сайт + регистрация/логин
- `Employee`: clock-in/clock-out + история часов
- `Admin`: управление users/teams/projects + отчёты + корректировка времени
- `Owner`: всё что Admin + admins management + company settings + allowed networks + billing/access

## Состояния доступа (важно для UI)
- `Access FULL`: обычная работа по RBAC
- `Access READ_ONLY`: вход разрешён, но любые Create/Update/Deactivate + clock-in/clock-out запрещены (кнопки disabled + объяснение)
- `Access BLOCKED`: редирект на экран “Access blocked” (только logout + contacts + promo code)

## 1 Public Area (без логина) ##

*1.1 Home Page*
Роль: Public  
Цель: презентация + CTA на регистрацию

Секции:
- Navbar: Logo + Features + Pricing + Contacts + Feedback + Sign In + Sign Up
- Hero: value proposition + CTA “Start free trial”
- Features preview (3–6 карточек)
- Pricing preview (карточки тарифов)
- How it works (3 шага)
- Footer: contacts, links

Переходы:
- CTA → Sign Up or Sign In Page
- Navbar → Features/Pricing/Contacts/Feedback/Sign In/Sign Up

*1.2 Features Page*
Роль: Public  
Контент:
- список Feature карточек (title, description, icon)

Действия:
- CTA “Start free trial” → Sign Up

*1.3 Pricing Page*
Роль: Public  
Контент:
- PricingPlan cards/table (monthly/annual, currency, is_featured)
- PricingPlanBenefit list per plan

Действия:
- CTA “Start free trial” → Sign Up

*1.4 Contacts Page*
Роль: Public  
Контент:
- контакты (email, phone, socials)
- mini-form “Request a demo” - в V2 будет чтото типа запрос на сотрудничество. Какая то формочка пока не важно какая

*1.5 Feedback Page*
Роль: Public  
Контент:
- форма: first_name (opt), last_name (opt), email (required), message (required)
- success state: “Thanks что то там успешно отправили”
- отдельная формочка - отправить баг или сообщить об ошибке или чтото такое. Нужно указать email, описание (своими словами или лучше саму ошибку), и приложить media (скриншот - опционально)

*1.6 Sign In Page*
Роль: Public  
Контент:
- form: login/email + password
- link: “Create company” → Sign Up
- error state: invalid credentials / user inactive / access blocked

Переходы:
- success → App Area route по роли (Employee/Admin/Owner)

*1.7 Sign Up Wizard (многошаговая форма)*
Роль: Public  
Цель: создать Company + Owner, применить trial и (опционально) promo_code

    Step 1 — Company Info
    - company_name (required)
    - industry (required)
    - timezone (optional)
    - max_active_teams_per_employee (required; preset 3/5/10)
    - work_location_policy (required; WIFI_REQUIRED)
    - promo_code (optional)
    - accept terms (optional)

    Step 2 — Owner Account
    - owner_email (required)
    - owner_password (required)
    - first_name/last_name (optional)

    Step 3 — Confirmation
    - “Company created”
    - trial_ends_at (дата) + “days left”
    - CTA “Go to Dashboard”

Переходы:
- success → Owner Dashboard

## 2 App Area (после логина)

## Общие элементы layout (все роли)
- App Topbar/Sidebar
- User menu: Profile / Logout
- Trial banner (если subscription_status=TRIAL)
- Expired banner (если subscription_status=EXPIRED)
- Read-only labels (если access_status=READ_ONLY)
- Access blocked screen (если access_status=BLOCKED)

## 3 Employee Area

## 3.1 Employee Dashboard
Цель: статус + быстрые действия + сводка

Блоки:
- Status card: clocked-in / clocked-out
- Active session (если есть): project, start_time
- Quick actions:
  - Clock-in
  - Clock-out
- My Teams: список команд, в которых состоит (ACTIVE)
- Weekly summary: часы за текущую неделю
- Link: Time History

Состояния:
- no teams: “You are not assigned to any team”
- no available projects: “No projects available”
- READ_ONLY: clock buttons disabled + banner
- WIFI required failed: error banner

Переходы:
- Clock-in → Clock-in Screen
- Clock-out → confirm modal → Dashboard
- Time History → Time History Screen
