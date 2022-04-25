<template>
  <div class="blog-item">
    <div class="bg-img" :style="{backgroundImage: 'url('+blog.firstPicture+')'}">
      <div class="home-title">
        <h2>
          <router-link :to="{name: 'Blog', params: {blogId: blog.id}}" class="blog-link">{{ blog.title }}
          </router-link>
        </h2>
        <span style="font-size: small;color: blue">创建时间：{{ blog.createTime.split(' ')[0] }}  </span>
        <span style="font-size: small;color: blue">  更新时间：{{ blog.updateTime.split(' ')[0] }}   </span>
        <span style="font-size: small;color: blue">  分类：{{ blog.typeName }}</span>
        <span style="font-size: small;color: blue">  字数：{{ blog.words }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "BlogItem",
  data() {
    return {
      // blogs: [],
      types: [],
      currentPage: 1,
      total: 0,
      pageSize: 5,
      pageShow: 0,
      url: undefined,
    }
  },
  props: {
    blog: {
      id: String,
      createTime: Date,
      updateTime: Date,
      typeName: String,
      words: String,
    }
  },

  methods: {
    //跳转到博客详情页
    toBlog(blog) {
      this.$router.push(`/blog/${blog.id}`)
    },
    //获取分类表
    getTypes() {
      const _this = this
      this.$axios.get('/types').then(res => {
        _this.types = res.data.data
      })
      //console.log(this.types)
    },
    //分页获取博客
    getData(currentPage) {
      if (currentPage !== 1) {
        $('#waypoint').hide();
        $('#header-photo').hide();
      } else {
        $('#waypoint').show();
        $('#header-photo').show();
      }
      const _this = this
      this.$axios.get('/blogs?currentPage=' + currentPage).then((res) => {

        _this.blogs = res.data.data.records
        _this.currentPage = res.data.data.current
        _this.total = res.data.data.total
        _this.pageSize = res.data.data.size
        _this.pageShow = 1

        for (var i in _this.blogs) {
          for (var j in _this.types) {
            if (_this.blogs[i].typeId == _this.types[j].id) {
              _this.blogs[i].typeName = _this.types[j].typeName
            }
          }

        }
      })

      //改变页号后返回顶部
      this.scrollToTop()

    }
  },
  created() {
    this.getTypes()
    this.getData(1);
    //console.log(this.blogs)
  },

  mounted() {

  },
  watch: {},
  computed: {
    backImage() {
      return {
        "--url": this.blog.firstPicture,
      }
    }
  }

}
</script>

<style scoped>
.blog-item {
  width: 100%;
  height: 300px;
  margin: 20px 0 20px;
  --border: dashed;
  border: var(--border);

}

.bg-img {
  height: 100%;
  /*background-color: rgb(255, 208, 0);*/
  /*background-size: 100% 100%;*/
  background-repeat: no-repeat;
  /*position: absolute;*/
  background-size: 100% 100%;
  /*  background-repeat: no-repeat;*/
}

/*.home-description-markdown-body {*/
/*  text-align: left;*/
/*}*/
home-description-markdown-body {
  text-align: left !important;
}

.image {
  width: 100%;
  height: auto;
}

.home-title {
  text-align: center;
}

.home-title {
  margin-bottom: 20px;
}

.blog-link:link, .blog-link:visited {
  text-decoration: none;
  color: black;

}

</style>
