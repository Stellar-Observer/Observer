<template>
  <div
    id="modal-1"
    class="modal fade"
    tabindex="-1"
    aria-labelledby="modal-1Label"
    aria-hidden="true"
  >
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 id="modal-1Label" class="modal-title">
            Welcome to StellarAtlas.io!
          </h5>
        </div>
        <div class="modal-body">
          <p>
            By clicking "I Agree", you acknowledge that you have read,
            understood, and agree to be bound by the
            <a
              :href="import.meta.env.VUE_APP_TERMS_LINK"
              target="_blank"
              rel="noopener"
              >Terms and Conditions</a
            >
            and our
            <a
              :href="import.meta.env.VUE_APP_PRIVACY_LINK"
              target="_blank"
              rel="noopener"
              >Privacy Policy</a
            >. If you do not agree to these terms, please do not use this
            service.
          </p>
        </div>
        <div class="modal-footer">
          <button
            type="button"
            class="btn btn-secondary"
            data-dismiss="modal"
            @click="onDecline"
          >
            Decline
          </button>
          <button type="button" class="btn btn-primary" @click="onAccept">
            I Agree
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import $ from "jquery";
import { onMounted, reactive, ref, type Ref } from "vue";
import { onBeforeRouteUpdate, useRoute } from "vue-router/composables";
import useStore from "@/store/useStore";
import useUserConsentManager from "@/store/useUserConsentManager";

const route = useRoute();
const queryForRouter = reactive({
  view: route.query.view,
  network: route.query.network,
  at: route.query.at,
});

const store = useStore();
const userConsentManager = useUserConsentManager();
const consentModal: Ref<HTMLElement | null> = ref(null);

const onAccept = () => {
  userConsentManager.giveConsent(store.latestTermsChangeTimestamp);
};

const onDecline = () => {
  window.location.href = "https://dashboard.stellar.org";
};

const checkConsent = () => {
  if (
    !userConsentManager.userHasGivenConsent(store.latestTermsChangeTimestamp)
  ) {
    if (!consentModal.value) return;

    $(consentModal.value).modal("show");
  }
};

const termsLink = import.meta.env.VUE_APP_TERMS_LINK;
const privacyLink = import.meta.env.VUE_APP_PRIVACY_LINK;

onMounted(() => {
  checkConsent();
});

onBeforeRouteUpdate((to, from, next) => {
  checkConsent();
  next();
});
</script>
