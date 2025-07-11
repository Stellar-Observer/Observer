<template>
  <div>
    <div v-if="requesting">
      <h4>Requesting subscription</h4>
      <div>
        <div class="loader"></div>
      </div>
    </div>
    <div v-else>
      <h4>Subscribe to</h4>
      <b-form
        novalidate
        :validated="validated"
        @submit.prevent="onSubscribe"
        @reset="onReset"
      >
        <b-alert variant="success" :show="requested"
          >Request received, you will receive an email shortly.</b-alert
        >
        <b-alert variant="danger" :show="submitError"
          >Something went wrong, please try again later or contact
          support.</b-alert
        >
        <b-form-group
          id="nodes-group"
          label="Node events"
          label-for="nodes-select"
          description="Triggered when inactive, not validating or history archive not up-to-date for three consecutive updates or when history archive verification error is detected"
        >
          <multiselect
            id="nodes-select"
            v-model="selectedNodes"
            :multiple="true"
            :close-on-select="false"
            :clear-on-select="false"
            placeholder="Nodes"
            label="name"
            track-by="name"
            :options="nodes"
            :internal-search="false"
            @search-change="searchNodes"
          >
          </multiselect>
        </b-form-group>

        <b-form-group
          id="organizations-group"
          label="Organization events"
          label-for="organization-select"
          description="Triggered when organization unavailable for three consecutive updates"
        >
          <multiselect
            id="organization-select"
            v-model="selectedOrganizations"
            :multiple="true"
            :close-on-select="false"
            :clear-on-select="false"
            placeholder="Organizations"
            label="name"
            track-by="name"
            :options="organizations"
          >
          </multiselect>
        </b-form-group>
        <b-form-group
          id="network-group"
          description="Triggered when transitive quorumSet changes, when liveness or danger risks drop below
            thresholds."
        >
          <b-form-checkbox
            id="network-checkbox"
            v-model="networkSubscription"
            name="network-checkbox"
            unchecked-value="not_subscribed"
          >
            Network events
          </b-form-checkbox>
        </b-form-group>
        <b-form-group id="email-group" class="mt-6">
          <b-form-input
            id="email-address"
            v-model="emailAddress"
            :state="emailAddressState"
            placeholder="Enter your email address"
            required
            trim
          ></b-form-input>
        </b-form-group>
        <b-form-group id="consent-group">
          <b-form-checkbox
            id="consent-checkbox"
            v-model="consented"
            name="consent-checkbox"
            unchecked-value="not_accepted"
          >
            I have read, understood, and agree to be bound by the
            <a :href="termsLink" target="_blank">Terms and Conditions</a>
            and our
            <a :href="privacyLink" target="_blank">Privacy Policy</a>
          </b-form-checkbox>
        </b-form-group>
        <button
          type="submit"
          class="btn-primary btn"
          role="button"
          @click="onSubscribe"
        >
          Create or update subscriptions
        </button>
        <button
          class="btn btn-danger ml-2"
          type="submit"
          :disabled="
            !(emailAddressState === true && consented !== 'not_accepted')
          "
          @click="onUnsubscribe"
        >
          Unsubscribe and remove email address
        </button>
        <button class="btn btn-secondary ml-3" type="reset">Clear form</button>
      </b-form>
    </div>
    <b-alert class="mt-5" variant="info" :show="true"
      ><ul>
        <li>
          Events are triggered when they first occur. For example a node that is
          down for three days, will only trigger one event at the start.
        </li>
        <li>
          To prevent notification overload, notifications for a specific event
          are muted (not sent) for 24H after they are first sent. You can
          however unmute event notifications by following the link in the
          notification email. For example if you resolve a reported issue with
          your node, and wish to be notified immediately when the issue should
          reappear.
        </li>
        <li>
          This service is provided best effort. If network issues are reported,
          be sure to check the
          <a href="https://dashboard.stellar.org" target="_blank"
            >official Stellar network dashboard</a
          >. There could be an issue with the StellarAtlas crawler, which itself
          is in essence a non-validating node.
        </li>
        <li>
          To improve the chances of the StellarAtlas crawler connecting to your
          node, add the crawler public keys to your PREFERRED_PEER_KEYS in your
          <a
            href="https://github.com/stellar/stellar-core/blob/ecd0a7462c84bd1d2445cdfd48aa9b38b1bbfd20/docs/stellar-core_example.cfg#L170"
            target="_blank"
            >Stellar core configuration file. </a
          >: GCMFBFXLCVWMYZL64U75DTRT6YNPFTG5ZV5P2PL2GTI3GNMEV4WC53JC and
          GAK7ZMDHKKEOYGZH3OSZQERK6UG5UDG5WEJV7GTN3Z4OIFXS3DXPUCRY
        </li>
        <li>
          The StellarAtlas notification service does not replace the monitoring
          setup advised in the
          <a
            href="https://developers.stellar.org/docs/run-core-node/monitoring/"
            >Stellar monitoring docs</a
          >.
        </li>
      </ul>
    </b-alert>
  </div>
