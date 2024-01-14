<template>
  <el-row class="nav">
    <el-col :span="12">
      <img
          style="width: 50px;border-radius: 10px"
          src="../../public/logo.jpg"
          alt="GitPic"
      />
      <span style="font-size: 50px;color: rgba(255, 220, 52);font-weight: bolder">G</span>
      <span style="font-size: 50px;color: rgba(72, 201, 255);font-weight: bolder">it</span>
      <span style="font-size: 50px;color: rgba(141, 114, 255);font-weight: bolder">P</span>
      <span style="font-size: 50px;color: rgba(189, 52, 254);font-weight: bolder">ic</span>
    </el-col>
    <el-col :span="12" style="text-align: right">
      <a href="https://github.com/rimdl/GitPic/" target="_blank" style="text-decoration: none;color: black;"><el-button style="height: 100%;background-image: url('../../public/github.png');background-size: cover;border: none;border-radius: 50px" >github</el-button></a>
    </el-col>
  </el-row>
  <el-row class="mg">
    <el-col :span="24">
      <el-alert title="提示：以下信息均会保存在浏览器本地，所以请务必提前备份，因GitHub获取的token只会显示一次，丢失后将无法恢复。" type="info" />
    </el-col>
    <el-col :span="24">
      <br>
    </el-col>

    <el-col :span="5" :offset="1">
      <div class="glass config">
        <img src="../../public/github.svg" alt="" style="width: 20px">
        <label style="margin-left: 1vw">仓库</label>
        <br>
        <el-input v-model="input_repo" placeholder="例如：user/example" style="width: 100%;"/>
        <br>
        <br>
        <img src="../../public/key.svg" alt="" style="width: 20px">
        <label style="margin-left: 1vw">Token </label>
        <br><span style="font-size: smaller;color: orangered">(请妥善保管，丢失后无法恢复。)</span>
        <br>
        <el-input v-model="input_token" placeholder="请从你的github设置中获取" style="width: 100%;"/>
        <br>
        <br>
        <img src="../../public/cdn.svg" alt="" style="width: 20px">
        <label style="margin-left: 1vw">cdn</label>
        <br>
        <el-input v-model="input_cdn" placeholder="用于加速的CDN" style="width: 100%;"/>
        <br>
        <br>
        <el-row>
          <el-col :span="11">
            <el-button class="btn" type="default" @click="clear_config">清空</el-button>
          </el-col>
          <el-col :span="11" :offset="1">
            <el-button class="btn" type="default" @click="save_config">保存</el-button>
          </el-col>

        </el-row>
      </div>
    </el-col>
    <el-col :span="17" :offset="1">
      <el-upload
          class="glass"
          drag
          list-type="picture"
          :show-file-list="false"
          :http-request="handleUpload"
          accept="image/*"
      >
        <el-icon class="el-icon--upload"><upload-filled /></el-icon>
        <div class="el-upload__text">
          拖动文件到此处 或者 <em>点击上传</em>
        </div>
        <template #tip>
          <div class="el-upload__tip">
            注意：上传的文件不宜过大，上传成功的图片不能立刻就在下方看到，请等待10s以上的时间后点击刷新按钮。
            <br>
          </div>
          <el-progress v-if="show_progress" :percentage="50" :indeterminate="true" :format="format"/>
          <el-input placeholder="获取失败了哦！" v-model="d_url" type="text" id="durl" v-if="d_url !== ''">
            <template #append><el-button @click="copy_url('durl')" class="copy_btn" type="primary">复制</el-button></template>
          </el-input>
        </template>
      </el-upload>
    </el-col>
  </el-row>

  <el-row class="mg" style="padding: 10px" v-if="repo !== null && token !== null">
    <el-col :span="6">
      <el-row>
        <el-col :span="22" :offset="1" style="text-align: center;padding: 10px" class="glass">
          <a :href="'https://github.com/'+repo" target="_blank"><el-avatar :size="50" :src="avatar_url" /></a>
          <br>
          <span style="font-size: smaller;color: gray">{{name}}</span>
          <br>
          <span style="font-size: smaller;color: gray">{{email}}</span>
          <br>
          <div v-if="total_size !== ''">
            <span style="font-size: small;font-weight:bold;color: gray">当前仓库占用空间：<el-tag effect="dark" round>{{total_size}}</el-tag></span>
            <br>
            <span style="font-size: small;font-weight:bold;color: gray">当前仓库文件数：<el-tag effect="dark" round>{{file_list.length}}</el-tag></span>
            <br>
            <br>
          </div>

          <el-row>
            <el-col :span="24">
              <el-button @click="listFile" size="small" class="btn1">获取当前仓库文件</el-button>
            </el-col>
            <el-col :span="24">
              <el-button @click="get_user_info" size="small" class="btn1">重新获取用户信息</el-button>
            </el-col>
          </el-row>
        </el-col>
      </el-row>
    </el-col>
    <el-col :span="18" class="glass" style="padding: 10px" v-if="file_list.length>0">
      <el-row>
        <el-col :span="24">
          <el-tag>
          <img src="../../public/tiger.svg" style="width: 20px" alt="">
          <label>选择图片填充方式</label>
          </el-tag>
          <el-button class="copy_btn" @click="listFile" size="small" style="margin-left: 1vw;border-radius: 20px">
            <el-icon class="is-loading"><Loading /></el-icon>
            刷新
          </el-button>
          <br><br>
          <el-button @click="fit = 'fill'">fill</el-button>
          <el-button @click="fit = 'contain'">contain</el-button>
          <el-button @click="fit = 'cover'">cover</el-button>
          <el-button @click="fit = 'none'">none</el-button>
          <el-button @click="fit = 'scale-down'">scale-down</el-button>

          <el-button @click="get_user_info">get</el-button>
        </el-col>
      </el-row>
      <el-row class="mg">
        <el-col v-for="(item,index) in file_show"  :span="4" style="padding: 10px">
          <el-image :preview-teleported="true" style="width: 100%;height: 20vh" :src="item.url" :fit="fit" loading="lazy" :preview-src-list="srcList" :initial-index="index" :key="item.sha"/>
          <span style="font-size: smaller;color: gray">文件大小：{{(item.size/1024).toFixed(2)}}KB</span>
          <p style="color: gray;font-size: smaller;white-space: nowrap;width: 90%;overflow: hidden;text-overflow:ellipsis;">文件名：{{item.name}}</p>
          <input type="text" v-model="item.url" :id="index" style="display: none">
          <el-row>
            <el-col :span="12">
              <el-button @click="copy_url(index)" class="btn" size="small">复制链接</el-button>
            </el-col>
            <el-col :span="11" :offset="1">
              <el-popconfirm
                  width="120"
                  confirm-button-text="确认"
                  cancel-button-text="取消"
                  icon-color="#626AEF"
                  title="确定删除?"
                  @confirm="delete_file(item.sha,item.filename)"
              >
                <template #reference>
              <el-button type="default" size="small" style="border-radius: 10px"><img src="../../public/delete.svg" style="width: 15px" alt=""></el-button>
                </template>
              </el-popconfirm>
            </el-col>
          </el-row>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24" style="text-align: center">
          <el-pagination background page-size="12" :hide-on-single-page="true" v-model:current-page="current_page" default-page-size="12" layout="prev, pager, next" :page-count="page_count"/>
        </el-col>
      </el-row>
    </el-col>
  </el-row>
