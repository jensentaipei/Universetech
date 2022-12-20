<template>
  <div style="height: 50px; overflow: scroll" ref="theList">
    <table v-if="finishGet">
      <tr>
        <th>name</th>
        <th>description</th>
        <th>url</th>
      </tr>
      <tr v-for="l of list" :key="l.id">
        <td>{{ l.name }}</td>
        <td>{{ l.des }}</td>
        <td>
          <a :href="l.url">{{ l.url }}</a>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import { onMounted, reactive, ref, onBeforeUnmount } from "vue";

export default {
  setup() {
    const list = reactive([]);
    let allList = [];
    const finishGet = ref(false);
    const theList = ref(null);

    const getGithub = async () => {
      const res = await fetch(
        "https://api.github.com/users/jensentaipei/repos"
      );
      const data = await res.json();
      allList = data.map((d) => {
        return {
          id: d.id,
          name: d.name,
          des: d.description ? d.description : "-",
          url: d.html_url,
        };
      });

      for (let i = 0; i <= 1; i++) {
        list.push(...allList.splice(0, 1));
      }
      finishGet.value = true;
    };
    getGithub();

    onMounted(() => {
      theList.value.addEventListener("scroll", scrollEvent);
    });

    const scrollEvent = () => {
      if (!allList.length) return;

      if (
        theList.value.scrollTop + theList.value.clientHeight >=
        theList.value.scrollHeight
      ) {
        list.push(...allList.splice(0, 1));
      }
    };

    onBeforeUnmount(() => {
      theList.value.removeEventListener("scroll", scrollEvent);
    });

    return { list, finishGet, theList };
  },
};
</script>

<style scoped></style>
