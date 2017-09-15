<template>

  <div id="app" class="container">
    <div class="page-header">
      <h1>SSENSE live Search</h1>
    </div>
    <!--- <div class="panel panel-default">
      <div class="panel-heading">
        <h3>Add Book</h3>
      </div>
      <div class="panel-body">
      <form id ="form" class="form-inline" v-on:submit.prevent="addBook">
        <div class ="form-group">
          <label for = "bookTitle">Title:</label>
          <input type="text" id = "bookTitle" class="form-control" v-model="newBook.title">
        </div>
        <div class ="form-group">
          <label for = "bookAuthor">Author:</label>
          <input type="text" id = "bookAuthor" class="form-control" v-model="newBook.author">
        </div>
        <div class ="form-group">
          <label for = "bookUrl">Url:</label>
          <input type="text" id = "bookUrl" class="form-control" v-model="newBook.url">
        </div>
        <input type="submit" class="btn btn-primary" value = "Add Book">
      </form>
      </div>
    </div> /__IMAGE_PARAMS__/-->
    <div class="panel panel-default">
      <div class="panel-heading">
      <h3> Products List</h3>
      </div>
      <input class ="form-control" placeholder="Search" v-model="filterInput"/>
      <br/>
      <div class="panel-body">
        <table class="table table-striped">
          <thead>
          <tr>

          </tr>
          </thead>
          <tbody>

          <tr>
            <div class="browsing-product-list">

              <figure v-for ="product in filterProduct" itemscope="itemscope" itemtype="http://schema.org/Product" class="browsing-product-item">
                <meta itemprop="sku" content="172751M237024">
                <meta itemprop="image" content="https://res-1.cloudinary.com/ssenseweb/image/upload/w_1024,dpr_2.0/f_auto/172751M237024_1.jpg">
                <meta itemprop="url" content="https://www.ssense.com/men/product/adidas-originals/beige-tubular-rise-sneakers/2085777">
                <a href="/en-ca/men/product/adidas-originals/beige-tubular-rise-sneakers/2085777" class="">
                  <div class="image-container">
                    <picture data-v-2737e8bc="">
                       <img  v-bind:src="product.images.replace('/__IMAGE_PARAMS__','')" v-bind:alt="product.brand_name" class="product-thumbnail lazyloaded"  width="200px" >
                    </picture>
                  </div>
                  <figcaption class="browsing-product-description text-center vspace1">
                    <p itemprop="brand" class="bold">{{product.brand_name}}</p>
                    <p itemprop="name" class="hidden-smartphone-landscape">{{product.name}}</p>
                    <span itemscope="itemscope" itemprop="offers" itemtype="http://schema.org/Offer">
                      <meta itemprop="price" content="200"><meta itemprop="priceCurrency" content="CAD">
                    </span></figcaption><!----></a>
                <div><p class="price">
                  <span class="price">{{product.regular}} {{product.currency}} {{product.full_format}} </span>
                  <!----></p>
                </div>
              </figure>

            </div>



          </tr>
          </tbody>
        </table>
      </div>
    </div>

  </div>
</template>

<script>

export default {
  name: 'products',

    data() {
      return{
          newProduct:[],
          DtoB:0,
          products:[],
          meta:[],
          filterInput:'',
          q:'',
          currentPage:1,
          show   : false, // display content after API request
          offset : 5,     // items to display after scroll
          display: 5,     // initial items
          trigger: 300,   // how far from the bottom to trigger infinite scroll// server response data
          end    : false,
          pollingForData :false

      }
    },
    computed: {

        // slice the array of data to display
        sliced() {
            return this.items.slice(0, this.display);
        },
        filterProduct:function () {
            if(this.q!=this.filterInput){
                window.setTimeout(() => {




                    //if(this.q!=this.filterInput){
                    axios.get("http://ad81a1eeb671711e7b0b106999280352-976315296.us-west-2.elb.amazonaws.com/search?gender=men&country=ca&language=en&q="+this.filterInput+"page="+this.currentPage)
                        .then(response => {


                              this.q=this.filterInput;
//                            for (var i=0; i<response.data.products.length; i++) {
//
//                                for (var j=0; j<this.products.length; j++) {
//                                         if (response.data.products.id!=this.products.id){
//                                             this.products.push(response.data.products[i]);
//                                         }
//
//                                        }
//
//                            }


                            // Merges both arrays and gets unique items
                            this.products= this.products.concat(response.data.products);
                            this.meta= this.meta.concat(response.data.meta);
//                            this.products =  this.newProduct;





                            //this.products = response.data.products;
                            //this.meta = response.data.meta;

                            return this.products.filter((product)=>{
                                return product.name.toLowerCase().match(this.filterInput),  product.brand_name.toLowerCase().match(this.filterInput)
                                this.show = true;
                            })
                        })

                }, 100);


            }else{
                return this.products.filter((product)=>{
                    return product.name.toLowerCase().match(this.filterInput),  product.brand_name.toLowerCase().match(this.filterInput)
                    this.show = true;
                })
            }




        }
    },
    methods: {
        handleScroll: function (event) {
            this.scroll();
        },
        scroll() {

            console.log(' scroll out '+this.loaedPage+' '+this.currentPage);
            var self =this;

            var win = $(window);
            var doc = $(document);
            console.log(' DocHeight= '+doc.height() +' -(win Inner Height  ='+win.innerHeight()+' window page YOffset= '+window.pageYOffset)+' )';
            var DtB=doc.height()-(win.innerHeight()+window.pageYOffset);
            this.DtoB=DtB;
            console.log('BToB= ' +this.DtoB);
            if(this.DtoB<200){
                this.fetch();
            }




        },
        fetch() {
            window.setTimeout(() => {
                axios.get("http://ad81a1eeb671711e7b0b106999280352-976315296.us-west-2.elb.amazonaws.com/search?country=ca&language=en&page="+this.currentPage)
                    .then(response => {
                        console.log('receivedData');
                        //this.products = response.data.products;
                        this.products= this.products.concat(response.data.products);
                        this.pollingForData=false;
                    });
                this.show = true;
            });
            this.currentPage++;
        }

    },
    mounted() {
        // track scroll event
        window.onscroll=this.scroll();
    },
    created() {
        // get the data by performing API request
        window.addEventListener('scroll', this.handleScroll);
        this.fetch();
    }

}



</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
