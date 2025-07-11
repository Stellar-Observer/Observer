<template>
  <div class="row row-cards row-deck">
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.nrOfConnectableNodes"
        :is-loading="isLoading"
        title="Connectable nodes"
        tooltip="Number of nodes allowing incoming connections"
        :initial-data-loaded="initialDataLoaded"
        stats-property="nrOfConnectableNodesAverage"
        :year-statistics="yearStatistics"
      >
        <template #info>
          <h3>Nodes allowing incoming connections</h3>
          <p class="my-4">
            Number of nodes that allow incoming connections (including
            validators)
          </p>

          <h4>What if your node is missing?</h4>
          <p>
            A common issue is a closed port (default: 11625). Send us an
            <a
              :href="`mailto:${store.appConfig.brandEmail}`"
              rel="noopener"
              target="_blank"
            >
              e-mail</a
            >
            if the problem persists.
          </p>
        </template>
      </network-statistics-card>
    </div>
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.nrOfActiveValidators"
        :is-loading="isLoading"
        title="Validator nodes"
        tooltip="Number of active validators"
        :initial-data-loaded="initialDataLoaded"
        stats-property="nrOfActiveValidatorsAverage"
        :year-statistics="yearStatistics"
      >
        <template #info>
          <h3>Validator nodes</h3>
          <p class="my-4">
            Nodes that allow incoming connections and are actively voting on the
            network in the current crawl (including full validators)
          </p>

          <h4>What if your node not showing up?</h4>
          <p>
            A common issue is a closed port (default: 11625). Send us an
            <a
              :href="`mailto:${store.appConfig.brandEmail}`"
              rel="noopener"
              target="_blank"
            >
              e-mail</a
            >
            if the problem persists.
          </p>

          <h4>How to update the name of your validator?</h4>
          <p>
            Support
            <a
              target="_blank"
              href="https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0020.md"
              >sep-0020</a
            >
            and update the linked
            <a
              target="_blank"
              href="https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0001.md"
              rel="noopener"
              >stellar.toml</a
            >
            file to include your validator info.
          </p>
          <h4>
            What if your node is validating, but StellarAtlas labels it as not
            validating?
          </h4>
          <ul>
            <li>
              StellarAtlas detects nodes validating in a specific crawl/time
              window. It could be the validator is too slow for this window, or
              catching up.
            </li>
            <li>
              If StellarAtlas cannot connect to the node because it is at max
              capacity and the node is not trusted by other nodes, StellarAtlas
              cannot detect the validating status and the node is marked as not
              validating.
            </li>
          </ul>
        </template>
      </network-statistics-card>
    </div>
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.nrOfActiveFullValidators"
        :is-loading="isLoading"
        title="Full validators"
        tooltip="Number of active full validators"
        :initial-data-loaded="initialDataLoaded"
        stats-property="nrOfActiveFullValidatorsAverage"
        :year-statistics="yearStatistics"
      >
        <template #info>
          <h3>Full validators</h3>
          <p class="my-4">
            In addition to voting on the network, a full validator manages an
            up-to-date history archive in long term storage.
          </p>
          <h4>How does my node gain full validator status?</h4>
          <p>
            Support
            <a
              target="_blank"
              rel="noopener"
              href="https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0020.md"
              >sep-0020</a
            >
            and update the linked
            <a
              target="_blank"
              rel="noopener"
              href="https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0001.md"
              >stellar.toml</a
            >
            file to include your history archive url's and make sure the
            archive(s) are up-to-date.
          </p>
        </template>
      </network-statistics-card>
    </div>
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.nrOfActiveOrganizations"
        :is-loading="isLoading"
        title="Organizations"
        tooltip="Number of active organizations"
        :initial-data-loaded="initialDataLoaded"
        stats-property="nrOfActiveOrganizationsAverage"
        :year-statistics="yearStatistics"
      >
        <template #info>
          <h3>Active Organizations</h3>
          <p class="my-4">
            Nodes are grouped into organizations. Organizations are active when
            a majority of it's nodes are validating.
          </p>
          <h4>How can you add your organization?</h4>
          <p>
            Support
            <a
              target="_blank"
              rel="noopener"
              href="https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0020.md"
              >sep-0020</a
            >
            and update the linked
            <a
              target="_blank"
              rel="noopener"
              href="https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0001.md"
              >stellar.toml</a
            >
            file to include your organization and validator info.
          </p>
        </template>
      </network-statistics-card>
    </div>
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.topTierSize ?? false"
        :is-loading="isLoading || store.networkAnalyzer.analyzing"
        title="Top tier validators"
        tooltip="Number of validators in the top tier"
        :unknown="store.networkAnalyzer.manualMode"
        :initial-data-loaded="initialDataLoaded"
        stats-property="topTierAverage"
        :year-statistics="yearStatistics"
        :is-simulation-sensitive="true"
      >
        <template #info>
          <h3>Top tier validators</h3>
          <p class="my-4">
            The top tier is the set of nodes in the network that make up the
            minimal quorums of the network.
          </p>

          <p class="my-4">
            If you want to learn more about the top tier concept, check out the
            following
            <a
              target="_blank"
              rel="noopener"
              href="https://arxiv.org/pdf/2002.08101.pdf"
              >white paper</a
            >
          </p>
        </template>
      </network-statistics-card>
    </div>
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.topTierOrgsSize ?? false"
        :is-loading="isLoading || store.networkAnalyzer.analyzing"
        title="Top tier organizations"
        :unknown="store.networkAnalyzer.manualMode"
        tooltip="Number of organizations in the top tier"
        :initial-data-loaded="initialDataLoaded"
        stats-property="topTierOrgsAverage"
        :year-statistics="yearStatistics"
        :is-simulation-sensitive="true"
      >
        <template #info>
          <h3>Top tier organizations</h3>
          <p class="my-4">The top tier grouped by organization.</p>

          <p class="my-4">
            If you want to learn more about the top tier concept, check out the
            following
            <a
              target="_blank"
              rel="noopener"
              href="https://arxiv.org/pdf/2002.08101.pdf"
              >white paper</a
            >
          </p>
        </template>
      </network-statistics-card>
    </div>
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.hasTransitiveQuorumSet"
        :is-loading="isLoading"
        :is-bool="true"
        title="Transitive quorum set"
        tooltip="Does the network have a transitive quorum set"
        :initial-data-loaded="initialDataLoaded"
        stats-property="hasTransitiveQuorumSetAverage"
        :year-statistics="yearStatistics"
      >
        <template #info>
          <h3>Network transitive quorum set</h3>
          <p class="my-4">
            The network transitive quorum set is a heuristic for a correctly
            configured network.<br />
            It is the most important strongly connected component that has no
            outgoing edges. <br />In the main graph above, it is marked with a
            light blue background.
          </p>
        </template>
      </network-statistics-card>
    </div>
    <div class="col-md-6 col-lg-3 col-xl-3">
      <network-statistics-card
        :value="network.networkStatistics.hasQuorumIntersection ?? false"
        :unknown="store.networkAnalyzer.manualMode"
        :is-bool="true"
        :is-loading="isLoading || store.networkAnalyzer.analyzing"
        title="Quorum intersection"
        tooltip="Does the network have quorum intersection"
        :initial-data-loaded="initialDataLoaded"
        stats-property="hasQuorumIntersectionAverage"
        :year-statistics="yearStatistics"
        :is-simulation-sensitive="true"
      >
        <template #info>
          <h3>Quorum intersection</h3>
          <p class="my-4">
            A prerequisite for network safety. Indicates that all quorums are
            overlapping.
          </p>
          <h4>Limitations</h4>
          <p class="my-4">
            If quorum intersection is failing, additional analysis is needed.
            Safety could still be present for the majority of the network.<br />
            Because quorum intersection is calculated for the entire network, it
            could be that badly configured nodes on the edge of the network can
            cause quorum intersection to fail.<br />
            To mitigate this, StellarAtlas filters out 'badly configured' nodes.
            At the moment these are nodes that have only themselves in their
            quorumset.
          </p>
        </template>
      </network-statistics-card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { NetworkStatisticsAggregation } from "shared";
import NetworkStatisticsCard from "@/components/network/cards/network-statistics/network-statistics-card.vue";
import useStore from "@/store/useStore";
import { useIsLoading } from "@/composables/useIsLoading";
import { onMounted, ref, type Ref } from "vue";
import useNetworkMeasurementsStore from "@/store/useNetworkMeasurementsStore";

const store = useStore();
const networkMeasurementStore = useNetworkMeasurementsStore();
const network = store.network;
const { isLoading } = useIsLoading();

const initialDataLoaded = ref(false);
const yearStatistics: Ref<NetworkStatisticsAggregation[]> = ref([]);

onMounted(async () => {
  if (!store.isSimulation && store.networkContext.enableHistory) {
    const oneYearAgo = new Date(store.network.time.getTime());
    oneYearAgo.setFullYear(oneYearAgo.getFullYear() - 1);
    yearStatistics.value = await networkMeasurementStore.getMonthStatistics(
      "stellar-public",
      oneYearAgo,
      store.network.time,
    );
  }
  isLoading.value = false;
  initialDataLoaded.value = true;
});
</script>