</template>

<script setup>
import {onBeforeMount, onBeforeUpdate, ref,watch} from 'vue';
import { UploadFilled } from '@element-plus/icons-vue'
import { ElNotification } from 'element-plus'

const current_page = ref(1)
const file_suffix = ref('');
const repo = ref('')
const token = ref('')
const d_url = ref('')

const input_repo = ref('')
const input_token = ref('')
const input_cdn = ref('')

const show_progress = ref(false)

const file_list = ref([])
const srcList = ref([])
const fit = ref('cover')

const avatar_url = ref('')
const name = ref('')
const email = ref('')
const total_size = ref("")

const file_show = ref([])

const page_count = ref(1)

const clear_config = () =>{
  localStorage.removeItem("repo")
  localStorage.removeItem("token")
  localStorage.removeItem("email")
  localStorage.removeItem("avatar")
  localStorage.removeItem("name")
  localStorage.removeItem("cdn")
  repo.value = null;
  token.value = null;
  input_repo.value = "";
  input_token.value = "";
  input_cdn.value = "";
  avatar_url.value = "";
  name.value = "";
  email.value = "";
}
const format = () => ("上传中...")
const handleUpload = (content) => {
  console.log(content.file);
  console.log(content.file.name)
  file_suffix.value = content.file.name.substr(content.file.name.lastIndexOf("."));
  const reader = new FileReader();
  reader.readAsDataURL(content.file);    // 解析成base64格式
  reader.onload = function () {
    upload(this.result);
    // console.log(this.result)
  }
}

