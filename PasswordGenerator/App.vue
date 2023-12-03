<template>
  <div class="container d-flex vh-70 w-40 justify-content-center align-items-center" style="margin-top: 100px;">
  <div class="password-generator-container" >
    <h1 class="mb-4">Password Generator</h1>

    <div class="passfield mb-4">
      <div class="container">
        <div class="d-flex justify-content-between align-items-center border p-3" style="height: 60px;">
          <h3 ref="textToCopy" class="m-0">{{ NGpass || ' ' }}</h3>
          <button @click="copyText" class="btn btn-primary">Copy</button>
        </div>
      </div>

      <div class="length d-flex justify-content-between align-items-center">
        <h5 class="m-0">Password length</h5>
        <input type="number" v-model="passLength" class="form-control" style="width: 100px;" min="3" max="15"/>
      </div>

      <div class="checkbox mt-3">
        <label v-for="type in types" :key="type" class="d-flex align-items-center justify-content-between mb-3">    
          <div>
            <h5 class="mb-0" v-if="type === 'lower'">{{ "Include lowercase letters" }}</h5>
            <h5 class="mb-0" v-else-if="type === 'upper'">{{ "Include uppercase letters" }}</h5>
            <h5 class="mb-0" v-else-if="type === 'number'">{{ "Include numbers" }}</h5>
            <h5 class="mb-0" v-else-if="type === 'symbol'">{{ "Include symbols" }}</h5>
          </div>

          <div class="form-check">
            <input type="checkbox" class="form-check-input" name="condition" :value="type" v-model="conditions"/>
          </div>
        </label>
      </div> 
    </div>
    
    <button class="btn btn-primary btn-block" @click="generatePass" style="width: 100%;">Generate Password</button>
  </div>
</div>

</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from "vue";

export default defineComponent({
  name: "App",
  components: {},
  setup(){
    const password= ref("")
    const index= ref()
    const passLength= ref(3)
    const Gpass= ref("")
    const NGpass= ref("")
    const typeIndex= ref()
    const shuffle = (word: string): string => {
      const wordArray = word.split('');

      for (let i = wordArray.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [wordArray[i], wordArray[j]] = [wordArray[j], wordArray[i]];
      }

      return wordArray.join('');
    };
    const types= ref(['lower','upper','number','symbol'])
    const lower = ref([
      'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l',
      'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x',
      'y', 'z'
    ])
    const upper = ref([
      'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J',
      'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V',
      'W', 'X', 'Y', 'Z'
    ])
    const number= ref([
    '0', '1', '2', '3', '4', '5', '6', '7', '8', '9'
    ])
    const symbol= ref([
    '!', '#', '$', '%', '&', '(', ')', '*', '+'
    ])

    const conditions= ref([])
    const generatePass= ()=>{
      Gpass.value= ""
      const newTypes= ref([])
      newTypes.value= conditions.value
   
      for(let i=0; i<passLength.value; i++){
        typeIndex.value= Math.floor(Math.random()*newTypes.value.length)
        switch(newTypes.value[typeIndex.value]){
          case 'lower': 
            index.value= Math.floor(Math.random()*lower.value.length) 
            password.value= lower.value[index.value]
            break;
          case 'upper': 
            index.value= Math.floor(Math.random()*upper.value.length) 
            password.value= upper.value[index.value]
            break;
          case 'number': 
            index.value= Math.floor(Math.random()*number.value.length) 
            password.value= number.value[index.value]
            break;
          case 'symbol': 
            index.value= Math.floor(Math.random()*symbol.value.length) 
            password.value= symbol.value[index.value]
            break;
        }
        Gpass.value+= password.value
      }
      NGpass.value= shuffle(Gpass.value)
    }

    // function to copy password to the clipcart

    const textToCopy = ref<HTMLElement | null>(null);

    onMounted(() => {
      // Set the reference to the h1 element after the component is mounted
      textToCopy.value = document.querySelector('h3');
    });

    const copyText = () => {
      if (textToCopy.value) {
        const text = textToCopy.value.innerText;

        // Copy text to clipboard
        navigator.clipboard.writeText(text)
          .then(() => {
            alert('Text copied to clipboard!');
          })
          .catch((error) => {
            console.error('Error copying text:', error);
            alert('Error copying text. Please try again.');
          });
      }
    };

    return {password, generatePass, passLength, Gpass, NGpass, types, conditions, textToCopy, copyText}
  }
});
</script>

<style>



</style>
