.. i-love-templates documentation master file, created by
   sphinx-quickstart on Wed Jan 22 03:37:39 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Качество веб-приложений
=======================

.. toctree::
   :maxdepth: 2
   :caption: Содержание:

   pages/preparations.rst
   pages/linters.rst
   pages/backend.rst
   pages/contract.rst
   pages/frontend.rst
   pages/tests.rst
   pages/documentation.rst


Критерии оценивания
===================

1. Линтер бекенда: проходит = 1, нет = 0
2. Линтер фронтенда: проходит = 1, нет = 0
3. Документация и термины (вашего проекта): есть = 1, нет = 0
4. Прохождение unit-test'ов бекенда: 1, если нет = 0
5. Прохождение unit-test'ов фронтенда: 1, если нет = 0
6. Прохождение E2E тестов: 1, если нет = 0
7. Наличие и валидность контракта между фронтендом и бекендом = 1, если нет = 0
8. Типизация бекенда: полная и корректная = 1, частичная = 0.5, нет = 0
9. Типизация фронтенда (кроме тестов): полная и корректная = 1, частичная = 0.5, нет = 0
10. Корретность работы с бизнес логикой (ручная проверка) = 1, если коряво = 0