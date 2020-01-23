Работа с Backend
====================

Конфигурация
------------

Устанавливаваем Django REST framework.

Для избежания проблем в дальнейшей работе с фронтендом
ставим библиотеку django-cors-headers и убираем модуль для
Content Security Policy.

.. code-block::

    poetry add djangorestframework
    poetry add django-cors-headers

Изменяем настройки по пути server/settings/components/common.py

.. code-block:: python

    INSTALLED_APPS: Tuple[str, ...] = (
        ...
        # Комментируем CSPMiddleware
        # Content Security Policy:
        # 'csp.middleware.CSPMiddleware',
        ...
        # В конец добавляем два модуля
        'corsheaders',
        'rest_framework',
    )

Далее пути будут указываться относительно папки server.

Модели
------

В apps/main/models.py создаём модель.

Пример модели:

.. code-block:: python

    @final
    class Collection(models.Model):
        """
        Represents :term:`Collection`
        """

        title = models.CharField(max_length=_COLLECTION_MAX_LENGTH)
        created_at = models.DateTimeField(auto_now_add=True)

        def __str__(self):
            """String representation of Collection."""
            return '{0}. {1}'.format(self.id, self.title)

После создания модели не забываем сделать миграции

.. code-block::

    python manage.py makemigrations
    python manage.py migrate

Serializer
----------

В apps/main/ создаём файл serializer.py и создаём сериализатор
для модели.

Пример:

.. code-block:: python

    # Imports
    from rest_framework import serializers
    from server.apps.main.models import Collection

    # Serializer
    class CollectionSerializer(serializers.ModelSerializer):
        """Serializer for Collection class."""
        class Meta:
            model = Collection
            fields = ('id', 'title', 'created_at')

Views
-----

В apps/main/views.py добавляем ViewSet для нашей модели.

Пример:

.. code-block:: python

    # Imports
    from server.apps.main.models import Collection
    from server.apps.main.serializers import CollectionSerializer

    # View
    class CollectionViewSet(viewsets.ModelViewSet):
        """
        REST API Viewset for Collection class.
        """
        queryset = Collection.objects.all()
        serializer_class = CollectionSerializer


Urls
----

В apps/main/urls.py создаём роутер для автоматической 
маршрутизации ViewSet. 

.. code-block:: python

    # Imports
    from rest_framework import routers
    from server.apps.main.views import CollectionViewSet

    # Router
    router = routers.DefaultRouter()
    router.register(r'collections', CollectionViewSet)

    urlpatterns = router.urls


В server/urls.py указываем ссылку на дочерний urls.

.. code-block:: python

    # Imports
    from server.apps.main import urls as main_urls

    urlpatterns = [
        # Apps:
        path('api/', include(main_urls, namespace='main')),
        ...
    ]

Проверка
--------

Теперь при запуске сервера и подключении по адресу
вашей view должен отобразиться веб-интерфейс 
Django REST framework

Пример полного пути к view:

.. code-block::

    http://127.0.0.1:8000/api/collections/

Теперь наш бэкенд готов для интеграции с фронтендом!