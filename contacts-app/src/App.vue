<script setup>
import { computed, ref } from 'vue'

const CONTACTS_INITIAL_VALUE = [
  {
    name: 'Gonzalo Gonzalez',
    work: 'Software Engineer',
    mobile: '+506 6000-1001',
    email: 'gonzalo@example.com',
    address: 'San Jose, Costa Rica'
  },
  {
    name: 'Mario Maroto',
    work: 'Accountant',
    mobile: '+506 6000-1002',
    email: 'mario@example.com',
    address: 'Alajuela, Costa Rica'
  },
  {
    name: 'Rodrigo Rodriguez',
    work: 'Teacher',
    mobile: '+506 6000-1003',
    email: 'rodrigo@example.com',
    address: 'Cartago, Costa Rica'
  },
  {
    name: 'Zara Soto',
    work: 'Designer',
    mobile: '+506 6000-1004',
    email: 'zara@example.com',
    address: 'Heredia, Costa Rica'
  }
]

/**
 * Creates a new contact object with empty values.
 * @returns {{ name: string, work: string, mobile: string, email: string, address: string }}
 */
function createEmptyContact() {
  return { name: '', work: '', mobile: '', email: '', address: '' }
}

const contacts = ref([...CONTACTS_INITIAL_VALUE])
const selectedContactIndex = ref(0)
const editableContact = ref({ ...contacts.value[0] })
const hasSelectedContact = computed(() => selectedContactIndex.value !== null)
const isDarkModeEnabled = ref(true)
const currentThemeModeLabel = computed(() => (isDarkModeEnabled.value ? 'Dark' : 'Light'))

/**
 * Loads the selected contact into the form.
 * @param {number} index
 * @returns {void}
 */
function selectContact(index) {
  selectedContactIndex.value = index
  editableContact.value = { ...contacts.value[index] }
}

/**
 * Adds a new contact and selects it.
 * @returns {void}
 */
function executeAddContact() {
  contacts.value.push(createEmptyContact())
  const lastContactIndex = contacts.value.length - 1
  selectContact(lastContactIndex)
}

/**
 * Removes the selected contact from the list.
 * @returns {void}
 */
function executeDeleteContact() {
  if (selectedContactIndex.value === null) {
    return
  }
  contacts.value.splice(selectedContactIndex.value, 1)
  if (contacts.value.length === 0) {
    selectedContactIndex.value = null
    editableContact.value = createEmptyContact()
    return
  }
  const nextContactIndex = Math.min(selectedContactIndex.value, contacts.value.length - 1)
  selectContact(nextContactIndex)
}

/**
 * Persists form values into the selected contact in memory.
 * @returns {void}
 */
function executeSaveContact() {
  if (selectedContactIndex.value === null) {
    return
  }
  contacts.value[selectedContactIndex.value] = { ...editableContact.value }
}

/**
 * Toggles the theme mode.
 * @returns {void}
 */
function executeToggleThemeMode() {
  isDarkModeEnabled.value = !isDarkModeEnabled.value
}
</script>

<template>
  <main :class="['contacts-page', isDarkModeEnabled ? 'contacts-page--dark' : 'contacts-page--light']">
    <div class="header-row">
      <h1 class="page-title">All Contacts</h1>
      <button class="theme-button" type="button" @click="executeToggleThemeMode">
        {{ currentThemeModeLabel }} Mode
      </button>
    </div>
    <section class="contacts-layout">
      <div class="contacts-list-container">
        <ul class="contacts-list">
          <li
            v-for="(contact, index) in contacts"
            :key="`${contact.name}-${index}`"
            :class="['contacts-item', { 'contacts-item--active': index === selectedContactIndex }]"
            @click="selectContact(index)"
          >
            {{ contact.name || 'New Contact' }}
          </li>
        </ul>
        <button class="action-button" type="button" @click="executeAddContact">Add</button>
      </div>
      <div class="form-container">
        <label class="field-label">
          name:
          <input v-model="editableContact.name" class="field-input" type="text" />
        </label>
        <label class="field-label">
          work:
          <input v-model="editableContact.work" class="field-input" type="text" />
        </label>
        <label class="field-label">
          mobile:
          <input v-model="editableContact.mobile" class="field-input" type="text" />
        </label>
        <label class="field-label">
          email:
          <input v-model="editableContact.email" class="field-input" type="email" />
        </label>
        <label class="field-label">
          address:
          <textarea v-model="editableContact.address" class="field-input field-input--textarea" />
        </label>
        <div class="buttons-row">
          <button class="action-button action-button--danger" type="button" @click="executeDeleteContact">
            Delete
          </button>
          <button class="action-button" type="button" :disabled="!hasSelectedContact" @click="executeSaveContact">
            Save
          </button>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
