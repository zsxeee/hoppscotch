<template>
  <div>
    <div
      class="bg-primary border-b border-dividerLight flex flex-1 top-upperSecondaryStickyFold pl-4 z-10 sticky items-center justify-between"
    >
      <span class="flex items-center">
        <label class="font-semibold text-secondaryLight">
          {{ $t("authorization.type") }}
        </label>
        <tippy
          ref="authTypeOptions"
          interactive
          trigger="click"
          theme="popover"
          arrow
        >
          <template #trigger>
            <span class="select-wrapper">
              <ButtonSecondary
                class="rounded-none ml-2 pr-8"
                :label="authName"
              />
            </span>
          </template>
          <SmartItem
            label="None"
            :icon="
              authName === 'None'
                ? 'radio_button_checked'
                : 'radio_button_unchecked'
            "
            @click.native="
              () => {
                authType = 'none'
                authTypeOptions.tippy().hide()
              }
            "
          />
          <SmartItem
            label="Basic Auth"
            :icon="
              authName === 'Basic Auth'
                ? 'radio_button_checked'
                : 'radio_button_unchecked'
            "
            @click.native="
              () => {
                authType = 'basic'
                authTypeOptions.tippy().hide()
              }
            "
          />
          <SmartItem
            label="Bearer Token"
            :icon="
              authName === 'Bearer'
                ? 'radio_button_checked'
                : 'radio_button_unchecked'
            "
            @click.native="
              () => {
                authType = 'bearer'
                authTypeOptions.tippy().hide()
              }
            "
          />
          <SmartItem
            label="OAuth 2.0"
            :icon="
              authName === 'OAuth 2.0'
                ? 'radio_button_checked'
                : 'radio_button_unchecked'
            "
            @click.native="
              () => {
                authType = 'oauth-2'
                authTypeOptions.tippy().hide()
              }
            "
          />
          <SmartItem
            label="API key"
            :icon="
              authName === 'API key'
                ? 'radio_button_checked'
                : 'radio_button_unchecked'
            "
            @click.native="
              () => {
                authType = 'api-key'
                authTypeOptions.tippy().hide()
              }
            "
          />
        </tippy>
      </span>
      <div class="flex">
        <!-- <SmartCheckbox
          :on="!URLExcludes.auth"
          @change="setExclude('auth', !$event)"
        >
          {{ $t("authorization.include_in_url") }}
        </SmartCheckbox> -->
        <SmartCheckbox
          :on="authActive"
          class="px-2"
          @change="authActive = !authActive"
        >
          {{ $t("state.enabled") }}
        </SmartCheckbox>
        <ButtonSecondary
          v-tippy="{ theme: 'tooltip' }"
          to="https://docs.hoppscotch.io/features/authorization"
          blank
          :title="$t('app.wiki')"
          svg="help-circle"
        />
        <ButtonSecondary
          v-tippy="{ theme: 'tooltip' }"
          :title="$t('action.clear')"
          svg="trash-2"
          @click.native="clearContent"
        />
      </div>
    </div>
    <div
      v-if="authType === 'none'"
      class="flex flex-col text-secondaryLight p-4 items-center justify-center"
    >
      <img
        :src="`/images/states/${$colorMode.value}/login.svg`"
        loading="lazy"
        class="flex-col object-contain object-center h-16 my-4 w-16 inline-flex"
        :alt="`${$t('empty.authorization')}`"
      />
      <span class="text-center pb-4">
        {{ $t("empty.authorization") }}
      </span>
      <ButtonSecondary
        outline
        :label="$t('app.documentation')"
        to="https://docs.hoppscotch.io/features/authorization"
        blank
        svg="external-link"
        reverse
        class="mb-4"
      />
    </div>
    <div v-else class="border-b border-dividerLight flex">
      <div class="border-r border-dividerLight w-2/3">
        <div v-if="authType === 'basic'">
          <div class="border-b border-dividerLight flex">
            <SmartEnvInput
              v-model="basicUsername"
              :placeholder="$t('authorization.username')"
              styles="bg-transparent flex flex-1 py-1 px-4"
            />
          </div>
          <div class="border-b border-dividerLight flex">
            <SmartEnvInput
              v-model="basicPassword"
              :placeholder="$t('authorization.password')"
              styles="bg-transparent flex flex-1 py-1 px-4"
            />
          </div>
        </div>
        <div v-if="authType === 'bearer'">
          <div class="border-b border-dividerLight flex">
            <SmartEnvInput
              v-model="bearerToken"
              placeholder="Token"
              styles="bg-transparent flex flex-1 py-1 px-4"
            />
          </div>
        </div>
        <div v-if="authType === 'oauth-2'">
          <div class="border-b border-dividerLight flex">
            <SmartEnvInput
              v-model="oauth2Token"
              placeholder="Token"
              styles="bg-transparent flex flex-1 py-1 px-4"
            />
          </div>
          <HttpOAuth2Authorization />
        </div>
        <div v-if="authType === 'api-key'">
          <div class="border-b border-dividerLight flex">
            <SmartEnvInput
              v-model="apiKey"
              placeholder="Key"
              styles="bg-transparent flex flex-1 py-1 px-4"
            />
          </div>
          <div class="border-b border-dividerLight flex">
            <SmartEnvInput
              v-model="apiValue"
              placeholder="Value"
              styles="bg-transparent flex flex-1 py-1 px-4"
            />
          </div>
          <div class="border-b border-dividerLight flex items-center">
            <label class="text-secondaryLight ml-4">
              {{ $t("authorization.pass_key_by") }}
            </label>
            <tippy
              ref="addToOptions"
              interactive
              trigger="click"
              theme="popover"
              arrow
            >
              <template #trigger>
                <span class="select-wrapper">
                  <ButtonSecondary
                    :label="addTo || $t('state.none')"
                    class="rounded-none ml-2 pr-8"
                  />
                </span>
              </template>
              <SmartItem
                :icon="
                  addTo === 'Headers'
                    ? 'radio_button_checked'
                    : 'radio_button_unchecked'
                "
                :label="'Headers'"
                @click.native="
                  () => {
                    addTo = 'Headers'
                    addToOptions.tippy().hide()
                  }
                "
              />
              <SmartItem
                :icon="
                  addTo === 'Query params'
                    ? 'radio_button_checked'
                    : 'radio_button_unchecked'
                "
                :label="'Query params'"
                @click.native="
                  () => {
                    addTo = 'Query params'
                    addToOptions.tippy().hide()
                  }
                "
              />
            </tippy>
          </div>
        </div>
      </div>
      <div
        class="bg-primary h-full top-upperTertiaryStickyFold min-w-46 max-w-1/3 p-4 z-9 sticky overflow-auto"
      >
        <div class="text-secondaryLight pb-2">
          {{ $t("helpers.authorization") }}
        </div>
        <SmartAnchor
          class="link"
          :label="`${$t('authorization.learn')} \xA0 →`"
          to="https://docs.hoppscotch.io/features/authorization"
          blank
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, ref, Ref } from "@nuxtjs/composition-api"
import {
  HoppRESTAuthBasic,
  HoppRESTAuthBearer,
  HoppRESTAuthOAuth2,
  HoppRESTAuthAPIKey,
} from "@hoppscotch/data"
import { pluckRef, useStream } from "~/helpers/utils/composables"
import { restAuth$, setRESTAuth } from "~/newstore/RESTSession"
import { useSetting } from "~/newstore/settings"

