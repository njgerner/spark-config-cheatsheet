<template>
  <div>
    <b-table :data="data" :columns="columns"></b-table>
    <br>
    <label class="title">spark-defaults.conf</label>
    <code v-html="sparkDefaultsConf"></code>
    <br>
    <label class="title">AWS EMR</label>
    <code v-html="awsEmr">
    </code>
  </div>

</template>

<script>
/* eslint-disable quote-props */

import {
  find, get, multiply, round, min, map, mapValues, keyBy,
} from 'lodash';

import executorConfigJson from '../data/executorConfig.json';

console.log(executorConfigJson);
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
    availableMasterMemory() {
      return get(this.sparkConfig, 'masterMemoryGB') - get(this.sparkConfig, 'osReservedMemoryGB');
    },
    availableMasterCores() {
      return get(this.sparkConfig, 'masterCoreCount') - get(this.sparkConfig, 'osReservedCoreCount');
    },
    availableWorkerMemory() {
      return get(this.sparkConfig, 'workerMemoryGB') - get(this.sparkConfig, 'osReservedMemoryGB');
    },
    availableWorkerCores() {
      return get(this.sparkConfig, 'workerCoreCount') - get(this.sparkConfig, 'osReservedCoreCount');
    },
    executorInstances() {
      return multiply(this.executorsPerNode, get(this.sparkConfig, 'workerNodeCount'));
    },
    driverMemoryOverhead() {
      return round(get(this.sparkConfig, 'memoryOverheadCoefficient') * this.availableMasterMemory, 0) * 1024;
    },
    sparkDefaultsConf() {
      return map(this.data, (data) => `${data.key} ${data.value}`).join('<br />');
    },
    awsEmr() {
      return {
        'Classification': 'spark-defaults',
        'Properties': mapValues(keyBy(this.data, 'key'), 'value'),
      };
    },
    data() {
      return [{
        key: 'spark.executor.instances',
        value: this.executorInstances,
      }, {
        key: 'spark.yarn.executor.memoryOverhead',
        value: this.executorConfig['Overhead Memory Per Executor'] * 1024,
      }, {
        key: 'spark.executor.memory',
        value: this.executorConfig['Memory Per Executor'],
      }, {
        key: 'spark.yarn.driver.memoryOverhead',
        value: this.driverMemoryOverhead,
      }, {
        key: 'spark.driver.memory',
        value: min([
          this.availableMasterMemory - (this.driverMemoryOverhead / 1024),
          get(this.sparkConfig, 'executorMemoryUpperGB') - (get(this.sparkConfig, 'executorMemoryUpperGB') * get(this.sparkConfig, 'memoryOverheadCoefficient')),
        ]),
      }, {
        key: 'spark.executor.cores',
        value: this.executorConfig['Cores Per Executor'],
      }, {
        key: 'spark.driver.cores',
        value: min([
          get(this.sparkConfig, 'executorCoreMemoryUpperGB'),
          this.availableMasterCores,
        ]),
      }, {
        key: 'spark.default.parallelism',
        value: this.executorInstances * this.executorConfig['Cores Per Executor'] * get(this.sparkConfig, 'coreParallelism'),
      }];
    },
  },
  data() {
    return {
      executorsPerNode: 6, // TODO - make configurable
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

<style lang="scss">
  code {
    display: block
  }
</style>
