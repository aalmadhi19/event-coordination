<template>
  <div>
    <Head :title="`${form.name}`" />
    <div class="flex justify-start mb-8 max-w-3xl">
      <h1 class="text-3xl font-bold">
        <Link class="text-indigo-400 hover:text-indigo-600" href="/users">{{ $t('Users') }} </Link>
        <span class="text-indigo-400 font-medium">/</span>
        {{ form.name }}
      </h1>
    </div>
    <trashed-message v-if="user.deleted_at" class="mb-6" @restore="restore"> {{ $t('This user has been deleted.') }} }} </trashed-message>
    <div class="max-w-3xl bg-white rounded-md shadow overflow-hidden">
      <form @submit.prevent="update">
        <div class="flex flex-wrap -mb-8 -mr-6 p-8">
          <text-input v-model="form.name" :error="form.errors.name" class="pb-8 pr-6 w-full lg:w-1/2" :label="$t('Name')" />
          <text-input v-model="form.email" :error="form.errors.email" class="pb-8 pr-6 w-full lg:w-1/2" :label="$t('Email')" />
          <text-input v-model="form.phone" :error="form.errors.phone" class="pb-8 pr-6 w-full lg:w-1/2" :label="$t('Phone')" />
          <select-input v-model="form.role" :error="form.errors.role" class="pb-8 pr-6 w-full lg:w-1/2" :label="$t('Role')">
            <option :value="'Admin'">{{ $t('Admin') }}</option>
            <option :value="'User'">{{ $t('User') }}</option>
          </select-input>
          <Link v-if="user.ticket" class="text-indigo-400 hover:text-indigo-600" :href="`/tickets/${user.ticket.id}/`">
            <div class="pb-8 pr-6 h-full w-full">
              <label class="form-label" for="ticket">{{ $t('Ticket') }}:</label>
              <Image :width="120" :height="80" :src="'/' + user.ticket.qr_path" id="ticket" class="ml-5" />
            </div>
          </Link>
        </div>
        <div class="flex items-center px-8 py-4 bg-gray-50 border-t border-gray-100">
          <button v-if="!user.deleted_at" class="text-red-600 hover:underline" tabindex="-1" type="button" @click="destroy">{{ $t('Delete User') }}</button>
          <loading-button :loading="form.processing" class="btn-indigo ml-auto" type="submit"> {{   $t('Update User')  }} </loading-button>
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
    user: Object,
  },
  remember: 'form',
  data() {
    return {
      form: this.$inertia.form({
        _method: 'put',
        name: this.user.name,
        email: this.user.email,
        phone: this.user.phone,
        role: this.user.role,
      }),
    }
  },
  methods: {
    update() {
      this.form.post(`/users/${this.user.id}`, {
        onSuccess: () => this.form.reset('password', 'photo'),
      })
    },
    destroy() {
      if (confirm('Are you sure you want to delete this user?')) {
        this.$inertia.delete(`/users/${this.user.id}`)
      }
    },
    restore() {
      if (confirm('Are you sure you want to restore this user?')) {
        this.$inertia.put(`/users/${this.user.id}/restore`)
      }
    },
  },
}
</script>
