<template>
  <div class="calc">
    <div class="display">
      <DisplayScreen :currentValue="currentValue" />
    </div>
    <div class="keypad">
      <div class="keypad-row">
        <Button label="AC" value="AC" doubleSpace @handleClick="cleanAll" />
        <Button label="C" value="C" @handleClick="clean"/>
        <Button label="+/-" value="+/-" operation @handleClick="changeSign" />
      </div>
      <div class="keypad-row">
        <Button label="7" value="7" @handleClick="digit" />
        <Button label="8" value="8" @handleClick="digit" />
        <Button label="9" value="9" @handleClick="digit" />
        <Button label="÷" value="/" operation @handleClick="setOperation"/>
      </div>
      <div class="keypad-row">
        <Button label="4" value="4" @handleClick="digit" />
        <Button label="5" value="5" @handleClick="digit" />
        <Button label="6" value="6" @handleClick="digit" />
        <Button label="-" value="-" operation @handleClick="setOperation" />
      </div>
      <div class="keypad-row">
        <Button label="1" value="1" @handleClick="digit" />
        <Button label="2" value="2" @handleClick="digit" />
        <Button label="3" value="3" @handleClick="digit" />
        <Button label="+" value="+" operation @handleClick="setOperation" />
      </div>
      <div class="keypad-row">
        <Button label="0" value="0" doubleSpace @handleClick="digit" />
        <Button label="." value="." @handleClick="addDecimal" />
        <Button label="=" value="=" operation @handleClick="equal" />
      </div>
    </div>
  </div>
</template>

<script>
import DisplayScreen from '../components/DisplayScreen'
import Button from '../components/Button'
import { evaluate } from 'mathjs'

export default {
  name: 'Calculator',
  components: {
    DisplayScreen,
    Button
  },
  data: function () {
    return {
      currentValue: '0',
      values: ['0', '0'],
      indexValue: 0,
      operation: null,
      operationOn: false,
      sign: false
    }
  },
  methods: {
    digit (digit) {
      if (this.operationOn) {
        this.currentValue = ''
        this.operationOn = false
      }

      if (this.values[this.indexValue].length > 7) return

      if (this.values[this.indexValue].indexOf('.') >= 0) {
        if (this.values[this.indexValue].substr(this.values[this.indexValue].indexOf('.'), 4).length > 3) return
      }

      this.currentValue = this.currentValue === '0' ? digit : this.currentValue + digit
      this.values[this.indexValue] = this.currentValue
    },
    setOperation (op) {
      if (this.currentValue === 'ERR') return

      if (this.currentValue !== '0') {
        if (this.values[0] !== '0' && this.values[1] !== '0') {
          this.equation()
          this.operation = op
          this.operationOn = true
          this.indexValue = 1
          return
        }
        this.operation = op
        this.operationOn = true
        this.indexValue = 1
      }
    },
    equation () {
      this.indexValue = 0
      if (this.currentValue === 'ERR') return
      const result = evaluate(`${this.values[0]} ${this.operation} ${this.values[1]}`)
      const resultFixed = result.toFixed(3)

      if (resultFixed.toString().length < 9) {
        this.values[0] = parseFloat(resultFixed).toString()
        this.values[1] = '0'
        this.currentValue = this.values[0]
      } else {
        this.currentValue = 'ERR'
      }
    },
    equal () {
      if (this.currentValue === 'ERR') return

      if (this.operation) {
        this.equation()
      }
    },
    clean () {
      if (this.operationOn) {
        this.operation = null
        this.operationOn = false
      } else {
        this.currentValue = '0'
        this.operation = null
        this.values = ['0', '0']
        this.indexValue = 0
      }
    },
    cleanAll () {
      this.currentValue = '0'
      this.values = ['0', '0']
      this.indexValue = 0
      this.operation = false
      this.sign = false
    },
    changeSign () {
      if (this.currentValue === '0') return
      if (this.currentValue === 'ERR') return

      this.sign = !this.sign
      if (this.values[1] === '0') {
        this.currentValue = this.sign ? `-${this.currentValue}` : this.currentValue.match(/[\d.]+/g).pop()
        this.values[0] = this.currentValue
        return
      }

      this.currentValue = this.sign ? `-${this.currentValue}` : this.currentValue.match(/[\d.]+/g).pop()
      this.values[this.indexValue] = this.currentValue
    },
    addDecimal (dot) {
      if (this.values[this.indexValue].length > 7) return
      if (this.values[this.indexValue].includes('.')) return
      if (this.currentValue === 'ERR') return

      this.currentValue = this.currentValue !== '0' ? this.currentValue + dot : this.currentValue + dot
      this.values[this.indexValue] = this.currentValue
    }
  }
}
</script>

<style>
  .calc{
    display: flex;
    flex-direction: column;
    width: 300px;
    height: 460px;
    border-radius: 8px;
    background-color: #cdcdcd;
    overflow: hidden;
    box-shadow: 0 14px 28px rgba(0, 0, 0, 0.4);
  }

  .display {
    display: flex;
    flex-direction: column;
    width: 100%;
    height: 140px;
    background-color: #09203f;
    padding: 10px;
  }

  .keypad {
    display: flex;
    flex-direction: column;
    width: 100%;
  }

  .keypad-row {
    display: flex;
    flex-direction: row;
    height: 64px;
    width: 100%;
  }
</style>
