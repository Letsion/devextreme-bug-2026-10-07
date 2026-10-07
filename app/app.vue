<template>
  <div class="demo">
    <div
        v-if="data.contentType === ContentType.Sport && data.sportsDbContentSetting"
        class="sports-box">
      <h2>Sports DB</h2>

      <DxValidationGroup ref="sportsDbValidationGroup">
        <div class="switch-row">
          <span>Enabled</span>
          <DxSwitch
              v-model:value="data.sportsDbContentSetting.isEnabled"
              :disabled="!editMode" />
        </div>

        <div class="fields">
          <DxNumberBox
              v-model:value="data.sportsDbContentSetting.leagueId as number | undefined"
              label="LeagueId"
              styling-mode="underlined"
              label-mode="floating"
              :disabled="!editMode || !data.sportsDbContentSetting.isEnabled"
              :show-spin-buttons="false"
              :min="1">
            <DxValidator v-if="data.sportsDbContentSetting.isEnabled">
              <DxRequiredRule />
            </DxValidator>
          </DxNumberBox>

          <DxSelectBox
              v-model:value="data.sportsDbContentSetting.seasonType"
              :data-source="seasonTypes"
              display-expr="text"
              value-expr="value"
              label="SeasonType"
              styling-mode="underlined"
              label-mode="floating"
              width="100%"
              :disabled="!editMode || !data.sportsDbContentSetting.isEnabled">
            <DxValidator v-if="data.sportsDbContentSetting.isEnabled">
              <DxRequiredRule />
            </DxValidator>
          </DxSelectBox>
        </div>
      </DxValidationGroup>
    </div>

    <DxButton
        class="save-button"
        text="Save"
        type="default"
        styling-mode="contained"
        @click="saveContent" />

    <p role="status">{{ resultMessage }}</p>
    <pre>{{ JSON.stringify(data, null, 2) }}</pre>
  </div>
</template>

<script setup lang="ts">
import { ref, useTemplateRef } from 'vue';
import DxButton from 'devextreme-vue/button';
import DxNumberBox from 'devextreme-vue/number-box';
import DxSelectBox from 'devextreme-vue/select-box';
import DxSwitch from 'devextreme-vue/switch';
import DxValidationGroup from 'devextreme-vue/validation-group';
import DxValidator, { DxRequiredRule } from 'devextreme-vue/validator';
import 'devextreme/dist/css/dx.light.css';

interface SportsDbContentSetting {
  isEnabled: boolean;
  leagueId: number | null;
  seasonType: number | null;
}

interface ContentData {
  contentType: number;
  sportsDbContentSetting: SportsDbContentSetting | null;
}

const ContentType = { Sport: 0 } as const;

const data = ref<ContentData>({
  contentType: ContentType.Sport,
  sportsDbContentSetting: {
    isEnabled: false,
    leagueId: null,
    seasonType: null,
  },
});

const editMode = ref(true);
const resultMessage = ref('');

const seasonTypes = [
  { text: 'FullYear', value: 0 },
  { text: 'TwoYear', value: 1 },
];

const sportsDbValidationGroupRef =
    useTemplateRef<InstanceType<typeof DxValidationGroup>>('sportsDbValidationGroup');

function saveContent() {
  if (
      data.value.sportsDbContentSetting?.isEnabled &&
      !(sportsDbValidationGroupRef.value?.instance?.validate().isValid ?? false)
  ) {
    resultMessage.value = 'Validation failed. Saving is blocked.';
    return;
  }

  resultMessage.value = data.value.sportsDbContentSetting?.isEnabled
      ? 'Validation passed. Saving is allowed.'
      : 'Sports DB is disabled. Validation is skipped.';
}
</script>

<style scoped>
.demo {
  max-width: 680px;
  margin: 24px auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.sports-box {
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

h2 {
  margin: 0 0 16px;
  font-size: 16px;
  font-weight: 400;
}

.switch-row {
  display: flex;
  align-items: center;
  gap: 8px;
}

.fields {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 20px;
  margin-top: 20px;
}

.save-button {
  margin-top: 20px;
}

pre {
  padding: 16px;
  background: #f5f5f5;
  overflow: auto;
}
</style>