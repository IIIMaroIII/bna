# Functional Requirements (FR) для MVP v1

Ниже — **формальные функциональные требования** MVP v1 (без реализации, технологий и архитектуры).  
Это документ, по которому можно **разрабатывать, проектировать UI и писать тесты**.

---

## 0) Термины (минимум)

- **Public Area** — часть без логина (Home/Features/Pricing/Contacts/Feedback + Sign In/Sign Up).
- **App Area** — часть после логина.
- **Company** — tenant boundary (изоляция данных).
- **RBAC** — роли: Owner / Admin / Employee.
- **Access Status** — режим компании: FULL / READ_ONLY / BLOCKED.
- **TeamMember** — связь User↔Team.
- **ProjectTeamAssignment** — связь Team↔Project.
- **WorkSession** — рабочая смена (clock-in/clock-out), не login-сессия.

---

# Скорректированные Functional Requirements (FR) для MVP v1

## 1. Public Presentation Layer (Public Area)

- FR-1: Система должна предоставлять публичную Home Page с описанием продукта и CTA на регистрацию.
- FR-2: Система должна предоставлять страницу Features.
- FR-3: Система должна предоставлять страницу Pricing.
- FR-4: Система должна предоставлять страницу Contacts.
- FR-5: Система должна предоставлять страницу Feedback с формой обратной связи.
- FR-6: Public pages должны быть доступны без авторизации.

---

## 2. Общие требования системы (App Area + Multi-tenant + RBAC)

- FR-7: Система должна поддерживать работу нескольких компаний с полной изоляцией данных.
- FR-8: Все пользователи системы должны принадлежать ровно одной Company.
- FR-9: Пользователь должен иметь доступ только к данным своей Company.
- FR-10: Система должна поддерживать роли пользователей: Owner, Admin, Employee.

---

## 3. Регистрация компании и Owner (Sign Up)

- FR-11: Система должна позволять зарегистрировать новую Company.
- FR-12: При регистрации Company система должна автоматически создавать пользователя с ролью Owner.
- FR-13: Регистрация должна собирать минимум: company_name, industry, owner_email, owner_password.
- FR-14: После успешной регистрации система должна перенаправлять Owner в App Area (Owner Dashboard).

---

## 4. Аутентификация и авторизация

- FR-15: Система должна позволять пользователю входить в систему по логину/email и паролю.
- FR-16: Система должна определять роль пользователя после авторизации.
- FR-17: Система должна ограничивать доступ к функциям в зависимости от роли пользователя (RBAC).
- FR-18: Система должна предоставлять пользователю возможность Logout.

---

## 5. Access Status компании (FULL / READ_ONLY / BLOCKED)

- FR-19: Система должна поддерживать для Company режим доступа: FULL, READ_ONLY, BLOCKED.
- FR-20: В режиме READ_ONLY система должна разрешать вход и просмотр данных, но запрещать любые Create/Update/Deactivate операции и запрещать clock-in/clock-out.
- FR-21: В режиме BLOCKED система должна запрещать доступ в App Area и показывать экран “Access blocked” с возможностью Logout.

---

## 6. Trial (минимально, без реального биллинга)

- FR-22: При регистрации Company система должна устанавливать trial_ends_at и subscription_status=TRIAL.
- FR-23: Пока now < trial_ends_at, Company.access_status должен быть FULL.
- FR-24: Когда now >= trial_ends_at, система должна переводить Company в subscription_status=EXPIRED и access_status согласно политике MVP (READ_ONLY или BLOCKED).

Примечание: платежи/подписки/инвойсы в MVP не реализуются.

---

## 7. Управление пользователями (Employees) — Admin / Owner

- FR-25: Admin и Owner должны иметь возможность создавать пользователей с ролью Employee.
- FR-26: При создании Employee система должна назначать ему credentials (логин/пароль или иной идентификатор).
- FR-27: Admin и Owner должны иметь возможность деактивировать Employee (status=INACTIVE).
- FR-28: Деактивированный пользователь не должен иметь доступ к App Area.

---

## 8. Управление администраторами — Owner only

- FR-29: Owner должен иметь возможность создавать пользователей с ролью Admin внутри своей Company.
- FR-30: Owner должен иметь возможность деактивировать Admin внутри своей Company.
- FR-31: Система должна запрещать деактивацию последнего активного Owner компании.

---

## 9. Управление Teams — Admin / Owner

