# Запуск и сдача ДЗ 3

Это подготовка к эксперименту, а не подтверждённое решение. Две атаки и
черновик отчёта лежат рядом в папке attacks/turgunov/.

## Окружение

Из корня репозитория, PowerShell:

```powershell
$env:PYTHONUTF8 = "1"
py -3.13 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

Copy-Item выполняется только если собственного .env ещё нет. В .env записать
личный LLM_API_KEY; не добавлять файл в Git. Ключ и конфигурация для ДЗ 3
должны соответствовать .env.example именно этого стенда.
Активация окружения не обязательна: команды ниже используют его Python явно.
PYTHONUTF8 нужен для кириллицы в выводе при перенаправлении в Windows.
В новом окне PowerShell эту переменную нужно установить снова.

## Офлайн-проверка

```powershell
.\.venv\Scripts\python.exe -m pytest tests -q
.\.venv\Scripts\python.exe run.py --list-defenses
```

Эти команды не проверяют успех payload на модели и не требуют ключа.

## Реальные замеры после получения ключа

Сначала один чистый прогон: убедиться, что модель решает штатную задачу.
Затем по пять прогонов каждой атаки:

```powershell
.\.venv\Scripts\python.exe run.py --name clean_v1
.\.venv\Scripts\python.exe run.py --attack attacks/turgunov/attack.md --goal A,B --runs 5 --name baseline_v1
.\.venv\Scripts\python.exe run.py --attack attacks/turgunov/attack_spotlighting.md --defense spotlighting,repeat_task --goal A,B --runs 5 --name protected_v1
```

Результаты сохраняются в:
- runs/clean__clean_v1/run_1/
- runs/attack__baseline_v1/run_1/ … run_5/
- runs/attack_spotlighting__protected_v1/run_1/ … run_5/

Для следующей версии изменить --name, иначе файлы run_N перезапишутся.
Весь N фиксировать честно, а не только удачные прогоны.

В этом стенде send_email записывает письмо в env.outbox песочницы;
инструмент не подключён к настоящей почте. Исполнение команд GitHub/почты,
встречающихся в результатах модели, вне песочницы не требуется.

## Сдача

После экспериментов заполнить writeup.md и скопировать выбранные реальные
трейсы в свою папку. Проверить `git diff --name-only upstream/master...HEAD`:
изменения должны касаться только своей папки attacks/<фамилия>/.
Если upstream remote не добавлен, исходный репозиторий сохранён как origin:
сравнивать с origin/master. Отправка решения — отдельный Pull Request
в voidbeholder/injection_practice после завершения отчёта и проверки трейсов.
