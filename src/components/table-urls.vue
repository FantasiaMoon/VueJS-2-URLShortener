<template>
    <ul class="bloc-list">
        <li class="bloc" :key="url.uid" v-for="url in taburl"> <!-- On applique/ajoute la classe completed si la variable completed est true -->
            <div class="view">
                <label><a :href="url.shortenUrl" target="_blank">{{ url.shortenUrl }}</a></label>
                <button class="copy" v-clipboard:copy="url.shortenUrl" v-clipboard:success="onCopy" v-clipboard:error="onError"></button>
                <button class="destroy" @click.prevent="removeURL(url)"></button>
            </div>
        </li>
    </ul>
</template>

<script>
    export default {
        props : {
            taburl : Array
        },
        methods: {
            // Delete an url
            removeURL : function(index) {
                this.$emit('removeURL', index);
            },

            onCopy: function (e) {
                alert('You just copied: ' + e.text)
            },

            onError: function (e) {
                alert('Failed to copy URLs!')
            },
        }
    }
</script>