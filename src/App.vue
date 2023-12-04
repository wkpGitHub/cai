<script lang="jsx">
import {reactive, computed, nextTick} from 'vue'
import {Search, CaretRight, CircleClose} from '@element-plus/icons-vue'
import {list} from './data'
export default {
  setup() {
    const state = reactive({
      list,
      keyword: '',
      current: {
        isShow: false
      }
    })
    const filterList = computed(() => {
      return state.list.filter(item => {
        if (state.keyword.trim() === '') return state.list
        return item.title.indexOf(state.keyword.trim()) > -1
      })
    })

    function selectItem(item) {
      state.current = {
        isShow: true,
        url: item.url
      }
    }

    function loadVideo(video) {
      nextTick(() => {
        video?.webkitEnterFullscreen()
      })
    }

    return () => <div class="container">
      <ElInput v-model={state.keyword} placeholder="请输入名称查询" suffix-icon={Search}/>
      <ul class="video-list">
        {filterList.value.map(item => <li>
          <div class="video-box">
            <el-icon onClick={() => selectItem(item)}><CaretRight /></el-icon>
            <video src={item.url}></video>
          </div>
          <p>{item.title}</p>  
        </li>)}
      </ul>
      {state.current.isShow && <div class="fullscreen">
        <el-icon onClick={() => state.current.isShow = false}><CircleClose /></el-icon>
        <video ref={loadVideo} src={state.current.url} controls autoplay></video>
      </div>}
    </div>
  }
}
</script>
