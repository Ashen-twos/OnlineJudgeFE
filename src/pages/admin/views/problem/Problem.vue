<template>
  <div class="problem">

    <Panel :title="title">
      <el-form ref="form" :model="problem" :rules="rules" label-position="top" label-width="70px">
        <el-row :gutter="20">
          <el-col :span="6">
            <el-form-item prop="_id" :label="$t('m.Display_ID')"
                          :required="this.routeName === 'create-contest-problem' || this.routeName === 'edit-contest-problem'">
              <el-input :placeholder="$t('m.Display_ID')" v-model="problem._id"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="18">
            <el-form-item prop="title" :label="$t('m.Title')" required>
              <el-input :placeholder="$t('m.Title')" v-model="problem.title"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="24">
            <el-form-item prop="description" :label="$t('m.Description')" required>
              <Simditor v-model="problem.description"></Simditor>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="24">
            <el-form-item prop="input_description" :label="$t('m.Input_Description')" required>
              <Simditor v-model="problem.input_description"></Simditor>
            </el-form-item>
          </el-col>
          <el-col :span="24">
            <el-form-item prop="output_description" :label="$t('m.Output_Description')" required>
              <Simditor v-model="problem.output_description"></Simditor>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item :label="$t('m.Time_Limit') + ' (ms)' " required>
              <el-input type="Number" :placeholder="$t('m.Time_Limit')" v-model="problem.time_limit"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item :label="$t('m.Memory_limit') + ' (MB)' " required>
              <el-input type="Number" :placeholder="$t('m.Memory_limit')" v-model="problem.memory_limit"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item :label="$t('m.Difficulty')">
              <el-select class="difficulty-select" size="small" :placeholder="$t('m.Difficulty')" v-model="problem.difficulty">
                <el-option :label="$t('m.Low')" value="Low"></el-option>
                <el-option :label="$t('m.Mid')" value="Mid"></el-option>
                <el-option :label="$t('m.High')" value="High"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="4">
            <el-form-item :label="$t('m.Visible')">
              <el-switch
                v-model="problem.visible"
                active-text=""
                inactive-text="">
              </el-switch>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item :label="$t('m.ShareSubmission')">
              <el-switch
                v-model="problem.share_submission"
                active-text=""
                inactive-text="">
              </el-switch>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item :label="$t('m.Tag')" :error="error.tags" required>
              <span class="tags">
                <el-tag
                  v-for="tag in problem.tags"
                  :closable="true"
                  :close-transition="false"
                  :key="tag"
                  type="success"
                  @close="closeTag(tag)"
                >{{tag}}</el-tag>
              </span>
              <el-autocomplete
                v-if="inputVisible"
                size="mini"
                class="input-new-tag"
                popper-class="problem-tag-poper"
                v-model="tagInput"
                :trigger-on-focus="false"
                @keyup.enter.native="addTag"
                @select="addTag"
                :fetch-suggestions="querySearch">
              </el-autocomplete>
              <el-button class="button-new-tag" v-else size="small" @click="inputVisible = true">+ {{$t('m.New_Tag')}}</el-button>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item :label="$t('m.Languages')" :error="error.languages" required>
              <el-checkbox-group v-model="problem.languages">
                <el-tooltip class="spj-radio" v-for="lang in allLanguage.languages" :key="'spj'+lang.name" effect="dark"
                            :content="lang.description" placement="top-start">
                  <el-checkbox :label="lang.name"></el-checkbox>
                </el-tooltip>
              </el-checkbox-group>
            </el-form-item>
          </el-col>
        </el-row>
        <div>
          <el-form-item v-for="(sample, index) in problem.samples" :key="'sample'+index">
            <Accordion :title="'Sample' + (index + 1)">
              <el-button type="warning" size="small" icon="el-icon-delete" slot="header" @click="deleteSample(index)">
                Delete
              </el-button>
              <el-row :gutter="20">
                <el-col :span="12">
                  <el-form-item :label="$t('m.Input_Samples')" required>
                    <el-input
                      :rows="5"
                      type="textarea"
                      :placeholder="$t('m.Input_Samples')"
                      v-model="sample.input">
                    </el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label="$t('m.Output_Samples')" required>
                    <el-input
                      :rows="5"
                      type="textarea"
                      :placeholder="$t('m.Output_Samples')"
                      v-model="sample.output">
                    </el-input>
                  </el-form-item>
                </el-col>
              </el-row>
            </Accordion>
          </el-form-item>
        </div>
        <div class="add-sample-btn">
          <button type="button" class="add-samples" @click="addSample()"><i class="el-icon-plus"></i>{{$t('m.Add_Sample')}}
          </button>
        </div>
        <el-form-item style="margin-top: 20px" :label="$t('m.Hint')">
          <Simditor v-model="problem.hint" placeholder=""></Simditor>
        </el-form-item>

        <el-form-item :label="$t('m.Code_Template')">
          <el-row>
            <el-col :span="24" v-for="(v, k) in template" :key="'template'+k">
              <el-form-item>
                <el-checkbox v-model="v.checked">{{ k }}</el-checkbox>
                <div v-if="v.checked">
                  <code-mirror v-model="v.code" :mode="v.mode"></code-mirror>
                </div>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form-item>

        <!-- 附加测试 -->
        <el-form-item :label="$t('m.Extra_Judge')">
          <el-col :span="24">
            <el-checkbox v-model="problem.extra" @click.native.prevent="switchEx()">{{$t('m.Use_Extra_Judge')}}</el-checkbox>
          </el-col>
        </el-form-item>
        <el-form-item v-if="problem.extra">
          <el-tabs v-model="activeName" type="border-card">
            <el-tab-pane label="代码格式" name="first">
              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                  <el-form-item label="是否启用">
                    <el-switch v-model="problem.extra_config.format.enable"></el-switch>
                  </el-form-item>
                </el-col>
                <el-col :span="6">
                  <el-form-item label="分值" required>
                    <el-input type="Number" placeholder="5" v-model="problem.extra_score.format"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="评分模式" required>
                    <el-select v-model="problem.extra_config.format.score_type">
                      <el-option label="平均分配" :value="1"></el-option>
                      <el-option label="仅全部通过" :value="2"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                  <el-form-item label="缩进长度" required>
                    <el-input type="Number" placeholder="" v-model.number="problem.extra_config.format.indent_size"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="单行语句上限" required>
                    <el-input type="Number" placeholder="3" v-model.number="problem.extra_config.format.max_statement"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="大括号位置" required>
                    <el-select v-model="problem.extra_config.format.left_big_para">
                      <el-option label="行首" :value="true"></el-option>
                      <el-option label="行末" :value="false"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="逗号后空格" required>
                    <el-select v-model="problem.extra_config.format.comma_space">
                      <el-option label="开启" :value="true"></el-option>
                      <el-option label="关闭" :value="false"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>
            </el-tab-pane>

            <el-tab-pane label="代码风格" name="second">
              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                  <el-form-item label="是否启用">
                    <el-switch v-model="problem.extra_config.style.enable"></el-switch>
                  </el-form-item>
                </el-col>
                <el-col :span="6">
                  <el-form-item label="分值" required>
                    <el-input type="Number" placeholder="5" v-model="problem.extra_score.style"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="评分模式" required>
                    <el-select v-model="problem.extra_config.style.score_type">
                      <el-option label="平均分配" :value="1"></el-option>
                      <el-option label="仅全部通过" :value="2"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :span="24" :gutter="80">
                <el-col :span="8">
                  <el-form-item label="函数命名风格" required>
                    <el-select v-model="problem.extra_config.style.func_naming">
                      <el-option label="无" :value="0"></el-option>
                      <el-option label="多音节驼峰式" :value="1"></el-option>
                      <el-option label="全大写" :value="2"></el-option>
                      <el-option label="全小写" :value="3"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>

                <el-col :span="8">
                  <el-form-item label="全局变量命名风格" required>
                    <el-select v-model="problem.extra_config.style.global_naming">
                      <el-option label="无" :value="0"></el-option>
                      <el-option label="多音节驼峰式" :value="1"></el-option>
                      <el-option label="全大写" :value="2"></el-option>
                      <el-option label="全小写" :value="3"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>

                <el-col :span="8">
                  <el-form-item label="局部变量命名风格" required>
                    <el-select v-model="problem.extra_config.style.local_naming">
                      <el-option label="无" :value="0"></el-option>
                      <el-option label="多音节驼峰式" :value="1"></el-option>
                      <el-option label="全大写" :value="2"></el-option>
                      <el-option label="全小写" :value="3"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                  <el-form-item label="全局变量前缀" required>
                    <el-input type="text" v-model="problem.extra_config.style.global_prefix"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="禁用单字母命名" required>
                    <el-select v-model="problem.extra_config.style.single_name">
                      <el-option label="开启" :value="true"></el-option>
                      <el-option label="关闭" :value="false"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>

                <el-col :span="12">
                  <el-form-item label="白名单">
                    <span class="tags">
                      <el-tag
                        v-for="tag in problem.extra_config.style.white_list"
                        :closable="true"
                        :close-transition="false"
                        :key="tag"
                        type="success"
                        @close="closeStyleWhite(tag)"
                      >{{tag}}</el-tag>
                    </span>
                    <el-autocomplete
                      v-if="styleWhiteVisible"
                      size="mini"
                      class="input-new-tag"
                      popper-class="problem-tag-poper"
                      v-model="styleWhiteInput"
                      :trigger-on-focus="false"
                      @keyup.enter.native="addStyleWhite"
                      @select="addStyleWhite"
                      :fetch-suggestions="querySearch">
                    </el-autocomplete>
                    <el-button class="button-new-tag" v-else size="small" @click="styleWhiteVisible = true">+ 新增</el-button>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :span="24" :gutter="80">
                
              </el-row>
            </el-tab-pane>

            <el-tab-pane label="函数使用" name="third">
              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                    <el-form-item label="是否启用">
                      <el-switch v-model="problem.extra_config.function.enable"></el-switch>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                  <el-form-item label="分值" required>
                    <el-input type="Number" placeholder="5" v-model="problem.extra_score.function"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="评分模式" required>
                    <el-select v-model="problem.extra_config.function.score_type">
                      <el-option label="平均分配" :value="1"></el-option>
                      <el-option label="仅全部通过" :value="2"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                  <el-form-item label="函数内最多语句数" required>
                    <el-input type="Number" placeholder="10" v-model.number="problem.extra_config.function.max_statement"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="函数内禁止IO" required>
                    <el-select v-model="problem.extra_config.function.disableIO">
                      <el-option label="开启" :value="true"></el-option>
                      <el-option label="关闭" :value="false"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :span="24" :gutter="80">
                <el-col :span="12">
                  <el-form-item label="黑名单">
                    <span class="tags">
                      <el-tag
                        v-for="tag in problem.extra_config.function.black_list"
                        :closable="true"
                        :close-transition="false"
                        :key="tag"
                        type="success"
                        @close="closeFuncBlack(tag)"
                      >{{tag}}</el-tag>
                    </span>
                    <el-autocomplete
                      v-if="funcBlackVisible"
                      size="mini"
                      class="input-new-tag"
                      popper-class="problem-tag-poper"
                      v-model="funcBlackInput"
                      :trigger-on-focus="false"
                      @keyup.enter.native="addFuncBlack"
                      @select="addFuncBlack"
                      :fetch-suggestions="querySearch">
                    </el-autocomplete>
                    <el-button class="button-new-tag" v-else size="small" @click="funcBlackVisible = true">+ 新增函数</el-button>
                  </el-form-item>
                </el-col>

                <el-col :span="12">
                  <el-form-item label="白名单">
                    <span class="tags">
                      <el-tag
                        v-for="tag in problem.extra_config.function.white_list"
                        :closable="true"
                        :close-transition="false"
                        :key="tag"
                        type="success"
                        @close="closeFuncWhite(tag)"
                      >{{tag}}</el-tag>
                    </span>
                    <el-autocomplete
                      v-if="funcWhiteVisible"
                      size="mini"
                      class="input-new-tag"
                      popper-class="problem-tag-poper"
                      v-model="funcWhiteInput"
                      :trigger-on-focus="false"
                      @keyup.enter.native="addFuncWhite"
                      @select="addFuncWhite"
                      :fetch-suggestions="querySearch">
                    </el-autocomplete>
                    <el-button class="button-new-tag" v-else size="small" @click="funcWhiteVisible = true">+ 新增函数</el-button>
                  </el-form-item>
                </el-col>
              </el-row>
            </el-tab-pane>

            <el-tab-pane label="内存使用" name="furth">
              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                    <el-form-item label="是否启用">
                      <el-switch v-model="problem.extra_config.memory.enable"></el-switch>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                  <el-form-item label="分值" required>
                    <el-input type="Number" placeholder="5" v-model="problem.extra_score.memory"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="6">
                  <el-form-item label="评分模式" required>
                    <el-select v-model="problem.extra_config.memory.score_type">
                      <el-option label="平均分配" :value="1"></el-option>
                      <el-option label="仅全部通过" :value="2"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                  <el-form-item label="指针内存检测" required>
                    <el-select v-model="problem.extra_config.memory.check_ptr_free">
                      <el-option label="开启" :value="true"></el-option>
                      <el-option label="关闭" :value="false"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>

                <el-col :span="12">
                  <el-form-item label="白名单">
                    <span class="tags">
                      <el-tag
                        v-for="tag in problem.extra_config.memory.white_list"
                        :closable="true"
                        :close-transition="false"
                        :key="tag"
                        type="success"
                        @close="closePara(tag)"
                      >{{tag}}</el-tag>
                    </span>
                    <el-autocomplete
                      v-if="paraVisible"
                      size="mini"
                      class="input-new-tag"
                      popper-class="problem-tag-poper"
                      v-model="paraInput"
                      :trigger-on-focus="false"
                      @keyup.enter.native="addPara"
                      @select="addPara"
                      :fetch-suggestions="querySearch">
                    </el-autocomplete>
                    <el-button class="button-new-tag" v-else size="small" @click="paraVisible = true">+ 新增数组</el-button>
                  </el-form-item>
                </el-col>
              </el-row>
            </el-tab-pane>

            <el-tab-pane label="运行效率" name="fifth">
              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                    <el-form-item label="是否启用">
                      <el-switch v-model="problem.extra_config.runtime.enable"></el-switch>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                  <el-form-item label="分值" required>
                    <el-input type="Number" placeholder="5" v-model.number="problem.extra_score.runtime"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="6">
                  <el-form-item label="评分模式" required>
                    <el-select v-model="problem.extra_config.runtime.score_type">
                      <el-option label="平均分配" :value="1"></el-option>
                      <el-option label="仅全部通过" :value="2"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :span="24" :gutter="80">
                <el-col :span="6">
                  <el-form-item label="运行时间(ms)" required>
                    <el-input type="Number" placeholder="100" v-model.number="problem.extra_config.runtime.time"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="6">
                  <el-form-item label="内存占用(MB)" required>
                    <el-input type="Number" placeholder="64" v-model.number="problem.extra_config.runtime.memory"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
            </el-tab-pane>
          </el-tabs>
        </el-form-item>

        <el-tabs value="first" type="border-card">
          <el-tab-pane label="测试选项" name="first">
            <el-row>
              <el-col :span="12">
                <el-form-item :label="$t('m.Type')">
                  <el-radio-group v-model="problem.judge_config.judge_mode" :disabled="disableRuleType">
                    <el-radio :label="0">完全模式</el-radio>
                    <el-radio :label="1">函数模式</el-radio>
                    <el-radio :label="2">语句模式</el-radio>
                  </el-radio-group>
                </el-form-item>
              </el-col> 
            </el-row>

            <el-form-item v-if="problem.judge_config.judge_mode===1">
              <el-row :gutter="80">
                <el-col :span="8">
                  <el-form-item label="函数名称" required>
                    <el-input type="text" placeholder="请输入函数名称" v-model="problem.judge_config.func_config.name"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="返回值类型" required>
                    <el-select v-model="problem.judge_config.func_config.return_type" placeholder="请选择">
                      <el-option v-for="item in dataType" :key="item.value" :label="item.label" :value="item.value"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="参数选项" required>
                      <el-button type="primary" @click="dialogFormVisible = true">添加参数</el-button>                   
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="预览" required>
                      <el-button type="primary" @click="functionPreview">预览</el-button>                   
                  </el-form-item>
                </el-col>

                <el-dialog title="函数预览" :visible.sync="dialogPreviewVisible">
                  <code-mirror v-model="funcPreview"></code-mirror>
                </el-dialog>

              </el-row>

              <el-dialog title="添加参数" :visible.sync="dialogFormVisible">
                <el-row>
                  <el-form :model="form">
                    <el-row>
                      <el-form-item label="参数名称" required>
                        <el-input v-model="parameterConfig.name" required></el-input>
                      </el-form-item>
                    </el-row>
                    <el-row>
                      <el-form-item label="参数类型" required>
                        <el-row :gutter="50">
                          <el-col :span="12">
                            <el-select v-model="parameterConfig.type" placeholder="请选择参数类型">
                              <el-option v-for="item in dataType" :key="item.value" :label="item.label" :value="item.value"></el-option>
                            </el-select>
                          </el-col>
                          <el-col :span="4">
                            <el-checkbox v-model="parameterConfig.ptr">指针</el-checkbox>
                          </el-col>
                          <el-col :span="4">
                            <el-checkbox v-model="parameterConfig.arr">数组</el-checkbox>
                          </el-col>
                        </el-row>
                      </el-form-item>
                    </el-row>
                  </el-form>
                </el-row>
                <div slot="footer" class="dialog-footer">
                  <el-button @click="dialogFormVisible = false">取 消</el-button>
                  <el-button type="primary" @click="addParameter">确 定</el-button>
                </div>
              </el-dialog>

              <el-dialog title="编辑参数" :visible.sync="dialogEditVisible">
                <el-row>
                  <el-form :model="form">
                    <el-row>
                      <el-form-item label="参数名称" required>
                        <el-input v-model="parameterConfig.name" required></el-input>
                      </el-form-item>
                    </el-row>
                    <el-row>
                      <el-form-item label="参数类型" required>
                        <el-row :gutter="50">
                          <el-col :span="12">
                            <el-select v-model="parameterConfig.type" placeholder="请选择参数类型">
                              <el-option v-for="item in dataType" :key="item.value" :label="item.label" :value="item.value"></el-option>
                            </el-select>
                          </el-col>
                          <el-col :span="4">
                            <el-checkbox v-model="parameterConfig.ptr">指针</el-checkbox>
                          </el-col>
                          <el-col :span="4">
                            <el-checkbox v-model="parameterConfig.arr">数组</el-checkbox>
                          </el-col>
                        </el-row>
                      </el-form-item>
                    </el-row>
                  </el-form>
                </el-row>
                <div slot="footer" class="dialog-footer">
                  <el-button @click="dialogEditVisible = false">取 消</el-button>
                  <el-button type="primary" @click="editParameter">确 定</el-button>
                </div>
              </el-dialog>

              <el-row>
                <el-table :data="problem.judge_config.func_config.parameter">
                  <el-table-column prop="name" label="参数名称"></el-table-column>
                  <el-table-column prop="type" label="参数类型"></el-table-column>
                  <el-table-column prop="ptr" label="指针" :formatter="boolFormatter"></el-table-column>
                  <el-table-column prop="arr" label="数组" :formatter="boolFormatter"></el-table-column>
                  <el-table-column fixed="right" label="操作">
                    <template slot-scope="scope">
                      <el-button size="small" @click="openEditParameter(scope.row)">编辑</el-button>
                      <el-button size="small" @click="deleteParameter(scope.row.id)">删除</el-button>
                    </template>
                  </el-table-column>
                </el-table>
              </el-row>
              <el-row>
                <Accordion title="初始化代码" information="根据测试用例, 自行读入样例中的数据, 作为函数的参数">
                  <code-mirror v-model="problem.judge_config.func_config.init"></code-mirror>
                </Accordion>
              </el-row>
            </el-form-item>

            <el-form-item v-if="problem.judge_config.judge_mode===2">
              <el-row>
                <el-button type="primary" @click="dialogCondVisible = true">生成条件代码</el-button>
              </el-row>
              <el-row>
                <Accordion title="初始代码" information="不含头文件的完整代码, $CODE$代表用户输入位置, $JUDGE$代表条件判定位置">
                  <code-mirror v-model="problem.judge_config.segment_config.code"></code-mirror>
                </Accordion>
              </el-row>
              <el-row>
                <Accordion title="条件代码" information="返回值为int的judge函数, 使用原代码同名变量作为参数, 返回0代表合法">
                  <code-mirror v-model="problem.judge_config.segment_config.judge_code"></code-mirror>
                </Accordion>
              </el-row>

              <el-dialog title="添加条件" :visible.sync="dialogCondVisible">
                <el-row>
                  <el-form :model="form">
                    <el-row>
                      <el-button @click="addCondition" type="text" size="small" icon="el-icon-circle-plus">添加</el-button>
                    </el-row>
                    <el-row>
                      <el-table :data="condition.condition" style="width: 100%">
                        <el-table-column prop="id" label="序号" width="50" type="index" align="center"
                        :index="index=>index+1"/>
                        <el-table-column prop="parameter" label="变量名" align="center">
                          <template #default="scope">
                              <el-input v-model="scope.row.parameter"> </el-input>
                          </template>
                        </el-table-column>
                        <el-table-column prop="data_type" label="变量类型" align="center">
                          <template #default="scope">
                            <el-select v-model="scope.row.data_type" placeholder="请选择">
                              <el-option v-for="item in conditionType" :key="item.value" :label="item.label" :value="item.value"></el-option>
                            </el-select>
                          </template>
                        </el-table-column>
                        <el-table-column align="center" prop="relation" label="关系">
                          <template #default="scope">
                            <el-select v-model="scope.row.relation" placeholder="请选择">
                              <el-option v-for="item in conditionRelation" :key="item.value" :label="item.label" :value="item.value"></el-option>
                            </el-select>
                          </template>
                        </el-table-column>
                        <el-table-column prop="value" label="值" align="center">
                          <template #default="scope">
                              <el-input v-model="scope.row.value"></el-input>
                          </template>
                        </el-table-column>
                        <el-table-column prop="value_type" label="值类型" align="center">
                          <template #default="scope">
                            <el-select v-model="scope.row.value_type" placeholder="请选择">
                              <el-option v-for="item in conditionType" :key="item.value" :label="item.label" :value="item.value"></el-option>
                            </el-select>
                          </template>
                        </el-table-column>
                        <el-table-column label="操作" width="120" align="center">
                            <template #default="scope">
                                <div style="display: flex;">
                                    <el-button size="small"
                                                icon="Delete"
                                                type="text"
                                                @click="delelteCondition(scope.$index)">删除
                                    </el-button>
                                </div>
                            </template>
                        </el-table-column>
                    </el-table>
                      
                    </el-row>
                  </el-form>
                </el-row>
                <div slot="footer" class="dialog-footer">
                  <el-button @click="dialogCondVisible = false">取 消</el-button>
                  <el-button type="primary" @click="createJudgeCode">确 定</el-button>
                </div>
              </el-dialog>

            </el-form-item>
          </el-tab-pane>

          <el-tab-pane label="测试用例" name="second">
            <el-row>
              <el-col :span="6">
                <el-form-item :label="$t('m.TestCase')" :error="error.testcase">
                  <el-upload
                    action="/api/admin/test_case"
                    name="file"
                    :data="{spj: problem.spj}"
                    :show-file-list="true"
                    :on-success="uploadSucceeded"
                    :on-error="uploadFailed">
                    <el-button size="small" type="primary" icon="el-icon-fa-upload">Choose File</el-button>
                  </el-upload>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="24">
                <el-table
                  :data="problem.test_case_score"
                  style="width: 100%">
                  <el-table-column
                    prop="input_name"
                    :label="$t('m.Input')">
                  </el-table-column>
                  <el-table-column
                    prop="output_name"
                    :label="$t('m.Output')">
                  </el-table-column>
                  <el-table-column
                    prop="score"
                    :label="$t('m.Score')">
                    <template slot-scope="scope">
                      <el-input
                        size="small"
                        :placeholder="$t('m.Score')"
                        v-model="scope.row.score">
                      </el-input>
                    </template>
                  </el-table-column>
                </el-table>
              </el-col>
            </el-row>
          </el-tab-pane>
        </el-tabs>

        <el-form-item :label="$t('m.Source')">
          <el-input :placeholder="$t('m.Source')" v-model="problem.source"></el-input>
        </el-form-item>
        <save @click.native="submit()">Save</save>
      </el-form>
    </Panel>
  </div>
