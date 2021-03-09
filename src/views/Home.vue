
<script>
import ConditionEditor from "@/components/conditionEditor/ConditionEditor.vue";

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

    handleValid (val) {
      console.log(val);
    },
    handleChangeField ({ key, field, position, callback }) {
      if (field === 'fieldName' && key === 'a2') {
        callback({
          tag: "a-select",
          props: {
            options: [{ value: "4", label: '4' }]
          },
          style: {
            width: '100px',
          }
        })
      }
    },
  },
  render () {
    return (
      <div class="home">
        <ConditionEditor
          value={this.conditionValue}
          fieldNameOptions={[{ label: "a1", value: 'a1' }, { label: "a2", value: 'a2' }, { label: "a3", value: 'a3' }, { label: "a4", value: 'a4' }]}
          operatorOptions={[{ label: "=", value: '==' }, { label: ">", value: '>' }, { label: "<", value: '<' }, { label: ">=", value: '>=' }]}
          onChange={this.handleLog}
          onChangeField={this.handleChangeField}
        />
      </div >
    )
  }
}
</script>
