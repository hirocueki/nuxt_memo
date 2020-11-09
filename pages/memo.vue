<template>
  <section class="container">
    <h1>Memo</h1>
    <table>
      <tr>
        <th>Title</th>
        <td>
          <input
            v-model="title"
            type="text"
            name="title"
            class="title"
            size="40"
            @focus="set_flag"
          />
          <button @click="find">find</button>
        </td>
      </tr>
      <tr>
        <th>Memo</th>
        <td>
          <textarea
            v-model="content"
            name="content"
            cols="50"
            rows="5"
            class="content"
          ></textarea>
        </td>
      </tr>
      <tr>
        <th></th>
        <td>
          <button @click="insert">save</button>
          <transition name='"del'>
            <button v-if="sel_flag != false" @click="remove">delete</button>
          </transition>
        </td>
      </tr>
    </table>
    <hr />
    <ul class="list">
      <li v-for="(item, index) in page_items" :key="index">
        <span @click="select(item)">
          {{ item.title }}（{{ item.created }}）
        </span>
      </li>
    </ul>
    <hr />
    <div class="nav">
      <span @click="prev">&lt;prev</span> | <span @click="next">next&gt;</span>
    </div>
  </section>
</template>

<script>
export default {
  data(){
    return {
      title: '',
      content: '',
      num_per_page:7,
      find_flag: false,
      sel_flag: false,
    }
  },
  computed: {
    memo() {
      return this.$store.state.memo.memo
    },
    page_items() {
      if (this.find_flag) {
        const arr = []
        const data = this.$store.state.memo.memo
        data.forEach((element) => {
          if (element.title.toLowerCase().includes(this.title.toLowerCase())) {
            arr.push(element)
          }
        })
        return arr
      } else if (this.sel_flag !== false) {
        return [this.sel_flag]
      } else {
        return this.$store.state.memo.memo.slice(
          this.num_per_page * this.$store.state.memo.page,
          this.num_per_page * (this.$store.state.memo.page + 1)
        )
      }
    },
    page: {
      get() {
        return this.$store.state.memo.page
      },
      set(p) {
        let pg =
          p > (this.$store.state.memo.memo.length - 1) / this.num_per_page
            ? Math.ceil(
                (this.$store.state.memo.memo.length - 1) / this.num_per_page
              ) - 1
            : p
        pg = pg < 0 ? 0 : pg
        this.$store.commit('memo/set_page', pg)
      },
    }
  }
}
</script>