- FR-32: Admin и Owner должны иметь возможность создавать Team.
- FR-33: Admin и Owner должны иметь возможность деактивировать Team.
- FR-34: Admin и Owner должны иметь возможность добавлять пользователя в Team (создавать TeamMember).
- FR-35: Admin и Owner должны иметь возможность убирать пользователя из Team (переводить TeamMember в INACTIVE).
- FR-36: Система должна ограничивать максимум активных команд для одного Employee согласно Company.max_active_teams_per_employee.

---

## 10. Управление Projects — Admin / Owner

- FR-37: Admin и Owner должны иметь возможность создавать Project.
- FR-38: Project должен содержать как минимум name и status (ACTIVE/INACTIVE).
- FR-39: Admin и Owner должны иметь возможность деактивировать Project.

---

## 11. Назначение Team на Project (Project access model) — Admin / Owner

- FR-40: Admin и Owner должны иметь возможность назначать Team на Project (создавать ProjectTeamAssignment).
- FR-41: Система должна запрещать назначение Team на Project, если Team.status=INACTIVE или Project.status=INACTIVE.
- FR-42: В MVP v1 система должна поддерживать ограничение: у одного Project может быть только одна активная (ACTIVE) связь ProjectTeamAssignment.
- FR-43: Employee должен видеть только те Projects, к которым есть доступ через:
  - TeamMember.status=ACTIVE (employee состоит в Team)
  - ProjectTeamAssignment.status=ACTIVE (эта Team назначена на Project)
  - Project.status=ACTIVE

---

## 12. Work location policy (Wi-Fi) + Allowed networks — Owner + Employee

- FR-44: Система должна поддерживать Company.work_location_policy = WIFI_REQUIRED.
- FR-45: Owner должен иметь возможность управлять списком разрешённых сетей компании (AllowedCompanyNetwork): добавлять и деактивировать.
- FR-46: Система должна поддерживать режим работы пользователя work_mode: ONSITE или REMOTE.
- FR-47: При clock-in система должна применять правила:
  - если work_mode=ONSITE и policy=WIFI_REQUIRED → clock-in разрешён только при подключении к разрешённой сети;
  - если work_mode=REMOTE → проверка Wi-Fi не требуется.
- FR-48: Если policy=WIFI_REQUIRED и список разрешённых сетей пуст, система должна запрещать clock-in onsite-сотрудникам с понятной причиной.

---

## 13. Учёт рабочего времени (WorkSession) — Employee

### Clock-in
- FR-49: Employee должен иметь возможность выполнить clock-in.
- FR-50: При clock-in Employee должен выбрать Project из списка доступных ему Projects.
- FR-51: Система должна запрещать clock-in при наличии активной WorkSession у Employee.
- FR-52: Система должна сохранять start_time и создавать WorkSession со статусом ACTIVE.

### Clock-out
- FR-53: Employee должен иметь возможность выполнить clock-out.
- FR-54: Система должна запрещать clock-out при отсутствии активной WorkSession у Employee.
- FR-55: При clock-out система должна сохранять end_time, переводить WorkSession в INACTIVE и вычислять duration автоматически.

---

## 14. Просмотр рабочего времени

### Employee
- FR-56: Employee должен иметь доступ к списку своих WorkSession.
- FR-57: Employee должен видеть часы по датам и проектам.

### Admin / Owner
- FR-58: Admin и Owner должны видеть рабочее время всех сотрудников своей Company.
- FR-59: Admin и Owner должны видеть рабочее время по проектам (агрегации).
- FR-60: Система должна отображать активные WorkSession (кто сейчас clocked-in).

---

## 15. Корректировка времени (опционально для MVP, но если включено — правила обязательны)

- FR-61: Если корректировка включена в MVP, Admin и Owner должны иметь возможность корректировать только завершённые (INACTIVE) WorkSession.
- FR-62: Любая корректировка должна фиксировать updated_at и updated_by_user_id.

---

## 16. Бизнес-правила (инварианты)

- FR-63: У Employee может быть только одна активная WorkSession одновременно.
- FR-64: WorkSession должна быть связана с Company, User, Team и Project.
- FR-65: Все WorkSession должны сохраняться для просмотра и аудита (не удаляются).

---

## 17. Edge cases: авто-закрытие активной WorkSession

- FR-66: Если условия, необходимые для активной WorkSession, становятся недействительными (например: деактивация User/Team/Project или снятие назначения Team↔Project), система должна автоматически закрывать активную WorkSession (auto clock-out) с фиксацией причины.

---

## Следующий шаг (логичный)

1) Разложить FR по экранам (на базе UI Map v1).
2) Превратить FR в User Stories / Tasks.
3) Зафиксировать решения для MVP:
   - политика EXPIRED: READ_ONLY или BLOCKED
   - включаем ли корректировку времени в MVP или переносим в v2
