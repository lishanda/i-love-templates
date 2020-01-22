Подготовка проекта
==================

В качестве основы для написания веб-приложения используются шаблоны от
Wemake. Каждый из шаблонов содержит подробную документацию и инструкцию по
установке. В данном разделе рассмотрим быструю установку шаблонов. Все команды 
приведены для bashonwindows, команды на вашей ОС могут отличаться.

Также не забываем запускать все команды в режиме администратора/суперпользователя!

Установка Backend
-----------------
Ссылка на шаблон: `wemake-django-template <https://github.com/wemake-services/wemake-django-template>`_
    *  `Документация <https://wemake-django-template.readthedocs.io/en/latest/?badge=latest>`_ 

Вам понадобятся:
    * Python 3.7.6
    * PostgreSQL 9.6.9

Поверх чистого Python ставятся зависимости для установки шаблона и менеджер зависимостей
`Poetry <https://python-poetry.org/docs/#installation>`_ :

.. code-block::

    pip install cookiecutter jinja2-git
    curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python

Ставим шаблон:

.. code-block::

    cookiecutter gh:wemake-services/wemake-django-template

Настраиваем Poetry:

.. code-block::

    poetry config virtualenvs.in-project true
    poetry config --list

Убеждаемся, что напротив virtualenvs.in-project стоит true

Заходим в папку проекта, делаем установку через Poetry. Этот модуль сам создаёт
виртуальное окружение, самостоятельно ничего создавать не нужно!

.. code-block::

    poetry install

Заходим в консоль PostgreSQL:

.. code-block::

    psql -U postgres

Создаём суперпользователя и таблицу, имена должны совпадать с названием проекта,
указанным при использовании cookiecutter

.. code-block:: sql

    CREATE USER имя_проекта SUPERUSER;
    CREATE DATABASE имя_проекта OWNER имя_проекта ENCODING 'utf-8';

Выходим из консоли PostgreSQL и пытаемся запустить миграции

.. code-block::

    python manage.py migrate

Запускаем сервер
~~~~~~~~~~~~~~~~

.. code-block::

    python manage.py runserver

Если возникают ошибки при запуске сервера по типу "module named django not found",
то, возможно, необходимо сначала активировать виртуальное окружение Poetry:

.. code-block::

    poetry shell




Установка Frontend
------------------
Ссылка на шаблон: `wemake-vue-template <https://github.com/wemake-services/wemake-vue-template/>`_ 
    * `Документация <https://wemake-services.gitbook.io/wemake-vue-template/>`_ 


Вам понадобятся:
    * `Node.js v12.14 <https://github.com/wemake-services/wemake-vue-template/blob/master/template/.nvmrc>`_ 
    * Время для установки сотен зависимостей

Клонируем шаблон в любое удобное место

.. code-block::

    git clone https://github.com/wemake-services/wemake-vue-template.git

Устанавливаем шаблон в папку путь_к_бекенду/frontend

.. code-block::

    npx vue-cli init ./wemake-vue-template путь_к_бекенду/frontend

После установки шаблона он должен располагаться в корневой
папке проекта, рядом с папками docs и server.

Заходим в папку frontend и запускаем установку

.. code-block::

    npm install

Запускаем фронтенд
~~~~~~~~~~~~~~~~~~

.. code-block::

    npm run dev

Заключение
==========

Если всё успешно запустилось, поздравляю!

Если нет, вот список частых проблем:
    * Неподходящие версии Python/Node.js
    * Отсутствие указателей на утилиты и библиотеки в системных путях (PATH для Windows)
    * Отсутствие прав на чтение/запись
