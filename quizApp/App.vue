<template>
  <div class="app">
    <div class="container1">
      
      <label for="Category">Category:</label>
        <select v-model="category">
          <option value="linux">Linux</option>
          <option value="bash">Bash</option>
          <option value="uncategorized">Uncategorized</option>
          <option value="code">Code</option>
          <option value="sql">sql</option>
        </select>
        <br>

        <label for="difficulty" >Difficulty:</label>
        <select v-model="difficulty">
          <option value="easy">Easy</option>
          <option value="medium">Medium</option>
          <option value="hard">Hard</option>
        </select>
        <br>

        <label for="numberofquestions">Number of Questions:</label>
        <select v-model="limit">
          <option value="4">4</option>          
          <option value="8">8</option>         
          <option value="12">12</option>      
          <option value="16">16</option>     
          <option value="20">20</option>
        </select>
        <br><br>
      
      <button @click="showQuestion" :disabled="isAvailable">Show</button>
      <button :disabled="!isAvailable">Reset</button>
    </div>


    <div class="container2" v-if="isAvailable">
      <h3>Q.{{countNext+1}} {{question}}</h3>
      <input type="radio" name="answer" :value="answer_a" v-model="answer" @change="checkanswer" v-if="!radioinvisible && isPrevious===false">{{ answer_a }}
      <br>
      <input type="radio" name="answer" :value="answer_b" v-model="answer" @change="checkanswer" v-if="!radioinvisible && isPrevious===false">{{ answer_b }}
      <br>
      <input type="radio" name="answer" :value="answer_c" v-model="answer" @change="checkanswer" v-if="answer_c !== null && !radioinvisible && isPrevious===false">{{ answer_c }}
      <br>
      <input type="radio" name="answer" :value="answer_d" v-model="answer" @change="checkanswer" v-if="answer_d !== null && !radioinvisible && isPrevious===false">{{ answer_d }}
      <br>
      <input type="radio" name="answer" :value="answer_d" v-model="answer" @change="checkanswer" v-if="answer_e !== null && !radioinvisible && isPrevious===false">{{ answer_e }}
      <br>
      <br><br>
      <!-- <h6>{{ answer }}</h6> -->
      <h6>The correct answer is {{ correctanswer }}</h6>

      <h3 v-if="radioinvisible">{{ noti }}</h3>


      <div style="display: flex; justify-content: space-between;">
        <button @click="previous" :disabled="countNext===0" style="order: 1;">Previous</button>
        <button v-if="limit===countNext+1" style="order: 2;">Submit</button>
        <button @click="next" :disabled="limit<=countNext+1" style="order: 3;">Next</button>
      </div>
      <label v-if="limit===countcheckanswer">
        <h2>Your score is- {{ score }}/{{limit}}</h2>
      </label>
      
    </div>

    
  </div>

</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import axiox from 'axios';


export default defineComponent({
  name: 'App',
  components: {},
  setup(){
    const API= ref("pxCOsbiQBbTmx6phBszVp75OoR8xrirMavvooeRm")
    const category= ref("")
    const difficulty= ref("Easy")
    const limit= ref(4)
    
    const quizData= ref<any>(null);
    const question= ref("")
    const isAvailable= ref(false)
    
    const answer_a= ref("")
    const answer_b= ref("")
    const answer_c= ref("")
    const answer_d= ref("")
    const answer_e= ref("")
    const correctanswer_a= ref("")
    const correctanswer_b= ref("")
    const correctanswer_c= ref("")
    const correctanswer_d= ref("")
    const correctanswer_e= ref("")

    const answer= ref("")
    const correctanswer= ref("")
    const score= ref(0)

    const showQuestion= async ()=>{
      isAvailable.value= true
      try{
        const response= await axiox.get(
          "https://quizapi.io/api/v1/questions",
          {
            params:{
              apiKey: API.value,
              category: category.value,
              difficulty: difficulty.value,
              limit: limit.value,
            },
          }
        );
        quizData.value= response.data;

      }catch(error){
        alert("error fetching weather data");
      }
      showquestionanswer(0)        
    }

    const showquestionanswer=(term: number)=>{
      question.value= quizData.value[term].question
      answer_a.value= quizData.value[term].answers.answer_a
      answer_b.value= quizData.value[term].answers.answer_b
      answer_c.value= quizData.value[term].answers.answer_c
      answer_d.value= quizData.value[term].answers.answer_d
      answer_e.value= quizData.value[term].answers.answer_e

      correctanswer_a.value= quizData.value[term].correct_answers.answer_a_correct //3.correct_answers.answer_a_correct
      correctanswer_b.value= quizData.value[term].correct_answers.answer_b_correct
      correctanswer_c.value= quizData.value[term].correct_answers.answer_c_correct
      correctanswer_d.value= quizData.value[term].correct_answers.answer_d_correct
      correctanswer_e.value= quizData.value[term].correct_answers.answer_e_correct

      if(correctanswer_a.value==="true"){
        correctanswer.value= quizData.value[term].answers.answer_a
        console.log(correctanswer.value);
        //3.answers.answer_a
      }else if(correctanswer_b.value==="true"){
        correctanswer.value= quizData.value[term].answers.answer_b
        console.log(correctanswer.value);
      }else if(correctanswer_c.value==="true"){
        correctanswer.value= quizData.value[term].answers.answer_c
        console.log(correctanswer.value);
      }else if(correctanswer_d.value==="true"){
        correctanswer.value= quizData.value[term].answers.answer_d
        console.log(correctanswer.value);
      }else{
        correctanswer.value= quizData.value[term].answers.answer_e
        console.log(correctanswer.value);
      }
    }
    
    const countcheckanswer= ref(0)
    const radioinvisible= ref(false)
    const checkanswer= ()=>{
      countcheckanswer.value++
      radioinvisible.value= true
      console.log("The correct answer is "+ correctanswer.value);
      console.log("The user answer is "+ answer.value);

      checkifcorrect()

    }

    var countNext= ref<number>(0)
    const next= ()=>{
        radioinvisible.value= false
        answer.value= ""
        countNext.value++ 
        showquestionanswer(countNext.value)
    }

    const isPrevious= ref(false)
    const previous= ()=>{
        isPrevious.value= true
        answer.value= ""
        countNext.value--
        showquestionanswer(countNext.value)
    }

    const noti= ref("") 
    const checkifcorrect=()=>{
      if(answer.value===correctanswer.value){
        noti.value= "Your answer is correct !!"
        score.value++
        console.log(score.value);
        
      }else{
        noti.value= "Your answer is wrong !!"
      }
    }
    
    return {question, isAvailable, showQuestion,quizData, answer_a, answer_b, answer_c, answer_d, answer_e,
       answer, correctanswer, difficulty, limit, category,next, countNext, previous, checkanswer, noti, 
       radioinvisible, score, countcheckanswer, isPrevious }
  }
});
</script>

<style>
body {
  background-color: #003554;
}
.container1 {
  position: absolute;
  width: 20%;
  height: 40%;
  padding: 20px;
  top: 40%;
  left: 20%;
  border-radius: 6px;
  flex-direction: column;
  overflow-y: auto;
  /* Your container styles */
  background-color: #f4f4f2;
  color: #27374d;
}
.container2 {
  position: absolute;
  width: 30%;
  height: 60%;
  padding: 20px;
  top: 20%;
  left: 50%;
  border-radius: 6px;
  flex-direction: column;
  overflow-y: auto;
  /* Your container styles */
  background-color: #f4f4f2;
  color: #27374d;
}

</style>
