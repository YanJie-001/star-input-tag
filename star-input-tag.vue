<!-- 
v-model="tagList"  标签内容，tagList为数组或者字符串，字符串每个标签以逗号隔开
theme="+ New Tag"  新增按钮名称，默认为+ New Tag 
created(){   字符串转数组，定义在父组件
	if(typeof this.tagList =='string'){
		this.tagList = this.tagList.split(',');
	}
},
-->
<template>
  <div class="input-tag">
    <el-tag
      v-for="(tag, index) in dynamicTags"
      :key="tag"
      closable
      :disable-transitions="false"
      @click="editTag(tag, index)"
      @close="handleClose(tag)"
    >
      <span v-if="index != num">{{ tag }}</span>
      <input
        class="custom_input"
        type="text"
        v-model.trim="words"
        v-if="index == num"
        ref="editInput"
        @keyup.enter="$event.target.blur()"
        @blur="handleInput(tag, index)"
      />
    </el-tag>
    <el-input
      class="input-new-tag"
      v-if="inputVisible"
      v-model="inputValue"
      ref="saveTagInput"
      size="small"
      @keyup.enter.native="handleInputConfirm"
      @blur="handleInputConfirm"
    >
		</el-input>
    <div class="add-icon" :class="tagList.length>0 || inputVisible ?'ml10':''" v-if="tagList.length < max ">
      <i class="el-icon-circle-plus-outline" @click="showInput"></i>
    </div>
  </div>
</template>

<script>
export default {
  name: "input-tag",
  model: {
    prop: "tagList",
    event: "input",
  },
  props: {
    tagList: {
      type: Array,
      default() {
        return [];
      },
    },
    theme: {
      type: String,
      default: "+ 新标签",
    },
    max:{
      type:Number,
      default:1000
    }
  },
  data() {
    return {
      inputVisible: false,
      inputValue: "",
      num: -1,
      words: "",
    };
  },
  computed: {
    dynamicTags: {
      get() {
        return this.tagList;
      },
      set(tagList) {
        this.$emit("input", tagList);
      },
    },
  },
  methods: {
    // 数组去重
    unique(arr) {
      let x = new Set(arr);
      return [...x];
    },
    handleClose(tag) {
      this.dynamicTags.splice(this.dynamicTags.indexOf(tag), 1);
    },
    showInput() {
      this.inputVisible = true;
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus();
      });
    },
    handleInputConfirm() {
      let inputValue = this.inputValue;
      if (inputValue) {
        this.dynamicTags.push(inputValue);
      }
      this.dynamicTags = this.unique(this.dynamicTags);
      this.inputVisible = false;
      this.inputValue = '';
    },
    editTag(tag, index) {
      this.num = index;
      this.$nextTick((_) => {
        this.$refs.editInput[0].focus();
      });
      this.words = tag;
    },
    handleInput(tag, index) {
      let words = this.words;
      if (words) {
        this.dynamicTags[index] = words;
      }
      this.dynamicTags = this.unique(this.dynamicTags);
      this.words = "";
      this.num = -1;
    }
  }
};
</script>
<style scoped lang="less">
.input-tag {
  display: flex;
  align-items: center;
  height: 40px;
}

.add-icon {
  width: 40px;
  height: 40px;
  font-size: 20px;
  display: flex;
  align-items: center;

  i {
    cursor: pointer;
  }
}
.el-tag {
  // margin-top: 4px ;
}
.el-tag + .el-tag {
  margin-left: 10px;
}

.button-new-tag {
  margin-left: 10px;
  height: 32px;
  line-height: 30px;
  padding-top: 0;
  padding-bottom: 0;
}
.input-new-tag {
  width: 90px;
  margin-left: 10px;
  vertical-align: bottom;

  &:first-child {
    margin-left: 0;
  }
}


.custom_input {
  width: 80px;
  height: 16px;
  outline: none;
  border: transparent;
  background-color: transparent;
  font-size: 12px;
  color: #409eff;
}
</style>
