<template>
    <div>
      <h2>加密/解密系统</h2>
      <select v-model="selectedCategory" @change="updateAlgorithms">
        <option value="" disabled selected>选择算法类别</option>
        <option v-for="category in categories" :key="category.id" :value="category.value">
          {{ category.label }}
        </option>
      </select>
  
      <select v-model="selectedAlgorithm" :disabled="!selectedCategory">
         <option value="" disabled selected>选择具体算法</option>
        <option v-for="algorithm in filteredAlgorithms" :key="algorithm.id" :value="algorithm.value">
          {{ algorithm.label }}
        </option>
      </select>
  
      <input type="text" v-model="key" placeholder="密钥">
      <textarea v-model="inputText" placeholder="输入文本"></textarea>
      <button @click="processData">
        {{ isEncrypting ? '加密' : '解密' }}
      </button>
      <textarea v-model="outputText" placeholder="输出文本" readonly></textarea>
      <div>
        <label><input type="radio" value="encrypt" v-model="processMode">加密</label>
        <label><input type="radio" value="decrypt" v-model="processMode">解密</label>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
          selectedCategory: '',
          selectedAlgorithm: '',
          key: "",
          inputText: "",
          outputText: "",
          isEncrypting: true,
          processMode: 'encrypt',
          categories: [
            { id: 1, label: '古典密码', value: 'classical' },
            { id: 2, label: '对称密码', value: 'symmetric' },
            { id: 3, label: '非对称密码', value: 'asymmetric' },
            { id: 4, label: '流密码', value: 'stream' },
             { id: 5, label: '哈希密码', value: 'hash' },
             {id:6,label:'python算法',value:'python'}
          ],
                          //   id分级处**************************
          algorithms: {
            'classical': [
              { id: 1, label: 'Playfair', value: 'Playfair' },
              { id: 2, label: 'Hill', value: 'Hill' },
              {id:3,label:'无密钥换位',value:'无密钥换位'},
              {id:4,label:'单表代替密码',value:'单表代替密码'},
              {id:5,label:'有密钥换位',value:'有密钥换位'},
              {id:6,label:'仿射密码',value:'仿射密码'},
              {id:7,label:'维吉尼亚算法',value:'维吉尼亚算法'},

            ],
            'symmetric': [
              { id: 8, label: 'AES', value: 'AES' },
              { id: 9, label: 'DES', value: 'DES' },
            ],
            'asymmetric': [
              { id: 10, label: 'RSA', value: 'RSA' },
              { id: 11, label: 'ECC', value: 'ECC' },
            ],
            'stream': [
                 { id: 12, label: 'RC4', value: 'RC4' },
             ],
              'hash':[
                 { id: 13, label: 'SHA-1', value: 'SHA-1' },
              ],
               'python':[
                 { id: 14, label: 'your_python_algorithm', value: 'your_python_algorithm' }
              ]
  
          }
        };
    },
     computed:{
      filteredAlgorithms() {
        return this.algorithms[this.selectedCategory] || [];
      },
      },
       methods: {
      updateAlgorithms(){
          this.selectedAlgorithm = ''
      },
          async processData() {
              this.isEncrypting = this.processMode === 'encrypt'
               try{
                      const response = await axios.post('http://localhost:5000/process', {
                          text: this.inputText,
                          key: this.key,
                          encrypt: this.isEncrypting,
                          selectedAlgorithm: this.selectedAlgorithm,
                           selectedCategory: this.selectedCategory
                      });
                      this.outputText = response.data.result;
                     } catch (error) {
                          console.error('请求 Python 后端失败:', error);
                          this.outputText = '请求 Python 后端失败，请检查终端';
                      }
          },
      },
  }
  </script>
  <style scoped>
  div {
  margin: 10px;
  }
  input, textarea, select {
  display: block;
  margin-bottom: 10px;
  padding: 5px;
  width: 300px;
  }
  button {
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
  }
  </style>