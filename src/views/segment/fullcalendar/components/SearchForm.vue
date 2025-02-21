<template>
  <div v-if="showSearchBtn" class="search-form-box">
    <el-form ref="searchFormRef" :inline="true" :model="searchForm" size="default">
      <el-row>
        <el-col v-for="cond in sliceCodList" :key="cond.id" :span="cond.span">
          <el-form-item :label="cond.label + ':'" :prop="cond.prop" class="flex-form-item">
            <!-- 输入框 -->
            <el-input
              v-if="cond.type === 'input'"
              v-model="searchForm[cond.prop]"
              :placeholder="cond.placeholder"
              clearable
            />
            <!-- 下拉框 -->
            <el-select
              v-else-if="cond.type === 'select'"
              v-model="searchForm[cond.prop]"
              :placeholder="cond.placeholder"
              clearable
            >
              <el-option
                v-for="op in cond.optionList"
                :key="op.value"
                :label="op.label"
                :value="op.value"
              />
            </el-select>
            <!-- 日期组合 -->
            <el-date-picker
              v-else-if="cond.type === 'daterange'"
              v-model="searchForm[cond.prop]"
              type="daterange"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
            />
            <!-- 日期时间 -->
            <el-date-picker
              v-else-if="cond.type === 'datetime'"
              v-model="searchForm[cond.prop]"
              type="datetime"
              placeholder="请选择日期时间"
              style="width: 100%"
            />
          </el-form-item>
        </el-col>
        <el-col :span="rSpanCount" class="el-col-wrapper">
          <el-form-item class="btn-group-item flex-end">
            <el-button type="primary" :icon="Search" @click="search">查询</el-button>
            <el-button plain :icon="RefreshRight" @click="reset">重置</el-button>
            <el-button v-show="showFoldBtn" type="primary" link @click="toggleFold">
              {{ conditionFold ? "展开" : "收起" }}
              <el-icon v-if="conditionFold">
                <ArrowDown />
              </el-icon>
              <el-icon v-else>
                <ArrowUp />
              </el-icon>
            </el-button>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
  </div>
</template>

<script lang="ts" setup name="searchForm">
import { Search, RefreshRight, ArrowDown, ArrowUp } from "@element-plus/icons-vue";
import { reactive, ref, computed, onMounted } from "vue";

// 查询条件的类型接口
interface SearchFormInterFace {
  //string | number | []
  [key: string]: any;
}

/**
 * updateTableList 组件外部传入的拉取表格数据的方法
 * conditionList   组件外部传入的查询条件的配置列表数据
 */
const props = defineProps(["updateTableList", "conditionList"]);

// 查询条件组件的实例
const searchFormRef = ref();

// 切换展开和收起查询条件
const conditionFold = ref(true);

const toggleFold = () => {
  conditionFold.value = !conditionFold.value;
};

// 初始化折叠查询条件的断点，从第几个查询条件开始（默认是从第3个，因为默认配置的span值是6）
const initConditionFoldLen = 3;

// 展示右侧按钮组（折叠||收起按钮）
const showFoldBtn = computed(() => {
  return props.conditionList.length > initConditionFoldLen;
});

// 展示查询条件组件
const showSearchBtn = computed(() => {
  return props.conditionList.length > 0;
});

// 右侧按钮组动态计算的span值
const rSpanCount = computed(() => {
  let totalSpan = 0;
  if (initConditionFoldLen > 0) {
    if (!conditionFold.value) {
      totalSpan = props.conditionList.reduce((prev: number, next: any) => {
        return prev + next.span;
      }, 0);
    } else {
      const sliceCondList = props.conditionList.slice(0, initConditionFoldLen);
      totalSpan = sliceCondList.reduce((prev: number, next: any) => {
        return prev + next.span;
      }, 0);
    }
  } else {
    totalSpan = props.conditionList.reduce((prev: number, next: any) => {
      return prev + next.span;
    }, 0);
  }
  return 24 - (totalSpan % 24);
});

// 打印计算出来的，右侧按钮组占的span值，再通过flex布局，flex-end 定位到最右边
console.log(rSpanCount.value, "rSpanCount@@@");

// 初始化查询条件的列表
const sliceCodList = computed(() => {
  return props.conditionList.slice(
    0,
    conditionFold.value ? initConditionFoldLen : props.conditionList.length
  );
});

// 查询条件表单数据
const searchForm = reactive<SearchFormInterFace>({});

// 搜索表单
const search = () => {
  props.updateTableList({
    pageSize: 10,
    currentPage: 1,
    ...searchForm,
  });
};
// 重置搜索
const reset = () => {
  searchFormRef.value.resetFields();
  props.updateTableList({
    pageSize: 10,
    currentPage: 1,
    ...searchForm,
  });
};

// 初始化查询条件的值
onMounted(() => {
  props.conditionList.forEach((cond: any) => {
    searchForm[cond.prop] = cond.type === "datetimerange" ? [] : "";
  });
});
</script>

<style lang="scss" scoped>
.search-form-box {
  width: calc(100% - 20px);
  height: 100px;
  box-sizing: border-box;
  box-shadow: var(--el-box-shadow-light);
  background-color: #fff;
  border-radius: 4px;
  padding: 12px;
  margin: 10px 10px 20px 10px;

  .flex-form-item {
    display: flex;
    align-items: center;
  }

  .el-col-wrapper {
    display: flex;

    .btn-group-item {
      flex: 1;
      display: flex;

      :deep(.el-form-item__content) {
        flex: 1;
        display: flex;
        align-items: center;
      }

      &.flex-end {
        :deep(.el-form-item__content) {
          justify-content: flex-end;
        }
      }
    }
  }
}
</style>
