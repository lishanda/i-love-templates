Документация
============

Установка
---------

В wemake-django-template уже встроен и сконфигурирован модуль для генерации 
документации `sphinx`.

Синтаксис
---------

Для написания документации используется синтаксис ReStructuredText.

`Гайд <https://docs22.readthedocs.io/en/latest/rst-markup.html>`_ 

`Официальная документация <https://www.sphinx-doc.org/en/master/usage/restructuredtext/index.html>`_ 

Генерация
---------

Из корневой папки переходим в docs и запускаем утилиту для 
сборки документации.

.. code-block:: bash
    
    cd docs
    make html

Использование
-------------

При генерации Sphinx преобразует все ReStructuredText (.rst)
файлы в html. В папке docs/pages создаём отдельные .rst
на каждое приложение, и затем в docs/index.rst добавляем ссылки на эти
файлы:

.. code-block::

    .. toctree::
       :maxdepth: 2
       :caption: Содержание:

       pages/app1.rst
       pages/app2.rst
       и тд...


Для создания документации Python-кода используется расширение
`autodoc`.
Всё, что остаётся сделать - внутри нового rst файла
указать путь к py-файлам приложения. Sphinx сам сгенерирует
документацию для каждого метода и класса, а также вставит
docstrings из исходного кода.

.. code-block::

    .. automodule:: app1.serializers
        :members:
        :undoc-members:
        :show-inheritance:

    .. automodule:: app1.models
        :members:
        :undoc-members:
        :show-inheritance:

    и тд...

Термины
-------

Для организованной работы с терминами создаём отдельный файл
docs/pages/glossary.rst

Не забываем добавить на него ссылку в docs/index.rst

У него должно быть следующее содержание:

.. code-block::

    ############
    Glossary
    ############


    .. glossary::
        Термин1
            Всё о термине1
        
        Термин2
            Всё о термине2

        и тд...

Теперь при каждом упоминании терминов в rst-документации
и исходном коде, выделяем их специальной конструкцией

.. code-block::
    
    Пример в документации:
    >> some_document.rst

    Я пишу документацию и использую :term:`Термин1`.

    Пример в коде:
    >> model.py

    class MyModel(models.Model):
    """This model represents :term:`Термин2` entity."""

При генерации документации любое упоминание термина создаст
ссылку на глоссарий.