.contacts-page {
  --page-background-color: #f4f6f8;
  --text-color: #1f2933;
  --card-background-color: #ffffff;
  --list-item-background-color: #ffffff;
  --list-item-text-color: #1f2933;
  --list-item-hover-background-color: #dde7f2;
  --list-item-active-background-color: #2c3e50;
  --list-item-active-text-color: #ffffff;
  --field-background-color: #ffffff;
  --field-text-color: #1f2933;
  --field-border-color: #cfcfcf;
  --list-border-color: #cfcfcf;
  --list-divider-color: #ebebeb;
  --button-background-color: #2c3e50;
  --button-hover-color: #1f2f3e;
  --danger-button-background-color: #8d2f2f;
  --danger-button-hover-color: #722424;
  --theme-button-background-color: #37495d;
  max-width: 980px;
  margin: 0 auto;
  padding: 2rem;
  color: var(--text-color);
  background-color: var(--page-background-color);
}
.contacts-page--dark {
  --page-background-color: #111418;
  --text-color: #f1f1f1;
  --card-background-color: #161b22;
  --list-item-background-color: #141414;
  --list-item-text-color: #f1f1f1;
  --list-item-hover-background-color: #31475d;
  --list-item-active-background-color: #2c3e50;
  --list-item-active-text-color: #ffffff;
  --field-background-color: #ffffff;
  --field-text-color: #0f1720;
  --field-border-color: #cfcfcf;
  --list-border-color: #5f6b76;
  --list-divider-color: #5f6b76;
  --button-background-color: #2c3e50;
  --button-hover-color: #1f2f3e;
  --danger-button-background-color: #8d2f2f;
  --danger-button-hover-color: #722424;
  --theme-button-background-color: #3a4f64;
}
.contacts-page--light {
  --page-background-color: #f4f6f8;
  --text-color: #1f2933;
  --card-background-color: #ffffff;
  --list-item-background-color: #ffffff;
  --list-item-text-color: #1f2933;
  --list-item-hover-background-color: #dde7f2;
  --list-item-active-background-color: #2c3e50;
  --list-item-active-text-color: #ffffff;
  --field-background-color: #ffffff;
  --field-text-color: #1f2933;
  --field-border-color: #cfcfcf;
  --list-border-color: #cfcfcf;
  --list-divider-color: #ebebeb;
  --button-background-color: #2c3e50;
  --button-hover-color: #1f2f3e;
  --danger-button-background-color: #8d2f2f;
  --danger-button-hover-color: #722424;
  --theme-button-background-color: #4a6580;
}
.header-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 1rem;
}
.page-title {
  font-size: 1.5rem;
  font-weight: 700;
}
.theme-button {
  padding: 0.45rem 0.85rem;
  color: #ffffff;
  cursor: pointer;
  background-color: var(--theme-button-background-color);
  border: none;
}
.contacts-layout {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}
.contacts-list-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}
.contacts-list {
  min-height: 320px;
  max-height: 320px;
  padding: 0;
  margin: 0;
  overflow-y: auto;
  list-style: none;
  border: 1px solid var(--list-border-color);
  background-color: var(--card-background-color);
}
.contacts-item {
  padding: 0.75rem 1rem;
  color: var(--list-item-text-color);
  cursor: pointer;
  background-color: var(--list-item-background-color);
  border-bottom: 1px solid var(--list-divider-color);
}
.contacts-item:hover {
  background-color: var(--list-item-hover-background-color);
}
.contacts-item--active {
  color: var(--list-item-active-text-color);
  background-color: var(--list-item-active-background-color);
}
.form-container {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  padding: 1rem;
  background-color: var(--card-background-color);
  border: 1px solid var(--field-border-color);
}
.field-label {
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
  font-size: 0.95rem;
  font-weight: 600;
}
.field-input {
  width: 100%;
  padding: 0.55rem 0.7rem;
  color: var(--field-text-color);
  background-color: var(--field-background-color);
  border: 1px solid var(--field-border-color);
}
.field-input--textarea {
  min-height: 88px;
  resize: vertical;
}
.buttons-row {
  display: flex;
  gap: 0.75rem;
  margin-top: 0.25rem;
}
.action-button {
  padding: 0.5rem 1rem;
  color: #ffffff;
  cursor: pointer;
  background-color: var(--button-background-color);
  border: none;
}
.action-button:hover {
  background-color: var(--button-hover-color);
}
.action-button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}
.action-button--danger {
  background-color: var(--danger-button-background-color);
}
.action-button--danger:hover {
  background-color: var(--danger-button-hover-color);
}
@media (max-width: 820px) {
  .header-row {
    align-items: flex-start;
    gap: 0.75rem;
    flex-direction: column;
  }
  .contacts-layout {
    grid-template-columns: 1fr;
  }
  .contacts-list {
    min-height: 220px;
    max-height: 220px;
  }
}
</style>
