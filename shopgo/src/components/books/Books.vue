<template>
  <div class="wrap">
    <div v-if="len>=1" class="gouwukong">
      <header class="header">
        <img src="" alt="">
        <span>购物车居然是空的</span>
        <a href="/">去首页逛逛</a>
      </header>
      <main class="main">
        <h3>大家都在买的宝贝</h3>
        <!-- 纵向 图文混排  -->
        <tuwen-navtu :tuwen_arrtu2="arr" ></tuwen-navtu>
      </main>
    </div>

    <div v-else class="guowu">
      <div>
        <header class="header">
          <input type="checkbox">
          <i></i>
          <span>{{arr.MallName}}</span>
        </header>
        <main class="gou_main">
          <dl v-for=" m in arr.Products">
            <dt :style="{backgroundImage:'url('+m.Pictures+')'}"></dt>
            <dd>
                <h3><span>{{m.Subject}}</span><span ref="shan" @click="shan($event,m.CartID)">删除</span></h3>
                <p><span>￥ {{m.Price}}</span><span> x {{m.Quantity}}</span></p>
            </dd>
          </dl>
        </main>
        <footer class="footer">
          <aside><input type="checkbox">全选</aside>
          <aside>
            <aside><span>合计：￥0</span><span>以优惠 0 元</span></aside>
            <aside><button>结算</button></aside>
          </aside>
        </footer>
      </div>
    </div>

    <footer-nav v-bind:footer_nav="footer_nav"></footer-nav>
  </div>
</template>

<script>
/*图文混排  纵列nav标题  */
import tuwenNavtu from '../base/tuwen_navtu'
import {alertbox} from '../../lib/alert' //注意路径
/*nav 页脚*/
import footerNav from '../base/Footer'
import { mapState } from 'vuex'

export default {
  name: 'hello',
  props:['footer_nav'],
  components: {
    footerNav,
    tuwenNavtu
  },
  data () {
    return {
      arr: [],
      len:0
    }
  },
  mounted () {
    this.$nextTick(function (){
      /*nav 数据请求 */
      this.getData()   
    })
  },
  methods: {
    getData (){
      let that=this;
      this.$http.jsonp('http://m.haimi.com/api/cart/info?clearInvalidProducts=Y&platform=WAP',{
        jsonp:"_callback"
      }).then(function(data){
          //赋值给alllist数组,
         
         this.arr=data.body.extra.Suggest.length>=1?data.body.extra.Suggest:data.data.data[0];
          this.len=data.body.extra.Suggest.length
          //console.log(this.arr.MallName)
      })
    },
    /*  删除选项 */
    shan (eve,CartIDs) {    
      var that=this;
      alertbox()
      .Alerts("确定要删除吗？",
        {
          btns:["取消",'确定'],
          yes : function(){
            alertbox().cancelBox()

           /* alertbox().succeedBox("三秒钟后，将要跳转页面，进行支付操作",{
                title:"跳转支付宝"
            },function(data){
              if(data=="true"){
                setTimeout(function(){
                    alertbox().cancelBox()
                
                 location.href="controller/tijiao/tijiao.html"
                },2000)

              }
            })*/
          },
          no : function(){
            alertbox().cancelBox()

            alertbox().cancelAlert("您确定不够买，这么便宜的商品吗",{
              title:"没钱的渣渣",
              btns:["确定",'取消'],
              yes : function(){
                /*取消遮层*/
                  alertbox().cancelBox()


              that.$http.jsonp('http://www.haimi.com/api/cart/delete?CartID='+CartIDs+'&platform=WAP',{
                jsonp:"_callback"
              }).then(function(data){

                  //赋值给alllist数组,
                 console.log(CartIDs)
                 
                  console.log(data)
              })



                  /*console.log(that.$refs.shan)
                 var main=eve.target.parentNode.parentNode.parentNode.parentNode
                 var fu=eve.target.parentNode.parentNode.parentNode

                 main.removeChild(fu)*/
              },
              no : function(){
                alertbox().cancelBox()
              }
            })
          }

      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.wrap{
  .gouwukong{
    .header{
    display: -webkit-box;
    -webkit-box-orient:vertical;
    -webkit-box-align:center;
    -webkit-box-pack:center;
    background: #eee;
    padding:10rem 0 6rem;
    img,a,span{
      display: block;
    }
    a{
      border:1px solid #000;
      padding:0.6rem 1.4rem;
      color:#000;
    }
    span{
      line-height: 4.2rem;
      color:#666;
    }
  }
  .main{
    &>h3{
      height:4rem;
      line-height: 4rem;
      text-align: center;
      border-bottom:1px solid #eee;
    }
  }
  }
}
.guowu{
  position:fixed;
  height:100%;
  width:100%;
  &>div{
    position:relative;
    height:100%;
    width:100%;
    .header{
      display:-webkit-box;
      padding:0 1.5rem;
      box-sizing: border-box;
      -webkit-box-align:center;
      border-bottom:1px solid #eee;
      height:4.4rem;
      i,span{
        display: block;
        color:#333;
      }
      i{
        color:#999;
        &:first-child{

        };
      }
    }
    .gou_main{
      margin:2rem 1.5rem;
      dl{
        display: -webkit-box;
        padding-bottom: 1rem;
        margin-bottom:1.5rem;
        border-bottom:1px solid #eee;
        dt{
          width:5rem;
          height:5rem;
          border:1px solid #eee;
          margin-right:1.5rem;
          background-size:cover;
          background-position: center center;
        }

        dd{
          -webkit-box-flex:1;
          display: -webkit-box;
          -webkit-box-orient:vertical;
          -webkit-box-pack:justify;
          height:5rem;
          h3{
            display: -webkit-box;
            span{
              display:block;
              &:first-child{
                width:80%;
                overflow:hidden;
                white-space: nowrap;
                text-overflow: ellipsis;
              }

              &:last-child{
                color:#999;
                margin-left:1.4rem;
              };
            }
          }

          p{
            display: -webkit-box;
            -webkit-box-pack:justify;
            width:100%;
            span{
              display: block;
              &:last-child{

            color:#999;
              };
            }
          }
        }
      }
    }

    .footer{
      position: absolute;
      bottom:5rem;
      width:100%;
      display: -webkit-box;
      -webkit-box-pack:justify;
      border-top:1px solid #eee;
      height:4.4rem;
      aside{
        display: -webkit-box;
        -webkit-box-align:center;
        i,span{
          display: block;
        }

        &:first-child{
          padding:0 1.5rem;
          box-sizing: border-box;
        };

        &:last-child{
          height:100%;
          aside{
            height:100%;
            display: -webkit-box;
            -webkit-box-orient:vertical;
            button,span{
              display: block;
            }
            button{
              border:none;
              width:6.5rem;
              height:100%;
              color:#fff;
              background:#991f33;
            }
            &:first-child{
              padding:0.4rem;
              span{
                line-height: 2rem;
                &:first-child{
                  color:#991f33;
                  font-size:1.4rem;
                };

                &:last-child{
                  color:#999;
                };
              }
            };
          }
          
        };
      }
    }
  }
}

</style>