</template>

<script setup lang="ts">
import { Multiselect } from "vue-multiselect";
import {
  BAlert,
  BForm,
  BFormCheckbox,
  BFormGroup,
  BFormInput,
} from "bootstrap-vue";
import { computed, type ComputedRef, onMounted, type Ref, ref } from "vue";
import useStore from "@/store/useStore";

type EventSourceId = {
  type: string;
  id: string;
};

type SelectNode = {
  name: string;
  publicKey: string;
};

type SelectedOrganization = {
  name: string;
  id: string;
};

const store = useStore();
const network = store.network;

const emailAddress = ref("");
const requested = ref(false);
const consented = ref("not_accepted");
const requesting = ref(false);
const submitError = ref(false);
const networkSubscription = ref(false);
const selectedNodes: Ref<SelectNode[] | null> = ref(null);
const nodes: Ref<SelectNode[]> = ref([]);
const selectedOrganizations: Ref<SelectedOrganization[] | null> = ref(null);
const validated = ref(false);

const privacyLink = import.meta.env.VUE_APP_PRIVACY_LINK;
const termsLink = import.meta.env.VUE_APP_TERMS_LINK;

const organizations: ComputedRef<SelectedOrganization[]> = computed(() => {
  return network.organizations.map((org) => {
    return {
      name: org.name,
      id: org.id,
    };
  });
});

function searchNodes(query: string) {
  nodes.value = network.nodes
    .filter(
      (node) =>
        node.publicKey.toLowerCase().search(query.toLowerCase()) !== -1 ||
        node.displayName.toLowerCase().search(query.toLowerCase()) !== -1,
    )
    .map((node) => {
      return { name: node.displayName, publicKey: node.publicKey };
    });
}

const emailAddressState = computed(() => {
  if (emailAddress.value === "") return null;
  const re =
    /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
  return re.test(emailAddress.value.toLowerCase());
});

function onReset(event: Event) {
  event.preventDefault();
  resetForm();
}

function resetForm() {
  emailAddress.value = "";
  selectedOrganizations.value = null;
  selectedNodes.value = null;
  networkSubscription.value = false;
  consented.value = "not_accepted";
}

function getSelectedEventSourceIds(): EventSourceId[] {
  const eventSourceIds: EventSourceId[] = [];
  if (networkSubscription.value) {
    eventSourceIds.push({
      type: "network",
      id: store.network.id
        ? store.network.id
        : "Public Global Stellar Network ; September 2015",
    });
  }

  if (selectedNodes.value !== null) {
    selectedNodes.value.forEach((node: SelectNode) => {
      eventSourceIds.push({
        type: "node",
        id: node.publicKey,
      });
    });
  }

  if (selectedOrganizations.value !== null) {
    selectedOrganizations.value.forEach((org: SelectedOrganization) => {
      eventSourceIds.push({
        type: "organization",
        id: org.id,
      });
    });
  }

  return eventSourceIds;
}

async function onSubscribe(event: Event) {
  validated.value = true;
  event.preventDefault();
  submitError.value = false;
  requested.value = false;
  if (!emailAddressState.value || consented.value === "not_accepted") return;
  try {
    requesting.value = true;
    const response = await fetch(
      import.meta.env.VUE_APP_PUBLIC_API_URL + "/v1/subscription",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          emailAddress: emailAddress.value,
          eventSourceIds: getSelectedEventSourceIds(),
        }),
      },
    );
    if (!response.ok) {
      requesting.value = false;
      submitError.value = true;
    }
    requested.value = true;
    requesting.value = false;
    resetForm();
  } catch (e) {
    requesting.value = false;
    submitError.value = true;
  }
}

async function onUnsubscribe(event: Event) {
  event.preventDefault();
  submitError.value = false;
  requested.value = false;
  if (!emailAddressState.value || consented.value === "not_accepted") return;
  try {
    requesting.value = true;
    const response = await fetch(
      import.meta.env.VUE_APP_PUBLIC_API_URL +
        "/v1/subscription/request-unsubscribe",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          emailAddress: emailAddress.value,
        }),
      },
    );
    if (!response.ok) {
      requesting.value = false;
      submitError.value = true;
    }
    requested.value = true;
    requesting.value = false;
    resetForm();
  } catch (e) {
    requesting.value = false;
    submitError.value = true;
  }
}
onMounted(() => {
  nodes.value = network.nodes.map((node) => {
    return {
      name: node.displayName,
      publicKey: node.publicKey,
    };
  });
});
</script>

<style scoped></style>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
