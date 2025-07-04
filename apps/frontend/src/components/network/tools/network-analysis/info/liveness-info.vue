<template>
  <b-modal
    id="network-analysis-liveness-info"
    ref="livenessInfoModal"
    lazy
    hide-header
  >
    <h3>What does this analysis show?</h3>
    <p class="my-4">
      The network is scanned for sets of validating nodes that intersect every
      quorum (network blocking sets). It reports the minimum sizes as a risk
      analysis, and lists all the sets. The nodes can be grouped by
      organization, ISP and country
    </p>
    <h3>What does it mean?</h3>
    <p class="my-4">
      When all the nodes of a network blocking set fail, the network could halt.
      If the analysis indicates the minimum size is 3, then 3 specific nodes
      have to fail before this will happen. The same goes for the other
      groupings (organization, isp, country)
    </p>
    <h3>Limitations of the analysis</h3>
    <p class="my-4">
      StellarAtlas indicates a node as 'not validating' if it was not validating
      in the latest crawl, a specific time window. However a node could be very
      slow or catching up. If other (fast) nodes of a network blocking set are
      down, the slow node could catch up and the network would continue to
      validate. Hence this graph is a risk analysis. For ISP and country
      information of nodes we rely on the
      <a href="https://ipstack.com/">IPstack api</a>. This data is not
      guaranteed to be correct.
    </p>
    <h3>More information</h3>
    <p class="my-4">
      If you want to learn more about network blocking sets, check out the
      following
      <a
        target="_blank"
        rel="noopener"
        href="https://arxiv.org/pdf/2002.08101.pdf"
        >white paper</a
      >, more specific the section regarding minimal blocking sets.
    </p>
    <template #modal-footer>
      <div class="w-100">
        <p class="float-left">
          Powered by
          <a
            target="_blank"
            rel="noopener"
            href="https://github.com/wiberlin/fbas_analyzer"
            >wiberlin/fbas_analyzer</a
          >
        </p>
        <button class="btn btn-primary btn-sm float-right" @click="hideModal">
          Close
        </button>
      </div>
    </template>
  </b-modal>
</template>

<script setup lang="ts">
import { BModal } from "bootstrap-vue";
import { ref } from "vue";

const livenessInfoModal = ref(null);
function hideModal() {
  if (livenessInfoModal.value) (livenessInfoModal.value as BModal).hide();
}
</script>
