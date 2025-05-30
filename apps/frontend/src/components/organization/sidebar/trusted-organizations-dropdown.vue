<template>
  <div class="sb-nav-item">
    <nav-link
      class="sb-nav-dropdown-title"
      :title="'Trusted Organizations'"
      :show-dropdown-toggle="true"
      :drop-down-showing="showing"
      :has-warnings="
        OrganizationWarningDetector.someOrganizationsHaveWarnings(
          trustedOrganizations,
          store.network,
        )
      "
      :warnings="'Some organizations have warnings'"
      @click="toggleShow"
    >
      <template #action-dropdown>
        <organization-actions
          :supports-halt="false"
          :supports-delete="false"
          :supports-add="true"
          :organization="organization"
        />
      </template>
    </nav-link>
    <div v-show="showing" class="sb-nav-dropdown">
      <nav-link
        v-for="organizationLink in trustedOrganizations"
        :key="organizationLink.id"
        :title="organizationLink.name"
        :is-link-in-dropdown="true"
        :has-danger="network.isOrganizationFailing(organizationLink)"
        :dangers="store.getOrganizationFailingReason(organizationLink)"
        :has-warnings="
          OrganizationWarningDetector.organizationHasWarnings(
            organizationLink,
            store.network,
          )
        "
        :warnings="
          OrganizationWarningDetector.getOrganizationWarningReasons(
            organizationLink,
            store.network,
          ).join(' | ')
        "
        @click="selectOrganization(organizationLink)"
      >
        <template #action-dropdown>
          <organization-actions
            :supports-delete="true"
            :organization="organizationLink"
          />
        </template>
      </nav-link>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Organization } from "shared";
import NavLink from "@/components/side-bar/nav-link.vue";
import OrganizationActions from "@/components/organization/sidebar/organization-actions.vue";
import useStore from "@/store/useStore";
import { computed } from "vue";
import { useRoute, useRouter } from "vue-router/composables";
import { useDropdown } from "@/composables/useDropdown";
import { OrganizationWarningDetector } from "@/services/OrganizationWarningDetector";

const props = defineProps<{
  organization: Organization;
}>();

const store = useStore();
const network = store.network;
const route = useRoute();
const router = useRouter();

const emit = defineEmits(["toggleExpand"]);
const { showing, toggleShow } = useDropdown(true, emit);

const trustedOrganizations = computed(() => {
  const trustedOrganizations = new Set<Organization>();
  props.organization.validators.forEach((publicKey) => {
    const validator = network.getNodeByPublicKey(publicKey);
    network.getTrustedOrganizations(validator.quorumSet).forEach((org) => {
      if (org.id !== props.organization.id) trustedOrganizations.add(org);
    });
  });
  return Array.from(trustedOrganizations);
});

function selectOrganization(organization: Organization) {
  router.push({
    name: "organization-dashboard",
    params: { organizationId: organization.id },
    query: {
      center: "1",
      "no-scroll": "0",
      view: route.query.view,
      network: route.query.network,
      at: route.query.at,
    },
  });
}
</script>

<style scoped></style>
