# UI MAP — Screen Cards

## 0) ID FORMAT (паспорт экрана)

S###/name/area/roles/access

S###  / name        / area / roles               / access
ID   / screen_name  / P|A  / P|E|A|O (concat)     / F|RO|B (comma)

### area
- P = Public (до логина)
- A = App (после логина)

### roles (всегда в порядке E A O)
- P = Public
- E = Employee
- A = Admin
- O = Owner

### access (всегда в порядке F,RO,B)
- F  = FULL
- RO = READ_ONLY
- B  = BLOCKED (Settings-only mode)
- "-" = не применимо (Public экраны)

### naming rules
- screen_name: только `snake_case` (без пробелов)
- roles: только `E`, `A`, `O` в порядке `EAO`
- access: только `F,RO,B` в порядке `F,RO,B`

---

## 1) Диапазоны ID
- Public:      S001–S049
- App common:  S050–S099
- Employee:    S100–S149
- Admin/Owner: S150–S199
- Owner-only:  S200–S249

---

## 2) Access behavior (важно для всех)
- FULL: всё работает по RBAC
- READ_ONLY:
  - доступ к App Area есть
  - запрещены Create/Update/Deactivate и clock-in/clock-out
  - UI показывает disabled кнопки + reason
- BLOCKED:
  - доступ к App Area есть, но активен только Settings → Subscription
  - sidebar: только Settings + Logout
  - любые попытки открыть другие разделы → redirect в Settings

**Связка с доменом**
- `subscription_status = EXPIRED` ⇒ `access_status = BLOCKED` (причина vs поведение)

---

# 3) SCREEN CARDS INDEX (короткая таблица)

| ID Passport | Purpose | Primary CTA | Key Entities |
|---|---|---|---|
| S001/home/P/P/- | Landing + CTA | start_free_trial | Feature, PricingPlan |
| S002/features/P/P/- | Features listing | start_free_trial | Feature |
| S003/pricing/P/P/- | Pricing plans | start_free_trial | PricingPlan, PricingPlanBenefit |
| S004/contacts/P/P/- | Contacts / demo | contact_support | - |
| S005/feedback/P/P/- | Feedback + bug report | submit | FeedbackMessage |
| S006/sign_in/P/P/- | Login | sign_in | User, Company |
| S007/sign_up_wizard/P/P/- | Create Company + Owner | create_company | Company, User, PromoCode |

| S050/app_shell/A/EAO/F,RO,B | App layout + routing | navigate | User, Company |
| S051/settings/A/EAO/F,RO,B | Profile + subscription | save / apply_promo | User, Company, PromoCode |

| S100/employee_dashboard/A/E/F,RO | Status + quick actions | clock_in / clock_out | WorkSession, TeamMember, Project |
| S101/clock_in/A/E/F,RO | Select project + start shift | clock_in | WorkSession, Project, ProjectTeamAssignment |
| S102/time_history/A/E/F,RO | Own sessions list | filter | WorkSession |

| S150/admin_dashboard/A/AO/F,RO | Company overview | create_employee | User, Team, Project, WorkSession |
| S151/employees_list/A/AO/F,RO | Employees CRUD entry | create_employee | User, TeamMember |
| S152/employee_form/A/AO/F,RO | Create/Edit employee | save | User |
| S153/employee_details/A/AO/F,RO | Membership + sessions | add_to_team | User, TeamMember, WorkSession |
| S160/teams_list/A/AO/F,RO | Teams CRUD entry | create_team | Team, TeamMember |
| S161/team_form/A/AO/F,RO | Create/Edit team | save | Team, TeamMember |
| S162/team_details/A/AO/F,RO | Members + projects | add_member | Team, TeamMember, ProjectTeamAssignment |
| S170/projects_list/A/AO/F,RO | Projects CRUD entry | create_project | Project, ProjectTeamAssignment |
| S171/project_form/A/AO/F,RO | Create/Edit project | save | Project, ProjectTeamAssignment |
| S172/project_details/A/AO/F,RO | Assign/change team + summary | assign_team | Project, ProjectTeamAssignment, WorkSession |
| S180/time_overview/A/AO/F,RO | Reports | apply_filter | WorkSession, User, Project |
| S181/work_session_detail/A/AO/F,RO | View/Edit session | save_changes | WorkSession, User |

