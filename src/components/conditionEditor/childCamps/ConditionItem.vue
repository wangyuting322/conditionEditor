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
    },
    /**
    * 字段名下拉框的所有数据
    */
    fieldNameOptions: {
      type: Array,
      default: () => []
    },
    /**
     * 关系运算符下拉框的所有数据
     */
    operatorOptions: {
      type: Array,
      default: () => []
    },
  },
  data () {
    return {

    }
  },
  methods: {
    handleChange (e, field) {
      let key = typeof e === 'string' ? e : e.target.value
      this.$emit('changeField', { key, field, position: this.position })
    },
    /**
     * 渲染第三项
     */
    renderThird () {
      if (this.value.customTag) {
        let { tag, props, on, style, domProps } = { ...this.value.customTag }
        return <this.value.customTag.tag {...
          {
            props: {
              value: this.value.value,
              ...props
            },
            on: {
              change: ($event) => { this.handleChange($event, 'value') },
              ...on
            },
            domProps: {
              ...domProps
            },
            style: {
              ...style
            }
          }} />
      }
      return <a-input
        style='width:100px;marginRight:5px'
        default-value={this.value.value}
        onChange={($event) => { this.handleChange($event, 'value') }}
      />
    }
  },
  render () {
    return (
      <div class='condition-item'>
        {/**字段选择栏 */}
        <div>
          <a-select
            style='width:100px;marginRight:5px'
            options={this.fieldNameOptions}
            default-value={this.value.fieldName}
            onChange={($event) => { this.handleChange($event, 'fieldName') }}
          />
          <a-select
            style='width:100px;marginRight:5px'
            options={this.operatorOptions}
            default-value={this.value.operator}
            onChange={($event) => { this.handleChange($event, 'operator') }}
          />
          {this.renderThird()}

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