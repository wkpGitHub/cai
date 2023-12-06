<script lang="jsx">
import { reactive, computed } from 'vue'
import { Search, CaretRight, CircleClose } from '@element-plus/icons-vue'
import { Swiper, SwiperSlide } from 'swiper/vue';
import 'swiper/css';
export default {
  setup () {
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

    function selectItem (item) {
      state.current = {
        isShow: true,
        urls: item.urls,
      }
    }

    return () => <div class="container">
      <ElInput v-model={state.keyword} placeholder="请输入名称查询" suffix-icon={Search} />
      <ul class="video-list">
        {filterList.value.map(item => <li>
          <div class="video-box" onClick={() => selectItem(item)}>
            <el-icon><CaretRight /></el-icon>
            {item.urls[0].endsWith('.mp4') ? <video src={item.urls[0]}></video>
              : <img src={item.urls[0]} />}
          </div>
          <p>{item.title}</p>
        </li>)}
      </ul>
      {state.current.isShow && <div class="fullscreen">
        <el-icon onClick={() => state.current.isShow = false}><CircleClose /></el-icon>
        <Swiper>
          {state.current.urls.map(s => <SwiperSlide>
            {s.endsWith('.mp4') ? <video src={s} controls></video> : <img src={s} />}
          </SwiperSlide>)}
        </Swiper>
      </div>}
    </div>
  }
}
</script>
