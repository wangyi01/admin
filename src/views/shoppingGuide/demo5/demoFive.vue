<template>
  <div>
    <el-form
      :model="ruleForm"
      :rules="rules"
      ref="ruleForm"
      label-width="120px"
      class="demo-ruleForm"
    >
      <el-form-item label="名称展示" class="show" prop="showName">
        <el-checkbox v-model="ruleForm.showName" @change="isShow()">展示</el-checkbox>
      </el-form-item>

      <el-form-item label="导购名称" prop="name" class="show">
        <el-input v-model="ruleForm.name" placeholder="请输入导购名称"></el-input>
      </el-form-item>
      <el-form-item label="商圈" prop="type" class="show">
        <el-checkbox-group v-model="ruleForm.type" @change="chooseArea(ruleForm.type)">
          <el-checkbox
            v-for="(item,index) in areaLists"
            :label="item.traName"
            :key="index"
            name="type"
            @change="chooseArea1(item)"
          ></el-checkbox>
        </el-checkbox-group>
      </el-form-item>
      <el-form-item label="开始时间" required>
        <el-col :span="11">
          <el-form-item prop="startTime">
            <el-date-picker
              type="datetime"
              placeholder="选择时间日期"
              v-model="ruleForm.startTime"
              style="width: 100%;"
              default-time="16:00:00"
              @change="startTime(ruleForm.startTime)"
              value-format="yyyy-MM-dd HH:mm:ss"
            ></el-date-picker>
          </el-form-item>
        </el-col>
      </el-form-item>
      <el-form-item label="结束时间" required>
        <el-col :span="11">
          <el-form-item prop="endTime">
            <el-date-picker
              type="datetime"
              placeholder="选择时间日期"
              v-model="ruleForm.endTime"
              style="width: 100%;"
              default-time="16:00:00"
              value-format="yyyy-MM-dd HH:mm:ss"
              @change="endTime(ruleForm.endTime)"
            ></el-date-picker>
          </el-form-item>
        </el-col>
      </el-form-item>
      <el-form-item label="活动1" required>
        <el-form-item label="活动图片" prop="fileOne" class="show">
          <el-upload
            class="upload-demo"
            :action="upImgUrl"
            :headers="headers"
            :on-remove="removeOne"
            :before-remove="beforeRemoveOne"
            :on-success="imgOne"
            :limit="1"
            :file-list="fileOne"
            name="file"
          >
            <el-button type="primary" size="mini">上传图片</el-button>
            <span slot="tip" class="el-upload__tip">只能上传1张图片</span>
          </el-upload>
        </el-form-item>
        <el-form-item label="跳转页面" class="show">
          <el-radio-group
            v-model="oneChoose.type"
            @change="chooseTypes(oneChoose.type,1)"
            style="margin-right:10px;"
          >
            <el-radio v-for="(item,index) in jumpType" :label="item.name" :key="index"></el-radio>
          </el-radio-group>
          <el-select
            style="display:block"
            v-model="oneChoose.selectText"
            placeholder="请选择"
            @change="selectPage(oneChoose.selectText,1)"
            v-if="oneChoose.showSelect"
          >
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
          <!-- <el-input placeholder="搜索专题名称" v-if="showSpecial"></el-input> -->
          <el-autocomplete
            v-if="oneChoose.special"
            v-model="oneChoose.topicName"
            :fetch-suggestions="querySearchAsync"
            placeholder="请输入专题名称"
            @select="select1"
          ></el-autocomplete>
          <el-button type="primary" v-if="oneChoose.special" @click="toSpecialGuide">创建新专题</el-button>
          <el-input
            placeholder="页面可根据数据变化动态显示"
            @blur="h5Path(1)"
            v-model="oneChoose.path"
            v-if="oneChoose.param"
          ></el-input>
        </el-form-item>
      </el-form-item>
      <el-form-item label="活动2" required>
        <el-form-item label="活动图片" class="show">
          <el-upload
            class="upload-demo"
            :action="upImgUrl"
            :headers="headers"
            :on-remove="removeTwo"
            :before-remove="beforeRemoveTwo"
            :on-success="imgTwo"
            :limit="1"
            :file-list="fileTwo"
            name="file"
          >
            <el-button type="primary" size="mini">上传图片</el-button>
            <span slot="tip" class="el-upload__tip">只能上传1张图片</span>
          </el-upload>
        </el-form-item>
        <el-form-item label="跳转页面" class="show">
          <el-radio-group
            v-model="twoChoose.type"
            @change="chooseTypes(twoChoose.type,2)"
            style="margin-right:10px;"
          >
            <el-radio v-for="(item,index) in jumpType" :label="item.name" :key="index"></el-radio>
          </el-radio-group>
          <el-select
            style="display:block"
            v-model="twoChoose.selectText"
            placeholder="请选择"
            @change="selectPage(twoChoose.selectText,2)"
            v-if="twoChoose.showSelect"
          >
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
          <el-autocomplete
            v-model="twoChoose.topicName"
            :fetch-suggestions="querySearchAsync"
            placeholder="请输入专题名称"
            @select="select2"
            v-if="twoChoose.special"
          ></el-autocomplete>
          <el-button type="primary" v-if="twoChoose.special" @click="toSpecialGuide">创建新专题</el-button>
          <el-input
            placeholder="页面可根据数据变化动态显示"
            @blur="h5Path(2)"
            v-model="twoChoose.path"
            v-if="twoChoose.param"
          ></el-input>
        </el-form-item>
      </el-form-item>
      <el-form-item label="活动3" required>
        <el-form-item label="活动图片" class="show">
          <el-upload
            class="upload-demo"
            :action="upImgUrl"
            :headers="headers"
            :on-remove="removeThree"
            :before-remove="beforeRemoveThree"
            :on-success="imgThree"
            :limit="1"
            :file-list="fileThree"
            name="file"
          >
            <el-button type="primary" size="mini">上传图片</el-button>
            <span slot="tip" class="el-upload__tip">只能上传1张图片</span>
          </el-upload>
        </el-form-item>
        <el-form-item label="跳转页面" class="show">
          <el-radio-group
            v-model="threeChoose.type"
            @change="chooseTypes(threeChoose.type,3)"
            style="margin-right:10px;"
          >
            <el-radio v-for="(item,index) in jumpType" :label="item.name" :key="index"></el-radio>
          </el-radio-group>
          <el-select
            style="display:block"
            v-model="threeChoose.selectText"
            placeholder="请选择"
            @change="selectPage(threeChoose.selectText,3)"
            v-if="threeChoose.showSelect"
          >
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
          <el-autocomplete
            v-if="threeChoose.special"
            v-model="threeChoose.topicName"
            :fetch-suggestions="querySearchAsync"
            placeholder="请输入专题名称"
            @select="select3"
          ></el-autocomplete>
          <el-button type="primary" v-if="threeChoose.special" @click="toSpecialGuide">创建新专题</el-button>
          <el-input
            placeholder="页面可根据数据变化动态显示"
            @blur="h5Path(3)"
            v-model="threeChoose.path"
            v-if="threeChoose.param"
          ></el-input>
        </el-form-item>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">保存</el-button>
        <el-button @click="cancel()">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { Message } from "element-ui";
