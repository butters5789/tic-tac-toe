<template>
  <div class="box" @click.once="selectThisBox">
    {{ boxContent }}
  </div>
</template>

<script>
export default {
  name: 'box-component',
  props: {
    whosTurn: {
      type: String,
      required: true,
    },
    row: {
      type: Number,
      required: true,
    },
    col: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      boxContent: '',
    };
  },
  methods: {
    selectThisBox() {
      this.boxContent = this.whosTurn;
      this.$emit('boxComponent:logMove', this.row, this.col);
    },
  },
};
</script>

<style>
.box {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 5rem;
  height: 5rem;
  font-size: 2rem;
  border-right: 1px solid black;
}

.box:last-child {
  border: none;
}
</style>
