<template>
  <div class="app">
    <div class="container">
      <div class="input-group mb-3">
        <input
          type="text"
          class="form-control"
          placeholder="Search your books here..."
          id="bookName"
          aria-label="Search your books here..."
          aria-describedby="basic-addon2"
        />
        <div class="input-group-append">
          <button
            class="btn btn-outline-secondary"
            @click="search()"
            type="button"
          >
            <i class="fa fa-search"></i>
          </button>
        </div>
      </div>
      <div class="card mt-5">
        <h4>Result</h4>
        <table class="table">
          <thead class="thead-light">
            <th>Number</th>
            <th>Icon</th>
            <th>Title</th>
            <th>Author</th>
            <th>Details</th>
          </thead>
          <tbody>
            <tr v-for="(items, index) in data" :key="index" v-cloak class="table_tr">
              <td>{{ index + 1 }}</td>
              <td v-if="items.volumeInfo.imageLinks.smallThumbnail == null">
                None
              </td>
              <td v-else>
                <img
                  :src="items.volumeInfo.imageLinks.smallThumbnail"
                  alt="noImage"
                />
              </td>
              <td>{{ items.volumeInfo.title }}</td>
              <td v-if="items.volumeInfo.authors != null">
                <p v-for="name in items.volumeInfo.authors" :key="name">
                  {{ name }}
                </p>
                <br />
              </td>
              <td v-else>
                <p>None</p>
              </td>
              <td>
                <button
                  type="button"
                  class="btn btn-primary"
                  data-toggle="modal"
                  data-target="#exampleModal"
                  @click="view(index)"
                >
                  View
                </button>

                <!-- Modal -->
                <div
                  class="modal fade"
                  id="exampleModal"
                  tabindex="-1"
                  role="dialog"
                  aria-labelledby="exampleModalLabel"
                  aria-hidden="true"
                >
                  <div class="modal-dialog" role="document">
                    <div class="modal-content">
                      <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">
                          {{ book_name }}
                        </h5>
                        <button
                          type="button"
                          class="close"
                          data-dismiss="modal"
                          aria-label="Close"
                        >
                          <span aria-hidden="true">&times;</span>
                        </button>
                      </div>
                      <div class="modal-body">
                        <table class="table">
                          <div class="row">
                            <tbody>
                              <tr>
                                <div class="col-6">
                                  <td>
                                    <img :src="book_icon" alt="noImage" />
                                  </td>
                                </div>
                                <td>
                                  
                                </td>
                                <td v-if="pdf_link != ''">
                                  <a :href="pdf_link"><i class="fas fa-file-pdf"></i>PDF download</a>                                   
                                </td>
                                <td v-if="epub_link !=''">
                                  <a :href="epub_link"><i class="fas fa-file-epub"></i>EPUB download</a> 
                                </td>
                                <td v-else>
                                  kechirasiz yuklab olish imkonyati mavjud emas
                                </td>
                              </tr>
                            </tbody>
                          </div>
                        </table>
                      </div>
                      <div class="modal-footer">
                        <button
                          type="button"
                          class="btn btn-secondary"
                          data-dismiss="modal"
                        >
                          Close
                        </button>
                        <button type="button" class="btn btn-primary">
                          Save changes
                        </button>
                      </div>
                    </div>
                  </div>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "app",
  props: {},
  data() {
    return {
      data: [],
      book_icon: "",
      book_name: "",
      published_date: null,
      searchInfo: "",
      epub_link: '',
      pdf_link: '',
    };
  },
  created() {},
  mounted() {},
  methods: {
    search: function () {
      let val = document.getElementById("bookName").value;
      axios
        .get("https://www.googleapis.com/books/v1/volumes?q=" + val)
        // https://www.pdfdrive.com/search?q=python&pagecount=&pubyear=&searchin=&em=
        // .get('http://localhost:8080/search?q='+val+'&pagecount=&pubyear=&searchin=&em=')
        .then((response) => {
          // (this.info = response)
          this.data = response.data.items;
          console.log(response.data.items);
        });
    },
    view: function (index) {
      this.book_name = this.data[index].volumeInfo.title;
      this.book_icon = this.data[index].volumeInfo.imageLinks.smallThumbnail;
      this.searchInfo = this.data[index].searchInfo.textSnippet;

      if(this.data[index].accessInfo.epub.isAvailable){
        this.epub_link = this.data[index].accessInfo.epub.downloadLink
        if(this.epub_link ==''){
          this.epub_link = this.data[index].accessInfo.epub.acsTokenLink
        }
      }
      if(this.data[index].accessInfo.pdf.isAvailable){
        this.pdf_link = this.data[index].accessInfo.pdf.downloadLink
        this.pdf_link = this.data[index].accessInfo.pdf.acsTokenLink
         console.log(this.pdf_link)
         if(this.pdf_link == ''){ 
           console.log('pustoy pdf link')
          this.pdf_link = this.data[index].accessInfo.pdf.acsTokenLink
        }
          console.log(this.pdf_link)
      }
      console.log(this.data[index].volumeInfo.title);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
[v-cloak] {
  display: none;
}
.table_tr:hover {
  background-color: deepskyblue;
  /* cursor: pointer; */
}
.modal-content {
  text-align: center;
}
</style>
