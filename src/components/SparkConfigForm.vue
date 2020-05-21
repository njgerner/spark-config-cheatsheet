<template>
  <section>
    <b-field label="Master Memory (GB)"
             message="The RAM available on your master node. This can be the entirety of the
             node’s available memory, or you can set it lower if you have other processes running
             on the master node."
    >
      <b-input type="number" min="0" v-model="sparkConfig.masterMemoryGB"></b-input>
    </b-field>

    <b-field label="Master Cores"
             message="The cores available on your master node. As with the memory, this can be all
             available cores, or it can be set to a lower number if other processes are running on
             the master node."
    >
      <b-input type="number" min="0" v-model="sparkConfig.masterCoreCount">
      </b-input>
    </b-field>

    <b-field label="Number of Worker Nodes"
             message="The number of worker machines in your cluster. This can be as low as one
             machine."
    >
      <b-input type="number" min="0" v-model="sparkConfig.workerNodeCount">
      </b-input>
    </b-field>

    <b-field label="Memory Per Worker Node (GB)"
             message="The amount of RAM per node that is available for Spark’s use. If using Yarn,
             this will be the amount of RAM per machine managed by Yarn Resource Manager."
    >
      <b-input type="number" min="0" v-model="sparkConfig.workerMemoryGB">
      </b-input>
    </b-field>

    <b-field label="Cores Per Worker Node"
             message="The number of cores per node that are available for Spark’s use. If using
             Yarn, this will be the number of cores per machine managed by Yarn Resource Manager."
    >
      <b-input type="number" min="0" v-model="sparkConfig.workerCoreCount">
      </b-input>
    </b-field>

    <hr>

    <div class="field">
      <b-switch v-model="sparkConfig.overrideSettings">Override Recommended Settings</b-switch>
    </div>

    <b-field label="Memory Overhead Coefficient"
             message="The percentage of memory in each executor that will be reserved for
             spark.yarn.executor.memoryOverhead."
    >
      <b-input
        type="number" min="0" v-model="sparkConfig.memoryOverheadCoefficient"
        :disabled="!overrideSettings">
      </b-input>
    </b-field>

    <b-field label="Executor Memory Upper Bound (GB)"
             message="The upper bound for executor memory. Each executor runs on its own JVM.
             Upwards of 64GB of memory and garbage collection issues can cause slowness."
    >
      <b-input
        type="number" min="0" v-model="sparkConfig.executorMemoryUpperGB"
        :disabled="!overrideSettings">
      </b-input>
    </b-field>

    <b-field label="Executor Core Upper Bound"
             message="The upper bound for cores per executor. More than 5 cores per executor can
             degrade HDFS I/O throughput. I believe this value can safely be increased if writing
             to a web-based “file system” such as S3, but significant increases to this limit are
             not recommended."
    >
      <b-input
        type="number" min="0" v-model="sparkConfig.executorCoreMemoryUpperGB"
        :disabled="!overrideSettings">
      </b-input>
    </b-field>

    <b-field label="OS Reserved Cores"
             message="Cores per machine to reserve for OS processes. Should be zero if only a
             percentage of the machine’s cores were made available to Spark (i.e. entered in the
             Cores Per Node field above)."
    >
      <b-input
        type="number" min="0" v-model="sparkConfig.osReservedCoreCount"
        :disabled="!overrideSettings">
      </b-input>
    </b-field>

    <b-field label="OS Reserved Memory (GB)"
             message="The amount of RAM per machine to reserve for OS processes. Should be zero if
             only a percentage of the machine’s RAM was made available to Spark (i.e. entered in
             the Memory Per Node field above)."
    >
      <b-input
        type="number" min="0" v-model="sparkConfig.osReservedMemoryGB"
        :disabled="!overrideSettings">
      </b-input>
    </b-field>

    <b-field label="Parallelism Per Core"
             message="The level of parallelism per allocated core. This field is used to determine
             the spark.default.parallelism setting. Generally recommended setting for this value
             is double the number of cores."
    >
      <b-input
        type="number" min="0" v-model="sparkConfig.coreParallelism" :disabled="!overrideSettings">
      </b-input>
    </b-field>

  </section>
</template>

<script>
export default {
  name: 'spark-config-form',
  props: {
    sparkConfig: Object,
  },
};
</script>
