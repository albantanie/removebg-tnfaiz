<script >
import { ref, watch, onMounted, onUnmounted } from 'vue';
import removeBackground from '@imgly/background-removal';

export default {
  name: 'App',
  setup() {
    const imageUrl = ref("");
    const isRunning = ref(false);
    const seconds = ref(0);
    const startDate = ref(Date.now());
    const caption = ref('Click me to remove background');
    const downloadUrl = ref('');
    let interval = "";

    const calculateSecondsBetweenDates = (startDate, endDate) => {
      const milliseconds = endDate - startDate;
      const seconds = (milliseconds / 1000.0).toFixed(1);
      return seconds;
    };

    watch(
      () => isRunning.value,
      (newVal) => {
        if (newVal) {
          interval = setInterval(() => {
            seconds.value = calculateSecondsBetweenDates(
              startDate.value,
              Date.now()
            );
          }, 100);
        } else {
          clearInterval(interval);
        }
      }
    );

    onMounted(() => {
      if (isRunning.value) {
        interval = setInterval(() => {
          seconds.value = calculateSecondsBetweenDates(
            startDate.value,
            Date.now()
          );
        }, 100);
      }
    });

    onUnmounted(() => {
      clearInterval(interval);
    });

    const resetTimer = () => {
      isRunning.value = true;
      startDate.value = Date.now();
      seconds.value = 0;
    };

    const stopTimer = () => {
      isRunning.value = false;
    };

    const handleFileInputChange = (event) => {
      const file = event.target.files[0];
      imageUrl.value = URL.createObjectURL(file);
    };

    const executeTransparency = async () => {
      if (imageUrl.value) {
        resetTimer();
        isRunning.value = true;
        try {
          const imageBlob = await removeBackground(imageUrl.value);
          const url = URL.createObjectURL(imageBlob);
          downloadUrl.value = url;
          imageUrl.value = url; // Mengganti gambar sebelumnya dengan gambar yang sudah ditransparansi
        } catch (error) {
          console.error(error);
          // Handle error
        } finally {
          isRunning.value = false;
          stopTimer();
        }
      }
    };

    return {
      imageUrl,
      isRunning,
      seconds,
      caption,
      executeTransparency,
      handleFileInputChange,
      downloadUrl,
    };
  },
};
</script>






<style scoped>
/* Add your styles here */
.container {
  padding: 30px;
}

.img-fluid {
  max-width: 100%;
  height: auto;
}

.text-center {
  text-align: center;
}
</style>

<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-6">
        <h1>Simple Remove Background Tool</h1>
        <label for="photo" class="form-label">Input photo</label>
        <input type="file" id="photo" class="form-control" accept="image/*" @change="handleFileInputChange" ref="fileInput" />
        <br>
        <div class="text-center">
          <img v-if="imageUrl" :src="imageUrl" alt="logo" class="img-fluid" />
        </div>
        <p class="text-center">{{ caption }}</p>
        <p class="text-center">Processing: {{ seconds }} s</p>
        <div class="d-grid gap-2">
          <button class="btn btn-danger" :disabled="isRunning" @click="executeTransparency">Click me</button>
          <br>
          <a class="btn btn-primary" v-if="!isRunning && downloadUrl" :href="downloadUrl" download="background_removed_image.png">
            Download
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

