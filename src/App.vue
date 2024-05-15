<template>
  <div>
    <header>
      <nav>
        <ul>
          <li :class="{ active: currentView === 'todos' }" @click="currentView = 'todos'">Todos</li>
          <li :class="{ active: currentView === 'posts' }" @click="currentView = 'posts'">Posts</li>
        </ul>
      </nav>
    </header>

    <div v-if="currentView === 'todos'">
      <h2>Kegiatan yang Sudah Ditambahkan</h2>
      <ul>
        <li v-for="kegiatan in kegiatanList" :key="kegiatan.id">
          <input type="checkbox" v-model="kegiatan.isDone" />
          <del v-if="kegiatan.isDone">{{ kegiatan.nama }}</del>
          <span v-else>{{ kegiatan.nama }}</span>
          <button @click="deleteKegiatan(kegiatan)">Hapus</button>
        </li>
      </ul>
      <form @submit.prevent="addKegiatan">
        <input type="text" v-model="newKegiatan.nama" placeholder="Tambah Kegiatan Baru" />
        <button type="submit">Tambah</button>
      </form>
    </div>

    <div v-else>
      <h2>Posts</h2>
      <div>
        <label for="userSelect">Select User:</label>
        <select v-model="selectedUserId" @change="fetchPosts">
          <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
        </select>
      </div>
      <ul v-if="posts.length">
        <li v-for="post in posts" :key="post.id">
          <h3>{{ post.title }}</h3>
          <p>{{ post.body }}</p>
        </li>
      </ul>
      <p v-else>No posts available.</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentView: 'todos',
      kegiatanList: [
        { id: 1, nama: 'Belajar pemograman', isDone: false },
        { id: 2, nama: 'Berangkat main futsal', isDone: false }
      ],
      newKegiatan: { nama: '' },
      users: [],
      selectedUserId: null,
      posts: []
    };
  },
  methods: {
    addKegiatan() {
      if (this.newKegiatan.nama.trim()) {
        this.kegiatanList.push({ id: this.kegiatanList.length + 1, nama: this.newKegiatan.nama, isDone: false });
        this.newKegiatan.nama = '';
      }
    },
    deleteKegiatan(kegiatan) {
      this.kegiatanList = this.kegiatanList.filter(k => k.id !== kegiatan.id);
    },
    fetchUsers() {
      fetch('https://jsonplaceholder.typicode.com/users')
        .then(response => response.json())
        .then(data => {
          this.users = data;
          if (this.users.length) {
            this.selectedUserId = this.users[0].id;
            this.fetchPosts();
          }
        });
    },
    fetchPosts() {
      if (this.selectedUserId) {
        fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUserId}`)
          .then(response => response.json())
          .then(data => {
            this.posts = data;
          });
      }
    }
  },
  mounted() {
    this.fetchUsers();
  }
};
</script>

<style>
/* Navigation Styles */
nav ul {
  list-style: none;
  padding: 0;
  display: flex;
  gap: 20px;
}

nav li {
  cursor: pointer;
  padding: 10px 20px;
  background-color: #f0f0f0;
  border-radius: 5px;
  transition: background-color 0.3s ease; /* Animasi transisi */
}

nav li.active {
  background-color: #4CAF50;
  color: white;
}

nav li:hover {
  background-color: #ddd; /* Warna latar belakang saat hover */
}

/* Todo and Post Styles */
li {
  list-style: none;
  padding: 10px;
  border-bottom: 1px solid #ccc;
  transition: background-color 0.3s ease; /* Animasi transisi */
}

li:last-child {
  border-bottom: none;
}

li:hover {
  background-color: #f9f9f9; /* Warna latar belakang saat hover */
}

input[type="text"] {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

button[type="submit"] {
  background-color: #4CAF50;
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease; /* Animasi transisi */
}

button[type="submit"]:hover {
  background-color: #45a049; /* Warna tombol saat hover */
}

/* Post Section Styles */
select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  transition: border-color 0.3s ease; /* Animasi transisi */
}

select:hover {
  border-color: #999; /* Warna border saat hover */
}
</style>