export default defineComponent({
  setup() {
    const auth = useStream(
      restAuth$,
      { authType: "none", authActive: true },
      setRESTAuth
    )
    const authType = pluckRef(auth, "authType")
    const authName = computed(() => {
      if (authType.value === "basic") return "Basic Auth"
      else if (authType.value === "bearer") return "Bearer"
      else if (authType.value === "oauth-2") return "OAuth 2.0"
      else if (authType.value === "api-key") return "API key"
      else return "None"
    })
    const authActive = pluckRef(auth, "authActive")
    const basicUsername = pluckRef(auth as Ref<HoppRESTAuthBasic>, "username")
    const basicPassword = pluckRef(auth as Ref<HoppRESTAuthBasic>, "password")
    const bearerToken = pluckRef(auth as Ref<HoppRESTAuthBearer>, "token")
    const oauth2Token = pluckRef(auth as Ref<HoppRESTAuthOAuth2>, "token")
    const apiKey = pluckRef(auth as Ref<HoppRESTAuthAPIKey>, "key")
    const apiValue = pluckRef(auth as Ref<HoppRESTAuthAPIKey>, "value")
    const addTo = pluckRef(auth as Ref<HoppRESTAuthAPIKey>, "addTo")
    if (typeof addTo.value === "undefined") {
      addTo.value = "Headers"
      apiKey.value = ""
      apiValue.value = ""
    }

    const URLExcludes = useSetting("URL_EXCLUDES")
    const clearContent = () => {
      auth.value = {
        authType: "none",
        authActive: true,
      }
    }
    return {
      auth,
      authType,
      authName,
      authActive,
      basicUsername,
      basicPassword,
      bearerToken,
      oauth2Token,
      URLExcludes,
      clearContent,
      apiKey,
      apiValue,
      addTo,
      authTypeOptions: ref<any | null>(null),
      addToOptions: ref<any | null>(null),
    }
  },
})
</script>
