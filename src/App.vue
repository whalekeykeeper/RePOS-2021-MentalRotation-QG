<template>
  <Experiment title="Magpie ! Not the bird but the experiment">
    <InstructionScreen :title="'Welcome to this rotation experiment'">
      This is a sample introduction screen.
      <br />
      <br />
      This screen welcomes the participant and gives general information about
      the experiment.
      <br />
      <br />
      This mock up experiment is a showcase of the functionality of magpie.
    </InstructionScreen>

    <InstructionScreen :title="'General Instructions'">
      
      This is a sample instructions view.
      <br />
      <br />
      First you will go through 12 practice trials. The practice trial view
      uses magpie's forced choice trial input.
    </InstructionScreen>

    <!-- Here we create screens in a loop for every entry in forced_choice -->
    <template v-for="(mr_training_trials_task, i) of mr_training_trials">

      <KeypressScreen
        question="Are they the same object? Press f for yes or j for no."
        :feedback-time="1000"
        :key="mr_training_trials_task.key"
        :keys= "random_keys"
        :fixationTime=getRandomFixationTime()
        :responseTime="7500"
        :feedbackTime="800" 
      >

        <template #stimulus>
          <img :src="mr_training_trials_task.picture"/>
          <Record :data="{
                angle: mr_training_trials_task.angle,
                expected_answer: mr_training_trials_task.expected,
                item_number: mr_training_trials_task.item,
                trial_type: 'training_trial',
                trail_number: i
              }" />
        </template>

        <template #feedback>
            Hello, Feedback!
            <p v-if="!$magpie.measurements.hasOwnProperty('response')">Faster!</p>
            <p v-else-if="$magpie.measurements.response === expected_answer">Your answer is correct!</p>
            <p v-else>Sorry, your answer is wrong.</p>
        </template>

      <!-- <template #feedback>
        You are ??.
      </template> -->
      </KeypressScreen>
      
    </template>
    <PostTestScreen />
    <DebugResultsScreen />
    <!--

      Comment this in, to try out interactive components like the Chat component.

      <ConnectInteractiveScreen />

      <Screen>
          <Chat :messages.sync="$magpie.measurements.messages"></Chat>
          <button @click="$magpie.saveAndNextScreen()">Next</button>
      </Screen>

      -->

    <PostTestScreen />

    <!-- While developing your experiment, using the DebugResults screen is fine,
      once you're going live, you can use the <SubmitResults> screen to automatically send your experimental data to the server. -->
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
// import forced_choice from '../trials/forced_choice.csv';
// import multi_dropdown from '../trials/multi_dropdown.csv';
// import sentenceChoice from '../trials/sentence_choice.csv';
import mr_main_trials from '../trials/mr_main_trials.csv';
import mr_training_trials from '../trials/mr_training_trials.csv';
import _ from 'lodash';

export default {
  name: 'App',
  data() {
    return {
      mr_training_trials: _.shuffle(mr_training_trials),
      mr_main_trials: _.shuffle(mr_main_trials),
      random_keys: _.sample([
        {f: 'same', j: 'different'},
        {f: 'different', j: 'same'},
      ])

      // // Expose lodash.range to template above
      // range: _.range
    };
  },
  methods: {
    getRandomFixationTime() {
      return _.sample(['200','300','400','500'])
    }
  },
  mounted() {
    this.$magpie.addExpData({
      key1 : this.keys.f,
      key2: this.keys.j
    });
  }
};

</script>
