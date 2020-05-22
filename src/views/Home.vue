<template>
  <div class="container">
    <h1 class="title">Apache Spark Config Cheatsheet</h1>
    <a href="https://www.patreon.com/bePatron?u=35846156" data-patreon-widget-type="become-patron-button">Become a Patron!</a><script async src="https://c6.patreon.com/becomePatronButton.bundle.js"></script>
    <br>
    <div class="columns">
      <div class="column">
        <spark-config-form :spark-config.sync="sparkConfig"></spark-config-form>
      </div>
      <div class="column">
        <spark-config-output :spark-config="sparkConfig"></spark-config-output>
      </div>
    </div>
    <p><b>More Information: </b></p><a href="https://blog.cloudera.com/how-to-tune-your-apache-spark-jobs-part-1/">https://blog.cloudera.com/how-to-tune-your-apache-spark-jobs-part-1/</a>
  </div>
</template>

<script>
// @ is an alias to /src
import SparkConfigOutput from '@/components/SparkConfigOutput';
import SparkConfigForm from '@/components/SparkConfigForm';

export default {
  name: 'Home',
  data() {
    return {
      sparkConfig: {
        // configurable
        masterMemoryGB: 256,
        masterCoreCount: 65,
        workerNodeCount: 5,
        workerMemoryGB: 256,
        workerCoreCount: 64,

        overrideSettings: false,
        // configurable but not recommended
        memoryOverheadCoefficient: 0.1,
        executorMemoryUpperGB: 64,
        executorCoreMemoryUpperGB: 5,
        osReservedCoreCount: 1,
        osReservedMemoryGB: 1,
        coreParallelism: 2,
      },
    };
  },
  components: {
    SparkConfigForm,
    SparkConfigOutput,
  },
};
</script>

<style lang="scss">
  .container {
    top: 2rem;
    margin-bottom: 2rem;
  }
</style>
