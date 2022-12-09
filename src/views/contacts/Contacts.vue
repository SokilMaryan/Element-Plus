<template>
  <div class="flex items-center gap-4">
    <h3 class="font-medium m-0">Contact list</h3>

    <el-button 
    @click="createNewContact"
    type="primary"
    >
      <template #icon>
        <IconPlus class="w-5 h-5" />
      </template>
      Add Contact
    </el-button>

    <el-button
      class="!ml-0"
      type="danger"
      @click="$router.replace({ name: $routeNames.login })"
    >
      Logout
    </el-button>
  </div>

  <div>
    <el-tabs 
      v-model="activeName" 
      type="card"
      class="el-tabs py-[32px] text-xl" 
      @tab-click="handleClick">
    <el-tab-pane label="Card view" name="ContactCard"/>
    <el-tab-pane label="Table view" name="Table"/>
  </el-tabs>
  </div>

  <div v-if="activeName === 'ContactCard'" class="grid-cols-[repeat(auto-fill,_minmax(320px,_1fr))] grid gap-5 my-5">
    <ContactItem
      v-for="contact in contacts"
      :key="contact.id"
      class="cursor-pointer"
      :contact="contact"
      @click="editContact(contact.id)"
      @delete="deleteContact"
      @save="updateContact"
    />
  </div>

  <ContactTableView
    v-else
    @click="editContact"  
    @delete="deleteContact"
    @save="updateContact"
  />
      
</template>
<script lang="ts" setup>
import { ref } from 'vue'
import type { TabsPaneContext } from 'element-plus'
const router = useRouter()
const { $routeNames } = useGlobalProperties()

const activeName = ref('')

const handleClick = (tab: TabsPaneContext, event: Event) => {
  console.log(tab, event)
}

const contactsStore = useContactsStore()
const { contacts } = storeToRefs(contactsStore)
const { updateContact, deleteContact } = contactsStore

function createNewContact () {
  router.push({ name: $routeNames.upsertContact, params: { contactId: 'new' } })
}

function editContact (contactId: number) {
  router.push({ name: $routeNames.upsertContact, params: { contactId } })
}

</script>


