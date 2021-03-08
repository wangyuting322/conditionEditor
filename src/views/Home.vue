
<script>
import ConditionEditor from "@/components/conditionEditor/ConditionEditor.vue";
import { cloneDeep } from "lodash";

export default {
  name: 'Home',
  components: {
    ConditionEditor
  },
  data () {
    return {
      conditionValue: [
        {
          type: 'or',
          condition: [
            {
              type: 'and',
              condition: [
                {
                  type: 'or',
                  condition: [
                    {
                      fieldName: "a1",
                      operator: '==',
                      value: '1'
                    },
                    {
                      fieldName: "a1",
                      operator: '==',
                      value: 'aa'
                    }
                  ]
                },
                {
                  fieldName: "a3",
                  operator: '>=',
                  value: '10'
                }
              ]
            },
            {
              fieldName: "a4",
              operator: '<',
              value: '5'
            }
          ]
        }
      ]
    }
  },
  computed: {

  },
  methods: {
    /**
     * 修改条件的逻辑类型
     */
    changeType (key, position) {
      this.conditionValue = this.literator(this.conditionValue, position, 'type', key)
    },
    /**
     * 修改条件字段
     */
    changeField ({ key, field, position }) {
      this.conditionValue = this.literator(this.conditionValue, position, field, key)
    },
    /**
     * 修改条件
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
    },
    /**
    * 删除条件/条件组
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
     * 添加条件/条件组
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
    handleValid () {
      console.log(this.conditionValue);
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
    }
  },
  render () {
    return (
      <div class="home">
        <ConditionEditor
          value={this.conditionValue}
          onChangeType={this.changeType}
          onChangeField={this.changeField}
          onHandleDelete={this.handleDelete}
          onAddCondition={this.handleAdd}
        >
        </ConditionEditor>

        <a-button onClick={this.handleValid}>输出数组</a-button>
        <a-button onClick={this.transferValue}>输出字符串</a-button>
      </div >
    )
  }
}
</script>
