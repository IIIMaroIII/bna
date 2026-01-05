### ================================= UI MAP =================================
## Abbreviation 
- CTA - Call to Action (кнопка/ссылка/баннер)
- RBAC - Role-based access control 

## Роли
- `Public` (неавторизованный): сайт + регистрация/логин
- `Employee`: clock-in/clock-out + история часов
- `Admin`: управление users/teams/projects + отчёты + корректировка времени
- `Owner`: всё что Admin + admins management + company settings + allowed networks + billing/access

---

## 1) Глобальная логика доступа (важно)

### 1.1 Access states (Company)
- FULL: App Area доступен по RBAC
- READ_ONLY: App Area доступен, но любые Create/Update/Deactivate + clock-in/clock-out запрещены
- BLOCKED:
  - App Area доступен, но активен только Settings → Subscription
  - остальные разделы недоступны (redirect в Settings)

> `subscription_status = EXPIRED` логически приводит к `access_status = BLOCKED`.  
> (то есть EXPIRED — причина, BLOCKED — поведение UI)

### 1.2 Trial banner
- Показывать только если subscription_status = TRIAL (ненавязчиво сверху)
- Если subscription_status = ACTIVE — баннеров нет
- Если subscription_status = EXPIRED — показывать Subscription-блок в Settings (так как будет BLOCKED)

### 1.3 Если подписка истекла во время активной WorkSession
- Система автоматически закрывает WorkSession (auto clock-out)
- Пользователь попадает в BLOCKED режим (Settings only) с сообщением “Subscription expired”

---

## 2) Глобальные UI паттерны (layout)

### 2.1 Public Layout
- Navbar: Logo + Features + Pricing + Contacts + Feedback + Sign In + Sign Up
- Footer

### 2.2 App Layout (после логина)
- Sidebar/Topbar
- User menu: Profile / Logout
- Banner area:
  - показывать только TRIAL (compact)
- BLOCKED:
  - Sidebar содержит только Settings + Logout

---

# 3) Public Area (без логина)

## 3.1 Home Page
Назначение: презентация продукта + CTA

Секции:
- Hero + CTA “Start free trial”
- Features preview (3–6 карточек)
- Pricing preview (карточки тарифов)
- How it works
- Footer

Переходы:
- CTA → Sign Up Wizard
- Navbar → Features / Pricing / Contacts / Feedback / Sign In / Sign Up

## 3.2 Features Page
Контент:
- список Feature карточек (title, description, icon)

Действия:
- CTA “Start free trial” → Sign Up

---

## 3.3 Pricing Page
Контент:
- PricingPlan cards/table (monthly/annual, currency, featured)
- PricingPlanBenefit list per plan

Действия:
- CTA “Start free trial” → Sign Up

---

## 3.4 Contacts Page
Контент:
- контакты
- (optional) форма “Request a demo”

---

## 3.5 Feedback Page
Контент:
- форма Feedback:
  - first_name (opt)
  - last_name (opt)
  - email (opt)
  - message (required)
- форма Bug report:
  - first_name (required)
  - email (required)
  - description (required)
  - media (optional)
- success state

---

## 3.6 Sign In Page
Контент:
- form: login/email + password
- link: “Create company” → Sign Up

Состояния:
- invalid credentials
- user inactive

Переходы:
- success → App Area (по роли)
- если company в BLOCKED:
  - success → App Area → Settings (Subscription)

---

## 3.7 Sign Up Wizard (multi-step)
Цель: создать Company + Owner + trial + (optional) promo

### Step 1 — Company Info
- company_name (required)
- industry (required)
- timezone (optional)
- max_active_teams_per_employee (required; preset 3/5/10)
- work_location_policy (required; WIFI_REQUIRED)
- promo_code (optional)

### Step 2 — Owner Account
- owner_email (required)
- owner_password (required)
- first_name/last_name (optional)

### Step 3 — Confirmation
- “Company created”
- trial_ends_at (дата) + days left
- CTA “Go to Dashboard” → Owner Dashboard

---

# 4) App Area — Common

## 4.1 Settings (Common entry point for BLOCKED)
Роль: Employee/Admin/Owner

### Profile section
- first_name, last_name
- email (если есть)
- work_mode (ONSITE/REMOTE) read-only для Employee (назначает Admin)

Actions:
- Save 

### Subscription section (видимость)
- для Owner: всегда видим
- для остальных: показывать только если BLOCKED (read-only)

Содержит:
- subscription_status
- access_status
- trial_ends_at + days left
- subscription_name
Actions:
- Owner: Enter promo code
- Owner: Manage subscription → Pricing page (Public)
- Contact support link

---

# 5) Employee Area (FULL/READ_ONLY)

## 5.1 Employee Dashboard
Блоки:
- Status: clocked-in / clocked-out
- Active session card (если есть): project + start_time
- Quick actions:
  - Clock-in
  - Clock-out
- My Teams list (ACTIVE memberships)
- Projects:
    - active (list)
    - inactive (list)
- Weekly summary (часы за текущую неделю)
- Link: Time History

Состояния:
- no teams → empty state “Not assigned to any team”
- no available project → empty state “No projects available”
- READ_ONLY → Clock-in/Clock-out disabled + подсказка
- work_mode:
  - ONSITE:
    - not verified → error banner + CTA “Try again”
    - verified → status “Successfully verified”
  - REMOTE:
    - status “Remote position” (wifi check не нужен)

Переходы:
- Clock-in → Clock-in Screen
- Clock-out → confirm modal → Dashboard
- Time History → Time History Screen

