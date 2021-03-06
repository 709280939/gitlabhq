<script>
  import jobNameComponent from './job_name_component.vue';
  import jobComponent from './job_component.vue';
  import tooltip from '../../../vue_shared/directives/tooltip';

  /**
   * Renders the dropdown for the pipeline graph.
   *
   * The following object should be provided as `job`:
   *
   * {
   *   "id": 4256,
   *   "name": "test",
   *   "status": {
   *     "icon": "icon_status_success",
   *     "text": "passed",
   *     "label": "passed",
   *     "group": "success",
   *     "details_path": "/root/ci-mock/builds/4256",
   *     "action": {
   *       "icon": "icon_action_retry",
   *       "title": "Retry",
   *       "path": "/root/ci-mock/builds/4256/retry",
   *       "method": "post"
   *     }
   *   }
   * }
   */
  export default {
    props: {
      job: {
        type: Object,
        required: true,
      },
    },

    directives: {
      tooltip,
    },

    components: {
      jobComponent,
      jobNameComponent,
    },

    computed: {
      tooltipText() {
        return `${this.job.name} - ${this.job.status.label}`;
      },
    },

    methods: {
      /**
     * When the user right clicks or cmd/ctrl + click in the job name
     * the dropdown should not be closed and the link should open in another tab,
     * so we stop propagation of the click event inside the dropdown.
     *
     * Since this component is rendered multiple times per page we need to guarantee we only
     * target the click event of this component.
     */
      stopDropdownClickPropagation() {
        $(this.$el.querySelectorAll('.js-grouped-pipeline-dropdown a.mini-pipeline-graph-dropdown-item'))
          .on('click', (e) => {
            e.stopPropagation();
          });
      },
    },

    mounted() {
      this.stopDropdownClickPropagation();
    },
  };
</script>
<template>
  <div>
    <button
      v-tooltip
      type="button"
      data-toggle="dropdown"
      data-container="body"
      class="dropdown-menu-toggle build-content"
      :title="tooltipText">

      <job-name-component
        :name="job.name"
        :status="job.status" />

      <span class="dropdown-counter-badge">
        {{job.size}}
      </span>
    </button>

    <ul class="dropdown-menu big-pipeline-graph-dropdown-menu js-grouped-pipeline-dropdown">
      <li class="scrollable-menu">
        <ul>
          <li v-for="item in job.jobs">
            <job-component
              :job="item"
              :is-dropdown="true"
              css-class-job-name="mini-pipeline-graph-dropdown-item"
              />
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>
