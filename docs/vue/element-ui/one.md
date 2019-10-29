## el table里的label赋值为变量

```html
<el-dialog title="修改信息" :visible.sync="dialogFormVisible" :data="editRow">


            <el-form :label-position="labelPosition" label-width="80px" >
              <el-form-item :label=editRow.id>

                <el-input ></el-input>
              </el-form-item>
              <el-form-item label="活动区域">
                <el-input ></el-input>
              </el-form-item>
              <el-form-item label="活动形式">
                <el-input ></el-input>
              </el-form-item>
            </el-form>


          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
          </div>
        </el-dialog>
```
label前面加个逗号，表示label是个变量