## 5.2 Clock-in Screen
Верхний блок:
- Work mode (ONSITE/REMOTE) (read-only, “Assigned by Admin”)
- Если ONSITE:
  - Work location verification status: Verified / Not verified
  - CTA “Try again” (повторить проверку)
  - если Not verified и WIFI_REQUIRED → "Clock in" disabled

Основной блок:
- Список доступных проектов (только ACTIVE)
- Для проекта показывать: Assigned team (команда проекта)
- CTA "Clock in"

Состояния:
- no projects → empty state
- READ_ONLY → "Clock in" disabled + причина

Переходы:
- "Clock in" → Employee Dashboard (active session)

---

## 5.3 Time History Screen
Контент:
- таблица/список: date, project, start_time, end_time, duration, team / close_reason
Фильтры:
- period (week/month/custom)
- project (optional)

Состояния:
- empty history

---

# 6) Admin Console (FULL/READ_ONLY)

## 6.1 Admin Dashboard
Widgets:
- total employees (active/inactive)
- total teams
- active projects / inactive projects
- active sessions (кто clocked-in + project + start_time)
Shortcuts:
- Create Employee
- Create Team
- Create Project

READ_ONLY:
- create shortcuts disabled

---

## 6.2 Employees (List)
Контент:
- table: name, role, status, teams, work_mode, projects
Кнопки:
- Create Employee
Действия в строке:
- Open details
- Edit (Update)
- Deactivate

READ_ONLY:
- Create/Edit/Deactivate disabled

---

## 6.3 Create/Edit Employee (Form / Modal)
Поля:
- first_name, last_name
- login (required)
- email (optional; required только для Owner)
- password (set or generate)
- role (default employee)
- work_mode: ONSITE/REMOTE (назначает Admin)
- status (ACTIVE by default) -> поменять статус можно в окне Edit

Действия:
- Save / Cancel

---

## 6.4 Employee Details
Блоки:
- Profile + status
- Teams membership:
  - list teams (ACTIVE/INACTIVE)
  - Add to team (Create TeamMember)
  - Remove from team (Deactivate TeamMember)
- Recent sessions preview СTA -> to Time Overview filtered

READ_ONLY:
- Add/Remove disabled

---

## 6.5 Teams (List)
Контент:
- table: name, status, members, projects
Кнопки:
- Create Team
Действия в строке:
- Open details
- Edit
- Deactivate

READ_ONLY:
- Create/Edit/Deactivate disabled

---

## 6.6 Create/Edit Team (Form)
Поля:
- name (required)
- description (optional)
- add members -> выбор со списка уже созданных Employees
Действия:
- Save / Cancel

---

## 6.7 Team Details
Блоки:
- team info (name/description/status)
- Members list + add/remove
- Projects list
  - empty state: “Not assigned projects yet”
  - list of Active Projects


READ_ONLY:
- add/remove disabled

---

## 6.8 Projects (List)
Контент:
- table: name, status, assigned team
Кнопки:
- Create Project
Действия в строке:
- Open details
- Edit
- Deactivate

READ_ONLY:
- Create/Edit/Deactivate disabled

---

## 6.9 Create/Edit Project (Form)
Поля:
- name (required)
- add a team (выбор со списка уже созданных, если есть) (optional) 
- description (optional)
- location (optional)
Действия:
- Save / Cancel

---

## 6.10 Project Details
Блоки:
- project info (name/description/location/status)
- Assigned team:
  - если нет → Assign team (Create ProjectTeamAssignment)
  - если есть → Show team + Change team
    - Change team: Deactivate old ProjectTeamAssignment + Create new
- Sessions summary:
  - total hours (period)
  - active sessions list

READ_ONLY:
- assign/change disabled

---

## 6.11 Time Overview
Tabs:
- By Employees: employee → total hours (period)
- By Projects: project → total hours (period)
- Active Sessions: employee + project + start_time

READ_ONLY:
- просмотр разрешён, редактирование нет

---

## 6.12 WorkSession Detail (Admin)
Контент:
- employee, project, team
- start_time, end_time, duration_ms, close_reason
Действия:
- Edit WorkSession (Update) 
    - выпадающий список активных WorkSessions
    - поиск по (filtered list of Employees)
        - First name Employee
        - Last Name Employee
        - etc
    - поле редактирования времени начала WorkSession
    - Save (фиксирует updated_by_user_id)

READ_ONLY:
- edit disabled

---

# 7) Owner-only extensions

## 7.1 Admins Management
Actions:
- Create / Edit / Deactivate
- create Admin:
  - выбор из Users (или создание нового User с role=ADMIN)

READ_ONLY:
- actions disabled

---

## 7.2 Company Settings (Owner)
Блоки:
- company_name
- industry
- timezone
- policies:
  - max_active_teams_per_employee
  - work_location_policy
Actions:
- Update

READ_ONLY:
- disabled

---
## 7.3 Allowed Networks (Owner)
Контент:
- list: label, network_identifier, status
Actions:
- Add / Edit / Deactivate

Empty state:
- если WIFI_REQUIRED и список пуст → warning “Clock-in not possible for onsite employees”

READ_ONLY:
- disabled

---

## 7.4 Billing & Access (Owner)
Контент:
- subscription_status
- access_status
- trial_ends_at + days left
- subscription_name
Actions:
- Enter promo code
- Manage subscription → Pricing page (Public)
- Contact support

Состояния:
- invalid promo code
- promo expired / promo inactive
- max redemptions reached
