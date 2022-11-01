<template>
  <the-header></the-header>
  <a-layout>
    <a-layout-sider width="200" style="background: #fff">
      <a-menu
          v-model:selectedKeys="selectedKeys2"
          v-model:openKeys="openKeys"
          mode="inline"
          :style="{ height: '100%', borderRight: 0 }"
      >
        <a-menu-item key="1">Calendar</a-menu-item>
        <a-menu-item key="2">Student Tree Map</a-menu-item>
        <a-menu-item key="3">Time Line</a-menu-item>

      </a-menu>
    </a-layout-sider>
    <a-layout style="padding: 0 24px 24px; margin-top: 25px;height: 100%;">
      <!--      <a-breadcrumb style="margin: 16px 0">-->
      <!--        <a-breadcrumb-item>-->
      <!--          <router-link to="/">-->
      <!--            Management-->
      <!--          </router-link>-->
      <!--        </a-breadcrumb-item>-->
      <!--        <a-breadcrumb-item>-->
      <!--          <router-link to="/videso">-->
      <!--            videos-->
      <!--          </router-link>-->
      <!--        </a-breadcrumb-item>-->
      <!--      </a-breadcrumb>-->
      <a-layout-content
          :style="{ background: '#fff', padding: '24px', margin: 0, height: '800px' }"
      >
        <div v-if="selectedKeys2[0] === '1'">
          <a-calendar v-model:value="value">
            <template #dateCellRender="{ current }">
              <ul class="events">
                <li v-for="item in getListData(current)" :key="item.content">
                  <a-popover title="Modify">
                    <template #content>
                      <div class="inlinebuttondiv">
                        <a-button type="link" @click="editdata(item.id)">edit</a-button>
                        <a-button type="link">delete</a-button>
                      </div>
                    </template>
                    <span class="event"> <a-badge :status="item.type" :text="item.content"
                                                  @click="deleteData(item.id)"/></span>


                  </a-popover>

                </li>
              </ul>
            </template>
            <template #monthCellRender="{ current }">
              <div v-if="getMonthData(current)" class="notes-month">
                <section>{{ getMonthData(current) }}</section>
                <span>Backlog number</span>
              </div>
            </template>
          </a-calendar>
        </div>
        <div v-if="selectedKeys2[0] === '2'">
          <a-tree
              v-model:expandedKeys="expandedKeys"
              v-model:selectedKeys="selectedKeys"
              :load-data="onLoadData"
              :tree-data="treeData"
          />
        </div>
        <div v-if="selectedKeys2[0] === '3'">
          <a-timelinew>
            <a-list :data-source="timeline">
            <template #renderItem="{item}">
              <a-timeline-item>{{item}}</a-timeline-item>

            </template>

          </a-list>
          </a-timelinew>

        </div>

      </a-layout-content>
    </a-layout>
  </a-layout>
</template>
<script lang="ts">
import {defineComponent, onMounted, ref} from 'vue';
// import { UserOutlined, LaptopOutlined, NotificationOutlined } from '@ant-design/icons-vue';
import dayjs, {Dayjs} from 'dayjs';
import TheHeader from "@/components/TheHeader.vue";
import type {TreeProps} from 'ant-design-vue';

export default defineComponent({
  components: {TheHeader},
  setup: function () {
    const date = ref(dayjs());
    const isclicked = ref<boolean>();
    const selectedValue = ref(dayjs());
    const timeline = ref<string[]>();
    timeline.value = ["test","test"];

    const onSelect = (value: Dayjs) => {
      date.value = value;
      selectedValue.value = value;
    };
    const onPanelChange = (value: Dayjs) => {
      date.value = value;
    };
    const value = ref<Dayjs>();

    onMounted(() => {
      isclicked.value = false;
    })
    const getListData = (value: Dayjs) => {
      let listData;
      switch (value.year()) {
        case 2022:
          switch (value.month()) {
            case 9:
              switch (value.date()) {
                case 8: 
                  listData = [
                    {id: '1', type: 'warning', content: 'This is warning event.'},
                    {id: '2', type: 'success', content: 'This is usual event.'},
                  ];
                  break;
                case 10:
                  listData = [
                    {id: '3', type: 'warning', content: 'This is warning event.'},
                    {id: '4', type: 'success', content: 'This is usual event.'},
                    {id: '5', type: 'error', content: 'This is error event.'},
                  ];
                  break;
                case 15:
                  listData = [
                    {id: '6', type: 'warning', content: 'This is warning event'},
                    {id: '7', type: 'success', content: 'This is very long usual event。。....'},
                    {id: '8', type: 'error', content: 'This is error event 1.'},
                    {id: '9', type: 'error', content: 'This is error event 2.'},
                    {id: '10', type: 'error', content: 'This is error event 3.'},

                    {id: '11', type: 'error', content: 'This is error event 4.'},
                  ];
                  break;
              }
              break;
            default:
          }
          return listData || [];
      }
    };
    const deleteData = (a: any) => {
      console.log(a)
    }
    const editdata = (dataid: any) => {
      console.log(dataid)
    }

    const getMonthData = (value: Dayjs) => {
      if (value.month() === 8) {
        return 1394;
      }
    };
    const expandedKeys = ref<string[]>([]);
    const selectedKeys = ref<string[]>([]);
    const treeData = ref<TreeProps['treeData']>([
      {title: 'Expand to load', key: '0'},
      {title: 'Expand to load', key: '1'},
      {title: 'Tree Node', key: '2', isLeaf: true},
    ]);
    const onLoadData: TreeProps['loadData'] = treeNode => {
      return new Promise(resolve => {
        //@ts-ignore
        if (treeNode.dataRef.children) {

          resolve();
          return;
        }
        setTimeout(() => {
          //@ts-ignore

          treeNode.dataRef.children = [

            {title: 'Child Node', key: `${treeNode.eventKey}-0`},
            {title: 'Child Node', key: `${treeNode.eventKey}-1`},
          ];
          //@ts-ignore

          treeData.value = [...treeData.value];
          resolve();

        }, 1000);
      });
    };
    return {
      date,
      selectedValue,
      onSelect,
      onPanelChange,
      selectedKeys1: ref<string[]>(['2']),
      selectedKeys2: ref<string[]>(['1']),
      openKeys: ref<string[]>(['sub1']),

      value,
      getListData,
      getMonthData,
      deleteData,
      editdata,
      expandedKeys,
      selectedKeys,
      treeData,
      onLoadData,
      timeline,
    };
  },
});
</script>
<style>

#components-layout-demo-top-side .logo {
  float: left;
  width: 120px;
  height: 31px;
  margin: 16px 24px 16px 0;
  background: rgba(255, 255, 255, 0.3);
}

.inlinebuttondiv {
  margin-left: 10px;
}

.ant-row-rtl #components-layout-demo-top-side .logo {
  float: right;
  margin: 16px 0 16px 24px;
}

.site-layout-background {
  background: #fff;
}

.events {
  list-style: none;
  margin: 0;
  padding: 0;
}

.events .ant-badge-status {
  overflow: hidden;
  white-space: nowrap;
  width: 100%;
  text-overflow: ellipsis;
  font-size: 12px;
}

.notes-month {
  text-align: center;
  font-size: 28px;
}

.notes-month section {
  font-size: 28px;
}

.event:hover {
  /*background-color: #999598;*/
  font-style: italic;
}
</style>