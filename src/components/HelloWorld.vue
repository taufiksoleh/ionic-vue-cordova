<template>
  <div>
    <ion-button @click="insert"> INSERT SQLITE </ion-button>
    <ion-button @click="openCamera"> CAMERA </ion-button>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data: function () {
    return {
      db: null,
    };
  },
  mounted() {
    document.addEventListener("deviceready", async () => {
      await this.openDB();
      await this.insert();
    });
  },
  methods: {
    openDB() {
      this.db = window.sqlitePlugin.openDatabase({
        name: "my.db",
        location: "default",
      });
      console.log(this.db);
    },
    insert() {
      this.db.transaction(function (tx) {
        tx.executeSql(
          "CREATE TABLE IF NOT EXISTS test_table (id integer primary key, data text, data_num integer)"
        );

        tx.executeSql(
          "INSERT INTO test_table (data, data_num) VALUES (?,?)",
          ["test", 100],
          function (tx, res) {
            console.log("insertId: " + res.insertId + " -- probably 1");
            console.log(
              "rowsAffected: " + res.rowsAffected + " -- should be 1"
            );

            tx.executeSql(
              "select count(id) as cnt from test_table;",
              [],
              function (tx, res) {
                console.log(
                  "res.rows.length: " + res.rows.length + " -- should be 1"
                );
                console.log(
                  "res.rows.item(0).cnt: " +
                    res.rows.item(0).cnt +
                    " -- should be 1"
                );
              }
            );
          },
          function (tx, e) {
            console.log("ERROR: " + e.message);
          }
        );
      });
    },
    setOptions(srcType) {
      var options = {
        // Some common settings are 20, 50, and 100
        quality: 50,
        destinationType: 0, // 0 based64return
        // In this app, dynamically set the picture source, Camera or photo gallery
        sourceType: srcType,
        encodingType: 0, // 0 = JPEG, 1 = PNG
        mediaType: 0, // 0 = PICTURE, 1 = VIDEO, 2 = ALLMEDIA
      };
      return options;
    },

    async permission(permissions) {
      var Permission = window.plugins.Permission;
      let result = false
      await Permission.has(
        permissions,
        function (results) {
          if (results[permissions]) {
            Permission.request(
              permissions,
              function (result) {
                if (result[permissions]) {
                  console.log("PERMISSION : " + result[permissions] + " GRANTED");

                  result = true
                }
              },
              alert
            );
          }
        },
        alert
      );

      return result
    },

    openCamera() {
      var srcType = 1;
      var options = this.setOptions(srcType);
      //var func = createNewFileEntry;
      if(this.permission("android.permission.READ_EXTERNAL_STORAGE") && this.permission("android.permission.CAMERA") && this.permission("android.permission.WRITE_EXTERNAL_STORAGE")){
       navigator.camera.getPicture(
        function cameraSuccess(imageUri) {
          console.log(imageUri)
          this.displayImage(imageUri);
          // You may choose to copy the picture, save it somewhere, or upload.
          //func(imageUri);
        },
        function cameraError(error) {
          console.debug("Unable to obtain picture: " + error, "app");
        },
        options
      ); 
      }
    },

    displayImage(imgUri) {
      console.log(imgUri);
      // var elem = document.getElementById('imageFile');
      // elem.src = imgUri;
    },
  },
};
</script>