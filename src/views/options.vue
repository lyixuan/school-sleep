<template>
  <div class="report-search">
    <s-navi :nData="navi_text"></s-navi>
    <div style="width: 100%;height: 100%;">
      <div class="condition">
        <div class="bg-blue">账号管理</div>
        <div class="condition0">
          <div class="accontMg tt">
            <span>登录账号</span>
            <span>姓名</span>
            <span>备注</span>
            <span>操作</span>
            <span>操作</span>
          </div>
          <div class="accontMg" v-for="item in accounts" :keys="item.user_code">
            <span>{{item.user_code}}</span>
            <span>{{item.user_des}}</span>
            <span>{{item.status==0?'停用':'开启'}}</span>
            <span class="set" @click="openAccountEdit(item)">编辑</span>
            <span class="set" @click="accountResetPWD(item)">重置密码</span>
          </div>
          <div class="addnew" @click="openAccountAdd()">
            + 新增账号
          </div>
        </div>

        <div class="bg-blue" style="margin-top: 20px;">角色管理</div>
        <div class="condition0">
          <div class="left_card1" style="position: relative">
            <div class="r_tt">角 色 列 表</div>
            <el-button size="mini" type="primary" style="position:absolute;right: 20px; top:14px;z-index: 100"
                       @click="openAddRole()"><i class="el-icon-plus
"></i> 新 增
            </el-button>
            <div class="itable">
              <span>角色ID</span><span>角色名称</span><span>操作</span>
            </div>
            <div class="itable" v-for="item in roles" :keys="item.role_id"
                 :class="{ itableactive: item.role_id==checkedRoleId?true:false }">
              <span>{{item.role_id}}</span><span @click="getRights(item)">{{item.role_name}}</span>
              <span><i class="el-icon-delete" @click="openDelRole(item)"></i><i class="el-icon-edit"
                                                                                @click="openRoleEdit(item)"></i></span>
            </div>
          </div>
          <div class="right_card right_card1" style="position: relative">
            <div class="r_tt"> 菜 单 列 表</div>
            <el-button size="mini" type="primary" style="position:absolute;right: 20px; top:14px;z-index: 100"
                       @click="saveRights()"><i class="el-icon-check
"></i> 保 存
            </el-button>
            <el-checkbox-group v-model="role_rights" v-loading="loading1">
              <el-checkbox :label=item.value v-for="item in menus" :key="item.value" >{{item.name}}</el-checkbox>
            </el-checkbox-group>
            </p>
          </div>
        </div>

        <div style="clear:both"></div>

        <div class="bg-blue" style="margin-top: 20px;">权限分配</div>
        <div class="condition0">
          <div class="left_card">
            <div class="r_tt">账 号 列 表</div>
            <p v-for="item in accounts" :keys="item.user_code" @click="getUserRoles(item)"
               :class="{ active: item.user_code==checkedBtnId?true:false }">
              <span class="r_name">{{item.user_des}}</span>
            </p>
          </div>
          <div class="right_card"  style="position: relative">
            <div class="r_tt">角 色 列 表 &nbsp; [{{checkedBtnName}}]</div>
            <el-button size="mini" type="primary" style="position:absolute;right: 20px; top:14px;z-index: 100"
                       @click="saveUserRole()"><i class="el-icon-check