const save_config = () =>{
  if (input_repo.value !==null && input_token.value !== null){
    localStorage.setItem("repo",input_repo.value);
    localStorage.setItem("token",input_token.value);
    localStorage.setItem("cdn",input_cdn.value);
    repo.value = input_repo.value;
    token.value = input_token.value;
    get_user_info()
    listFile()
  }
  else {
    open_notification("warning","请输入仓库地址和token")
  }
}

 onBeforeMount(() => {
   input_repo.value = localStorage.getItem("repo");
   input_token.value = localStorage.getItem("token");
   input_cdn.value = localStorage.getItem("cdn");
   repo.value = input_repo.value;
   token.value = input_token.value;
   avatar_url.value = localStorage.getItem("avatar")
   name.value = localStorage.getItem("name")
   email.value = localStorage.getItem("email")
   listFile()
 })

onBeforeUpdate(() => {

})

const upload = (content) => {
  content = content.split(',')[1]
  const UUID = crypto.randomUUID();
  const path = `${UUID}${file_suffix.value}`
  const imageUrl = 'https://api.github.com/repos/' + repo.value + '/contents/' + path
  const body_data = { branch: 'main', message: 'upload', content, path }
  const body = JSON.stringify(body_data);
  show_progress.value = true
  fetch(imageUrl, {
    method: 'PUT',
    headers: {'Accept':'application/vnd.github+json','Authorization':'Bearer '+token.value,'X-GitHub-Api-Version': '2022-11-28'},
    body: body
  }).then(response => response.json()) // 如果服务器返回的是JSON数据，则进行解析
      .then(data => {
        console.log('PUT request succeeded with:', data)
        let download_url = data.content.download_url;
        if (input_cdn.value !==""){
          d_url.value = input_cdn.value + input_repo.value + download_url.substring(download_url.indexOf("/main"));
        }
        else {
          d_url.value = download_url
        }
        show_progress.value = false
        open_notification("上传图片","上传成功！")
      })
      .catch(error => {
        console.error('Error:', error)
        show_progress.value = false
      });
}

const listFile = () =>{
  file_list.value = []
  const url = 'https://api.github.com/repos/' + repo.value + '/contents/'
  fetch(url, {
    method: 'GET',
    headers: {'X-GitHub-Api-Version': '2022-11-28'},
  }).then(response => response.json()) // 如果服务器返回的是JSON数据，则进行解析
      .then(data => {
        console.log('Get request succeeded with:', data)
        let all_size = 0
        for (let i = 0; i < data.length; i++) {
          if (input_cdn !== ""){
            data[i].download_url = data[i].download_url.substring(data[i].download_url.indexOf("/main"))
            file_list.value.push({"url":input_cdn.value + input_repo.value + data[i].download_url,"size":data[i].size,"name":data[i].name,"sha":data[i].sha,"filename":data[i].name})
            srcList.value.push(input_cdn.value + input_repo.value + data[i].download_url)
            all_size =  all_size + data[i].size
          }
        }
        if (all_size < 1024){
          total_size.value = all_size+"B"
        }
        else if(all_size >= 1024 && all_size < 1024*1024){
          total_size.value = (all_size/1024).toFixed(2)+"KB"
        }
        else if (all_size >= 1024*1024 && all_size < 1024*1024*1024){
          total_size.value = (all_size/(1024*1024)).toFixed(2)+"MB"
        }
        else {
          total_size.value = (all_size/(1024*1024*1024)).toFixed(2)+"GB"
        }
        console.log("size:"+total_size.value)
        if (file_list.value.length <= 12){
          file_show.value = file_list.value
          page_count.value = 1
        }
        else {
          file_show.value = file_list.value.slice(0, 12)
          page_count.value = Math.ceil((file_list.value.length)/12)
        }
      })
      .catch(error => {
        console.error('Error:', error)
      });
}