| S200/admins_management/A/O/F,RO | Manage admins | create_admin | User |
| S210/company_settings/A/O/F,RO | Company policies | update | Company |
| S220/allowed_networks/A/O/F,RO | Wi-Fi allowlist | add_network | AllowedCompanyNetwork, Company |
| S230/billing_access/A/O/F,RO,B | Subscription + promo | apply_promo | Company, PromoCode |

---

# 4) DETAILED SCREEN CARDS (полные карточки)

> Формат карточки:
> - Purpose
> - Visible to
> - Access behavior
> - Entities touched
> - UI Components
> - Actions (CRUD/CTA)
> - States (empty/error/readonly/blocked)
> - Key Rules (из domain-model + FR)

---

## S001/home/P/P/-
### Purpose
Landing page: объяснить продукт и привести к регистрации.
### Visible to
Public
### Access behavior
-
### Entities touched
Feature (read), PricingPlan (read)
### UI Components
Navbar, Hero, Features preview, Pricing preview, How it works, Footer
### Actions
CTA: start_free_trial → S007/sign_up_wizard/P/P/-
### States
-
### Key Rules
Public area, без авторизации.

---

## S002/features/P/P/-
### Purpose
Показать преимущества (features) платформы.
### Entities touched
Feature (read)
### UI Components
Feature cards (title, description, icon)
### Actions
CTA: start_free_trial → S007
### States
-

---

## S003/pricing/P/P/-
### Purpose
Показать тарифы/планы.
### Entities touched
PricingPlan (read), PricingPlanBenefit (read)
### UI Components
Plans list (monthly/annual), benefits, featured plan highlight
### Actions
CTA: start_free_trial → S007
### States
-

---

## S004/contacts/P/P/-
### Purpose
Дать контакт (и опционально demo request).
### Entities touched
-
### UI Components
Contact info, optional form
### Actions
contact_support (link)
### States
optional success/error

---

## S005/feedback/P/P/-
### Purpose
Сбор feedback + bug reports.
### Entities touched
FeedbackMessage (create)
### UI Components
- Feedback form: first_name(opt), last_name(opt), email(opt), message(req)
- Bug report form: first_name(req), email(req), description(req), media(opt)
### Actions
submit
### States
validation errors, success

---

## S006/sign_in/P/P/-
### Purpose
Вход пользователя.
### Entities touched
User (auth), Company (access status check)
### UI Components
login/email + password, link to SignUp
### Actions
sign_in
### States
- invalid credentials
- user inactive
- company BLOCKED: success → S050/app_shell → redirect S051/settings (Subscription)
### Key Rules
- Inactive user cannot login
- If access_status=BLOCKED ⇒ Settings-only mode

---

## S007/sign_up_wizard/P/P/-
### Purpose
Регистрация Company + Owner + trial + promo(optional).
### Entities touched
Company (create), User (create owner), PromoCode (optional redeem)
### UI Components
Step1 Company info:
- company_name (req)
- industry (req)
- timezone (opt)
- max_active_teams_per_employee (req preset 3/5/10)
- work_location_policy (req WIFI_REQUIRED)
- promo_code (opt)
Step2 Owner account:
- owner_email (req)
- owner_password (req)
- first_name/last_name (opt)
Step3 Confirmation:
- trial_ends_at + days_left
### Actions
create_company, go_to_dashboard
### States
validation errors, promo invalid/expired, success
### Key Rules
- Registration creates Company + Owner
- Trial initialized on creation
- Promo applies only if valid and within limits

---

