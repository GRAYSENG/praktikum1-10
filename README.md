# Nama: GRAYVEN C.W
# Nim: 312410435
# Kelas: I241C
# Matkul: Pemrograman Web 2

## Langkah-Langkah Praktikum 11: VueJS
VueJS adalah framework JavaScript untuk membangun antarmuka pengguna yang interaktif dan dinamis, dengan keseimbangan kemudahan penggunaan dan performa.

Siapkan library VueJS dan Axios via CDN:
```<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>```
```<script src="https://unpkg.com/axios/dist/axios.min.js"></script>```

Membuat file `index.html` pada direktori `lab8_vuejs`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Frontend Vuejs</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <link rel="stylesheet" href="assets/css/style.css">
</head>
<body>
    <div id="app">
        <h1>Daftar Artikel</h1>
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Judul</th>
                    <th>Status</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(row, index) in artikel">
                    <td class="center-text">{{ row.id }}</td>
                    <td>{{ row.judul }}</td>
                    <td>{{ statusText(row.status) }}</td>
                    <td class="center-text">
                        <a href="#" @click="edit(row)">Edit</a>
                        <a href="#" @click="hapus(index, row.id)">Hapus</a>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <script src="assets/js/app.js"></script>
</body>
</html>
```
Penjelasan: Menampilkan data artikel dari backend CI4.

Membuat file `app.js`:
```JavaScript
const { createApp } = Vue

const apiUrl = 'http://localhost/lab11_ci/ci4/public'

createApp({
    data() {
        return {
            artikel: [],
            formData: {
                id: null,
                judul: '',
                isi: '',
                status: 0
            },
            showForm: false,
            formTitle: 'Tambah Data',
            statusOptions: [
                { text: 'Draft', value: 0 },
                { text: 'Publish', value: 1 }
            ]
        }
    },
    mounted() {
        this.loadData()
    },
    methods: {
        loadData() {
            axios.get(apiUrl + '/post')
            .then(response => {
                this.artikel = response.data.artikel
            })
            .catch(error => {
                console.log(error)
            })
        },
        tambah() {
            this.showForm = true
            this.formTitle = 'Tambah Data'
            this.formData = { id: null, judul: '', isi: '', status: 0 }
        },
        edit(data) {
            this.showForm = true
            this.formTitle = 'Ubah Data'
            this.formData = { id: data.id, judul: data.judul, isi: data.isi, status: data.status }
        },
        hapus(index, id) {
            if (confirm('Yakin menghapus data?')) {
                axios.delete(apiUrl + '/post/' + id)
                .then(response => {
                    this.artikel.splice(index, 1)
                })
                .catch(error => {
                    console.log(error)
                })
            }
        },
        saveData() {
            if (this.formData.id) {
                axios.put(apiUrl + '/post/' + this.formData.id, this.formData)
                .then(response => { this.loadData() })
                .catch(error => { console.log(error) })
            } else {
                axios.post(apiUrl + '/post', this.formData)
                .then(response => { this.loadData() })
                .catch(error => { console.log(error) })
            }
            this.formData = { id: null, judul: '', isi: '', status: 0 }
            this.showForm = false
        },
        statusText(status) {
            if (!status) return 'Draft'
            return status == 1 ? 'Publish' : 'Draft'
        }
    }
}).mount('#app')
```
Fungsi utama: `loadData`, `tambah`, `edit`, `hapus`, `saveData`.

Form tambah/ubah data pada `index.html`:
```html
<button id="btn-tambah" @click="tambah">Tambah Data</button>
<div class="modal" v-if="showForm">
    <div class="modal-content">
        <span class="close" @click="showForm = false">&times;</span>
        <form id="form-data" @submit.prevent="saveData">
            <h3 id="form-title">{{ formTitle }}</h3>
            <div><input type="text" name="judul" id="judul" vmodel="formData.judul" placeholder="Judul" required></div>
            <div><textarea name="isi" id="isi" rows="10" vmodel="formData.isi"></textarea></div>
            <div>
                <select name="status" id="status" vmodel="formData.status">
                    <option v-for="option in statusOptions" :value="option.value">{{ option.text }}</option>
                </select>
            </div>
            <input type="hidden" id="id" v-model="formData.id">
            <button type="submit" id="btnSimpan">Simpan</button>
            <button @click="showForm = false">Batal</button>
        </form>
    </div>
