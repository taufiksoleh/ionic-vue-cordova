<template>
  <div>
    <ion-button>
      Text Ionic Button
    </ion-button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  mounted() {
    document.addEventListener("deviceready", () => {
      this.openDB();
    });
  },
  methods: {
    openDB(){
      let db = window.sqlitePlugin.openDatabase({ name: 'my.db', location: 'default' });
      console.log(db)
    }
  }
}
</script>