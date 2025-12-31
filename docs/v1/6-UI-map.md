## Что такое UI Map
  **UI Map** — это:

    - карта экранов приложения
    - список страниц
    - описание, **что есть на каждой странице**
    - и как пользователь между ними переходит


## Проще одной фразой
  UI Map — это описание того, какие страницы есть в приложении и зачем каждая из них нужна.**

  ## Что именно входит в UI Map
    - названия экранов (страниц)
    - роль пользователя, которая их видит
    - основное назначение экрана
    - ключевые действия на экране

  ## Что UI Map НЕ является

    - не архитектура
    - не схема сервисов
    - не API
    - не дизайн
    - не реализация


### ================================= UI MAP =================================
  - # Home page
      - Navigation
      - Sign In Page
      - Sign Up page
      - Features
      - Pricing
      - Contacts
      - Feedback
  - # App Area (после логина)
    - ## Employee
      - Employee Dashboard
        - Статус: clocked-in / clocked-out
        - Team команда сотрудника 
        - Быстрые действия:
          - Clock-in
          - Clock-out
        - Time history:
          - Кратко за текущую неделю
      - Clock-in
        - Список проектов доступных сотруднику через его Team
        - Время менять не может (текущее по default)
      - Time history:
          - История часов по датам

    - ## Admin/Owner
        - Admin/Owner Dashboard
          - total employees
          - archived projects
          - active projects
          - active sessions (те кто в данный момент clocked-in)
        - Employees
          - список сотрудников
          - create employee
          - deactivate employee
          - назначить сотрудника в Team
        - Teams
          - список команд
          - create team
          - add member
          - remove member
        - Projects
          - список проектов 
          - create project
          - activate project
          - deactivate project
          - назначить Team на Project
        - Time Overview
          - часы по сотрудникам
          - часы по проектам
          - active sessions
        - Setings
          - Базовые настройки
          - Logout
    - ## Owner-only (добавление к Админу)
        - Admins Management
          - create admin
          - remove admin
        - Company Settings
          - ** расширенные настройки компании


