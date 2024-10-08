<script setup lang="ts">
import { useRouteQuery } from '@vueuse/router'
type ZglvRegistry = {
  id:number,
  version:string,
  data:string,
  filename:string,
  filename1:string,
  sd_z:number,
}

type SchetRegistry = {
  id:number,
  code:string,
  code_mo:string,
  year:number,
  month:number,
  nschet:string,
  dschet:string,
  plat:string,
  summav:string,
  coments:string,
}

const registryStore = useRegistryStore()

const id = useRouteQuery('header_id')

await useAsyncData(`header-${id.value}`, () => registryStore.fetchHeader(id.value), {
  watch: [id]
})

const hasShowZglvEditDialog = ref(false)
const hasShowSchetEditDialog = ref(false)

const currentZglv = ref<ZglvRegistry|null>(null)

const openZglvEditor = (zglv:ZglvRegistry) => {
  currentZglv.value = zglv
  hasShowZglvEditDialog.value = true
}

const currentSchet = ref<SchetRegistry|null>(null)

const openSchetEditor = (schet:SchetRegistry) => {
  currentSchet.value = schet
  console.log(currentSchet.value)
  hasShowSchetEditDialog.value = true
}

const openZaps = async (zl_list_id:number) => {
  await navigateTo({ name: 'editor-zaps', query: { zl_list_id } })
}

const openErrors = async (zlListId:number) => {
  await navigateTo({ name: 'editor-errors', query: { zlListId } })
}
</script>

<template>
  <div v-if="registryStore.currentHeader" class="container max-w-5xl mx-auto py-4">
    <NCollapse :default-expanded-names="[0, 1, 2, 3, 4, 5, 6]">
      <NCollapseItem :name="index" :title="`${zlList.zglv.filename} [${zlList.schet.coments}]`" v-for="(zlList, index) in registryStore.currentHeader.zl_lists">
        <div class="grid grid-cols-3 gap-4">
          <NCard v-if="zlList.zglv" size="medium" title="Заголовок файла">
            <template #header-extra>
              <NButton quaternary circle @click="openZglvEditor(zlList.zglv)">
                <template #icon>
                  <NaiveIcon name="tabler:pencil" />
                </template>
              </NButton>
            </template>
            {{ zlList.zglv.filename }}
          </NCard>

          <NCard v-if="zlList.schet" size="medium" title="Счёт">
            <template #header-extra>
              <NButton quaternary circle @click="openSchetEditor(zlList.schet)">
                <template #icon>
                  <NaiveIcon name="tabler:pencil" />
                </template>
              </NButton>
            </template>
            <p>
              {{ zlList.schet.summav }}
            </p>
            <p>
              {{ zlList.schet.coments }}
            </p>
          </NCard>

          <NCard v-if="zlList.schet" size="medium" title="Записи случаев">
            <template #header-extra>
              <NButton quaternary circle @click="openZaps(zlList.id)">
                <template #icon>
                  <NaiveIcon name="tabler:external-link" />
                </template>
              </NButton>
            </template>
            Записей: {{ zlList.zglv.sd_z }}
          </NCard>

          <NCard size="medium" title="Ошибки">
            <template #header-extra>
              <NButton quaternary circle @click="openErrors(zlList.id)">
                <template #icon>
                  <NaiveIcon name="tabler:external-link" />
                </template>
              </NButton>
            </template>
            <template v-if="zlList.flk_p">
              {{ zlList.flk_p.fname }}
            </template>
            <NButton v-else text>
              Загрузить файл
            </NButton>
          </NCard>
        </div>
      </NCollapseItem>
    </NCollapse>

    <DialogEditZglv :has-open="hasShowZglvEditDialog" @update:open="value => hasShowZglvEditDialog = value" :zglv="currentZglv" />

    <DialogEditSchet :has-open="hasShowSchetEditDialog" @update:open="value => hasShowSchetEditDialog = value" :schet="currentSchet" />
  </div>
</template>

<style scoped>

</style>