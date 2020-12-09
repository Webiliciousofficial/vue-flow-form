// Create and setup your form here
 
<template>
  <div>
    <header class="vff-header">
      <div class="f-container">
       <!-- Add custom logo here -->
        
        <h2 style="color:yellow;" class="f-logo">adplus</h2>
      </div>
    </header>

    <flow-form
      ref="flowform"
      v-on:complete="onComplete"
      v-on:submit="onSubmit"
      v-bind:questions="questions"
      v-bind:language="language"
      v-bind:standalone="true"
    >
    <!-- Custom content for the Complete/Submit screen slots in the FlowForm component -->
      <!-- We've overriden the default "complete" slot content -->
     <template v-slot:complete>
        <div class="f-section-wrap">
          <p>
            <span class="fh2">Thank you. 🙏</span>
            <span class="f-section-text">
              Great work, the survey is completed. You can press submit button.
            </span>
          </p>
          <!-- <p class="f-description">Note: No data will be saved and/or sent in this demo.</p> -->
        </div>   
      </template>

      <!-- We've overriden the default "completeButton" slot content -->
      <template v-slot:completeButton>
        <div class="f-submit" v-if="!submitted">
          <button 
            class="o-btn-action"
            ref="button"
            type="submit"
            href="#"
            v-on:click.prevent="onSendData()"
            aria-label="Press to submit"
          >
            <span>{{ language.submitText }}</span>
          </button>
          <a class="f-enter-desc"
            href="#"
            v-on:click.prevent="onSendData()"
            v-html="language.formatString(language.pressEnter)">
          </a>
        </div>

        <p class="text-success" v-if="submitted">Submitted succesfully.</p>
      </template>
    </flow-form>
  </div>
</template>

<script>
  /*
    Copyright (c) 2020 - present, DITDOT Ltd. - MIT Licence
    https://www.ditdot.hr/en
  */

  // Import necessary components and classes
  import FlowForm from '../../src/components/FlowForm.vue'
  import QuestionModel, { QuestionType, ChoiceOption, LinkOption } from '../../src/models/QuestionModel'
  import LanguageModel from '../../src/models/LanguageModel'
  // If using the npm package, use the following line instead of the ones above.
  // import FlowForm, { QuestionModel, QuestionType, ChoiceOption, LanguageModel } from '@ditdot-dev/vue-flow-form'

  export default {
    name: 'example',
    components: {
      FlowForm
    },
    data() {
      return {
        submitted: false,
        completed: false,
        language: new LanguageModel(),
        // Create question list with QuestionModel instances
        questions: [
          new QuestionModel({
            id: 'first_name',
            tagline: 'Hi! Welcome to our survey 😊',
            title: 'What is your  name?',
            type: QuestionType.Text,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'last_name',
            tagline: "Nice to meet you 👀, let's continue",
            title: 'What is your  Company name?',
            type: QuestionType.Text,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'email',
            tagline: "That's nice",
            title: 'Provide an example email.',
            type: QuestionType.Email,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'phone',
            title: 'Doing great! 👍 Go ahead and try with a phone number.',
            type: QuestionType.Phone,
            required: true,
            mask: '####-###-####'
          }),
          new QuestionModel({
            id: 'city',
            title: 'Which place you are from',
            type: QuestionType.Text,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'last_date',
            title: 'When have you published ad last time.',
            type: QuestionType.Date,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'multiple_choice',
            title: 'What was your satisfaction level in your last ad campaign.',
            helpTextShow: false,
            type: QuestionType.MultipleChoice,
            multiple: false,
            allowOther: true,
            required: true,
            options: [
              new ChoiceOption({
                label: 'Higly satisfied'
              }),
              new ChoiceOption({
                label: 'That was fine'
              }),
              new ChoiceOption({
                label: 'Less than expected'
              }),
              new ChoiceOption({
                label: 'Worst Experience'
              })
            ]
          }),
          new QuestionModel({
            id: 'last_date',
            title: 'What to feel to be improved in your advertisement.',
            type: QuestionType.LongText,
            required: true,
            placeholder: 'Start typing here...'
          }),
          new QuestionModel({
            id: 'multiple_choices',
            title: 'What is your usual budget for advertisement.',
            type: QuestionType.MultipleChoice,
            multiple: false,
            helpText: 'Select any one that apply. 👇',
            required: true,
            options: [
              new ChoiceOption({
                label: 'Less than 1000'
              }),
              new ChoiceOption({
                label: '1000 - 10,000'
              }),
              new ChoiceOption({
                label: '10,000 - 50,000'
              }),
              new ChoiceOption({
                label: '50,000 - 100,000'
              }),
              new ChoiceOption({
                label: '100,000 and above'
              })
            ]
          })
        ]
      }
    },
    mounted() {
      document.addEventListener('keyup', this.onKeyListener)
    },
    beforeDestroy() {
      document.removeEventListener('keyup', this.onKeyListener)
    },
    methods: {
      onKeyListener($event) {
        // We've overriden the default "complete" slot so
        // we need to implement the "keyup" listener manually.

        if ($event.key === 'Enter' && this.completed && !this.submitted) {
          this.onSendData()
        }
      },

      /* eslint-disable-next-line no-unused-vars */
      onComplete(completed, questionList) {
        // This method is called whenever the "completed" status is changed.
        this.completed = completed
      },

      /* eslint-disable-next-line no-unused-vars */
      onSubmit(questionList) {
        // This method will only be called if you don't override the
        // completeButton slot.
        this.onSendData()
      },
      
      onSendData() {
        // Set `submitted` to true so the form knows not to allow back/forward
        // navigation anymore.
        this.$refs.flowform.submitted = true

        this.submitted = true

        /* eslint-disable-next-line no-unused-vars */
        const data = this.getData()
        console.log("data:", data);
        /*
          You can use Fetch API to send the data to your server, eg.:

          fetch(url, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(data)
          })
        */
      },

      getData() {
        const data = {
          questions: [],
          answers: []
        }

        this.questions.forEach(question => {
          if (question.title) {
            let answer = question.answer
            if (Array.isArray(answer)) {
              answer = answer.join(', ')
            }

            data.questions.push(question.title)
            data.answers.push(answer)
          }
        })

        return data
      }
    },
  }
</script>

<style lang="css">
  @import '../../src/assets/css/themes/theme-minimal.css';
  /* If using the npm package, use the following lines instead of the one above */
  /* @import '~@ditdot-dev/vue-flow-form/dist/vue-flow-form.css'; */
  /* @import '~@ditdot-dev/vue-flow-form/dist/vue-flow-form.theme-minimal.css'; */
</style>