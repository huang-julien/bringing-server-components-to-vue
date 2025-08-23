<template>
    <NConfigProvider :theme="darkTheme">
        <n-timeline horizontal size="large">
            <n-timeline-item class="flex-1" title="V1" time="2016" color="white" />
            <n-timeline-item content="V2" class="flex-1" time="2018" color="white" />
            <n-timeline-item  line-type="dashed" title="V3" class="flex-1" time="2023" color="white" />
            <n-timeline-item class="flex-1" title="V4" time="2025" />
        </n-timeline>
    </NConfigProvider>
</template>

<script setup lang="ts">
import { NTimeline, NTimelineItem, NConfigProvider, darkTheme } from 'naive-ui';
</script>