<template>
  <div class="p-8">
    <div class="flex items-start space-x-4">
      <div class="min-w-0 flex-1">
        <form action="#">
          <div class="border-b border-gray-200 focus-within:border-pink-600">
            <label for="comment" class="sr-only">Add your markdown</label>
            <textarea rows="10" name="comment" id="comment" v-model="markdowninput"
                      class="block w-full border-0 border-b border-transparent p-0 pb-2 resize-none focus:ring-0 focus:border-pink-600 sm:text-sm p-2 resize-y"
                      placeholder="Add your markdown..."/>
          </div>
        </form>
      </div>
    </div>

    <div class="mt-4">
      <h1 class="text-base font-medium text-2xl uppercase text-red-600">Output</h1>
      <div class="mt-4 border p-4 prose prose-pink">
        <div v-html="markdownoutput"></div>
      </div>
    </div>
    <div class="mt-4">
      <h1 class="text-base font-medium text-2xl uppercase text-red-600">Output HTML</h1>
      <button
          @click="copyToClipboard"
          type="button"
              class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-pink-600 hover:bg-pink-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-pink-500">
        Copy
      </button>

      <div class="mt-4 border p-4">
        <code @click="copyToClipboard">
          {{ markdownoutput }}
        </code>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import {ref, watch} from 'vue'
import {
  EmojiHappyIcon as EmojiHappyIconSolid,
  EmojiSadIcon,
  FireIcon,
  HeartIcon,
  ThumbUpIcon,
  XIcon,
} from '@heroicons/vue/solid'
//@ts-ignore
import DOMPurify from 'dompurify';
//@ts-ignore
import MarkdownIt from "markdown-it";
//@ts-ignore
import hljs from "highlight.js";

//@ts-ignore
import emoji from "markdown-it-emoji";

const md = new MarkdownIt({
  //@ts-ignore
  highlight: function (str, lang) {
    if (lang && hljs.getLanguage(lang)) {
      try {
        return '<pre class="hljs"><code>' +
            hljs.highlight(str, {language: lang, ignoreIllegals: true}).value +
            '</code></pre>';
      } catch (__) {
      }
    }

    return '<pre class="hljs"><code>' + md.utils.escapeHtml(str) + '</code></pre>';
  },
  html: true,
  linkify: true,
});

md.use(emoji);


const moods = [
  {name: 'Excited', value: 'excited', icon: FireIcon, iconColor: 'text-white', bgColor: 'bg-red-500'},
  {name: 'Loved', value: 'loved', icon: HeartIcon, iconColor: 'text-white', bgColor: 'bg-pink-400'},
  {name: 'Happy', value: 'happy', icon: EmojiHappyIconSolid, iconColor: 'text-white', bgColor: 'bg-green-400'},
  {name: 'Sad', value: 'sad', icon: EmojiSadIcon, iconColor: 'text-white', bgColor: 'bg-yellow-400'},
  {name: 'Thumbsy', value: 'thumbsy', icon: ThumbUpIcon, iconColor: 'text-white', bgColor: 'bg-blue-500'},
  {name: 'I feel nothing', value: null, icon: XIcon, iconColor: 'text-gray-400', bgColor: 'bg-transparent'},
]

const markdownoutput = ref('')
const markdowninput = ref()

watch(markdowninput, (value) => {
  const dirty = md.render(value);
  markdownoutput.value = DOMPurify.sanitize(dirty)
})

const copyToClipboard = () => {
  const el = document.createElement('textarea');
  el.value = markdownoutput.value;
  el.setAttribute('readonly', '');
  el.style.position = 'absolute';
  el.style.left = '-9999px';
  document.body.appendChild(el);
  el.select();
  document.execCommand('copy');
  document.body.removeChild(el);
}

</script>

<style>
</style>
