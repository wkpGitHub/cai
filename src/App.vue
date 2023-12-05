<script lang="jsx">
import {reactive, computed, nextTick} from 'vue'
import {Search, CaretRight, CircleClose} from '@element-plus/icons-vue'
export default {
  setup() {
    fetch('./data.json').then(res => {
      return res.json()
    }).then(data => {
      state.list = data
    })
    const state = reactive({
      list: [],
      keyword: '',
      current: {
        isShow: false
      }
    })
    const filterList = computed(() => {
      const _list = state.list.filter(item => {
        if (state.keyword.trim() === '') return state.list
        return item.title.indexOf(state.keyword.trim()) > -1
      })
      return _list.slice(0, 20)
    })

    function selectItem(item) {
      state.current = {
        isShow: true,
        url: item.url
      }
    }

    function loadVideo(video) {
      // nextTick(() => {
      //   video?.webkitEnterFullscreen()
      // })
    }

    return () => <div class="container">
      <ElInput v-model={state.keyword} placeholder="请输入名称查询" suffix-icon={Search}/>
      <ul class="video-list">
        {filterList.value.map(item => <li>
          <div class="video-box" onClick={() => selectItem(item)}>
            <el-icon><CaretRight /></el-icon>
            {item.url.endsWith('.mp4') ? <video src={item.url}></video>
            :<img src={item.url} />}
          </div>
          <p>{item.title}</p>  
        </li>)}
      </ul>
      {state.current.isShow && <div class="fullscreen">
        <el-icon onClick={() => state.current.isShow = false}><CircleClose /></el-icon>
        {state.current.url.endsWith('.mp4') ? <video ref={loadVideo} src={state.current.url} controls autoplay></video> : <img src={state.current.url} />}
      </div>}
    </div>
  }
}
</script>