"></i> 保 存
            </el-button>
            <el-checkbox-group v-model="user_roles" v-loading="loading">
              <el-checkbox :label=item.role_id v-for="item in roles" :key="item.role_id">{{item.role_name}}</el-checkbox>
            </el-checkbox-group>
            </p>
          </div>
        </div>
      </div>
    </div>
    <!--编辑dialog-->
    <el-dialog class="actEdit" title="编辑" :visible.sync="actEditDialog">
      <el-form :model="actEditForm">
        <el-form-item label="登录账号" :label-width="formLabelWidth">
          <el-input v-model="actEditForm.user_code" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="姓名" :label-width="formLabelWidth">
          <el-input v-model="actEditForm.user_des"></el-input>
        </el-form-item>
        <el-form-item label="状态" :label-width="formLabelWidth">
          <el-select v-model="actEditForm.status" placeholder="请选择状态">
            <el-option label="开启" value="1"></el-option>
            <el-option label="停用" value="0"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="actEditDialog = false">取 消</el-button>
        <el-button type="primary" @click="saveAccountEdit()">确 定</el-button>
      </div>
    </el-dialog>

    <!--新增dialog-->
    <el-dialog class="actEdit" title="新建账号" :visible.sync="actAddDialog">
      <el-form :model="actAddForm">
        <el-form-item label="登录账号" :label-width="formLabelWidth" required>
          <el-input v-model="actAddForm.user_code"></el-input>
        </el-form-item>
        <el-form-item label="姓名" :label-width="formLabelWidth">
          <el-input v-model="actAddForm.user_des"></el-input>
        </el-form-item>
        <el-form-item label="状态" :label-width="formLabelWidth">
          <el-select v-model="actAddForm.status" placeholder="请选择状态">
            <el-option label="开启" value="1"></el-option>
            <el-option label="停用" value="0"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div style="font-size: 12px;text-align: center">新增用户初始密码均为:123456。管理员分配账号后，请用户及时更改密码。</div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="actAddDialog = false">取 消</el-button>
        <el-button type="primary" @click="saveAccountAdd()">确 定</el-button>
      </div>
    </el-dialog>

    <!--重置密码dialog-->
    <el-dialog class="actEdit" title="重置密码" :visible.sync="actResetDialog">
      <div style="text-align: left;margin-left: 40px;">密码重置后将设置为初始密码123456。确定要为 {{resetName}} 重置密码吗？</div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="actResetDialog = false">取 消</el-button>
        <el-button type="primary" @click="saveReset()">确 定</el-button>
      </div>
    </el-dialog>

    <!--编辑角色-->
    <el-dialog class="actEdit" title="新增角色" :visible.sync="addRoleDialog">
      <el-form :model="roleAddForm">
        <el-form-item label="角色名称" :label-width="formLabelWidth">
          <el-input v-model="roleAddForm.role_name"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addRoleDialog = false">取 消</el-button>
        <el-button type="primary" @click="saveRoleAdd()">确 定</el-button>
      </div>
    </el-dialog>

    <!--编辑角色-->
    <el-dialog class="actEdit" title="编辑角色" :visible.sync="editRoleDialog">
      <el-form :model="roleEditForm">
        <el-form-item label="角色ID" :label-width="formLabelWidth">
          <el-input v-model="roleEditForm.role_id" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="角色名称" :label-width="formLabelWidth">
          <el-input v-model="roleEditForm.role_name"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editRoleDialog = false">取 消</el-button>
        <el-button type="primary" @click="saveRoleEdit()">确 定</el-button>
      </div>
    </el-dialog>

    <!--s删除角色dialog-->
    <el-dialog class="actEdit" title="删除角色" :visible.sync="delRole">
      <div style="text-align: left;margin-left: 40px;">你确定要删除角色 【{{roleName}}】 吗？</div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="delRole = false">取 消</el-button>
        <el-button type="primary" @click="saveDelRole()">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import SNavi from '../components/Navi.vue'
  export default {
    name: 'search-index',
    components: {
      SNavi
    },
    data () {
      return {
        navi_text: {
          title: '权限配置',
          subTitle: '',
          btn: ''
        },
        checkedBtnId: '',
        checkedBtnName: '',
        accounts: [],
        menus: [],
        formLabelWidth: '120px',
        actEditDialog: false,
        actEditForm: {
          user_des: '',
          user_code: '',
          status: ''
        },
        actAddDialog: false,
        actAddForm: {
          user_des: '',
          user_code: '',
          status: '1'
        },
        actResetDialog: false,
        resetId: '',
        resetName: '',
        user_roles: [],
        loading: false,
        loading1: false,

        roles: [],
        checkedRoleId: '',
        role_rights: [],
        editRoleDialog: false,
        roleEditForm: {
          role_id: '',
          role_name: ''
        },
        delRole: false,
        roleName: '',
        roleID: '',
        addRoleDialog: false,
        roleAddForm: {
          role_name: ''
        }
      }
    },
    mounted () {
      this.getAccounts();
      this.getRoles();
      this.getMenus();
    },

    methods: {
      getAccounts() {
        this.$resource(P_PRIVILEGE + 'accounts').get().then((response) => {
          if (response.body.code == 200) {
            this.accounts = response.body.data
            for (var i = 0; i < this.accounts.length; i++) {
              if (this.accounts[i].user_code == 'admin') {
                this.accounts.splice(i, 1)
              }
            }
            this.getUserRoles(this.accounts[0])
          } else {
            this.alertMsg("warning", '获取账号信息有误')
          }
        })
      },
      getMenus() {
        this.$resource(P_PRIVILEGE + 'menus').get().then((response) => {
          if (response.body.code == 200) {
            this.menus = response.body.data
          } else {
            this.alertMsg("warning", '获取菜单信息有误')
          }
        })
      },
      getRoles(){
        this.$resource(P_PRIVILEGE + 'roles').get().then((response) => {
          if (response.body.code == 200) {
            this.roles = response.body.data
            this.getRights(this.roles[0])
          } else {
            this.alertMsg("warning", '获取角色信息有误')
          }
        })
      },
      openRoleEdit(item){
        this.editRoleDialog = true;
        this.roleEditForm = {
          role_id: item.role_id,
          role_name: item.role_name
        }
      },
      saveRoleEdit(){
        this.$resource(P_PRIVILEGE + 'edit_role').save({}, this.roleEditForm).then((response) => {
          if (response.body.code == 200) {
            this.getRoles()
            this.alertMsg("success", '保存成功')
            this.editRoleDialog = false;
          } else {
            this.alertMsg("warning", '服务器错误')
          }
        })
      },
      openDelRole(item){
        this.delRole = true;
        this.roleID = item.role_id
        this.roleName = item.role_name
      },
      saveDelRole(){
        this.$resource(P_PRIVILEGE + 'del_role').get({role_id: this.roleID}).then((response) => {
          if (response.body.code == 200) {
            this.getRoles()
            this.alertMsg("success", '删除成功')
            this.delRole = false;
          } else {
            this.alertMsg("warning", '服务器错误')
          }
        })
      },
      openAddRole(){
        this.addRoleDialog = true;
        this.roleAddForm = {
          role_name: ''
        }
      },
      saveRoleAdd(){
        if (!this.roleAddForm.role_name) {
          this.alertMsg("warning", '请填写角色名')
          return
        }
        this.$resource(P_PRIVILEGE + 'add_role').save({}, this.roleAddForm).then((response) => {
          if (response.body.code == 200) {
            this.getRoles()
            this.alertMsg("success", '新增成功')
            this.addRoleDialog = false;
          } else {
            this.alertMsg("warning", '服务器错误')
          }
        })
      },
      getRights(item){
        this.loading1 = true;
        this.checkedRoleId = item.role_id;
        this.$resource(P_PRIVILEGE + 'role_rights').get({role_id: item.role_id}).then((response) => {
          if (response.body.code == 200) {
            this.role_rights = response.body.data
          } else {
            this.alertMsg("warning", '获取角色权限信息有误')
          }
          this.loading1 = false
        })
      },
      saveRights(){
        let param = {
          role_id: this.checkedRoleId,
          rights: this.role_rights
        };
        let _this = this;
        this.$resource(P_PRIVILEGE + 'update_role_rights').save({}, param).then((response) => {
          if (response.body.code == 200) {
            _this.alertMsg("success", '角色权限保存成功');
          } else {
            _this.alertMsg("error", response.body.msg ? response.body.msg : '服务器端错误')
          }
          this.getRights({role_id: this.checkedRoleId})
        }, (response) => {
          this.getRights({role_id: this.checkedRoleId})
          console.log(response.body)
        })
      },
      openAccountEdit(item){
        this.actEditForm = {
          user_des: item.user_des,
          user_code: item.user_code,
          status: item.status
        };
        this.actEditDialog = true
      },
      saveAccountEdit(){
        let _this = this;
        let param = this.actEditForm
        this.$resource(P_PRIVILEGE + 'edit_accounts').save({}, param).then((response) => {
          if (response.body.code == 200) {
            _this.alertMsg("success", '修改成功');
            _this.actEditDialog = false;
            _this.getAccounts()

          } else {
            _this.alertMsg("error", response.body.msg ? response.body.msg : '服务器端错误')
          }
        }, (response) => {
          console.log(response.body)
        })
      },
      openAccountAdd(){
        this.actAddForm = {
          user_des: '',
          user_code: '',
          status: '1'
        };
        this.actAddDialog = true
      },
      saveAccountAdd(){
        if (!this.actAddForm.user_code) {
          this.alertMsg("warning", '请填写登录账号')
          return
        }
        let param = this.actAddForm;
        let _this = this;
        this.$resource(P_PRIVILEGE + 'add_accounts').save({}, param).then((response) => {
          if (response.body.code == 200) {
            _this.alertMsg("success", '添加成功');
            _this.actAddDialog = false;
            _this.getAccounts()
          } else {
            _this.alertMsg("error", response.body.msg ? response.body.msg : '服务器端错误')
          }
        }, (response) => {
          _this.alertMsg("error", response.body.msg ? response.body.msg : '服务器端错误')
        })
      },
      accountResetPWD(item){
        this.resetName = item.user_des;
        this.resetId = item.user_code;
        this.actResetDialog = true
      },
      saveReset(){
        this.$resource(P_OPTIONS + 'reset_pwd').get({user_code: this.resetId}).then((response) => {
          if (response.body.code == 200) {
            this.alertMsg("success", '重置成功');
            this.actResetDialog = false
          } else {
            this.alertMsg("warning", '重置失败')
          }
        })
      },
      getUserRoles(item){
//          获取用户角色
        this.loading = true
        this.checkedBtnId = item.user_code;
        this.checkedBtnName = item.user_des;
        this.$resource(P_PRIVILEGE + 'user_roles').get({user_code: item.user_code}).then((response) => {
          if (response.body.code == 200) {
            this.user_roles = response.body.data
          } else {
            this.alertMsg("warning", '获取菜单权限有误')
          }
          this.loading = false
        })
      },
      saveUserRole(){
        let param = {
          user_code: this.checkedBtnId,
          roles: this.user_roles
        };
        let _this = this;
        this.$resource(P_PRIVILEGE + 'update_user_roles').save({}, param).then((response) => {
          if (response.body.code == 200) {
            _this.alertMsg("success", '用户角色保存成功');
          } else {
            _this.alertMsg("error", response.body.msg ? response.body.msg : '服务器端错误')
          }
          this.getUserRoles({user_code: this.checkedBtnId})
        }, (response) => {
          this.getUserRoles({user_code: this.checkedBtnId})
          console.log(response.body)
        })
      }
    }
  }
