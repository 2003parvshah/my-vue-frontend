<!-- src/components/ChatInputBox.vue -->
<template>
    <div class="chat-input-container">
        <button @click="triggerFileSelect" class="file-button">＋</button>
        <input type="file" ref="fileInput" @change="handleFileUpload" hidden />
        <input v-model="newMessage" @keyup.enter="handleSend" placeholder="Type a message..." class="message-input" />
        <button @click="handleSend" class="send-button">➤</button>
    </div>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['send', 'upload'])
const newMessage = ref('')
const fileInput = ref(null)

const handleSend = () => {
    const trimmed = newMessage.value.trim()
    if (trimmed) {
        emit('send', trimmed)
        newMessage.value = ''
    }
}

const triggerFileSelect = () => {
    fileInput.value?.click()
}

const handleFileUpload = async (e) => {
    const file = e.target.files[0]
    if (!file) return

    const fileType = getFileType(file)
    const formData = new FormData()
    formData.append('file', file)
    formData.append('type', fileType)

    try {
        await emit('upload', formData)
    } catch (err) {
        console.error('❌ Upload failed:', err)
    } finally {
        fileInput.value.value = null // reset
    }
}

const getFileType = (file) => {
    const mime = file.type
    if (mime.startsWith('image/')) return 'image'
    if (mime.startsWith('video/')) return 'video'
    if (mime.startsWith('audio/')) return 'audio'
    return 'file'
}
</script>

<style scoped>
.chat-input-container {
    display: flex;
    gap: 0.5rem;
    align-items: center;
    padding-top: 0.5rem;
}

.message-input {
    flex: 1;
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 0.5rem;
}

.send-button {
    background-color: #3b82f6;
    color: white;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 0.5rem;
}
</style>
