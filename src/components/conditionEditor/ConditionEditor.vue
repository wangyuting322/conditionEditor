
<script>
import ConditionItem from "./childCamps/ConditionItem.vue";

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


    }
  },
  methods: {
    /**
     * 修改条件的逻辑类型
     */
    handleMenuClick ({ key }, position) {
      this.$emit('changeType', key, position);
    },
    /**
     * 添加条件
     */
    handleAddCondition (position) {
      this.$emit('addCondition', position)
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
              <a-menu slot="overlay" onClick={($event) => this.handleMenuClick($event, position)}>
                <a-menu-item key="and">and</a-menu-item>
                <a-menu-item key="or"> or</a-menu-item>
              </a-menu>
              <a-button style="margin-left: 8px"> {type} <a-icon type="down" /> </a-button>
            </a-dropdown>
            <div>
              <a-button type='primary' style='marginRight:5px' onClick={() => this.$emit('addCondition', { position })}>添加条件</a-button>
              <a-button type='primary' onClick={() => this.$emit('addCondition', { position, type: 'group' })}>添加一组条件</a-button>
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
                onChangeField={($event) => { this.$emit('changeField', $event) }}
                onHandleDelete={($event) => { this.$emit('handleDelete', $event) }}
              ></ConditionItem>
            })}
          </div>
        </div>
      )
    }
  },
  mounted () {

  },
  render () {
    return (
      < div >
        {
          this.value.map((item, index) => {
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