</template>

<script>
  import Simditor from '../../components/Simditor'
  import Accordion from '../../components/Accordion'
  import CodeMirror from '../../components/CodeMirror'
  import api from '../../api'

  export default {
    name: 'Problem',
    components: {
      Simditor,
      Accordion,
      CodeMirror
    },
    data () {
      return {
        styleObject: {
          'border-left': '2px solid green'
        },
        funcPreview: '',
        dataType: [
          {
            value: 'void',
            label: 'void'
          },
          {
            value: 'int',
            label: 'int'
          },
          {
            value: 'short',
            label: 'short'
          },
          {
            value: 'float',
            label: 'float'
          },
          {
            value: 'double',
            label: 'double'
          },
          {
            value: 'char',
            label: 'char'
          }
        ],
        conditionRelation: [
          {
            value: '=',
            label: '='
          },
          {
            value: '>',
            label: '>'
          },
          {
            value: '>=',
            label: '>='
          },
          {
            value: '<',
            label: '<'
          },
          {
            value: '<=',
            label: '<='
          },
          {
            value: '!=',
            label: '!='
          }
        ],
        conditionType: [
          {
            value: 'const',
            label: '常量'
          },
          {
            value: 'int',
            label: 'int'
          },
          {
            value: 'int*',
            label: 'int*'
          },
          {
            value: 'int[]',
            label: 'int[]'
          },
          {
            value: 'double',
            label: 'double'
          },
          {
            value: 'double*',
            label: 'double*'
          },
          {
            value: 'double[]',
            label: 'double[]'
          },
          {
            value: 'char',
            label: 'char'
          },
          {
            value: 'char*',
            label: 'char*'
          },
          {
            value: 'char[]',
            label: 'char[]'
          }
        ],
        dialogFormVisible: false,
        dialogEditVisible: false,
        dialogCondVisible: false,
        dialogPreviewVisible: false,
        condition: {
          condition: []
        },
        nextID: 1,
        parameterConfig: {
          id: 1,
          name: '',
          type: '',
          arr: false,
          ptr: false
        },
        activeName: 'first',
        rules: {
          _id: {required: true, message: 'Display ID is required', trigger: 'blur'},
          title: {required: true, message: 'Title is required', trigger: 'blur'},
          input_description: {required: true, message: 'Input Description is required', trigger: 'blur'},
          output_description: {required: true, message: 'Output Description is required', trigger: 'blur'}
        },
        loadingCompile: false,
        mode: '',
        contest: {},
        problem: {
          extra: false,
          languages: [],
          io_mode: {'io_mode': 'Standard IO', 'input': 'input.txt', 'output': 'output.txt'},
          judge_config: {
            judge_mode: 0,
            code_template: '',
            func_config: {
              name: '',
              parameter: [],
              init: '',
              return_type: ''
            },
            segment_config: {
              code: '',
              judge_code: ''
            }
          }
        },
        reProblem: {
          languages: [],
          io_mode: {'io_mode': 'Standard IO', 'input': 'input.txt', 'output': 'output.txt'}
        },
        testCaseUploaded: false,
        allLanguage: {},
        inputVisible: false,
        funcBlackVisible: false,
        funcWhiteVisible: false,
        styleWhiteVisible: false,
        paraVisible: false,
        tagInput: '',
        funcBlackInput: '',
        funcWhiteInput: '',
        styleWhiteInput: '',
        paraInput: '',
        template: {},
        title: '',
        spjMode: '',
        disableRuleType: false,
        routeName: '',
        error: {
          tags: '',
          spj: '',
          languages: '',
          testCase: ''
        }
      }
    },
    mounted () {
      this.routeName = this.$route.name
      if (this.routeName === 'edit-problem' || this.routeName === 'edit-contest-problem') {
        this.mode = 'edit'
      } else {
        this.mode = 'add'
      }
      api.getLanguages().then(res => {
        this.problem = this.reProblem = {
          _id: '',
          title: '',
          description: '',
          input_description: '',
          output_description: '',
          time_limit: 1000,
          memory_limit: 256,
          difficulty: 'Low',
          visible: true,
          share_submission: false,
          tags: [],
          languages: [],
          template: {},
          samples: [{input: '', output: ''}],
          spj: false,
          extra: false,
          spj_language: '',
          spj_code: '',
          spj_compile_ok: false,
          test_case_id: '',
          test_case_score: [],
          rule_type: 'ACM',
          hint: '',
          source: '',
          io_mode: {'io_mode': 'Standard IO', 'input': 'input.txt', 'output': 'output.txt'},
          extra_config: {
            format: {
              enable: false,
              score_type: 1,
              indent_size: 4,
              left_big_para: false,
              comma_space: false,
              max_statement: 3
            },
            function: {
              enable: false,
              score_type: 1,
              black_list: [],
              white_list: [],
              max_statement: 10,
              disableIO: false
            },
            memory: {
              enable: false,
              score_type: 1,
              white_list: [],
              check_ptr_free: false
            },
            style: {
              enable: false,
              score_type: 1,
              global_prefix: '',
              white_list: [],
              func_naming: 0,
              global_naming: 0,
              local_naming: 0,
              single_name: false
            },
            runtime: {
              enable: false,
              score_type: 1,
              time: 100,
              memory: 64
            }
          },
          extra_score: {
            format: 5,
            function: 5,
            memory: 5,
            style: 5,
            runtime: 5
          },
          judge_config: {
            judge_mode: 0,
            code_template: '',
            func_config: {
              name: '',
              parameter: [],
              init: '',
              return_type: ''
            },
            segment_config: {
              code: '',
              judge_code: ''
            }
          }
        }
        let contestID = this.$route.params.contestId
        if (contestID) {
          this.problem.contest_id = this.reProblem.contest_id = contestID
          this.disableRuleType = true
          api.getContest(contestID).then(res => {
            this.problem.rule_type = this.reProblem.rule_type = res.data.data.rule_type
            this.contest = res.data.data
          })
        }

        this.problem.spj_language = 'C'

        let allLanguage = res.data.data
        this.allLanguage = allLanguage

        // get problem after getting languages list to avoid find undefined value in `watch problem.languages`
        if (this.mode === 'edit') {
          this.title = this.$i18n.t('m.Edit_Problem')
          let funcName = {'edit-problem': 'getProblem', 'edit-contest-problem': 'getContestProblem'}[this.routeName]
          api[funcName](this.$route.params.problemId).then(problemRes => {
            let data = problemRes.data.data
            if (!data.spj_code) {
              data.spj_code = ''
            }
            data.spj_language = data.spj_language || 'C'
            this.problem = data
            this.testCaseUploaded = true
          })
        } else {
          this.title = this.$i18n.t('m.Add_Problem')
          for (let item of allLanguage.languages) {
            this.problem.languages.push(item.name)
          }
        }
      })
    },
    watch: {
      '$route' () {
        this.$refs.form.resetFields()
        this.problem = this.reProblem
      },
      'problem.languages' (newVal) {
        let data = {}
        // use deep copy to avoid infinite loop
        let languages = JSON.parse(JSON.stringify(newVal)).sort()
        for (let item of languages) {
          if (this.template[item] === undefined) {
            let langConfig = this.allLanguage.languages.find(lang => {
              return lang.name === item
            })
            if (this.problem.template[item] === undefined) {
              data[item] = {checked: false, code: langConfig.config.template, mode: langConfig.content_type}
            } else {
              data[item] = {checked: true, code: this.problem.template[item], mode: langConfig.content_type}
            }
          } else {
            data[item] = this.template[item]
          }
        }
        this.template = data
      },
      'problem.spj_language' (newVal) {
        this.spjMode = this.allLanguage.spj_languages.find(item => {
          return item.name === this.problem.spj_language
        }).content_type
      }
    },
    methods: {
      functionPreview () {
        let data = {
          name: this.problem.judge_config.func_config.name,
          return_type: this.problem.judge_config.func_config.return_type,
          parameter: this.problem.judge_config.func_config.parameter
        }
        api.getFunctionPreview(data).then(res => {
          this.funcPreview = res.data.data
          this.dialogPreviewVisible = true
        }).catch(() => {
        })
      },
      delelteCondition (index) {
        this.condition.condition.splice(index, 1)
      },
      addCondition () {
        this.condition.condition.push({
          parameter: '',
          data_type: '',
          relation: '',
          value: '',
          value_type: ''
        })
      },
      boolFormatter (row, column, cellValue) {
        return String(cellValue)
      },
      deleteParameter (id) {
        let index = this.problem.judge_config.func_config.parameter.findIndex(item => {
          if (item.id === id) {
            return true
          }
        })
        this.problem.judge_config.func_config.parameter.splice(index, 1)
      },
      clearParameter () {
        this.parameterConfig.name = ''
        this.parameterConfig.type = ''
        this.parameterConfig.arr = false
        this.parameterConfig.ptr = false
      },
      addParameter () {
        let index = this.problem.judge_config.func_config.parameter.findIndex(item => {
          if (item.name === this.parameterConfig.name) {
            return true
          }
        })
        if (index !== -1) {
          this.$message.error('参数名已存在')
          return
        }
        this.problem.judge_config.func_config.parameter.push({
          id: this.nextID++,
          name: this.parameterConfig.name,
          type: this.parameterConfig.type,
          arr: this.parameterConfig.arr,
          ptr: this.parameterConfig.ptr
        })
        this.clearParameter()
        this.dialogFormVisible = false
      },
      openEditParameter (row) {
        this.parameterConfig = {...row}
        this.dialogEditVisible = true
      },
      editParameter () {
        let index = this.problem.judge_config.func_config.parameter.findIndex(item => {
          if (item.id === this.parameterConfig.id) {
            return true
          }
        })
        if (index === -1) {
          this.$message.error('参数不存在')
          return
        }
        this.$set(this.problem.judge_config.func_config.parameter, index, {...this.parameterConfig})
        this.clearParameter()
        this.dialogEditVisible = false
      },
      switchSpj () {
        if (this.testCaseUploaded) {
          this.$confirm('If you change problem judge method, you need to re-upload test cases', 'Warning', {
            confirmButtonText: 'Yes',
            cancelButtonText: 'Cancel',
            type: 'warning'
          }).then(() => {
            this.problem.spj = !this.problem.spj
            this.resetTestCase()
          }).catch(() => {
          })
        } else {
          this.problem.spj = !this.problem.spj
        }
      },
      switchEx () {
        this.problem.extra = !this.problem.extra
        console.log(this.problem)
      },
      querySearch (queryString, cb) {
        api.getProblemTagList({ keyword: queryString }).then(res => {
          let tagList = []
          for (let tag of res.data.data) {
            tagList.push({value: tag.name})
          }
          cb(tagList)
        }).catch(() => {
        })
      },
      resetTestCase () {
        this.testCaseUploaded = false
        this.problem.test_case_score = []
        this.problem.test_case_id = ''
      },
      addTag () {
        let inputValue = this.tagInput
        if (inputValue) {
          this.problem.tags.push(inputValue)
        }
        this.inputVisible = false
        this.tagInput = ''
      },
      addFuncBlack () {
        let inputValue = this.funcBlackInput
        if (inputValue) {
          this.problem.extra_config.function.black_list.push(inputValue)
        }
        this.funcBlackVisible = false
        this.funcBlackInput = ''
      },
      addFuncWhite () {
        let inputValue = this.funcWhiteInput
        if (inputValue) {
          this.problem.extra_config.function.white_list.push(inputValue)
        }
        this.funcWhiteVisible = false
        this.funcWhiteInput = ''
      },
      addStyleWhite () {
        let inputValue = this.styleWhiteInput
        if (inputValue) {
          this.problem.extra_config.style.white_list.push(inputValue)
        }
        this.styleWhiteVisible = false
        this.styleWhiteInput = ''
      },
      addPara () {
        let inputValue = this.paraInput
        if (inputValue) {
          this.problem.extra_config.memory.white_list.push(inputValue)
        }
        this.paraVisible = false
        this.paraInput = ''
      },
      closeTag (tag) {
        this.problem.tags.splice(this.problem.tags.indexOf(tag), 1)
      },
      closeFuncBlack (tag) {
        this.problem.extra_config.function.black_list.splice(this.problem.extra_config.function.black_list.indexOf(tag), 1)
      },
      closeFuncWhite (tag) {
        this.problem.extra_config.function.white_list.splice(this.problem.extra_config.function.white_list.indexOf(tag), 1)
      },
      closeStyleWhite (tag) {
        this.problem.extra_config.style.white_list.splice(this.problem.extra_config.style.white_list.indexOf(tag), 1)
      },
      closePara (tag) {
        this.problem.extra_config.memory.white_list.splice(this.problem.extra_config.memory.white_list.indexOf(tag), 1)
      },
      addSample () {
        this.problem.samples.push({input: '', output: ''})
      },
      deleteSample (index) {
        this.problem.samples.splice(index, 1)
      },
      uploadSucceeded (response) {
        if (response.error) {
          this.$error(response.data)
          return
        }
        let fileList = response.data.info
        for (let file of fileList) {
          file.score = (100 / fileList.length).toFixed(0)
          if (!file.output_name && this.problem.spj) {
            file.output_name = '-'
          }
        }
        this.problem.test_case_score = fileList
        this.testCaseUploaded = true
        this.problem.test_case_id = response.data.id
      },
      uploadFailed () {
        this.$error('Upload failed')
      },
      compileSPJ () {
        let data = {
          id: this.problem.id,
          spj_code: this.problem.spj_code,
          spj_language: this.problem.spj_language
        }
        this.loadingCompile = true
        api.compileSPJ(data).then(res => {
          this.loadingCompile = false
          this.problem.spj_compile_ok = true
          this.error.spj = ''
        }, err => {
          this.loadingCompile = false
          this.problem.spj_compile_ok = false
          const h = this.$createElement
          this.$msgbox({
            title: 'Compile Error',
            type: 'error',
            message: h('pre', err.data.data),
            showCancelButton: false,
            closeOnClickModal: false,
            customClass: 'dialog-compile-error'
          })
        })
      },
      createJudgeCode () {
        for (let cond of this.condition.condition) {
          if (cond.data_type === 'const') {
            this.$error('变量不能为常量')
            return
          }
        }
        api.createJudgeCode(this.condition).then(res => {
          this.problem.judge_config.segment_config.judge_code = res.data.data
          this.dialogCondVisible = false
        }).catch(() => {
        })
      },
      submit () {
        if (!this.problem.samples.length) {
          this.$error('Sample is required')
          return
        }
        for (let sample of this.problem.samples) {
          if (!sample.input || !sample.output) {
            this.$error('Sample input and output is required')
            return
          }
        }
        if (!this.problem.tags.length) {
          this.error.tags = 'Please add at least one tag'
          this.$error(this.error.tags)
          return
        }
        if (this.problem.spj) {
          if (!this.problem.spj_code) {
            this.error.spj = 'Spj code is required'
            this.$error(this.error.spj)
          } else if (!this.problem.spj_compile_ok) {
            this.error.spj = 'SPJ code has not been successfully compiled'
          }
          if (this.error.spj) {
            this.$error(this.error.spj)
            return
          }
        }
        if (!this.problem.languages.length) {
          this.error.languages = 'Please choose at least one language for problem'
          this.$error(this.error.languages)
          return
        }
        if (!this.testCaseUploaded) {
          this.error.testCase = 'Test case is not uploaded yet'
          this.$error(this.error.testCase)
          return
        }
        if (this.problem.rule_type === 'OI') {
          for (let item of this.problem.test_case_score) {
            try {
              if (parseInt(item.score) <= 0) {
                this.$error('Invalid test case score')
                return
              }
            } catch (e) {
              this.$error('Test case score must be an integer')
              return
            }
          }
        }
        this.problem.languages = this.problem.languages.sort()
        this.problem.template = {}
        for (let k in this.template) {
          if (this.template[k].checked) {
            this.problem.template[k] = this.template[k].code
          }
        }
        let funcName = {
          'create-problem': 'createProblem',
          'edit-problem': 'editProblem',
          'create-contest-problem': 'createContestProblem',
          'edit-contest-problem': 'editContestProblem'
        }[this.routeName]
        // edit contest problem 时, contest_id会被后来的请求覆盖掉
        if (funcName === 'editContestProblem') {
          this.problem.contest_id = this.contest.id
        }
        api[funcName](this.problem).then(res => {
          if (this.routeName === 'create-contest-problem' || this.routeName === 'edit-contest-problem') {
            this.$router.push({name: 'contest-problem-list', params: {contestId: this.$route.params.contestId}})
          } else {
            this.$router.push({name: 'problem-list'})
          }
        }).catch(() => {
        })
      }
    }
  }
