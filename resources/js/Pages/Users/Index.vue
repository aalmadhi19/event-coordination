<template>
  <div>
    <Head :title="$t('Users')" />
    <h1 class="mb-8 text-3xl font-bold">{{ $t('Users') }}</h1>
    <div class="flex items-center justify-between mb-6">
      <search-filter v-model="form.search" class="mr-4 w-full max-w-md" @reset="reset">
        <label class="block text-gray-700"> {{ $t('Role') }} :</label>
        <select v-model="form.role" class="form-select mt-1 w-full">
          <option :value="null" />
          <option value="Admin">{{ $t('Admin') }}</option>
          <option value="User">{{ $t('User') }}</option>
        </select>
        <label class="block mt-4 text-gray-700"> {{ $t('Trashed') }} :</label>
        <select v-model="form.trashed" class="form-select mt-1 w-full">
          <option :value="null" />
          <option value="with">{{ $t('With Trashed') }}</option>
          <option value="only">{{ $t('Only Trashed') }}</option>
        </select>
      </search-filter>
      <Link class="btn-indigo" href="/users/create">
        <span> {{ $t('Create') }} </span>
        <span class="hidden md:inline">&nbsp; {{ $t('User') }} </span>
      </Link>
    </div>
    <div class="bg-white rounded-md shadow overflow-x-auto">
      <table class="w-full whitespace-nowrap">
        <tr class="text-left font-bold">
          <th class="pb-4 pt-6 px-6">{{ $t('Name') }}</th>
          <th class="pb-4 pt-6 px-6">{{ $t('Email') }}</th>
          <th class="pb-4 pt-6 px-6" colspan="2">{{ $t('Role') }}</th>
        </tr>
        <tr v-for="user in users.data" :key="user.id" class="hover:bg-gray-100 focus-within:bg-gray-100">
          <td class="border-t">
            <Link class="flex items-center px-6 py-4 focus:text-indigo-500" :href="`/users/${user.id}/edit`">
              <img v-if="user.photo" class="block -my-2 mr-2 w-5 h-5 rounded-full" :src="user.photo" />
              {{ user.name }}
              <icon v-if="user.deleted_at" name="trash" class="flex-shrink-0 ml-2 w-3 h-3 fill-gray-400" />
            </Link>
          </td>
          <td class="border-t">
            <Link class="flex items-center px-6 py-4" :href="`/users/${user.id}/edit`" tabindex="-1">
              {{ user.email }}
            </Link>
          </td>
          <td class="border-t">
            <Link class="flex items-center px-6 py-4" :href="`/users/${user.id}/edit`" tabindex="-1">
              {{ user.role }}
            </Link>
          </td>
          <td class="w-px border-t">
            <Link class="flex items-center px-4" :href="`/users/${user.id}/edit`" tabindex="-1">
              <icon name="cheveron-right" class="block w-9 h-9 fill-gray-600" />
            </Link>
          </td>
        </tr>
        <tr v-if="users.data.length === 0">
          <td class="px-6 py-4 border-t" colspan="4">{{ $t('No users found.') }}</td>
        </tr>
      </table>
    </div>
    <Pagination class="mt-6" :links="users.links" />
  </div>
</template>

<script>
import { Head, Link } from '@inertiajs/inertia-vue3'
import Icon from '@/Shared/Icon'
import pickBy from 'lodash/pickBy'
import Layout from '@/Shared/Layout'
import throttle from 'lodash/throttle'
import mapValues from 'lodash/mapValues'
import SearchFilter from '@/Shared/SearchFilter'
import Pagination from '@/Shared/Pagination'

export default {
  components: {
    Head,
    Icon,
    Link,
    SearchFilter,
    Pagination,
  },
  layout: Layout,
  props: {
    filters: Object,
    users: Object,
  },
  data() {
    return {
      form: {
        search: this.filters.search,
        role: this.filters.role,
        trashed: this.filters.trashed,
      },
    }
  },
  watch: {
    form: {
      deep: true,
      handler: throttle(function () {
        this.$inertia.get('/users', pickBy(this.form), { preserveState: true })
      }, 150),
    },
  },
  methods: {
    reset() {
      this.form = mapValues(this.form, () => null)
    },
  },
}
</script>
