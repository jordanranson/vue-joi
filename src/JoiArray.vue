<template>
  <div>
    <ul v-if="childSchema">
      <li v-for="(item, index) in initialValue">
        <div v-if="childSchema.schemaType === 'string' || childSchema.schemaType === 'number'">
          <span>{{item}}</span> <button v-on:click="remove(index)">x</button>
        </div>
        <div v-if="errors && errors[index]" class="errors">
          {{errors[index].message}}
        </div>
      </li>
      <li>
        <input v-if="childSchema.schemaType === 'string' && !childSchema._valids._set.length"  v-model="tempInput">
        <input v-if="childSchema.schemaType === 'number'" v-model="tempInput">
        <select v-if="childSchema.schemaType === 'string' && childSchema._valids._set.length" v-model="tempInput">
          <option v-for="option in childSchema._valids._set">
             {{ option }}
           </option>
        </select>
        <button v-on:click="add">Add {{singularName}}</button>
        <div v-if="tempError" class="errors">{{tempError}}</div>
      </li>
    </ul>
  </div>
</template>

<script>
  import JoiObject from './JoiObject.vue'
  import pluralize from 'pluralize'

  export default {
    name: 'JoiArray',
    components: { JoiObject },
    props: {
      schema: {
        type: Object
      },
      initialValue: {
        type: Array
      },
      name: {
        type: String
      },
      errors: { type: Array }
    },
    data () {
      return {
        children: null,
        childSchema: null,
        singularName: name,
        tempInput: null,
        tempError: null
      }
    },
    mounted () {
      this.$set(this.$data, 'singularName', pluralize.singular(this.name))
      this.$set(this.$data, 'childSchema', this.schema.items[0])
    },
    methods: {
      remove (index) {
        this.initialValue.splice(index, 1)
      },
      add () {
        this.childSchema.validate(this.tempInput, (err, resp) => {
          if (err) return this.$set(this.$data, 'tempError', err.message)

          this.$set(this.$data, 'tempError', null)
          this.initialValue.push(this.tempInput)
          this.$set(this.$data, 'tempInput', null)

        })

      }
    }
  }
</script>

<style>

</style>