</div>
```

File `style.css`:
```css
#app { margin: 0 auto; width: 900px; }
table { min-width: 700px; width: 100%; }
th { padding: 10px; background: #5778ff; color: #ffffff; }
tr td { border-bottom: 1px solid #eff1ff; }
tr:nth-child(odd) { background-color: #eff1ff; }
td { padding: 10px; }
.center-text { text-align: center; }
td a { margin: 5px; }
#form-data { width: 600px; }
form input { width: 100%; margin-bottom: 5px; padding: 5px; box-sizing: border-box; }
form select { margin-bottom: 5px; padding: 5px; box-sizing: border-box; }
form textarea { width: 100%; margin-bottom: 5px; padding: 5px; box-sizing: border-box; }
form div { margin-bottom: 5px; position: relative; }
form button { padding: 10px 20px; margin-top: 10px; margin-bottom: 10px; margin-right: 10px; cursor: pointer; }
#btn-tambah { margin-bottom: 15px; padding: 10px 20px; cursor: pointer; background-color: #3152d6; color: #ffffff; border: 1px solid #3152d6; }
#btnSimpan { background-color: #3152d6; color: #ffffff; border: 1px solid #3152d6; }
.modal { display: block; position: fixed; z-index: 1; left: 0; top: 0; width: 100%; height: 100%; overflow: auto; background-color: rgba(0, 0, 0, 0.4); }
.modal-content { background-color: #fefefe; margin: 15% auto; padding: 20px; border: 1px solid #888; width: 600px; }
.close { color: #aaa; float: right; font-size: 28px; font-weight: bold; cursor: pointer; }
```

Penjelasan: VueJS mempermudah pembuatan antarmuka CRUD secara dinamis dengan reaktivitas data dan pengikatan formulir otomatis tanpa reload halaman.



## Langkah-Langkah Praktikum 12: VueJS Komponen dan Routing (SPA)
Vue Components adalah bagian UI yang modular dan reusable. Vue Router menangani perpindahan halaman di sisi klien (Client-Side Routing).

Tambahkan library Vue Router via CDN:
```<script src="https://unpkg.com/vue-router@4/dist/vue-router.global.js"></script>```

Penjelasan: Menghubungkan Vue Router untuk navigasi antar halaman tanpa refresh.

#### 1. Komponen Halaman Utama (Home.js)
```JS
const Home = {
    template: `
        <div class="home-container">
            <h2>Selamat Datang di Portal Admin Artikel</h2>
            <p>Gunakan menu navigasi di atas untuk mengelola data artikel secara real-time
memanfaatkan RESTful API CodeIgniter 4 dan VueJS.</p>
        </div>
    `
};
```
Penjelasan: Tampilan halaman utama berisi sambutan dan panduan navigasi.

#### 2. Komponen Artikel (assets/js/components/Artikel.js)
Memindahkan logika CRUD dari `app.js` ke `Artikel.js`:
```JS
const Artikel = {
    template: `
    <div>
        <h2>Manajemen Data Artikel</h2>
        <button id="btn-tambah" @click="tambah">Tambah Data</button>
        <div class="modal" v-if="showForm">
            <div class="modal-content">
                <span class="close" @click="showForm = false">&times;</span>
                <form id="form-data" @submit.prevent="saveData">
                    <h3>{{ formTitle }}</h3>
                    <div><input type="text" name="judul" v-model="formData.judul" placeholder="Judul" required></div>
                    <div><textarea name="isi" rows="10" v-model="formData.isi" placeholder="Isi Artikel" required></textarea></div>
                    <div>
                        <select name="status" v-model="formData.status">
                            <option v-for="option in statusOptions" :key="option.value" :value="option.value">{{ option.text }}</option>
                        </select>
                    </div>
                    <input type="hidden" v-model="formData.id">
                    <button type="submit" id="btnSimpan">Simpan</button>
                    <button type="button" @click="showForm = false">Batal</button>
                </form>
            </div>
        </div>
        <table>
            <thead>
                <tr><th>ID</th><th>Judul</th><th>Status</th><th>Aksi</th></tr>
            </thead>
            <tbody>
                <tr v-for="(row, index) in artikel" :key="row.id">
                    <td class="center-text">{{ row.id }}</td>
                    <td>{{ row.judul }}</td>
                    <td>{{ statusText(row.status) }}</td>
                    <td class="center-text">
                        <a href="#" @click.prevent="edit(row)">Edit</a> |
                        <a href="#" @click.prevent="hapus(index, row.id)">Hapus</a>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    `,
    data() {
        return {
            artikel: [],
            formData: { id: null, judul: '', isi: '', status: 0 },
            showForm: false,
            formTitle: 'Tambah Data',
            statusOptions: [{ text: 'Draft', value: 0 }, { text: 'Publish', value: 1 }]
        };
    },
    mounted() { this.loadData(); },
    methods: {
        loadData() {
            axios.get(apiUrl + '/post')
                .then(response => { this.artikel = response.data.artikel; })
                .catch(error => { console.log(error); });
        },
        tambah() {
            this.showForm = true;
            this.formTitle = 'Tambah Data';
            this.formData = { id: null, judul: '', isi: '', status: 0 };
        },
        edit(data) {
            this.showForm = true;
            this.formTitle = 'Ubah Data';
            this.formData = { id: data.id, judul: data.judul, isi: data.isi, status: data.status };
        },
        hapus(index, id) {
            if (confirm('Yakin menghapus data?')) {
                axios.delete(apiUrl + '/post/' + id)
                    .then(response => { this.artikel.splice(index, 1); })
                    .catch(error => { console.log(error); });
            }
        },
        saveData() {
            if (this.formData.id) {
                axios.put(apiUrl + '/post/' + this.formData.id, this.formData)
                    .then(response => { this.loadData(); })
                    .catch(error => { console.log(error); });
            } else {
                axios.post(apiUrl + '/post', this.formData)
                    .then(response => { this.loadData(); })
                    .catch(error => { console.log(error); });
            }
            this.formData = { id: null, judul: '', isi: '', status: 0 };
            this.showForm = false;
        },
        statusText(status) {
            if (!status) return 'Draft';
            return status == 1 ? 'Publish' : 'Draft';
        }
    }
};
```
Penjelasan: Logika CRUD dipindahkan ke `Artikel.js` agar kode lebih terstruktur. `app.js` fokus ke konfigurasi routing, sedangkan `Artikel.js` menangani operasi data artikel.

#### 3. Konfigurasi Vue Router (assets/js/app.js)
```JS
const { createApp } = Vue;
const { createRouter, createWebHashHistory } = VueRouter;

const apiUrl = 'http://localhost/lab11_ci/ci4/public';

const routes = [
    { path: '/', component: Home },
    { path: '/artikel', component: Artikel },
    { path: '/about', component: About }
];

const router = createRouter({
    history: createWebHashHistory(),
    routes
});

const app = createApp({});
app.use(router);
app.mount('#app');
```
Penjelasan: Mengatur navigasi halaman dengan Hash History agar perpindahan antarhalaman berjalan tanpa reload.

#### 4. Master Layout (index.html)
```JS
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Frontend VueJS</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script src="https://unpkg.com/vue-router@4/dist/vue-router.global.js"></script>
    <link rel="stylesheet" href="assets/css/style.css">
</head>
<body>
    <div id="app">
        <header>
            <h1>Daftar Artikel</h1>
            <nav class="nav-menu">
                <router-link to="/">Beranda</router-link> |
                <router-link to="/artikel">Kelola Artikel</router-link>
                <router-link to="/about">About</router-link>
            </nav>
        </header>
        <main>
            <router-view></router-view>
        </main>
    </div>
    <script src="assets/js/components/Home.js"></script>
    <script src="assets/js/components/Artikel.js"></script>
    <script src="assets/js/components/About.js"></script>
    <script src="assets/js/app.js"></script>
</body>
</html>
```
Penjelasan: `router-link` untuk navigasi antar halaman, `router-view` untuk menampilkan komponen sesuai rute aktif.

#### 5. Tambahan CSS (assets/css/style.css)
```css
.nav-menu { padding: 10px; background: #eff1ff; border-radius: 5px; margin-bottom: 15px; }
.nav-menu a { text-decoration: none; color: #3152d6; font-weight: bold; padding: 5px 10px; }
.router-link-exact-active { background-color: #3152d6; color: #ffffff !important; border-radius: 3px; }
.home-container { padding: 20px; border: 1px solid #eff1ff; background: #fafafa; }
```

**Tugas:** Tambah rute `/about` dengan komponen `About.js` berisi profil (Nama, NIM, Kelas, Foto):
```html
<nav class="nav-menu">
    <router-link to="/">Beranda</router-link> |
    <router-link to="/artikel">Kelola Artikel</router-link>
    <router-link to="/about">About</router-link>
</nav>
```
```JS
const routes = [
    { path: '/', component: Home },
    { path: '/artikel', component: Artikel },
    { path: '/about', component: About }
];
```
```JS
const About = {
    template: `
    <div class="about-container">
        <div class="profile-card">
            <div class="profile-header">
                <img src="assets/img/avatar.jpg" alt="Foto Profil" class="profile-img">
                <div class="profile-title">
                    <h2>Bagus Aditya</h2>
                    <p>Pemrograman Web 2</p>
                </div>
            </div>
            <div class="profile-info">
                <table>
                    <tr><td>Nama</td><td>Bagus Aditya Hermawan</td></tr>
                    <tr><td>NIM</td><td>312410382</td></tr>
                    <tr><td>Kelas</td><td>I241C</td></tr>
                </table>
            </div>
        </div>
    </div>
    `
};
```
```css
.about-container { margin-top: 20px; }
.profile-card { background: #eef0fa; border-radius: 8px; padding: 25px; }
.profile-header { display: flex; align-items: center; gap: 20px; margin-bottom: 25px; }
.profile-img { width: 120px; height: 120px; border-radius: 8px; object-fit: cover; border: 2px solid #3b5bdb; }
.profile-title h2 { margin: 0; color: #3b5bdb; }
.profile-title p { margin-top: 5px; color: #666; }
.profile-info table { width: 100%; border-collapse: collapse; }
.profile-info td { padding: 12px; border-bottom: 1px solid #d6daf0; }
.profile-info td:first-child { font-weight: bold; width: 120px; color: #3b5bdb; }
```



## Langkah-Langkah Praktikum 13: VueJS Autentikasi dan Navigation Guards (SPA Security)
Navigation Guards menggunakan `router.beforeEach()` sebagai interceptor yang memeriksa status login sebelum rute ditampilkan.

#### Tahap 1: API Endpoint Login (Backend CI4)

**Langkah 1.1: Auth Controller** (`app/Controllers/Api/Auth.php`)
```php
<?php
namespace App\Controllers\Api;
use CodeIgniter\RESTful\ResourceController;
use App\Models\UserModel;

class Auth extends ResourceController
{
    protected $format = 'json';

    public function login()
    {
        $username = $this->request->getVar('username');
        $password = $this->request->getVar('password');
        $model = new UserModel();

        $user = $model->where('username', $username)
            ->orWhere('useremail', $username)
            ->first();

        if ($user) {
            if ($password === $user['userpassword'] || password_verify($password, $user['userpassword'])) {
                return $this->respond([
                    'status' => 200,
                    'error' => null,
                    'messages' => 'Login Berhasil',
                    'data' => [
                        'id' => $user['id'],
                        'username' => $user['username'],
                        'token' => base64_encode("TOKEN-SECRET-" . $user['username'])
                    ]
                ], 200);
            }
        }

        return $this->failUnauthorized('Username atau Password yang Anda masukkan salah.');
    }
}
```
Penjelasan: Memverifikasi username/email dan password. Jika cocok, mengembalikan data user beserta token. Jika tidak, mengembalikan pesan error.

**Langkah 1.2: Route Login** (`Routes.php`)
```php
$routes->post('api/login', 'Api\Auth::login');
```

#### Tahap 2: Frontend VueJS

**Langkah 2.1: Komponen Login** (`assets/js/components/Login.js`)
```php
const Login = {
    template: `
        <div class="login-container">
            <div class="login-box">
                <h2>Form Login Admin</h2>
                <form @submit.prevent="handleLogin">
                    <div class="form-group">
                        <label>Username / Email</label>
                        <input type="text" v-model="username" placeholder="Masukkan username" required>
                    </div>
                    <div class="form-group">
                        <label>Password</label>
                        <input type="password" v-model="password" placeholder="Masukkan password" required>
                    </div>
                    <button type="submit" class="btn-login">Masuk Aplikasi</button>
                </form>
                <p v-if="errorMessage" class="error-msg">{{ errorMessage }}</p>
            </div>
        </div>
    `,
    data() {
        return { username: '', password: '', errorMessage: '' }
    },
    methods: {
        handleLogin() {
            axios.post(apiUrl + '/api/login', { username: this.username, password: this.password })
            .then(response => {
                if (response.data.status === 200) {
                    localStorage.setItem('isLoggedIn', 'true');
                    localStorage.setItem('userToken', response.data.data.token);
                    const appInstance = this.$root;
                    appInstance.isLoggedIn = true;
                    this.$router.push('/artikel');
                }
            })
            .catch(error => {
                if (error.response && error.response.data.messages) {
                    this.errorMessage = error.response.data.messages;
                } else {
                    this.errorMessage = 'Terjadi kesalahan jaringan atau server.';
                }
            });
        }
    }
};
```
Penjelasan: Form login yang mengirim kredensial via Axios. Jika berhasil, token dan status login disimpan di localStorage lalu diarahkan ke halaman Artikel. Jika gagal, menampilkan pesan error.

**Langkah 2.2: Navigation Guard** (`assets/js/app.js`)
```php
const { createApp } = Vue;
const { createRouter, createWebHashHistory } = VueRouter;

const apiUrl = 'http://localhost/lab11_ci/ci4/public';

const routes = [
    { path: '/', component: Home },
    { path: '/login', component: Login },
    { path: '/artikel', component: Artikel, meta: { requiresAuth: true } },
    { path: '/about', component: About }
];

const router = createRouter({
    history: createWebHashHistory(),
    routes
});

router.beforeEach((to, from, next) => {
    const isAuthenticated = localStorage.getItem('isLoggedIn') === 'true';
    if (to.matched.some(record => record.meta.requiresAuth) && !isAuthenticated) {
        alert('Akses Ditolak! Anda harus login terlebih dahulu.');
        next('/login');
    } else {
        next();
    }
});

const app = createApp({
    data() {
        return { isLoggedIn: false }
    },
    mounted() {
        this.isLoggedIn = localStorage.getItem('isLoggedIn') === 'true';
    },
    methods: {
        logout() {
            if (confirm('Apakah Anda yakin ingin keluar aplikasi?')) {
                localStorage.removeItem('isLoggedIn');
                localStorage.removeItem('userToken');
                this.isLoggedIn = false;
                this.$router.push('/');
            }
        }
    }
});

app.use(router);
app.mount('#app');
```
Penjelasan: Navigation guard membatasi akses halaman Artikel hanya untuk pengguna yang sudah login. Mengelola status autentikasi via localStorage dan fitur logout.

**Langkah 2.3: Layout Dinamis** (`index.html`)
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Frontend VueJS</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script src="https://unpkg.com/vue-router@4/dist/vue-router.global.js"></script>
    <link rel="stylesheet" href="assets/css/style.css">
</head>
<body>
    <div id="app">
        <header>
            <h1>Daftar Artikel</h1>
            <nav class="nav-menu">
                <router-link to="/">Beranda</router-link> |
                <router-link to="/artikel">Kelola Artikel</router-link> |
                <router-link to="/about">About</router-link> |
                <router-link v-if="!isLoggedIn" to="/login">Login</router-link>
                <a v-else href="#" @click.prevent="logout">Logout</a>
            </nav>
        </header>
        <main>
            <router-view></router-view>
        </main>
    </div>
    <script src="assets/js/components/Home.js"></script>
    <script src="assets/js/components/Artikel.js"></script>
    <script src="assets/js/components/About.js"></script>
    <script src="assets/js/components/Login.js"></script>
    <script src="assets/js/app.js"></script>
</body>
</html>
```
Penjelasan: Menu navigasi dinamis — menampilkan link Login jika belum login, atau Logout jika sudah login.

**Langkah 2.4: CSS Form Login** (`assets/css/style.css`)
```css
.login-container { display: flex; justify-content: center; align-items: center; padding: 40px 0; }
.login-box { width: 350px; padding: 25px; border: 1px solid #ccc; border-radius: 8px; background-color: #ffffff; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
.login-box h2 { margin-top: 0; margin-bottom: 20px; text-align: center; color: #333; }
.form-group { margin-bottom: 15px; }
form-group label { display: block; margin-bottom: 5px; font-weight: bold; }
.form-group input { width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
.btn-login { width: 100%; padding: 10px; background-color: #3152d6; color: white; border: none; border-radius: 4px; font-weight: bold; cursor: pointer; }
.btn-login:hover { background-color: #203ca3; }
.error-msg { color: red; font-size: 14px; text-align: center; margin-top: 15px; }
```

**Tugas — Proteksi halaman About:**
```php
{
    path: '/about',
    component: About,
    meta: { requiresAuth: true }
}
```
Penjelasan: Halaman About ikut terproteksi — pengguna yang belum login akan diarahkan ke form login.

**Skenario A (Belum Login):** Akses halaman Artikel/About tanpa login → sistem menolak dan menampilkan alert, lalu diarahkan ke form login.

**Skenario B (Sudah Login):** Masukkan kredensial valid → sistem memvalidasi via Axios → diarahkan ke halaman Artikel → menu Login berubah menjadi Logout → saat logout muncul konfirmasi, lalu diarahkan ke halaman utama.

**Analisis:**
- `router.beforeEach`: memeriksa status login di localStorage. Jika belum login dan rute membutuhkan autentikasi, tampilkan peringatan dan arahkan ke Login.
- Axios HTTP POST: mengirim data login ke server, jika berhasil menyimpan token ke localStorage. Axios berperan sebagai penghubung frontend Vue.js dengan backend API.



## Langkah-Langkah Praktikum 14: Keamanan API, Autentikasi Token, dan Axios Interceptors

#### Tahap 1: Mengamankan Endpoint API (Backend CI4)

**Langkah 1.1: ApiAuthFilter** (`app/Filters/ApiAuthFilter.php`)
```php
<?php
namespace App\Filters;
use CodeIgniter\Filters\FilterInterface;
use CodeIgniter\Http\RequestInterface;
use CodeIgniter\Http\ResponseInterface;
use Config\Services;

class ApiAuthFilter implements FilterInterface
{
    public function before(RequestInterface $request, $arguments = null)
    {
        $authHeader = $request->getServer('HTTP_AUTHORIZATION');

        if (!$authHeader) {
            $response = Services::response();
            $response->setStatusCode(401);
            return $response->setJSON([
                'status' => 401,
                'error' => 401,
                'messages' => 'Akses Ditolak. Token tidak ditemukan pada request!'
            ]);
        }

        $token = null;
        if (preg_match('/Bearer\s(\S+)/', $authHeader, $matches)) {
            $token = $matches[1];
        }

        if (!$token || empty($token)) {
            $response = Services::response();
            $response->setStatusCode(401);
            return $response->setJSON([
                'status' => 401,
                'error' => 401,
                'messages' => 'Sesi Token tidak valid atau kedaluwarsa!'
            ]);
        }
    }

    public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
    {
        // Tidak diperlukan aksi setelah request diproses
    }
}
```
Penjelasan: Memeriksa token Authorization sebelum memproses request. Jika token tidak ada atau tidak valid, mengembalikan response 401.

**Langkah 1.2: Daftarkan Filter** (`Filters.php`)
```php
'apiauth' => \App\Filters\ApiAuthFilter::class,
```

**Langkah 1.3: Terapkan ke Route Artikel** (`Routes.php`)
```php
$routes->post('post', 'Api\\Post::create', ['filter' => 'apiauth']);
$routes->put('post/(:segment)', 'Api\\Post::update/$1', ['filter' => 'apiauth']);
$routes->delete('post/(:segment)', 'Api\\Post::delete/$1', ['filter' => 'apiauth']);
```
Penjelasan: Melindungi operasi CUD (Create, Update, Delete) dari pengguna tanpa token valid.

#### Tahap 2: Axios Interceptors (Frontend VueJS)

**Langkah 2.1: Konfigurasi Interceptor** (`assets/js/app.js`)
```php
const { createApp } = Vue;
const { createRouter, createWebHashHistory } = VueRouter;

const apiUrl = 'http://localhost/lab11_ci/ci4/public';

axios.interceptors.request.use(
    (config) => {
        const token = localStorage.getItem('userToken');
        if (token) {
            config.headers['Authorization'] = 'Bearer ' + token;
        }
        return config;
    },
    (error) => {
        return Promise.reject(error);
    }
);

axios.interceptors.response.use(
    (response) => {
        return response;
    },
    (error) => {
        if (error.response && error.response.status === 401) {
            alert('Sesi Anda telah berakhir atau Token tidak sah. Silakan login kembali.');
            localStorage.clear();
            window.location.href = '#/login';
            window.location.reload();
        }
        return Promise.reject(error);
    }
);

const routes = [
    { path: '/', component: Home },
    { path: '/login', component: Login },
    { path: '/artikel', component: Artikel, meta: { requiresAuth: true } },
    { path: '/about', component: About, meta: { requiresAuth: true } }
];

const router = createRouter({
    history: createWebHashHistory(),
    routes
});

router.beforeEach((to, from, next) => {
    const isAuthenticated = localStorage.getItem('isLoggedIn') === 'true';
    if (to.matched.some(record => record.meta.requiresAuth) && !isAuthenticated) {
        alert('Akses Ditolak! Anda harus login terlebih dahulu.');
        next('/login');
    } else {
        next();
    }
});

const app = createApp({
    data() {
        return { isLoggedIn: false }
    },
    mounted() {
        this.isLoggedIn = localStorage.getItem('isLoggedIn') === 'true';
    },
    methods: {
        logout() {
            if (confirm('Apakah Anda yakin ingin keluar aplikasi?')) {
                localStorage.removeItem('isLoggedIn');
                localStorage.removeItem('userToken');
                this.isLoggedIn = false;
                this.$router.push('/');
            }
        }
    }
});

app.use(router);
app.mount('#app');
```
Penjelasan: Axios Interceptor otomatis menyisipkan token ke setiap request API. Jika response 401, sesi dihapus dan pengguna diarahkan ke halaman login. Halaman Artikel dan About hanya bisa diakses oleh pengguna yang sudah login.

**Pertanyaan dan Tugas:**

1. Terapkan seluruh kode pengamanan Token-Based Authentication pada backend (CI4) dan frontend (VueJS).

2. Simulasi pembobolan via Postman: tembak endpoint `POST http://localhost/labci4/public/post` tanpa token di header.
Jawab: Response menunjukkan error HTTP 401 Unauthorized berhasil dikembalikan.

3. Login lalu coba manipulasi data artikel (Tambah/Ubah/Hapus). Berkat Axios Interceptors, token dikirim otomatis di latar belakang.
Jawab: Setelah membuka tab network, token berhasil dikirim secara terselubung di latar belakang sistem.

4. Kesimpulan perbedaan Vue Router Navigation Guards vs CodeIgniter Filters:
Vue Router Navigation Guards hanya melindungi tampilan antarmuka di browser, sehingga masih bisa dibypass dengan mengakses endpoint API langsung via Postman. CodeIgniter Filters melindungi data di sisi server dengan memvalidasi token setiap request masuk — tanpa token valid, server langsung menolak dengan response 401 tanpa menyentuh database. Keduanya sebaiknya diterapkan bersamaan: Navigation Guards untuk kenyamanan antarmuka, CodeIgniter Filters sebagai pelindung sistem.