## S050/app_shell/A/EAO/F,RO,B
### Purpose
Общий каркас App Area: routing + navigation.
### Entities touched
User (current), Company (access_status, subscription_status)
### UI Components
Sidebar/Topbar, User menu, optional Trial banner
### Actions
navigate, open_settings, logout
### States
- FULL: normal navigation
- READ_ONLY: actions across app are disabled (create/update/deactivate + clock-in/out)
- BLOCKED: sidebar shows only Settings + Logout; others redirect to Settings
### Key Rules
- BLOCKED is Settings-only
- Trial banner only if TRIAL

---

## S051/settings/A/EAO/F,RO,B
### Purpose
Profile + Subscription entry (и единственная точка в BLOCKED режиме).
### Entities touched
User (update profile), Company (subscription read), PromoCode (apply - Owner)
### UI Components
Profile section:
- first_name, last_name
- email (if exists)
- work_mode read-only for Employee (“Assigned by Admin”)
Subscription section:
- Owner: always visible
- Admin/Employee: visible only in BLOCKED (read-only)
Fields:
- subscription_status, access_status, trial_ends_at, subscription_name
### Actions
- save_profile
- apply_promo_code (Owner)
- manage_subscription → S003/pricing (Public)
- contact_support
### States
- READ_ONLY: save/apply promo disabled (или только promo disabled — выбрать и зафиксировать в реализации)
- BLOCKED: Subscription section prominent; other navigation disabled
### Key Rules
- Employee cannot change work_mode
- Promo apply only for Owner
- In BLOCKED mode this screen is always accessible

---

# Employee

## S100/employee_dashboard/A/E/F,RO
### Purpose
Статус, быстрые действия, сводка.
### Entities touched
WorkSession (read own active/latest), TeamMember (read), Project (read accessible)
### UI Components
- Status: clocked-in/clocked-out
- Active session: project + start_time (if active)
- Quick actions: clock_in / clock_out
- My Teams (active memberships)
- Projects:
  - active list (доступные)
  - inactive list (informational, optional)
- Weekly summary
- Link: Time History
### Actions
clock_in → S101, clock_out (confirm), open_time_history → S102
### States
- no teams (empty)
- no accessible projects (empty)
- READ_ONLY: clock buttons disabled + reason
- work_mode status:
  - ONSITE: wifi verified/not verified + CTA try_again
  - REMOTE: “Remote position”
### Key Rules
- Employee sees projects only via TeamMember(ACTIVE) + ProjectTeamAssignment(ACTIVE) + Project(ACTIVE)
- Only 1 active WorkSession allowed
- Clock-in requires verified Wi-Fi if onsite + WIFI_REQUIRED

---

## S101/clock_in/A/E/F,RO
### Purpose
Выбор проекта и старт WorkSession.
### Entities touched
Project (read accessible), ProjectTeamAssignment (read), WorkSession (create)
### UI Components
Top block:
- work_mode (read-only)
- if ONSITE:
  - verification status + CTA try_again
  - if not verified → disable clock_in
Main:
- list of available ACTIVE projects
- show assigned team for each project
- CTA clock_in
### Actions
try_again_verify, clock_in
### States
- no projects (empty)
- READ_ONLY: clock_in disabled + reason
### Key Rules
- Clock-in forbidden if already has active WorkSession
- Project must be ACTIVE and reachable by membership chain
- team_id on WorkSession фиксируется как “assigned team of selected project”

---

## S102/time_history/A/E/F,RO
### Purpose
История смен сотрудника.
### Entities touched
WorkSession (read own)
### UI Components
Table/list:
- date, project, team, start_time, end_time, duration, close_reason
Filters:
- period (week/month/custom)
- project (optional)
### Actions
apply_filter
### States
empty history
### Key Rules
Employee sees only own sessions.

---

# Admin/Owner shared (AO)

