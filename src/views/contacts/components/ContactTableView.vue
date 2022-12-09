<template>
  <el-table :data="contacts" class="el-tabs w-full">
    <el-table-column 
    label="Image" 
    prop="image">
      <template #default="scope">
        <div class="flex items-center justify-center w-[60px] h-[60px] ml-2 rounded-full shrink-0 overflow-hidden
      border border-gray-medium bg-gray-ultra-light">
          <span
            v-if="scope.row.imageHasError || !scope.row.image"
            class="font-semibold uppercase"
          >
            {{nameAbbrv(scope.row)}}
          </span>
          <img
            v-else
            class="w-[60px] h-[60px] rounded-full"
            :src="scope.row.image"
            :alt="scope.row.name"
            @error="scope.row.imageHasError = true"
            @load="scope.row.imageHasError = false"
          >
        </div>
      </template>
    </el-table-column>

    <el-table-column label="Name" prop="name" class="flex items-center justify-center">
      <template #default="scope">
          <p v-if="!editMode" class="font-medium cursor-text">{{ scope.row.name }}</p>
          <el-input
            v-else
            ref="inputRef"
            v-model="scope.row.name"
            type="text"
            class="block font-medium w-full"
          />
      </template>
    </el-table-column>

    <el-table-column label="Description" prop="description" class="flex items-center justify-center">
      <template #default="scope">
          <p v-if="!editMode" class="font-medium cursor-text">{{ scope.row.description }}</p>
          <el-input
            v-else
            ref="inputRef"
            v-model="scope.row.description"
            type="text"
            class="block font-medium w-full"
          />
      </template>
    </el-table-column>

    <el-table-column align ="right" v-if="editMode">
      <template #default="scope">
        <el-button size="small"  type="warning"
        @click.stop="editMode = false"
        >
        Cancel
        </el-button>

        <el-button size="small" type="success"
          @click.stop="onSave"
        >
          Save
        </el-button
        >
      </template>
    </el-table-column>

    <el-table-column align ="right"
    v-else>
      <template #default="scope">
        <el-button  size="small" type="primary"
        @click.stop="triggerEditMode" 
        >
        Edit
        </el-button>

        <el-button size="small" type="danger"
          @click.stop="$emit('delete', scope.row)"
        >
        Delete
        </el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script lang="ts" setup>

const contactsStore = useContactsStore()
const { contacts } = storeToRefs(contactsStore)
const editMode = ref(false)
const emit = defineEmits(['delete', 'save'])
const inputRef = ref<HTMLInputElement>()

const props = defineProps<{
  contact: IContact
}>()

const localContact = ref<Omit<IContact, 'id'>>({
  name: '',
  description: '',
  image: ''
})

function nameAbbrv (contact: IContact) {
  return contact.name.split(' ').reduce((acc, cur) => {
    if (acc.length < 2) {
      acc = acc.concat(cur.slice(0,2))
    }
    return acc
  }, '')
}

async function triggerEditMode () {
  editMode.value = true
  localContact.value = { ...props.contact }
  await nextTick()
  inputRef.value?.focus()
}

function onSave () {
  emit('save', localContact.value)
  editMode.value = false
}
</script>

<style>
.el-tabs > .el-tabs-column {
  padding: 32px;
  color: #6b778c;
  font-size: 32px;
  font-weight: 600;
}
</style>
