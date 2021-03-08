
<script>
import ConditionItem from "./childCamps/ConditionItem.vue";
import { cloneDeep } from "lodash";

export default {
  name: 'ConditionEditor',
  props: {
    value: {
      type: Array,
      default: () => []
    }
  },
  components: {
    ConditionItem
  },
  data () {
    return {
      conditionValue: [{
        type: "and",
        condition: [{
          fieldName: "",
          operator: '',
          value: ''
        }]
      }]
    }
  },
  methods: {
    /**
     * 修改条件的逻辑类型
     */
    changeType ({ key }, position) {
      this.conditionValue = this.literator(this.conditionValue, position, 'type', key)
      this.$emit('change', this.conditionValue);
    },
    /**
    * 修改条件字段
    */
    changeField ({ key, field, position }) {
      this.conditionValue = this.literator(this.conditionValue, position, field, key)
      this.$emit('change', this.conditionValue);
    },
    /**
    * 修改条件的方法
    */
    literator (value, position, field, key) {
      let cacheValue = cloneDeep(value)
      if (position.length > 1) {
        let cachePosition = cloneDeep(position)
        cachePosition.shift()
        cacheValue[position[0]].condition = this.literator(cacheValue[position[0]].condition, cachePosition, field, key)
      } else {
        cacheValue[position[0]][field] = key
      }
      return cacheValue
    },
    /**
    * 删除条件
    */
    handleDelete (position) {
      this.conditionValue = this.deleteLiterator(this.conditionValue, position)
      if (this.conditionValue.length === 0) {
        this.conditionValue = [{
          type: "and",
          condition: []
        }]
      }
      this.$emit('change', this.conditionValue);
    },
    /**
    * 删除条件/条件组的方法
    */
    deleteLiterator (value, position) {
      let cacheValue = cloneDeep(value)
      if (position.length > 1) {
        let cachePosition = cloneDeep(position)
        cachePosition.shift()
        let child = this.deleteLiterator(cacheValue[position[0]].condition, cachePosition)
        // 如果条件组下没有条件，则删除组
        if (child.length === 0) {
          cacheValue.splice(position[0], 1)
        } else {
          cacheValue[position[0]].condition = child
        }
      } else {
        // 删除指定的条件
        cacheValue.splice(position[0], 1)
      }
      return cacheValue
    },
    /**
    * 新增条件
    */
    handleAdd ({ position, type }) {
      this.conditionValue = this.addLiterator(this.conditionValue, position, type)
    },
    /**
     * 添加条件/条件组的方法
     */
    addLiterator (value, position, type) {
      let cacheValue = cloneDeep(value)
      if (position.length > 0) {
        let cachePosition = cloneDeep(position)
        cachePosition.shift()
        cacheValue[position[0]].condition = this.addLiterator(cacheValue[position[0]].condition, cachePosition, type)
      } else {
        if (!type) {
          // 添加一行条件
          cacheValue.push({
            fieldName: "",
            operator: '',
            value: ''
          })
        } else {
          // 添加一行条件
          cacheValue.push({
            type: "and",
            condition: [{
              fieldName: "",
              operator: '',
              value: ''
            }]
          })
        }
      }
      return cacheValue
    },
    /**
    * arr转string
    */
    transferValue () {
      let valueFormatter = this.conditionLiterator(this.conditionValue)
      console.log(valueFormatter);
    },
    /**
    * 条件编辑器格式化 - arr转string
    */
    conditionLiterator (arr, type = 'and') {
      return cloneDeep(arr).map(item => {
        if (item.type) {
          return ` ( ${this.conditionLiterator(item.condition, item.type)} ) `
        } else {
          return ` ${item.fieldName} ${item.operator} ${item.value} `
        }
      }).join(type === 'and' ? '&&' : '||')
    },
    /**
     * 渲染组
     */
    renderGroup (condition, type = 'and', position) {
      return (
        <div class='condition-group'>
          {/**按钮栏 */}
          <div class='condition-group-controller'>
            <a-dropdown>
              <a-menu slot="overlay" onClick={($event) => this.changeType($event, position)}>
                <a-menu-item key="and">and</a-menu-item>
                <a-menu-item key="or"> or</a-menu-item>
              </a-menu>
              <a-button style="margin-left: 8px"> {type} <a-icon type="down" /> </a-button>
            </a-dropdown>
            <div>
              <a-button type='primary' style='marginRight:5px' onClick={() => { this.handleAdd({ position }) }}>添加条件</a-button>
              <a-button type='primary' onClick={() => { this.handleAdd({ position, type: 'group' }) }}>添加一组条件</a-button>
            </div>
          </div>
          {/** 内容栏 */}
          <div>
            {condition.map((item, index) => {
              if (item.type) {
                return this.renderGroup(item.condition, item.type, [...position, index])
              }
              return <ConditionItem
                value={item}
                position={[...position, index]}
                onChangeField={this.changeField}
                onHandleDelete={this.handleDelete}
              ></ConditionItem>
            })}
          </div>
        </div>
      )
    }
  },
  mounted () {

  },
  watch: {
    value: {
      handler (val) {
        if (val.length > 0) {
          this.conditionValue = val
        }
      },
      immediate: true,
      deep: true
    }
  },
  render () {
    return (
      < div >
        {
          this.conditionValue.map((item, index) => {
            return this.renderGroup(item.condition, item.type, [index])
          })
        }
      </ div>
    )
  }
}
</script>

<style scoped>
.condition-group {
  background-color: rgba(253, 248, 234, 0.4);
  border: 1px solid #ede3ca;
  padding: 10px;
  margin: 10px;
}
.condition-group-controller {
  display: flex;
  margin: 10px;
  justify-content: space-between;
}
</style>