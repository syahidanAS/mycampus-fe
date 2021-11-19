<template>
  <b-container class="mt-4">
   <b-row>
     <b-col lg="4">
       <form v-on:submit.prevent="postData">
          <div class="form-group">
          <input type="text" class="form-control" name="id_mahasiswa" v-model="posts.id_mahasiswa" hidden>
        </div>
  <div class="form-group">
    <label for="exampleFormControlInput1">NIM</label>
    <input type="text" class="form-control" name="nim" v-model="posts.nim" placeholder="17111xxx" required>
  </div>
  <div class="form-group">
    <label for="exampleFormControlInput1">Nama Lengkap</label>
    <input type="text" class="form-control" name="nama" v-model="posts.nama" placeholder="Budi" required>
  </div>
  <div class="form-group">
    <label for="exampleFormControlSelect1">Jurusan</label>
    <select class="form-control" name="jurusan"  v-model="posts.jurusan">
      <option>Teknik Informatika</option>
      <option>Teknik Kimia</option>
      <option>Teknik Industri</option>
      <option>Desain Komunikasi Visual</option>
      <option>Matematika</option>
    </select>
  </div>
   
  <button class="btn btn-info" type="submit" v-show="saveSubmit">Simpan</button>
</form>

<button @click="updateData" class="btn btn-warning text-light" type="submit" v-show="updateSubmit">Ubah</button>


     </b-col>

     <b-col >
        <b-spinner variant="primary" label="Spinning" v-if="spinnerMantap"></b-spinner>
        <!-- <b-button v-b-modal.modal-1 class="mb-4">Tambah Mahasiswa</b-button> -->
               <div class="contact-form-success alert alert-success mt-4" v-if="success">
                    <strong>Success!</strong> Your post is created.
                </div>

                <div class="contact-form-error alert alert-danger mt-4" v-if="error">
                    <strong>Error!</strong> There was an error sending your request.
                </div>

    <div class="table-wrapper-scroll-y my-custom-scrollbar">
      <table class="table table-hover table-bordered">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">NIM</th>
            <th scope="col">Nama Lengkap</th>
            <th scope="col">Jurusan</th>
            <th scope="col">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(mahasiswa, key) in mahasiswas" :key="key">
            <th scope="row">{{ incrementIndex(key) }}</th>
            <td>{{ mahasiswa.nim }}</td>
            <td>{{ mahasiswa.nama }}</td>
            <td>{{ mahasiswa.jurusan }}</td>
            <td>
              <button class="btn btn-info text-light mr-2 btn-sm" >Detail</button>
              <button class="btn btn-warning text-light mr-2 btn-sm" @click="edit(mahasiswa)">Ubah</button>
              <button class="btn btn-danger btn-sm" @click="hapus(mahasiswa)">Hapus</button>
            </td>
          </tr>
        </tbody>
      </table>


    <!-- Ini adalah modal -->
  <b-modal  id="modal-1" title="Mahasiswa Baru" centered>
  
  </b-modal>
    </div>
     </b-col>
   </b-row>
  </b-container>
</template> 

<script>
import axios from "axios";

export default {
  data() {
    return {
      mahasiswas: [],
       posts:{
                id_mahasiswa:null,
                nim:null,
                nama:null,
                jurusan:null
            },
         updateSubmit: false,
         saveSubmit: true,
         cancelSubmit: false,
         spinnerMantap: false,
         success: false,
         error: false
    };
  },
  async mounted() {
   this.load()
  },
  methods: {
    incrementIndex(key) {
        return key + 1;
    },
    hapus(){
      alert('hapus mantap')
    },
        edit(mahasiswa){
            this.updateSubmit = true
            this.saveSubmit = false
            this.posts.id_mahasiswa = mahasiswa.id_mahasiswa
            this.posts.nim = mahasiswa.nim
            this.posts.nama = mahasiswa.nama
            this.posts.jurusan = mahasiswa.jurusan
    },

    async load(){
       const response = await axios.get(`http://localhost:3000/mahasiswa`);
       this.mahasiswas = response.data.values;
    },

     postData()
        {
          this.spinnerMantap = true
            console.warn(this.posts)
             axios.post('http://localhost:3000/mahasiswa', this.posts)
            .then(function (response) {
              this.success = true
              console.log(response)
            })
            .catch(function (error) {
                this.error = true
                console.log(error)
            }).finally(()=>{
              this.spinnerMantap = false
              
              this.posts.nim = ""
              this.posts.nama = ""
              this.posts.jurusan = ""
              this.load()
            }) 

              
        },

          updateData(e){
             axios.put('http://localhost:3000/mahasiswa', this.posts)
                  .then(function (response) {
                    console.log(response.status)
                if (response.status == 200 || response.status == 201){
                  location.reload();
                    alert('sukses!')
                     
                }else{
                    alert("Gagal mengubah data!!")
                }
            })
            .catch(function (error) {
                console.log(error);
            }); 
              e.preventDefault();
              this.updateSubmit = false
              this.saveSubmit = true
              this.posts.id_mahasiswa = ""
              this.posts.nim = ""
              this.posts.nama = ""
              this.posts.jurusan = ""
        
             
    },

          
},
};
</script>

<style>
.my-custom-scrollbar {
position: relative;
height: 500px;
overflow: auto;
}
.table-wrapper-scroll-y {
display: block;
}
</style>