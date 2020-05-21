<template>
  <b-table :data="data" :columns="columns"></b-table>
</template>

<script>
import { find, get, multiply } from 'lodash';

const executorConfigJson = '../data/executorConfig.json';
export default {
  name: 'spark-config-output',
  props: {
    sparkConfig: Object,
  },
  computed: {
    executorConfig() {
      return find(executorConfigJson, {
        'Executors Per Node': String(this.executorsPerNode),
      });
    },
  },
  data() {
    return {
      executorsPerNode: 6, // TODO - make configurable
      data: [{
        key: 'spark.executor.instances',
        value: multiply(this.executorsPerNode, get(this.sparkConfig, 'workerNodeCount')),
      }, {
        key: 'spark.yarn.executor.memoryOverhead',
        value: multiply(
          this.executorsPerNode,
          get(this.executorConfig, 'Overhead Memory Per Executor'),
        ) * 1024,
      }, {
        key: 'spark.executor.memory',
        value: '',
      }, {
        key: 'spark.yarn.driver.memoryOverhead',
        value: '',
      }, {
        key: 'spark.driver.memory',
        value: '',
      }, {
        key: 'spark.executor.cores',
        value: '',
      }, {
        key: 'spark.driver.cores',
        value: '',
      }, {
        key: 'spark.default.parallelism',
        value: '',
      }],
      columns: [{
        field: 'key',
        label: 'Spark Config',
      }, {
        field: 'value',
        label: 'Value',
      }],
    };
  },
};
</script>