</script>

<style lang="less" scoped>
  .problem {
    .difficulty-select {
      width: 120px;
    }
    .spj-radio {
      margin-left: 10px;
      &:last-child {
        margin-right: 20px;
      }
    }
    .input-new-tag {
      width: 78px;
    }
    .button-new-tag {
      height: 24px;
      line-height: 22px;
      padding-top: 0;
      padding-bottom: 0;
    }
    .tags {
      .el-tag {
        margin-right: 10px;
      }
    }
    .accordion {
      margin-bottom: 10px;
    }
    .add-samples {
      width: 100%;
      background-color: #fff;
      border: 1px dashed #aaa;
      outline: none;
      cursor: pointer;
      color: #666;
      height: 35px;
      font-size: 14px;
      &:hover {
        background-color: #f9fafc;
      }
      i {
        margin-right: 10px;
      }
    }
    .add-sample-btn {
      margin-bottom: 10px;
    }

  }
</style>

<style>
  .problem-tag-poper {
    width: 200px !important;
  }
  .dialog-compile-error {
    width: auto;
    max-width: 80%;
    overflow-x: scroll;
  }
</style>

<style scoped lang="less">
  pre {
    padding: 0;
    display: block;
    code {
      padding: 20px;
      font-size: 1.1em;
    }
  }
</style>

