<template>
  <div class="wrapper--data--list">
    <data-card :title="`${$t('stakingV3.vote')}/${$t('stakingV3.build')}`">{{
      periodName
    }}</data-card>
    <data-card :title="`${$t('stakingV3.era')} /${periodDuration}`">{{
      periodCurrentDay
    }}</data-card>
    <data-card :title="$t('stakingV3.era')">{{ protocolState?.era }}</data-card>
    <data-card :title="$t('stakingV3.tvl')">
      <token-balance-native :balance="tvl" />
    </data-card>
    <data-card :title="$t('stakingV3.unbonding')">
      <token-balance-native :balance="unlocking" />
    </data-card>
    <data-card :title="$t('stakingV3.numberOfDapps')">{{ totalDapps }}</data-card>
    <data-card :title="$t('stakingV3.filledSlot')">
      {{ dAppTiers?.dapps.length ?? 0 }} / {{ tiersConfiguration.numberOfSlots }}
    </data-card>
    <data-card :title="$t('stakingV3.unfilledSlot')">
      {{ unfilledSlots }} / {{ tiersConfiguration.numberOfSlots }}
    </data-card>
  </div>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue';
import DataCard from './DataCard.vue';
import { useDappStaking, useDapps, usePeriod } from 'src/staking-v3/hooks';
import TokenBalanceNative from 'src/components/common/TokenBalanceNative.vue';

export default defineComponent({
  components: {
    DataCard,
    TokenBalanceNative,
  },
  setup() {
    const { protocolState, currentEraInfo, dAppTiers, tiersConfiguration } = useDappStaking();
    const { registeredDapps } = useDapps();
    const { periodName, periodDuration, periodCurrentDay } = usePeriod();

    const totalDapps = computed<number>(() => registeredDapps.value?.length ?? 0);
    const tvl = computed<string>(() => (currentEraInfo.value?.totalLocked ?? BigInt(0)).toString());
    const unlocking = computed<string>(() =>
      (currentEraInfo.value?.unlocking ?? BigInt(0)).toString()
    );
    const unfilledSlots = computed<number>(
      () => tiersConfiguration.value.numberOfSlots - dAppTiers.value.dapps.length
    );

    return {
      protocolState,
      periodName,
      periodDuration,
      periodCurrentDay,
      totalDapps,
      tvl,
      unlocking,
      dAppTiers,
      unfilledSlots,
      tiersConfiguration,
    };
  },
});
</script>

<style lang="scss" scoped>
.wrapper--data--list {
  display: grid;
  width: 100%;
  gap: 8px;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  @media (min-width: $md) {
    grid-template-columns: repeat(4, minmax(0, 1fr));
  }
}
</style>