const copy_url = (index) => {
  if (index !=="durl"){
    let obj = document.getElementById(index);
    obj.select();
    open_notification("复制url","已复制到剪贴板:"+obj.value)

    document.execCommand("copy");
  }
  else {
    if (d_url.value === "") {
      open_notification("复制url","请先上传文件")
      return
    }
    open_notification("复制url","已复制到剪贴板:"+d_url.value)
    document.getElementById("durl").select();
    document.execCommand("copy");
  }
}

const delete_file = (sha,filename) => {
  console.log(sha)
  const url = 'https://api.github.com/repos/' + repo.value + '/contents/'+filename
  let body = {message: "delete file",committer: {name: name.value,email: email.value},sha: sha}
  console.log(body)
  fetch(url, {
    method: 'DELETE',
    body: JSON.stringify(body),
    headers: {'Accept':'application/vnd.github+json','Authorization':'Bearer '+token.value,'X-GitHub-Api-Version': '2022-11-28'},
  }).then(response => response.json()) // 如果服务器返回的是JSON数据，则进行解析
      .then(data => {
        console.log('Delete request succeeded with:', data)
        let f_obj = file_list.value.find(obj => obj.sha === sha)
        let obj_index = file_list.value.indexOf(f_obj)
        console.log(file_list)
        if(obj_index !== -1){
          file_list.value.splice(obj_index,1)
        }
        console.log(file_list)
        open_notification("删除文件","删除成功")
      })
      .catch(error => {
        console.error('Error:', error)
      });
}

const open_notification = (title,message) => {
  ElNotification({
    title: title,
    message: message,
    position: 'bottom-right',
  })
}

const get_user_info = () =>{
  const url = 'https://api.github.com/user'
  fetch(url, {
    method: 'GET',
    headers: {'Accept':'application/vnd.github+json','Authorization':'Bearer '+token.value,'X-GitHub-Api-Version': '2022-11-28'},
  }).then(response => response.json()) // 如果服务器返回的是JSON数据，则进行解析
      .then(data => {
        console.log('GET request succeeded with:', data)
        localStorage.setItem("name",data.name)
        localStorage.setItem("email",data.email)
        localStorage.setItem("avatar",data.avatar_url)
        avatar_url.value = localStorage.getItem("avatar")
        name.value = localStorage.getItem("name")
        email.value = localStorage.getItem("email")
      })
      .catch(error => {
        console.error('Error:', error)
      });
}

watch(current_page,
  (newVal, oldVal) => {
    if(newVal !== oldVal){
      file_show.value = []
      file_show.value = file_list.value.slice((newVal-1)*12,newVal*12)
    }
  }
)
</script>

<style>
.flex-grow {
  flex-grow: 1;
}
.mg{
  margin-top: 1vh;
}
.glass{
  background-color: rgba(255, 255, 255, 0.5);
  backdrop-filter: blur(30px);
  -webkit-backdrop-filter: blur(30px);
  border: 1px solid rgba(255, 255, 255, 0.18);
  box-shadow: rgba(142, 142, 142, 0.19) 0px 6px 15px 0px;
  -webkit-box-shadow: rgba(142, 142, 142, 0.19) 0px 6px 15px 0px;
  border-radius: 12px;
  -webkit-border-radius: 12px;
}
.config{
  padding: 10px;
}

@keyframes gradientBG {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

.nav{
  background: linear-gradient(0deg, rgba(192,242,255,1) 0%, rgba(190,255,217,1) 100%);
  height: 100%;
  animation: gradientBG 15s ease infinite;
  background-size: 300% 300%;
  border-radius: 10px;
  font-weight: bold;
  padding: 5px;
}
.btn{
  width: 100%;
  border-radius: 10px;
  background: linear-gradient(90deg, rgba(241,255,171,1) 0%, rgba(130,255,249,1) 50%, rgba(204,193,255,1) 100%);
  animation: gradientBG 15s ease infinite;
  background-size: 300% 300%;
  font-weight: bolder;
}
.btn1{
  width: 100%;
  border-radius: 10px;
  background: linear-gradient(90deg, rgba(157,135,255,1) 0%, rgba(48,255,245,1) 50%, rgba(198,255,70,1) 100%);
  animation: gradientBG 15s ease infinite;
  background-size: 300% 300%;
  font-weight: bolder;
}
.copy_btn{
  background: linear-gradient(90deg, rgba(241,255,171,1) 0%, rgba(130,255,249,1) 50%, rgba(204,193,255,1) 100%);
}
.copy_btn:hover{
  scale: 1.05;
}
</style>
