# Laboratory work II

### 1. Настройка переменных окружения

```bash
$ export GITHUB_USERNAME=<имя_пользователя>
$ export GITHUB_EMAIL=<адрес_почтового_ящика>
$ export GITHUB_TOKEN=<сгенирированный_токен>
$ alias edit=<nano|vi|vim|subl>
```

**Что делают эти команды:**
- `export` - устанавливает переменные окружения для текущей сессии терминала
- `GITHUB_USERNAME` - хранит имя пользователя GitHub
- `GITHUB_EMAIL` - хранит email, связанный с аккаунтом GitHub
- `GITHUB_TOKEN` - хранит токен доступа к GitHub API
- `alias edit=nano` - создает псевдоним `edit` для текстового редактора nano

---

### 2. Переход в рабочую директорию

```bash
cd ${GITHUB_USERNAME}/workspace
source scripts/activate
```

**Что делают эти команды:**
- `cd` - переходит в директорию workspace
- `source scripts/activate` - активирует окружение (выполняет скрипт настройки)

---

### 3. Настройка конфигурации для hub

```bash
mkdir ~/.config
cat > ~/.config/hub <<EOF
github.com:
- user: ${GITHUB_USERNAME}
  oauth_token: ${GITHUB_TOKEN}
  protocol: https
EOF
git config --global hub.protocol https
```

**Что делают эти команды:**
- `mkdir ~/.config` - создает директорию для конфигурационных файлов (если не существует)
- `cat > ~/.config/hub <<EOF` - создает файл с конфигурацией hub, подставляя значения переменных
- `github.com: - user:` - указывает username для GitHub
- `oauth_token:` - сохраняет токен для аутентификации
- `protocol: https` - устанавливает протокол HTTPS
- `git config --global hub.protocol https` - добавляет настройку протокола в глобальный конфиг Git

---

### 4. Создание и настройка локального репозитория

```bash
mkdir projects/lab02 && cd projects/lab02
git init
git config --global user.name ${GITHUB_USERNAME}
git config --global user.email ${GITHUB_EMAIL}
git config -e --global
```

**Что делают эти команды:**
- `mkdir projects/lab02` - создает директорию для проекта
- `cd projects/lab02` - переходит в созданную директорию
- `git init` - инициализирует пустой Git репозиторий (создает папку .git)
- `git config --global user.name` - устанавливает имя пользователя для всех коммитов
- `git config --global user.email` - устанавливает email для всех коммитов
- `git config -e --global` - открывает глобальный конфиг для проверки

---

### 5. Подключение к удаленному репозиторию

```bash
git remote add origin https://github.com/${GITHUB_USERNAME}/lab02.git
git pull origin main
```

**Что делают эти команды:**
- `git remote add origin` - связывает локальный репозиторий с удаленным на GitHub
- `origin` - традиционное имя для основного удаленного репозитория
- `git pull origin main` - скачивает изменения с GitHub (включая .gitignore)

**Результат выполнения:**
```
remote: Enumerating objects: 7, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 7 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (7/7), 2.47 KiB | 844.00 KiB/s, done.
From https://github.com/ferdosiakrymskaa-svg/lab02
 * branch            main       -> FETCH_HEAD
 * [new branch]      main       -> origin/main
```

---

### 6. Работа с README.md

```bash
git branch -a
touch README.md
git status
git add README.md
git commit -m"added README.md"
git push origin main
```

**Что делают эти команды:**
- `git branch -a` - показывает все ветки (локальные и удаленные)
- `touch README.md` - создает файл README.md
- `git status` - показывает состояние рабочей директории
- `git add README.md` - добавляет файл в staging area
- `git commit -m"added README.md"` - создает коммит с сообщением
- `git push origin main` - отправляет изменения на GitHub



---

### 7. Создание структуры проекта

```bash
mkdir sources include examples
```

**Создание файла sources/print.cpp:**
```bash
cat > sources/print.cpp <<EOF
#include <print.hpp>

void print(const std::string& text, std::ostream& out)
{
  out << text;
}

void print(const std::string& text, std::ofstream& out)
{
  out << text;
}
EOF
```

**Создание файла include/print.hpp:**
```bash
cat > include/print.hpp <<EOF
#include <fstream>
#include <iostream>
#include <string>

void print(const std::string& text, std::ofstream& out);
void print(const std::string& text, std::ostream& out = std::cout);
EOF
```

**Создание файла examples/example1.cpp:**
```bash
cat > examples/example1.cpp <<EOF
#include <print.hpp>

int main(int argc, char** argv)
{
  print("hello");
}
EOF
```

**Создание файла examples/example2.cpp:**
```bash
cat > examples/example2.cpp <<EOF
#include <print.hpp>

#include <fstream>

int main(int argc, char** argv)
{
  std::ofstream file("log.txt");
  print(std::string("hello"), file);
}
EOF
```

**Что делают эти команды:**
- `mkdir` - создает директории для исходного кода, заголовочных файлов и примеров
- `cat > файл <<EOF` - создает файл с многострочным содержимым
- `#include` - подключает заголовочные файлы
- `void print(...)` - объявление и определение функций

---

### 8. Проверка состояния и отправка изменений

```bash
git status
git add .
git commit -m"added sources"
git push origin main
```

**Результат `git status`:**
```
On branch main
Changes not staged for commit:
  modified:   README.md
Untracked files:
  examples/
  include/
  sources/
```

**Результат `git push`:**
```
Enumerating objects: 12, done.
Counting objects: 100% (12/12), done.
Delta compression using up to 3 threads
Compressing objects: 100% (8/8), done.
Writing objects: 100% (10/10), 1.04 KiB | 1.04 MiB/s, done.
Total 10 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/ferdosiakrymskaa-svg/lab02.git
   4a006c5..96893da  main -> main
```

**Что делают эти команды:**
- `git add .` - добавляет все изменения (новые и измененные файлы) в staging area
- `git commit -m"added sources"` - создает коммит с исходным кодом
- `git push` - отправляет все коммиты на GitHub

---

### 9. Создание отчета

```bash
cd ~/workspace/
export LAB_NUMBER=02
git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER}
mkdir reports/lab${LAB_NUMBER}
cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
cd reports/lab${LAB_NUMBER}
edit REPORT.md
```

**Что делают эти команды:**
- `cd ~/workspace/` - переходит в рабочую директорию
- `export LAB_NUMBER=02` - устанавливает номер лабораторной работы
- `git clone` - клонирует шаблон отчета
- `mkdir` - создает директорию для отчета
- `cp` - копирует шаблон в директорию отчета
- `edit REPORT.md` - открывает отчет для редактирования

---

## Выводы

В ходе выполнения лабораторной работы были изучены и отработаны следующие навыки работы с Git:

1. **Инициализация репозитория** (`git init`)
2. **Настройка пользователя** (`git config`)
3. **Работа с удаленными репозиториями** (`git remote add`, `git pull`, `git push`)
4. **Управление ветками** (`git branch`, переименование веток)
5. **Отслеживание изменений** (`git status`)
6. **Добавление файлов** (`git add`)
7. **Создание коммитов** (`git commit`)
8. **Работа с конфигурацией hub** для интеграции с GitHub
