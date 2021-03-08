
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

  },
  render () {
    return (
      <div class="home">
        <ConditionEditor
          // value={this.conditionValue}
          onChange={this.handleValid}
        >
        </ConditionEditor>
      </div >
    )
  }
}
</script>