import { checkSpecial } from "@/api/shoppingGuide";
import { create } from "@/api/shoppingGuide";
import { clearTimeout, setTimeout } from "timers";
export default {
  name: "demoFive",
  props: {
    areaLists: Array
  },
  data() {
    return {
      timer: null,
      ruleForm: {
        showName: false, //是否名称展示
        name: "", //导购名称
        startTime: "", //开始时间
        endTime: "", //结束时间
        type: [], //选择商圈
        // resource: "模版1", //默认模版
        fileList: [], //显示在页面上图片
        submitImg: "", //初阶上转的图片
        path: "", //页面路径
        goods: "" //是否展示商品
      },
      rules: {
        name: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { max: 10, message: "最多10个字", trigger: "blur" }
        ],
        startTime: [
          {
            type: "string",
            required: true,
            message: "请选择开始日期",
            trigger: "change"
          }
        ],
        endTime: [
          {
            type: "string",
            required: true,
            message: "请选择结束时间",
            trigger: "change"
          }
        ],
        type: [
          {
            type: "array",
            required: true,
            message: "请至少选择一个投放商圈",
            trigger: "change"
          }
        ],
        showName: [
          {
            type: "boolean",
            required: true,
            message: "请选择是否展示名称",
            trigger: "change"
          }
        ],
        // resource: [
        //   { required: true, message: "请选择使用模版", trigger: "change" }
        // ],
        // fileList: [
        //   {
        //     message: "请上传图片",
        //     trigger: "successImg"
        //   }
        // ],
        goods: [
          { required: true, message: "请至少选择一项", trigger: "change" }
        ]
      },
      oneChoose: {
        type: "", //app还是h5
        topicName: "", //专题名称
        topicId: "",
        showSelect: false, //app下拉框显示
        selectText: "", //下拉框选中的值
        special: false, //专题搜索框和专题跳转按钮显示
        param: false, //h5输入框显示
        path: "", //h5路径
        picUrl: ""
      },
      twoChoose: {
        type: "",
        topicName: "",
        topicId: "",
        showSelect: false,
        selectText: "",
        special: false,
        param: false,
        path: "",
        picUrl: ""
      },
      threeChoose: {
        type: "",
        topicName: "",
        topicId: "",
        showSelect: false,
        selectText: "",
        special: false,
        param: false,
        path: "",
        picUrl: ""
      },
      headers: { sessionId: localStorage.getItem(`sessionId`) },
      upImgUrl: `${process.env.VUE_APP_BASE_URL}support/uploadPic`,
      fileOne: [], //装图片
      fileTwo: [],
      fileThree: [],
      searchLists: [], //模糊搜索的数据
      jumpType: [{ name: "APP" }, { name: "H5" }],
      options: [
        {
          value: 2,
          label: "返利商品"
        },
        {
          value: 3,
          label: "我的红包"
        },
        {
          value: 4,
          label: "充值"
        },
        {
          value: 5,
          label: "我的钱包"
        },
        {
          value: 6,
          label: "我的订单"
        },
        {
          value: 11,
          label: "店铺信息"
        },
        {
          value: 12,
          label: "设置页面"
        },
        {
          value: 16,
          label: "专题详情"
        }
      ]
    };
  },
  methods: {
    toSpecialGuide(){
      this.$router.push("/specialinfor");
    },
    isShow() {
      console.log(`是否展示`);
      console.log(this.ruleForm.showName);
    },
    chooseArea(areaName) {
      // console.log(areaName);
      // areaName.length &&
      //   areaName.forEach(el => {
      //     this.areaLists.length &&
      //       this.areaLists.forEach(el1 => {
      //         if (el === el1.traName) {
      //           el1.checked = true;
      //         }
      //       });
      //   });
      // console.warn(this.areaLists);
    },
    chooseArea1(item){
      this.areaLists.length&&this.areaLists.forEach(el=>{
        if(el.traName===item.traName){
          item.checked=!item.checked;
        };
      });
      console.log(`最新数据`);
      console.log(this.areaLists);
    },
    //开始时间
    startTime(date) {
      console.info(`开始时间为${date}`);
    },
    endTime(date) {
      console.info(`结束时间为${date}`);
    },
    cancel() {
      this.$router.push("/ShoppingGuide");
    },
    submitForm(formName) {
      // this.subData();
      clearTimeout(this.timer);
      this.$refs[formName].validate(valid => {
        if (valid) {
          // alert("submit!");
          this.timer = setTimeout(() => {
            this.subData();
          }, 500);
        } else {
          console.log("error submit!!");
          return false;
        };
      });
    },
    //提交信息
    subData() {
      let lists = [],
        activity = [],
        fanllyLists = [];
      this.areaLists.forEach(el => {
        lists.push({ checked: el.checked, traId: el.traId });
      });
      Promise.all([
        this.checkActivityData1(),
        this.checkActivityData2(),
        this.checkActivityData3()
      ]).then(
        res => {
          console.log(res);
          // this.fanlly=[{
          //   actionContent:path||selectText,
          //   actionType:type,
          //   picUrl:picUrl,
          //   actionParam:topicId
          // }];
          if (this.oneChoose.type === "APP") {
            this.oneChoose.path = "";
          } else {
            this.oneChoose.selectText = "";
            this.oneChoose.topicId = "";
          }
          if (this.twoChoose.type === "APP") {
            this.twoChoose.path = "";
          } else {
            this.twoChoose.selectText = "";
            this.twoChoose.topicId = "";
          }
          if (this.threeChoose.type === "APP") {
            this.threeChoose.path = "";
          } else {
            this.threeChoose.selectText = "";
            this.threeChoose.topicId = "";
          }
          activity = [this.oneChoose, this.twoChoose, this.threeChoose];
          activity.forEach(el => {
            // console.log(el);
            fanllyLists.push({
              actionType: el.type,
              actionContent: el.type === "APP" ? el.selectText : el.path,
              picUrl: el.picUrl,
              actionParam: el.topicId
            });
          });
          console.log(fanllyLists);
          let params = {
            templateCode: "T5",
            guideNameDisplay: this.ruleForm.showName ? 1 : 0, //是否名称展示
            startTime: this.ruleForm.startTime,
            endTime: this.ruleForm.endTime,
            guideName: this.ruleForm.name, //导购名称
            traSelectionList: lists, //选择的商圈
            actionList: fanllyLists
          };
          console.log(params);
          return create(params);
        },
        error => {}
      ).then(res=>{
        console.log(res.data);
        if(res.data.statusCode===2000){
          this.$message({
            message:`创建成功`
          });
          this.$router.push('/shoppingGuide');
        }else{
          this.$message({
            message:res.data.msg
          });
        };
      },error=>{
        console.log(error);
      });
    },
    checkActivityData1() {
      let promise = new Promise((resolve, reject) => {
        if (this.oneChoose.picUrl === "") {
          this.$message({ message: `请上传活动一的图片` });
        } else {
          if (this.oneChoose.type === "APP") {
            if (this.oneChoose.selectText == "") {
              this.$message({ message: `请选择活动一的app页面` });
            } else {
              return resolve(`通过活动一的app页面`);
            }
          } else if (this.oneChoose.type === "H5") {
            if (this.oneChoose.path == "") {
              this.$message({ message: `请填写活动一的h5页面参数` });
            } else {
              return resolve(`通过活动一的h5页面参数`);
            }
          } else {
            this.$message({ message: `请选择活动一的跳转页面` });
          }
        }
      });
      return promise;
    },
    checkActivityData2() {
      let promise = new Promise((resolve, reject) => {
        if (this.twoChoose.picUrl === "") {
          this.$message({ message: `请上传活动二的图片` });
        } else {
          if (this.twoChoose.type === "APP") {
            if (this.twoChoose.selectText == "") {
              this.$message({ message: `请选择活动二的app页面` });
            } else {
              return resolve(`通过活动二的app页面`);
            }
          } else if (this.twoChoose.type === "H5") {
            if (this.twoChoose.path == "") {
              this.$message({ message: `请填写活动二的h5页面参数` });
            } else {
              return resolve(`通过活动二的h5页面参数`);
            }
          } else {
            this.$message({ message: `请选择活动二的跳转页面` });
          }
        }
      });
      return promise;
    },
    checkActivityData3() {
      let promise = new Promise((resolve, reject) => {
        if (this.threeChoose.picUrl === "") {
          this.$message({ message: `请上传活动三的图片` });
        } else {
          if (this.threeChoose.type === "APP") {
            if (this.threeChoose.selectText == "") {
              this.$message({ message: `请选择活动三的app页面` });
            } else {
              return resolve(`通过活动三的app页面`);
            }
          } else if (this.threeChoose.type === "H5") {
            if (this.threeChoose.path == "") {
              this.$message({ message: `请填写活动三的h5页面参数` });
            } else {
              return resolve(`通过活动三的h5页面参数`);
            }
          } else {
            this.$message({ message: `请选择活动三的跳转页面` });
          }
        }
      });
      return promise;
    },
    //模糊搜索
    searchTopic(topicName, fn) {
      let params = {
        topicName: topicName
      };
      checkSpecial(params).then(
        res => {
          console.log(res.data);
          if (res.data.statusCode === 2000) {
            res.data.body.length &&
              res.data.body.forEach(el => {
                el.value = el.topicName;
              });
            this.searchLists = res.data.body;
            fn(this.searchLists);
          } else {
          }
        },
        error => {
          console.log(error);
        }
      );
    },
    querySearchAsync(queryString, fn) {
      this.searchTopic(queryString, fn);
    },
    select1(item) {
      //只要topicId,当选择app页面中的专题详情时
      console.log(item);
      this.oneChoose.topicName = item.value;
      this.oneChoose.topicId = item.topicId;
      console.info(
        `第1栏搜索名称${this.oneChoose.topicName},id号${this.oneChoose.topicId}`
      );
    },
    select2(item) {
      console.info(`第2栏搜索`);
      console.log(item);
      this.twoChoose.topicName = item.value;
      this.twoChoose.topicId = item.topicId;
      console.info(
        `第2栏搜索名称${this.twoChoose.topicName},id号${this.twoChoose.topicId}`
      );
    },
    select3(item) {
      console.info(`第三栏搜索`);
      console.log(item);
      this.threeChoose.topicName = item.value;
      this.threeChoose.topicId = item.topicId;
      console.info(
        `第3栏搜索名称${this.threeChoose.topicName},id号${
          this.threeChoose.topicId
        }`
      );
    },
    //跳转的是app还是h5(单选框)
    chooseTypes(chooseType, num) {
      switch (num) {
        case 1:
          this.oneChoose.type = chooseType;
          console.log(`第1栏跳转方式${this.oneChoose.type}`);
          console.log(`第1栏app选中项${this.oneChoose.selectText}`);
          if (chooseType === "APP") {
            this.oneChoose.showSelect = true;
            this.oneChoose.param = false;
          } else {
            this.oneChoose.showSelect = false;
            this.oneChoose.param = true;
            this.oneChoose.special = false; //专题搜索框
          }
          if (
            this.oneChoose.type === "APP" &&
            this.oneChoose.selectText === 16
          ) {
            this.oneChoose.special = true;
          }
          break;
        case 2:
          this.twoChoose.type = chooseType;
          console.log(`第2栏跳转方式${this.twoChoose.type}`);
          console.log(`第2栏app选中项${this.twoChoose.selectText}`);
          if (chooseType === "APP") {
            this.twoChoose.showSelect = true;
            this.twoChoose.param = false;
          } else {
            this.twoChoose.showSelect = false;
            this.twoChoose.param = true;
            this.twoChoose.special = false; //专题搜索框
          }
          if (
            this.twoChoose.type === "APP" &&
            this.twoChoose.selectText === 16
          ) {
            this.twoChoose.special = true;
          }
          break;
        case 3:
          this.threeChoose.type = chooseType;
          console.log(`第3栏跳转方式${this.threeChoose.type}`);
          console.log(`第3栏app选中项${this.threeChoose.selectText}`);
          if (chooseType === "APP") {
            this.threeChoose.showSelect = true;
            this.threeChoose.param = false;
          } else {
            this.threeChoose.showSelect = false;
            this.threeChoose.param = true;
            this.threeChoose.special = false; //专题搜索框
          }
          if (
            this.threeChoose.type === "APP" &&
            this.threeChoose.selectText === 16
          ) {
            this.threeChoose.special = true;
          }
          break;
      }
    },
    //app这边的下拉选项框
    selectPage(page, num) {
      switch (num) {
        case 1:
          if (page === 16) {
            this.oneChoose.special = true;
          } else {
            this.oneChoose.special = false;
          }
          this.oneChoose.selectText = page;
          console.log(`第1栏选中code:${this.oneChoose.selectText}`);
          break;
        case 2:
          if (page === 16) {
            this.twoChoose.special = true;
          } else {
            this.twoChoose.special = false;
          }
          this.twoChoose.selectText = page;
          break;
        case 3:
          if (page === 16) {
            this.threeChoose.special = true;
          } else {
            this.threeChoose.special = false;
          }
          this.threeChoose.selectText = page;
          break;
      }
    },
    h5Path(num) {
      switch (num) {
        case 1:
          console.log(`第1栏h5路径${this.oneChoose.path}`);
          break;
        case 2:
          console.log(`第2栏h5路径${this.twoChoose.path}`);
          break;
        case 3:
          console.log(`第3栏h5路径${this.threeChoose.path}`);
          break;
        case 4:
          console.log(`第4栏h5路径${this.fourChoose.path}`);
          break;
        case 5:
          console.log(`第5栏h5路径${this.fiveChoose.path}`);
          break;
        case 6:
          console.log(`第6栏h5路径${this.sixChoose.path}`);
          break;
      }
    },
    imgOne(res) {
      console.log(res);
      if (res.statusCode === 2000) {
        this.fileOne.push({ name: res.body, url: res.body });
        this.oneChoose.picUrl = res.body;
      } else {
        this.$message({
          $message: res.msg
        });
      }
    },
    // handlePreview(file) {
    //   console.warn(file);
    // },
    removeOne(files, fileList) {
      //确认删除后，数组清空,因为只有一个文件
      this.fileOne = [];
    },
    beforeRemoveOne(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    },
    imgTwo(res) {
      console.log(res);
      if (res.statusCode === 2000) {
        this.fileTwo.push({ name: res.body, url: res.body });
        this.twoChoose.picUrl = res.body;
      } else {
        this.$message({
          $message: res.msg
        });
      }
    },
    removeTwo(files, fileList) {
      //确认删除后，数组清空,因为只有一个文件
      this.fileTwo = [];
    },
    beforeRemoveTwo(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    },
    imgThree(res) {
      console.log(res);
      if (res.statusCode === 2000) {
        this.fileThree.push({ name: res.body, url: res.body });
        this.threeChoose.picUrl = res.body;
      } else {
        this.$message({
          $message: res.msg
        });
      }
    },
    removeThree(files, fileList) {
      //确认删除后，数组清空,因为只有一个文件
      this.fileThree = [];
    },
    beforeRemoveThree(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    }
  }
};
</script>

<style scoped lang="less">
.guide_setting {
  margin-top: 20px;
}
.show {
  text-align: left;
}
.text_align {
  text-align: left;
}
</style>