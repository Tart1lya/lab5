# Отчёт по лабораторной работе 5

## Задание 1 - Автоматизация проверки формата файлов при коммите

Предположим, у вас есть задача автоматизировать проверку формата файлов при коммите с использованием Git Hooks.

  Перед каждым коммитом необходимо автоматически проверять, чтобы все .txt файлы в репозитории соответствовали определенному формату.

  Создайте bash-скрипт, который будет выполнять проверку того, что коммитится файл формата .txt и в файле присутствует какой-то текст (например, в конце файла есть подпись автора, неважно как она выглядит, главное чтобы была какая-нибудь проверка содержимого файла). Далее поместите этот скрипт в нужную папку и проверьте, что перед каждым коммитом проходит проверка, например, добавьте вывод о результате проверки в консоль. За этот функционал отвечает Git Hooks, там сказазно как автоматизировать такую проверку.


# Решение

1. Использую PyCharm
2. Создаём main.py
   ```
    import os
    import stat
    
    hook_path = '.git/hooks/pre-commit'
    hook_content = """#!/bin/bash
    
    set -e
    
    REQUIRED_TEXT="Автор:"
    
    FILES=$(git diff --cached --name-only --diff-filter=ACMR | grep -E '\\.txt$')
    
    if [[ -z "$FILES" ]]; then
      exit 0
    fi
    
    for FILE in $FILES; do
      if [[ ! -f "$FILE" ]]; then
        echo "Ошибка: Файл $FILE был удалён. Коммит отменён."
        exit 1
      fi
    
      if ! grep -q "$REQUIRED_TEXT" "$FILE"; then
        echo "Ошибка: Файл $FILE не содержит обязательной строки '$REQUIRED_TEXT'. Коммит отменён."
        exit 1
      fi
    done
    
    echo "Все .txt файлы прошли проверку."
    exit 0
    """
    
    # Создание папки .git/hooks (если её нет)
    os.makedirs(os.path.dirname(hook_path), exist_ok=True)
    
    # Запись хука
    with open(hook_path, 'w') as hook_file:
        hook_file.write(hook_content)
    
    # Установка прав на выполнение
    os.chmod(hook_path, os.stat(hook_path).st_mode | stat.S_IXUSR)
    
    print(f"Git hook создан по пути: {hook_path}")

   ```
  3. Создаём файл no_author.txt
   ```
   Горе от ума
   ```
  4. Запускаем main.py
     <img width="940" alt="image" src="https://github.com/user-attachments/assets/498b4eea-f409-42cb-bb50-34c65b075883" />
  5. Пробуем закоммитить no_author.txt
     ![telegram-cloud-photo-size-2-5404498747926374339-y](https://github.com/user-attachments/assets/527ab9b3-7881-4f70-9cd4-e465fdef18a3)


## Задание 2 - Использование Git Flow в проекте. Решение

1. Убедились, что Git Flow установлен на локальной машине:

```
sudo apt-get install git-flow
```

2. В корне репозитория выполняем инициализацию Git Flow.

```
git flow init
```

3. Создаём ветку для новой функциональности:

```
git flow feature start task-management
```

4. Вносим изменения в код для добавления функционала управления задачами:

```
def create_task(title, description):
    # Логика создания задачи
    print(f"Создана новая задача: {title}")
```

Выполняем коммит изменения по мере разработки:

```
git add task_manager.py
git commit -m "Добавлен функционал управления задачами"


```

5. После завершения разработки функции завершаем фичу и объединияем ее с основной веткой:

```
git flow feature finish task-management

```

Git Flow автоматически переключится на ветку develop и выполнит слияние.

6. Переключаемся на ветку "develop" и начинаем создание релиза:

```
git checkout develop
git flow release start v1.0.0
```

7. Вносим изменения, связанные с релизом:

```
echo "v1.0.0" > version.txt
git add version.txt
git commit -m "Обновлена версия для релиза v1.0.0"

```

8. Завершаем релиз и объединяем его с ветками "develop" и "master":

```
git flow release finish v1.0.0
```

9. Создаём hotfix:

```
git flow hotfix start hotfix-1.0.1
```

10. Вносим изменения для исправления ошибки и коммитим:

```
git add file_with_error.py
git commit -m "Исправлена критическая ошибка"
```

11. Завершаем hotfix и объединияем его с ветками "develop" и "master":

```
git flow hotfix finish hotfix-1.0.1
```

12. Отправляем изменения на удаленный репозиторий:

```
git push origin develop
git push origin master

```
![telegram-cloud-photo-size-2-5404498747926374386-y](https://github.com/user-attachments/assets/27e4a5d1-9e5e-419a-a272-06fb0b1c0d56)
![telegram-cloud-photo-size-2-5404498747926374387-y](https://github.com/user-attachments/assets/e0b3822c-36a8-42fe-a8a1-439978dc960b)

