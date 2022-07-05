<template>
  <p-layout-default class="queue">
    <template #header>
      <PageHeadingWorkQueue v-if="workQueue" :queue="workQueue" @update="workQueueSubscription.refresh" @delete="routeToQueues" />
    </template>

    <p-tabs :tabs="['Details', 'Deployments']">
      <template #details>
        <WorkQueueDetails v-if="workQueue" :work-queue="workQueue" />
      </template>
      <template #deployments>
        <DeploymentsTable :deployments="workQueueDeployments" @update="workQueueDeploymentSubscription.refresh()" @delete="workQueueDeploymentSubscription.refresh()" />
      </template>
    </p-tabs>
  </p-layout-default>
</template>

<script lang="ts" setup>
  import { UnionFilters, WorkQueueDetails, PageHeadingWorkQueue, DeploymentsTable } from '@prefecthq/orion-design'
  import { useSubscription, useRouteParam } from '@prefecthq/vue-compositions'
  import { computed } from 'vue'
  import { useRouter } from 'vue-router'
  import { routes } from '@/router'
  import { deploymentsApi } from '@/services/deploymentsApi'
  import { workQueuesApi } from '@/services/workQueuesApi'

  const router = useRouter()

  const workQueueId = useRouteParam('id')
  const subscriptionOptions = {
    interval: 300000,
  }

  const workQueueSubscription = useSubscription(workQueuesApi.getWorkQueue, [workQueueId.value], subscriptionOptions)
  const workQueue = computed(() => workQueueSubscription.response)
  const workQueueDeploymentIds = computed(() => workQueue?.value?.filter?.deploymentIds ?? [])

  const workQueueDeploymentFilter = computed<UnionFilters>(() => ({
    deployments: {
      id: {
        any_: workQueueDeploymentIds.value,
      },
    },
  }))
  const workQueueDeploymentSubscription = useSubscription(deploymentsApi.getDeployments, [workQueueDeploymentFilter], subscriptionOptions)
  const workQueueDeployments = computed(() => workQueueDeploymentSubscription.response ?? [])

  const routeToQueues = (): void => {
    router.push(routes.workQueues())
  }
</script>