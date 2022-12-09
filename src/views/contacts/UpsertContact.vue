<template>
  <div class="flex justify-center">
    <Card :title="cardTitle" class="w-[350px]">
      <div class="space-y-4">
        <el-input v-model.trim="contactForm.name" placeholder="Name" />

        <el-input v-model.trim="contactForm.description" placeholder="Description" />

        <el-input v-model.trim="contactForm.image" placeholder="Image Link" />
      </div>

      <template #footer>
        <div class="px-6 pb-6 mt-2 flex gap-3">
          <el-button class="flex-auto" type="warning" @click="$router.back"
          >
            Cancel
          </el-button>

          <el-button v-if="currentContact" class="flex-auto" type="danger" @click="onDelete">
            Delete
          </el-button>

          <el-button class="flex-auto" :disabled="!isFormValid" type="success" @click="onSave">
            <template #icon>
              <IconPlus class="w-5 h-5" />
            </template>
            Save
          </el-button>
        </div>
      </template>
    </Card>
  </div>
</template>

<script lang="ts" setup>
const router = useRouter()
const route = useRoute()

const { $routeNames } = useGlobalProperties()

const contactsStore = useContactsStore()
const { contacts } = storeToRefs(contactsStore)
const { addContact, updateContact, deleteContact } = contactsStore

const currentContact = computed(() => contacts.value.find(c => c.id === +route.params.contactId))

const cardTitle = computed(() => {
  return currentContact.value ? 'Edit Contact' : 'New Contact'
})

const contactForm = reactive<IContact>(currentContact.value
  ? { ...currentContact.value }
  : {
    id: contacts.value.length + 1,
    name: '',
    description: '',
    image: ''
  })

const isFormValid = computed(() => {
  const { image, ...contact } = contactForm
  return Object.values(contact).every(c => !!c)
})

function onDelete () {
  deleteContact(currentContact.value as IContact)
  router.replace({ name: $routeNames.contacts })
}

function onSave () {
  if (currentContact.value) {
    updateContact(contactForm)
  } else {
    addContact(contactForm)
  }
  router.push({ name: $routeNames.contacts })
}
</script>
