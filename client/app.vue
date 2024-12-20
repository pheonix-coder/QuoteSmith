<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-400 to-green-400 flex items-center justify-center p-4 sm:p-6">
    <div class="bg-white rounded-xl shadow-2xl w-full max-w-7xl p-4 sm:p-8">
      <h1 class="text-2xl sm:text-3xl font-bold mb-4 sm:mb-6 text-center text-gray-800">Quote Smith</h1>
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <div class="space-y-4">
          <textarea v-model="quote" placeholder="Enter your quote here..."
            class="w-full p-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 resize-none"
            rows="4"></textarea>
          <input v-model="author" placeholder="Author (optional)"
            class="w-full p-4 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500" type="text" />
          <div class="flex flex-wrap -mx-2">
            <div class="w-full sm:w-1/2 px-2 mb-4 sm:mb-0">
              <label class="block text-sm font-medium text-gray-700 mb-2">
                Font Size: {{ fontSize }}px
              </label>
              <input v-model="fontSize" type="range" min="14" max="48" class="w-full" />
            </div>
            <div class="w-full sm:w-1/2 px-2">
              <label class="block text-sm font-medium text-gray-700 mb-2">
                Text Color
              </label>
              <input v-model="color" type="color" class="w-full h-10 rounded-lg cursor-pointer" />
            </div>
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Quote Background
            </label>
            <select v-model="selectedBackground"
              class="w-full p-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
              <option value="solid">Solid Color</option>
              <option value="transparent">Transparent</option>
              <option v-for="(gradient, index) in gradients" :key="index" :value="gradient.value">
                {{ gradient.name }}
              </option>
            </select>
          </div>
          <div v-if="selectedBackground === 'solid'">
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Background Color
            </label>
            <input v-model="solidColor" type="color" class="w-full h-10 rounded-lg cursor-pointer" />
          </div>
          <button @click="downloadAsPng"
            class="w-full py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-colors duration-300">
            Download as PNG
          </button>
        </div>
        <div id="quoteContainer"
          :style="quoteContainerStyle"
          class="p-6 rounded-lg shadow-lg transition-all duration-300 flex flex-col justify-center min-h-[200px]">
          <p :style="quoteStyle" class="mb-4">{{ quote || 'Your quote will appear here' }}</p>
          <p v-if="author" class="text-right italic" :style="authorStyle">- {{ author }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import * as htmlToImage from 'html-to-image';

const quote = ref('');
const author = ref('');
const fontSize = ref(24);
const color = ref('#000000');
const selectedBackground = ref('solid');
const solidColor = ref('#ffffff');
const gradients = [
  { name: 'Sunset', value: 'linear-gradient(to right, #ff7e5f, #feb47b)' },
  { name: 'Ocean', value: 'linear-gradient(to right, #6a11cb, #2575fc)' },
  { name: 'Forest', value: 'linear-gradient(to right, #11998e, #38ef7d)' },
  { name: 'Berry', value: 'linear-gradient(to right, #fc466b, #3f5efb)' },
  { name: 'Peach', value: 'linear-gradient(to right, #ee9ca7, #ffdde1)' },
];

const quoteContainerStyle = computed(() => {
  if (selectedBackground.value === 'solid') {
    return { backgroundColor: solidColor.value };
  } else if (selectedBackground.value === 'transparent') {
    return { backgroundColor: 'transparent' };
  } else {
    return { background: selectedBackground.value };
  }
});

const quoteStyle = computed(() => ({
  fontSize: `${fontSize.value}px`,
  color: color.value,
}));

const authorStyle = computed(() => ({
  fontSize: `${Math.max(14, fontSize.value * 0.75)}px`,
  color: color.value,
}));

const downloadAsPng = async () => {
  try {
    const container = document.getElementById('quoteContainer');
    if (!container) return;

    const dataUrl = await htmlToImage.toPng(container);
    const link = document.createElement('a');
    link.href = dataUrl;
    link.download = 'quote.png';
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  } catch (error) {
    console.error('Error generating PNG:', error);
  }
};
</script>
