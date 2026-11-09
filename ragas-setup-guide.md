# Запуск оценки RAGAS

Основное окружение проекта: Python 3.11.9, `venv_py311` в корне `5-8`. Окружения `venv` и `venv_ragas` сохранены. Дубликат `assistant_api/venv_py311` удалён.

## Запуск из папки assistant_api

Если терминал уже открыт в `assistant_api`, запускайте через Python нового окружения:

```powershell
..\venv_py311\Scripts\python.exe .\evaluate_ragas.py
```

## Запуск из корня проекта

```powershell
.\venv_py311\Scripts\Activate.ps1
cd .\assistant_api
python .\evaluate_ragas.py
```

Рабочая папка при запуске должна быть `assistant_api`, поскольку скрипт читает `data/docs.txt` по относительному пути.

Оценка обращается к OpenAI API. Для неё нужен `OPENAI_API_KEY` в корневом `.env`; запросы расходуют баланс API. Значение ключа не нужно выводить в терминал или отправлять в чат.

## Проверка зависимостей без запросов к OpenAI

Выполнить из корня проекта:

```powershell
.\venv_py311\Scripts\python.exe -m pip check
.\venv_py311\Scripts\python.exe -c "from ragas import evaluate; from ragas.metrics import Faithfulness, ContextPrecision; print('RAGAS import OK')"
```

## Повторная установка

Команды выполняются из корня проекта `E:\ИИ\!Промпт-инженер\PEr08\5-8`. Создание окружения нужно только при его отсутствии:

```powershell
py -3.11 -m venv venv_py311
.\venv_py311\Scripts\python.exe -m pip install --upgrade pip
.\venv_py311\Scripts\python.exe -m pip install -r requirements.txt
```

В `requirements.txt` закреплены версии прямых зависимостей и связанных пакетов LangChain. Полный список зависимостей Python 3.11 сохраняется в `requirements-py311-lock.txt`. Прежний `requirements-ragas-lock.txt` относится к `venv_ragas` с Python 3.10.

Для повторения всех установленных версий вместо последней команды используйте:

```powershell
.\venv_py311\Scripts\python.exe -m pip install -r requirements-py311-lock.txt
```

Pillow указан явно: RAGAS использует его при импорте модуля мультимодальных промптов, но не устанавливает автоматически.

Для совместимости с NumPy 1.26 в Python 3.11 закреплены `datasets==3.6.0` и `pyarrow==20.0.0`. Более новая автоматически выбранная сборка PyArrow требовала NumPy 2 и мешала импорту скрипта, хотя `pip check` не обнаруживал конфликта.

Причина исходной ошибки: RAGAS 0.3.1 импортирует `ChatVertexAI` из `langchain_community.chat_models.vertexai`. В `langchain-community 0.4.2` этого модуля нет. Для этого проекта выбран LangChain 0.3.14 с `langchain-community 0.3.14`, где модуль присутствует, и NumPy 1.26.4, соответствующий ограничениям этой ветки для Python 3.10.

Это совместимый набор для существующего учебного кода. Обновление на современные основные версии LangChain требует отдельной проверки кода.