## S150/admin_dashboard/A/AO/F,RO
### Purpose
Обзор компании + shortcuts.
### Entities touched
User, Team, Project, WorkSession (read company)
### UI Components
Widgets:
- total employees (active/inactive)
- total teams
- active/inactive projects
- active sessions (who + project + start_time)
Shortcuts:
- create_employee / create_team / create_project
### Actions
go_to_employees, go_to_teams, go_to_projects, go_to_time_overview
### States
READ_ONLY: shortcuts disabled
### Key Rules
RBAC: Admin/Owner only.

---

## S151/employees_list/A/AO/F,RO
### Purpose
Список сотрудников + entry point для CRUD.
### Entities touched
User (list), TeamMember (list)
### UI Components
Table columns:
- name, role, status, teams, work_mode, projects
Buttons:
- create_employee
Row actions:
- open_details, edit, deactivate
### Actions
create_employee → S152, open_details → S153
### States
empty list, READ_ONLY disables create/edit/deactivate
### Key Rules
- login unique per company
- deactivated users cannot login

---

## S152/employee_form/A/AO/F,RO
### Purpose
Создать/редактировать сотрудника.
### Entities touched
User (create/update/deactivate)
### UI Components
Create:
- first_name, last_name
- login (req)
- email (opt)
- password (set/generate)
- role (default employee)
- work_mode (ONSITE/REMOTE)
Edit:
- same + status change + deactivate
### Actions
save, cancel, deactivate
### States
validation errors, READ_ONLY disables actions
### Key Rules
- work_mode assigned by Admin/Owner
- user belongs to exactly one company

---

## S153/employee_details/A/AO/F,RO
### Purpose
Membership management + sessions preview.
### Entities touched
User (read), TeamMember (create/deactivate), WorkSession (read)
### UI Components
- Profile summary
- Team memberships list (active/inactive)
- Add to team (Create TeamMember)
- Remove from team (Deactivate TeamMember)
- Recent sessions preview + link to Time Overview filtered
### Actions
add_to_team, remove_from_team, open_time_overview_filtered
### States
READ_ONLY disables membership actions
### Key Rules
- max active teams per employee ограничен Company.max_active_teams_per_employee
- Employee may be in multiple teams (до лимита)

---

## S160/teams_list/A/AO/F,RO
### Purpose
Список команд + CRUD entry.
### Entities touched
Team (list), TeamMember (list)
### UI Components
Table: name, status, members, projects
Button: create_team
Row actions: open, edit, deactivate
### Actions
create_team → S161, open → S162
### States
empty list, READ_ONLY disables actions

---

## S161/team_form/A/AO/F,RO
### Purpose
Создать/редактировать команду (+ опционально добавить участников).
### Entities touched
Team (create/update), TeamMember (create)
### UI Components
- name (req)
- description (opt)
- add_members (optional; pick from employees)
### Actions
save, cancel
### States
validation errors, READ_ONLY disables save
### Key Rules
- Team belongs to company
- Membership stored via TeamMember (status active/inactive)

---

## S162/team_details/A/AO/F,RO
### Purpose
Состав команды + список проектов, где она назначена.
### Entities touched
Team, TeamMember, ProjectTeamAssignment, Project
### UI Components
- team info
- members list + add/remove
- projects list (assigned)
- empty: “Not assigned projects yet”
### Actions
add_member, remove_member
### States
READ_ONLY disables membership actions

---

## S170/projects_list/A/AO/F,RO
### Purpose
Список проектов + CRUD entry.
### Entities touched
Project (list), ProjectTeamAssignment (read)
### UI Components
Table: name, status, assigned_team
Button: create_project
Row actions: open, edit, deactivate
### Actions
create_project → S171, open → S172
### States
empty list, READ_ONLY disables actions

---

## S171/project_form/A/AO/F,RO
### Purpose
Создать/редактировать проект (+ опционально назначить команду).
### Entities touched
Project (create/update), ProjectTeamAssignment (optional create)
### UI Components
- name (req)
- team (optional picker)
- description (opt)
- location (opt)
### Actions
save, cancel
### States
validation errors, READ_ONLY disables save
### Key Rules
- Project belongs to company
- Project can have only one ACTIVE assigned team at a time