</script>

<style scoped>
  @import "../assets/css/chart-table-page-com.css";

  * {
    box-sizing: border-box;
  }

  .condition {
    margin-top: 5px;
    background: #fff;
    padding: 5px;
    overflow: hidden;
  }

  .bg-blue {
    background: #E7F4FA;
    color: #2195CB;
    height: 25px;
    line-height: 25px;
    padding-left: 5px;
    width: 100%;
  }

  .condition0 {
    width: 100%;
    padding: 10px;

  }

  .accontMg {
    overflow: hidden;
    height: 30px;
    line-height: 30px;
  }

  .tt {
    font-weight: bold;
  }

  .set {
    color: #1c8de0;
    cursor: pointer;
  }

  .set:hover {
    color: #000080;
  }

  .addnew {
    box-sizing: border-box;
    text-align: center;
    border: 1px solid #1c8de0;
    color: #1c8de0;
    cursor: pointer;
  }

  .addnew:hover {
    background: #1c8de0;
    color: #fff;
  }

  .accontMg > span {
    display: inline-block;
    width: 19%;

  }

  .left_card {
    width: 36%;
    margin-right: 3%;
    min-height: 350px;
    overflow: hidden;
    float: left;
    padding: 20px;
    color: #333;
    font-size: 16px;
    text-align: center;
    border: 2px solid #E7F4FA;
  }

  .left_card1 {
    width: 36%;
    margin-right: 3%;
    text-align: left;
    min-height: 420px;
    overflow: hidden;
    float: left;
    padding: 20px;
    color: #333;
    font-size: 14px;
    border: 2px solid #E7F4FA;
  }

  .itable {
    line-height: 30px;
  }
  .itable span:nth-child(2){
    padding: 0 5px;
  }

  .itable:nth-child(n+4):hover span:nth-child(2){
    background: #efefef;
  }

  .itableactive  span:nth-child(2){
    background: #E7F4FA !important;
  }

  .itable > span {
    display: inline-block;
    width: 25%;
    margin-left: 1%;
    cursor: pointer;
  }

  .itable > span:first-child {
    width: 20%;
  }

  .itable > span:nth-child(2) {
    width: 45%;
  }

  .itable > span > i {
    margin-right: 15px;
    color: #1c8de0;
    cursor: pointer;
  }

  .itable > span > i:hover {
    color: #0000ff;
  }

  .right_card {
    width: 60%;
    min-height: 350px;
    overflow: hidden;
    float: left;
    border: 2px solid #E7F4FA;
    padding: 20px;
  }

  .right_card1 {
    width: 60%;
    min-height: 420px;
    overflow: hidden;
    float: left;
    border: 2px solid #E7F4FA;
    padding: 20px;
  }

  .left_card p {
    height: 30px;
    line-height: 30px;
    background: #E7F4FA;
    margin-bottom: 10px;
    cursor: pointer;
  }

  .left_card p:hover {
    background: #42A4D3;
    color: #fff;
  }

  .r_name {
    padding: 5px 10px;

  }

  .r_tt {
    text-align: center;
    background: #fff;
    margin-bottom: 20px;
    font-size: 14px;
    border-bottom: 1px solid #eee;
  }


  p.active {
    background: #42A4D3;
    color: #fff;
  }
  .right_card .el-checkbox:first-child {
    margin-left: 15px!important;
  }
  .right_card1 .el-checkbox:first-child {
    margin-left: 15px!important;
  }

</style>
