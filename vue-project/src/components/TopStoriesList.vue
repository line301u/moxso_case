
<script>
export default {
  data(){
    return {
      allTopStories: [],
      randomTopStories: [],
    }
  }, 

  mounted(){
    this.getAllTopStories();
    this.getRandomTopStories();
  },

  methods: {
    async getAllTopStories() {
      const response = await fetch(`https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty`);
      const data = await response.json();
      this.allTopStories = data;
    },
    async getRandomTopStories() {
      await this.getAllTopStories()

      let randomTopStoriesIds = [];
      for (var i=1;i<=10; i++) {
        const randomStoryID = this.allTopStories[Math.floor(Math.random()*this.allTopStories.length)];
        randomTopStoriesIds.push(randomStoryID);
      }

      const randomTopStories = await Promise.all(
        randomTopStoriesIds.map(async (id) => {
          const response = await fetch(`https://hacker-news.firebaseio.com/v0/item/${id}.json?print=pretty`);
          const data = await response.json();
          return data;
        })
      );

      this.randomTopStories = randomTopStories.sort((lowest, highest) => highest.score - lowest.score);;
    },
    convertTimestampToDate(timestamp){
      const months = ["January","February","March","April","May","June","July","August","September","October","November","December"];

      const date = new Date(timestamp)
      const day = date.getDate();
      const month = months[date.getMonth()];
      const year = date.getFullYear();
      return `${day} ${month} ${year}`;
    }
  },
}
</script>
 
 
<template>
  <div class="flex flex-col gap-5">
    <h1 class="font-medium text-3xl mt-6">Top stories</h1>
    <div class="flex flex-col bg-white shadow-md shadow-slate-200 border rounded border-slate-200" v-for="(story, index) in randomTopStories" :key="index">
      <h2 class="p-3 border-b border-slate-200 font-bold text-md">{{story.title}}</h2>
      <div class="p-3 flex flex-col gap-1">
        <div class="flex justify-between">
          <h3>Score</h3>
          <p>{{story.score}}</p>
        </div>
        <div class="flex justify-between">
          <h3>Author</h3>
          <p>{{story.by}}</p>
        </div>
        <div class="flex justify-between">
          <h3>Date</h3>
          <p>{{convertTimestampToDate(story.time)}}</p>
        </div>
        <div class="flex justify-between">
          <h3>Number of comments</h3>
          <p>{{(story.kids)?.length ? (story.kids).length : 0}}</p>
        </div>
      </div>
    </div>
  </div>
</template>