---

## S172/project_details/A/AO/F,RO
### Purpose
Назначение команды + summary по времени.
### Entities touched
Project, ProjectTeamAssignment, WorkSession
### UI Components
- project info
- assigned team block:
  - none → assign_team
  - exists → show team + change_team
- sessions summary:
  - total hours (period)
  - active sessions list
### Actions
assign_team, change_team, apply_period_filter
### States
READ_ONLY disables assign/change
### Key Rules
- Only one ACTIVE ProjectTeamAssignment per project
- If project becomes INACTIVE → new clock-in forbidden

---

## S180/time_overview/A/AO/F,RO
### Purpose
Отчёты по времени.
### Entities touched
WorkSession (read company), User, Project
### UI Components
Tabs:
- by_employees: employee → total hours
- by_projects: project → total hours
- active_sessions: who + project + start_time
Filters: period
### Actions
apply_filter, open_work_session_detail → S181
### States
empty states per tab
### Key Rules
- Admin/Owner can read company sessions
- In READ_ONLY: viewing ok, editing not

---

## S181/work_session_detail/A/AO/F,RO
### Purpose
Просмотр и (если включено) корректировка WorkSession.
### Entities touched
WorkSession (read/update), User (read)
### UI Components
- session info: employee, project, team, start/end/duration, close_reason
- edit section (if enabled):
  - search employee
  - select session
  - edit start_time/end_time
  - save (records updated_by_user_id)
### Actions
save_changes
### States
validation errors, READ_ONLY disables edit
### Key Rules
- duration рассчитывается автоматически (не редактируется вручную)
- update фиксирует updated_by_user_id
- корректировать лучше только закрытые (INACTIVE) сессии

---

# Owner-only

## S200/admins_management/A/O/F,RO
### Purpose
Создать/редактировать/деактивировать Admin пользователей.
### Entities touched
User (role=ADMIN create/update/deactivate)
### UI Components
- admins list
- create/edit/deactivate actions
### Actions
create_admin, edit_admin, deactivate_admin
### States
READ_ONLY disables actions
### Key Rules
- Owner-only
- нельзя деактивировать последнего Owner (инвариант на уровне правил)

---

## S210/company_settings/A/O/F,RO
### Purpose
Настройки компании и политики.
### Entities touched
Company (update)
### UI Components
- company_name, industry, timezone
- max_active_teams_per_employee
- work_location_policy (WIFI_REQUIRED)
### Actions
update
### States
READ_ONLY disables update

---

## S220/allowed_networks/A/O/F,RO
### Purpose
Управление списком разрешённых Wi-Fi сетей.
### Entities touched
AllowedCompanyNetwork (create/update/deactivate), Company (policy read)
### UI Components
- networks list: label, network_identifier, status
- actions: add/edit/deactivate
- warning if WIFI_REQUIRED and list empty
### Actions
add_network, edit_network, deactivate_network
### States
empty list warning, READ_ONLY disables actions
### Key Rules
- If WIFI_REQUIRED and allowlist empty → onsite employees cannot clock-in

---

## S230/billing_access/A/O/F,RO,B
### Purpose
Подписка/доступ/промокод (включая BLOCKED режим).
### Entities touched
Company (subscription/access), PromoCode (apply), PromoCodeRedemption (create)
### UI Components
- subscription_status, access_status
- trial_ends_at + days_left
- subscription_name
- promo code input
- manage subscription → S003/pricing
### Actions
apply_promo, manage_subscription, contact_support
### States
invalid promo, expired/inactive promo, max_redemptions reached
BLOCKED: this screen accessible only via Settings (Subscription section)
### Key Rules
- Promo apply Owner-only
- EXPIRED ⇒ BLOCKED behavior (Settings-only)
- Promo redemption stored for audit/history
