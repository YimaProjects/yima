<template>
  <div class="container">
    <FormKit
      v-slot="{ state: { valid: formValid } }"
      v-model="formData"
      type="form"
      form-class="flex mx-auto lg:w-4/5 xl:w-2/3 gap-10 xl:gap-20 "
      :actions="false"
      @submit="handleSubmit"
    >
      <div class="flex grow flex-col gap-8">
        <div class="flex flex-col gap-2">
          <TheH :level="2">{{ $t('email') }}</TheH>
          <FormKit type="email" name="email" validation="required|email" />
        </div>
        <div class="flex flex-col gap-2">
          <TheH :level="2">{{ $t('contactInfo') }}</TheH>
          <TheBaseCard class="grid gap-4 p-4 sm:grid-cols-2">
            <FormKit type="text" name="firstName" validation="required" :label="$t('firstName')" />
            <FormKit type="text" name="lastName" validation="required" :label="$t('lastName')" />
            <FormKit
              type="text"
              name="phoneNumber"
              validation="required"
              outer-class="col-span-full"
              :label="$t('phoneNumber')"
            />
          </TheBaseCard>
        </div>
        <div class="flex flex-col gap-2">
          <TheH :level="2">{{ $t('address') }}</TheH>
          <TheBaseCard class="grid gap-4 p-4 sm:grid-cols-2">
            <FormKit type="text" name="city" validation="required" :label="$t('city')" />
            <FormKit type="text" name="streetAndNum" validation="required" :label="$t('streetAndNum')" />
            <FormKit type="text" name="postCode" validation="required" :label="$t('postCode')" />
            <FormKit type="text" name="country" validation="required" :label="$t('country')" />
          </TheBaseCard>
        </div>
      </div>
      <div class="hidden w-1/3 md:block">
        <div class="sticky top-4 flex flex-col gap-2">
          <TheH :level="2">{{ $t('order') }}</TheH>
          <TheBaseCard class="flex flex-col divide-y px-4 py-2">
            <div v-for="product in orderState.products" :key="product.id" class="flex items-center gap-4 py-2">
              <div><img width="60" height="60" :src="product.imgUrl" :alt="product.name" /></div>
              <div>
                <div class="line-clamp-1">{{ product.name }}</div>
                <div class="divide-x">
                  <ThePrice :value="product.price" class="inline pr-2 text-sm font-semibold" />
                  <span class="pl-1 text-xs">{{ product.quantity }} {{ $t('piece') }}</span>
                </div>
              </div>
            </div>
          </TheBaseCard>
          <div class="mt-2 items-center text-right">
            <TheH class="inline" :level="4">{{ $t('totalPrice') }}: </TheH>
            <ThePrice class="ml-auto inline-block text-lg font-bold" :value="orderState.total" />
          </div>
          <div
            class="ml-auto mt-4"
            :class="{
              'tooltip tooltip-bottom': !formValid,
            }"
            :data-tip="$t('provideData')"
          >
            <TheButton class="btn btn-primary" type="submit" :disabled="!formValid">
              {{ $t('submit') }}
            </TheButton>
          </div>
        </div>
      </div>
    </FormKit>
  </div>
</template>
<script setup lang="ts">
import { useNuxtApp, navigateTo, useLocalePath, useYimaToast, ref } from '#imports'

const {
  $order: { state: orderState, setShippingAddress, completeOrder, removeOrder },
  $i18n: { t },
} = useNuxtApp()
const localePath = useLocalePath()
const { toastSuccess } = useYimaToast()

const formData = ref<Record<string, any>>({})

if (orderState.value.shippingAddress) {
  formData.value = { ...orderState.value.shippingAddress }
}

async function handleSubmit(data: Record<string, any>) {
  setShippingAddress(data)

  await completeOrder()

  await navigateTo(localePath('/'))

  toastSuccess(t('orderCompleteSuccess'))

  removeOrder()
}
</script>