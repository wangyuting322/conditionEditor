<script>
export default {
  name: 'ConditionItem',
  props: {
    value: {
      type: Object,
      default: () => ({})
    },
    position: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {

    }
  },
  methods: {
    handleChange (e, field) {
      let key = typeof e === 'string' ? e : e.target.value
      this.$emit('changeField', { key, field, position: this.position })
    }
  },
  render () {
    return (
      <div class='condition-item'>
        {/**字段选择栏 */}
        <div>
          <a-select
            style='width:100px;marginRight:5px'
            options={[{ label: "a1", value: 'a1' }, { label: "a2", value: 'a2' }, { label: "a3", value: 'a3' }, { label: "a4", value: 'a4' }]}
            default-value={this.value.fieldName}
            onChange={($event) => { this.handleChange($event, 'fieldName') }}
          />
          <a-select
            style='width:100px;marginRight:5px'
            options={[{ label: "=", value: '==' }, { label: ">", value: '>' }, { label: "<", value: '<' }, { label: ">=", value: '>=' }]}
            default-value={this.value.operator}
            onChange={($event) => { this.handleChange($event, 'operator') }}
          />
          <a-input
            style='width:100px;marginRight:5px'
            default-value={this.value.value}
            onChange={($event) => { this.handleChange($event, 'value') }}
          />
        </div>
        {/**按钮栏 */}
        <div>
          <a-button onClick={() => { this.$emit('handleDelete', this.position) }} type='danger'>删除</a-button>
        </div>
      </div>
    )
  }
}
</script>
<style scoped>
.condition-item {
  background-color: white;
  display: flex;
  justify-content: space-between;
  margin: 10px;
}
</style>