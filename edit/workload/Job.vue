<script>
import { WORKLOAD_TYPES } from '@/config/types';
import UnitInput from '@/components/form/UnitInput';
import LabeledInput from '@/components/form/LabeledInput';
import RadioGroup from '@/components/form/RadioGroup';
import { mapGetters } from 'vuex';

export default {
  components: {
    UnitInput, LabeledInput, RadioGroup
  },
  props:      {
    // workload spec
    value: {
      type:    Object,
      default: () => {
        return {};
      }
    },
    type: {
      type:    String,
      default: WORKLOAD_TYPES.JOB
    }
  },
  data() {
    const {
      failedJobsHistoryLimit, successfulJobsHistoryLimit, suspend, schedule
    } = this.value;

    if (this.type === WORKLOAD_TYPES.CRON_JOB) {
      if (!this.value.jobTemplate) {
        this.$set(this.value, 'jobTemplate', { spec: {} });
      }
      const {
        completions, parallelism, backOffLimit, activeDeadlineSeconds
      } = this.value.jobTemplate.spec;

      return {
        completions, parallelism, backOffLimit, activeDeadlineSeconds, failedJobsHistoryLimit, successfulJobsHistoryLimit, suspend, schedule
      };
    } else {
      const {
        completions, parallelism, backOffLimit, activeDeadlineSeconds
      } = this.value;

      return {
        completions, parallelism, backOffLimit, activeDeadlineSeconds, failedJobsHistoryLimit, successfulJobsHistoryLimit, suspend, schedule
      };
    }
  },
  computed: {
    isCronJob() {
      return this.type === WORKLOAD_TYPES.CRON_JOB;
    },
    ...mapGetters({ t: 'i18n/t' })
  },
  methods: {
    update() {
      if (this.type === WORKLOAD_TYPES.JOB) {
        const spec = {
          ...this.value,
          suspend:                    this.suspend,
          schedule:                   this.schedule,
          completions:           this.completions,
          parallelism:           this.parallelism,
          backOffLimit:          this.backOffLimit,
          activeDeadlineSeconds: this.activeDeadlineSeconds,

        };

        this.$emit('input', spec);
      } else {
        const spec = {
          ...this.value,
          failedJobsHistoryLimit:     this.failedJobsHistoryLimit,
          successfulJobsHistoryLimit: this.successfulJobsHistoryLimit,
          suspend:                    this.suspend,
          schedule:                   this.schedule,
          jobTemplate:                {
            ...this.value.jobTemplate,
            completions:           this.completions,
            parallelism:           this.parallelism,
            backOffLimit:          this.backOffLimit,
            activeDeadlineSeconds: this.activeDeadlineSeconds
          }
        };

        this.$emit('input', spec);
      }
    }
  }
};
</script>

<template>
  <form @input="update">
    <div class="row mb-20">
      <div class="col span-6">
        <UnitInput v-model="completions" :suffix="completions===1 ? 'Time' : 'Times'">
          <template #label>
            <label :style="{'color':'var(--input-label)'}">
              {{ t('workload.job.completions.label') }}
              <i v-tooltip="t('workload.job.completions.tip')" class="icon icon-info" style="font-size: 14px" />
            </label>
          </template>
        </UnitInput>
      </div>
      <div class="col span-6">
        <UnitInput v-model="parallelism" class="col span-6" :suffix="parallelism===1 ? 'Time' : 'Times'">
          <template #label>
            <label :style="{'color':'var(--input-label)'}">
              {{ t('workload.job.completions.label') }}
              <i v-tooltip="t('workload.job.completions.tip')" class="icon icon-info" style="font-size: 14px" />
            </label>
          </template>
        </UnitInput>
      </div>
    </div>
    <div class="row mb-20">
      <div class="col span-6">
        <UnitInput v-model="backOffLimit" :suffix="backOffLimit===1 ? 'Time' : 'Times'">
          <template #label>
            <label :style="{'color':'var(--input-label)'}">
              {{ t('workload.job.backOffLimit.label') }}
              <i v-tooltip="t('workload.job.backOffLimit.tip')" class="icon icon-info" style="font-size: 14px" />
            </label>
          </template>
        </UnitInput>
      </div>
      <div class="col span-6">
        <UnitInput v-model="activeDeadlineSeconds" :suffix="activeDeadlineSeconds===1 ? 'Second' : 'Seconds'">
          <template #label>
            <label :style="{'color':'var(--input-label)'}">
              {{ t('workload.job.activeDeadlineSeconds.label') }}
              <i v-tooltip="t('workload.job.activeDeadlineSeconds.tip')" class="icon icon-info" style="font-size: 14px" />
            </label>
          </template>
        </UnitInput>
      </div>
    </div>
    <template v-if="isCronJob">
      <div class="row mb-20">
        <div class="col span-6">
          <LabeledInput v-model.number="successfulJobsHistoryLimit">
            <template #label>
              <label :style="{'color':'var(--input-label)'}">
                {{ t('workload.job.successfulJobsHistoryLimit.label') }}
                <i v-tooltip="t('workload.job.successfulJobsHistoryLimit.tip')" class="icon icon-info" style="font-size: 14px" />
              </label>
            </template>
          </LabeledInput>
        </div>
        <div class="col span-6">
          <LabeledInput v-model.number="failedJobsHistoryLimit">
            <template #label>
              <label :style="{'color':'var(--input-label)'}">
                {{ t('workload.job.failedJobsHistoryLimit.label') }}
                <i v-tooltip="t('workload.job.failedJobsHistoryLimit.tip')" class="icon icon-info" style="font-size: 14px" />
              </label>
            </template>
          </LabeledInput>
        </div>
      </div>
      <span>Suspend</span>
      <RadioGroup v-model="suspend" row :options="[true, false]" :labels="['Yes', 'No']" />
    </template>
  </form>
</template>
