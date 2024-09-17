<template>
  <div class="container w-full">
    <h1 class="text-3xl font-bold mb-6 text-black" >Gestión de Posts</h1>
    <button @click="openModal('create')" class="bg-blue-500 text-white px-4 py-2 rounded mb-4">Crear Post</button>

    <div class="flex flex-col">
      <div class="-m-1.5 overflow-x-auto">
        <div class="p-1.5 min-w-full inline-block align-middle">
          <div class="overflow-hidden">
            <table class="min-w-full divide-y divide-gray-200 dark:divide-neutral-700">
              <thead >
                <tr >
                  <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-gray-500 uppercase dark:text-neutral-500">ID</th>
                  <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-gray-500 uppercase dark:text-neutral-500">Título</th>
                  <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-gray-500 uppercase dark:text-neutral-500">Descripción</th>
                  <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-gray-500 uppercase dark:text-neutral-500">Editar</th>
                  <th scope="col" class="px-6 py-3 text-start text-xs font-medium text-gray-500 uppercase dark:text-neutral-500">Eliminar</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="post in posts" :key="post.id" class="odd:bg-white even:bg-gray-100 dark:odd:bg-neutral-900 dark:even:bg-neutral-800">
                  <td class="px-6 py-4 whitespace-pre text-sm text-gray-800 dark:text-neutral-200">{{ post.id }}</td>
                  <td class="px-6 py-4 whitespace-pre text-sm text-gray-800 dark:text-neutral-200">{{ post.title }}</td>
                  <td class="px-6 py-4 whitespace-pre text-sm text-gray-800 dark:text-neutral-200">{{ post.body }}</td>
                  <td class="px-6 py-4 whitespace-pre text-sm text-gray-800 dark:text-neutral-200">
                    <button @click="openModal('edit', post)" class="bg-yellow-500 text-white px-4 py-2 rounded mr-2">Editar</button>
                  </td>
                  <td class="px-6 py-4 whitespace-pre text-sm text-gray-800 dark:text-neutral-200">
                    <button @click="openModal('delete', post)" class="bg-red-500 text-white px-4 py-2 rounded">Eliminar</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal -->
    <div v-if="isModalOpen" class="fixed inset-0 flex items-center justify-center bg-gray-900 bg-opacity-50">
      <div class="bg-white p-6 rounded shadow-lg w-1/3">
        <h3 class="text-xl font-bold mb-4">{{ modalTitle }}</h3>
        <div v-if="modalType === 'create' || modalType === 'edit'">
          <label class="block text-sm font-medium text-gray-700 mb-2" for="title">Título</label>
          <input type="text" v-model="currentPost.title" class="w-full px-4 py-2 border rounded-md mb-4" />
          <label class="block text-sm font-medium text-gray-700 mb-2" for="body">Descripción</label>
          <input type="text" v-model="currentPost.body" class="w-full px-4 py-2 border rounded-md mb-4" />
        </div>
        <div v-else>
          <p class="text-sm">¿Estás seguro de que deseas eliminar este post?</p>
        </div>
        <div class="flex justify-end space-x-4">
          <button @click="handleSubmit" class="bg-green-500 text-white px-4 py-2 rounded">{{ modalType === 'delete' ? 'Eliminar' : 'Guardar' }}</button>
          <button @click="closeModal" class="bg-gray-300 px-4 py-2 rounded">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],
      isModalOpen: false,
      modalType: '',
      modalTitle: '',
      currentPost: { id: null, title: '', body: '' }
    };
  },
  created() {
    this.fetchPosts();
  },
  methods: {
    // Obtener todos los posts
    fetchPosts() {
      axios.get('https://jsonplaceholder.typicode.com/posts')
        .then(response => {
          this.posts = response.data;
        })
        .catch(error => console.error(error));
    },
    // Abrir modal
    openModal(type, post = { id: null, title: '', body: '' }) {
      this.modalType = type;
      this.isModalOpen = true;
      if (type === 'create') {
        this.modalTitle = 'Crear Post';
        this.currentPost = { id: null, title: '', body: '' };
      } else if (type === 'edit') {
        this.modalTitle = 'Editar Post';
        this.currentPost = { ...post };
      } else if (type === 'delete') {
        this.modalTitle = 'Eliminar Post';
        this.currentPost = { ...post };
      }
    },
    // Cerrar modal
    closeModal() {
      this.isModalOpen = false;
    },
    // Enviar datos para crear/editar/eliminar
    handleSubmit() {
      if (this.modalType === 'create') {
        // craer de un post
        const newPost = {
          id: this.posts.length + 1,
          title: this.currentPost.title,
          body: this.currentPost.body,
          
        };
        this.posts.push(newPost);
        this.closeModal();
      } else if (this.modalType === 'edit') {
        // editar un post
        const postIndex = this.posts.findIndex(post => post.id === this.currentPost.id);
        if (postIndex !== -1) {
          this.posts[postIndex] = { ...this.currentPost };
        }
        this.closeModal();
      } else if (this.modalType === 'delete') {
        // eliminar un post
        this.posts = this.posts.filter(post => post.id !== this.currentPost.id);
        this.closeModal();
      }
    }
  }
};
</script>

<style>

</style>
