<template>
  <div>
    <Head :title="$t('Settings')" />
    <div class="flex items-center justify-between mb-6">
      <Link class="btn-indigo" href="/settings/create">
        <span>{{ $t('Create') }}</span>
      </Link>
    </div>
    <div class="max-w-3xl bg-white rounded-md shadow overflow-hidden">
      <form v-for="setting in settings" :key="setting.id" @change="update(setting)" >
        <div class="flex flex-wrap mb-2 -mr-6 p-4">
          <text-input v-if="setting.type == 'css'" v-model="setting.value" :label="setting.name" type="color" />
          <file-input v-if="setting.type == 'logo'" v-model="setting.value" :label="setting.name" />

          <select-input v-if="setting.type == 'forms'" v-model="setting.value" class="pb-8 pr-6 w-full lg:w-1/2" :label="$t('Forms Source')">
            <option value="jotform">{{ $t('Jot Form') }}</option>
            <option value="site">{{ $t('Site') }}</option>
          </select-input>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import { Head, Link } from '@inertiajs/inertia-vue3'
import Layout from '@/Shared/Layout'
import Image from '@/Shared/Image'
import TextInput from '@/Shared/TextInput'
import FileInput from '@/Shared/FileInput'
import SelectInput from '@/Shared/SelectInput'
import LoadingButton from '@/Shared/LoadingButton'
import TrashedMessage from '@/Shared/TrashedMessage'

export default {
  components: {
    FileInput,
    Head,
    Link,
    Image,
    LoadingButton,
    SelectInput,
    TextInput,
    TrashedMessage,
  },
  layout: Layout,
  props: {
    settings: Object,
  },
  remember: 'form',
  data() {
    return {
      form: this.$inertia.form({
        _method: 'put',
        type: '',
        selector: '',
        property: '',
        value: '',
      }),
    }
  },

  methods: {
    update(setting) {
      this.$inertia.put(`/settings/${setting.id}`, setting)
    },
    destroy() {
      if (confirm('Are you sure you want to delete this user?')) {
        this.$inertia.delete(`/users/${this.user.id}`)
      }
    },
  },
}
</script>
