<template>
  <div class="cell medium-8 large-6">
    <p class="score-error" v-if="error">The score could not be calculated due to an error. Please try again.</p>

    <p v-else>Profile sufficiency:
      <span class="score-category">{{ category }}</span>
      <star-rating
        v-model="scaledScore"
        active-color="#5aa4d2"
        :increment="0.5"
        :read-only="true"
        :show-rating="false"
        :inline="true"
        :star-size="16"
        :rounded-corners="true"/>
      <span class="define-score">The profile sufficiency rating represents how diagnostically useful your profile will be based on the symptoms you've added.</span>
    </p>

    <!-- Toggles the list of tips -->
    <p class="display-tips">
      <a href="#"
         role="button"
         aria-controls="symptom-tips"
         :aria-expanded="showTips"
         @keydown.space.prevent="toggleTips"
         @click.prevent="toggleTips">Tips for adding symptoms</a> to help your doctor diagnose you most accurately</p>

    <transition name="collapse">
      <div id="symptom-tips" class="callout secondary" v-if="showTips">
        <ul class="tips-list">
          <li>Enter as many symptoms as you can</li>
          <li>Be as specific as you can be</li>
          <li>Try to cover as many body symptoms as apply to you (e.g. symptoms in the head
            and neck, face, eyes, ears, muscles, joints, bones, internal organs, etc.)</li>
          <li>Don't forget conditions that are not local to a specific body part (e.g. stroke,
            fever, numbness, sensitivity to pain, etc.)</li>
          <li>Don't forget conditions like sleep disturbances, memory loss, cognitive disability,
            developmental delay</li>
        </ul>
      </div>
    </transition>
  </div>
</template>

<script>
import StarRating from 'vue-star-rating';

/**
 * Displays information about the diagnostic usefulness of the selected terms, based on
 * the score obtained from the Monarch Initiative API.
 */
export default {
  name: 'AnnotationSufficiency',

  components: {
    StarRating
  },

  props: {
    score: Number,
    error: Error
  },

  data() {
    return {
      categoryLabels: [
        'Very Poor',
        'Poor',
        'Fair',
        'Good',
        'Very Good'
      ],
      showTips: false
    };
  },

  methods: {
    /**
     * Coerces the input value to a valid index into the `categoryLabels` array.
     * @param {Number} input - an integer value to clamp.
     * @return {Number} an integer from 0 to `categoryCount - 1`
     * @private
     */
    _clampIndex(input) {
      const { categoryCount } = this;
      const maxIndex = categoryCount - 1;

      if (input < 0) {
        return 0;
      } else if (input > maxIndex) {
        return maxIndex;
      } else {
        return input;
      }
    },

    /**
     * Toggles display of the tips for adding symptoms.
     */
    toggleTips() {
      this.showTips = !this.showTips;
    }
  },

  computed: {
    /**
     * Returns the number of score categories.
     * @return {Number}
     */
    categoryCount() {
      const { categoryLabels } = this;
      return categoryLabels.length;
    },

    /**
     * Scales the score, which ranges from 0 to 1 to a number between 0 and 5.
     * @return {Number}
     */
    scaledScore() {
      const { score, categoryCount } = this;
      return score * categoryCount;
    },

    /**
     * Returns the category the score falls into, as a string.
     * @return {String} a string in `categoryLabels`
     */
    category() {
      const { scaledScore, categoryLabels } = this;
      const categoryIndex = this._clampIndex(Math.floor(scaledScore));
      return categoryLabels[categoryIndex];
    }
  }
};
</script>
