<script setup>
import { useStore } from 'vuex';
import { computed,onMounted, ref,watch } from 'vue';
import {useRoute} from "vue-router";
// import  marked from "marked";

let route = useRoute();
let chatInput = ref(null);
const store = useStore();
const chat = computed(() => store.state.chat.chat);
const isWaiting = computed(() => store.state.chat.isWaiting);


onMounted(() => {
  store.dispatch('getChat',{chat_id: route.params.id});
  console.log(isWaiting)
  console.log('mounted chat')
})

watch(()=> route.params.id, (newId,oldId) => {
  console.log('watching')
  store.dispatch('getChat',{chat_id: route.params.id})
})

const sendChat = () =>{
  let msg = chatInput.value.value
  store.dispatch('sendChat',{msg,chat_id: route.params.id})
}
</script>

<template>

  <!-- conversatons -->
  <div class="w-full h-full flex-col-reverse overflow-y-scroll flex gap-5 p-10 pb-32">

    <!-- message-out -->
    <!-- :src="i.is_user_message ? './../assets/logo.png' : './../assets/logo.png'" -->
    <div 
    v-if="chat.length >= 1"
    v-for="i in chat"
    :class="i.is_user_message ? 'chat-out' : ''"
    class="chat-in">
      <img 
      class="chat-image"
      :src="i.is_user_message ? './../assets/user_icon.png' : './../assets/logo.png'"
      alt="" />
      <p class="chat-message"> {{ i.message_text }} </p>
    </div>

  </div>
          <!-- stop loading -->
        <div 
        :class="!isWaiting ? 'scale-1' : 'scale-0'"
        class="group transition-all origin-center duration-700 w-1/4 flex items-center absolute bottom-[120px] left-1/2 -translate-x-1/2">
            <p class="input-style px-5 text-center flex items-center gap-1 p-1 rounded-full">
               Thinking...
            
            </p>
        </div>
        <div 
        class="group w-3/4 flex items-center absolute bottom-[50px] left-1/2 -translate-x-1/2">
            <input 
            class="placeholder-white/40 input-style pr-12  relative w-full rounded-full  h-fit p-4" 
            ref="chatInput"
            type="text"
            @keypress.enter="sendChat"
            placeholder="Ask me a question">

              <fa 
              @click="sendChat"
              class="inline cursor-pointer text-2xl absolute text-white right-0 hover:bg-white origin-center duration-200 hover:text-black/50 h-6 rounded-full p-4" icon="arrow-right" /> 
             
            </input>
        </div>
</template>