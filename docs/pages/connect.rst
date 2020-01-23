Связываем шаблоны
=================

Указываем путь к API
--------------------

Прописываем полный путь к API в файле frontend/config/.env

.. code-block::

    # === General ===

    API_URL=http://127.0.0.1:8000/api/

В frontend/nuxt.config.js ставим напротив https false

.. code-block:: js

    'axios': {
        // See https://axios.nuxtjs.org/options.html
        'debug': process.env.NODE_ENV === 'development',
        // Меняем значение на false
        'https': false,
        ...
    },

Теперь фронтенд подключается к бекенду. Для дальнейшей
корректной работы необходимо настроить сервисы, хранилище
vuex и компоненты vue.js.

Вся дальнейшая работа с файлами происходит относительно
пути frontend/client

В папке logic содержится вся бизнес-логика проекта.
В шаблоне реализован модуль для работы с комментариями
comments.

Будем использовать его в качестве основы. При желании можно
переименовать.

Модель данных
-------------

В файле logic/comments/models.ts прописываем модель данных.
Она должна однозначно соответствовать модели на бекенде.

Здесь же создаём из неё тип данных.

.. code-block:: js

    // Модель
    export const Collection = ts.type({
    'id': ts.number,
    'title': ts.string,
    'created_at': ts.string,
    })

    // Тип данных
    // Static TypeScript type, that can be used as a regular `type`:
    export type CollectionType = ts.TypeOf<typeof Collection>




Сервис
------

Создаём токен для сервиса:

.. code-block:: js

    export default {
        // My service
        'MY_SERVICE': new Token('my_service'),

        // Axios instance:
        'AXIOS': new Token('axios'),
        }


В logic/types.ts добавляем модуль:

.. code-block:: js

    import { CollectionType } from '~/logic/comments/models'
    ...
    export interface StateType {
        // Module:
        collections: {
            // Module's state:
            collections: CollectionType[]
        }
        ....
    }

В logic/comments/services/api.ts создаём сервис. 
Сюда же добавляем все взаимодействия с бэкендом.

.. code-block:: js
    :linenos:

    @Service(tokens.MY_SERVICE)
    export default class MyService {

        protected get $axios (): AxiosInstance {
            return Container.get(tokens.AXIOS) as AxiosInstance
        }

        // Метод для загрузки JSON'ов с бэкенда
        public async fetchCollections (): Promise<CollectionType[]> {
            const response = await this.$axios.get('collections/')
            return tPromise.decode(ts.array(Collection), response.data)
        }

        // Метод для обновления коллекции
        public async updateCollection (
            collection: CollectionType
        ): Promise<CollectionType> {
            const response = await this
            .$axios.put(`collections/${collection.id}/`, collection)
            return tPromise.decode(Collection, response.data)
        }
    }


Создаём модуль внутри logic/comments/module.ts

Это необходимо для связи локального хранилища и API

.. code-block:: js
    :linenos:

    import { CollectionType } from '~/logic/comments/models'
    import MyService from '~/logic/comments/services/api'

    @Injectable()
    /**
    * Represents a typed Vuex module.
    *
    * @see https://vuex.vuejs.org/guide/modules.html
    * @see https://github.com/sascha245/vuex-simple
    */
    export class MyModule {
        // Dependencies

        @Inject(tokens.MY_SERVICE)
        public service!: MyService

        // State

        @State()
        public collections: CollectionType[] = []

        // Getters

        @Getter()
        public get hasCollections (): boolean {
            return Boolean(this.collections && this.collections.length > 0)
        }

        // Mutations

        @Mutation()
        public setCollections (payload: CollectionType[]): void {
            this.collections = payload
        }

        @Mutation()
        public updateCollectionTitle (id: number, title: string): void {
            if (!this.collections) return

            const commentIndex = this.collections.findIndex((collection): boolean => {
            return collection.id === id
            })

            if (!this.collections || !this.collections[commentIndex]) return

            this.collections[commentIndex].title = title
        }

        // Actions

        @Action()
        public async fetchCollections (): Promise<CollectionType[]> {
            const collectionList = await this.service.fetchCollections()
            this.setCollections(collectionList)
            return collectionList
        }

        @Action()
        public async updateCollection (
            collection: CollectionType
        ): Promise<CollectionType> {
            return this.service.updateCollection(collection)
        }
    }

Добавляем модуль в хранилище logic/store.ts

.. code-block:: js

    import { MyModule } from '~/logic/comments/module'

    export default class TypedStore {
        @Module()
        public collections = new MyModule()
    }


logic/types.ts

.. code-block:: js

    import { CollectionType } from '~/logic/comments/models'


    export interface StateType {
        // Module:
        collections: {
            // Module's state:
            collections: CollectionType[]
        }
    }

Готово! Осталось добавить компонент Vue для отображения
загружаемых данных.

Компонент Vue
-------------

**Работоспособность нижеприведённого кода не гарантируется.
Опирайтесь на исходники шаблона.**

Создаём компонент в папке components/ под названием
CollectionFrame. Опираемся на components/Comment.vue
при написании собственного компонента.

.. code-block:: html

    <template>
    <div>
        <p>
        {{ collection.id }}
        </p>

        <p :class="$style.body">
        {{ collection.title }}
        </p>

        <div>
        <button
            @click="updateCollection"
            @keypress="updateCollection"
        >
            Update
        </button>
        </div>
    </div>
    </template>

.. code-block:: js

    <script lang="ts">
    import Component, { mixins } from 'nuxt-class-component'
    import { Prop } from 'vue-property-decorator'

    import { CollectionType } from '~/logic/comments/models'
    import TypedStoreMixin from '~/mixins/typed-store'

    // @vue/component
    @Component({})
    readonly collection!: CollectionType

    async updateCollection (): Promise<CollectionType> {
        return this
        .typedStore
        .collections
        .updateCollection(this.collection)
    }
    }
    </script>

    <style lang="scss" module>
    @import '~/scss/variables';

    .body {
    // text-align: justify;
    width: inherit;
    }

    button {
    margin: 0 0.5rem;
    border: none;
    cursor: pointer;
    width: 5rem;
    }

    </style>

Далее добавляем его для отображения в шаблоне главной
страницы pages/index.vue

.. code-block:: html

    <template>
    <main>

        <section
        v-if="typedStore.collections.has"
        :class="$style.container"
        >
        <text-entity-frame
            v-for="collection in typedStore.collections.collections"
            :key="collection.id"
            :text-entity="collection"
        />
        </section>

    </main>
    </template>

.. code-block:: js

    <script lang="ts">
    import Component, { mixins } from 'nuxt-class-component'
    import { Store } from 'vuex'
    import { useStore } from 'vuex-simple'

    import CollectionFrame from '~/components/CollectionFrame.vue'
    import { CollectionType } from '~/logic/comments/models'
    import { TypedStore } from '~/logic/store'
    import { StateType } from '~/logic/types'
    import TypedStoreMixin from '~/mixins/typed-store'

    // @vue/component
    @Component({
        'components': {
            CollectionFrame,
        },
    })

    export default class Index extends mixins(TypedStoreMixin) {

    fetch(
        { store }: { store: Store<StateType> }
    ): Promise<CollectionType[]> {
        const typedStore = useStore<TypedStore>(store)
        return typedStore.collections.fetchCollections()
    }
    }
    </script>

    <style lang="scss" module>

    .container {
    border: 1px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
    }
    </style>

