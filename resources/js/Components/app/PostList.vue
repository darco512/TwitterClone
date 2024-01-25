<script setup>
import PostItem from './PostItem.vue';



const post1 = {
    user: {
        id: 1,
        avatar: 'https://randomuser.me/api/portraits/men/68.jpg',
        name: 'John Doe'
    },
    group: null,
    body: `
        <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Magni excepturi voluptas facere odio vel qui consectetur magnam nobis quia, fugiat, illo eos dolores officiis. Architecto autem nisi eligendi nulla tempore.</p>

        <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Magni excepturi voluptas facere odio vel qui consectetur magnam nobis quia, fugiat, illo eos dolores officiis. Architecto autem nisi eligendi nulla tempore.</p>
        `,
    created_at: '2023-11-19 15:12',
}

const post2 = {
    user: {
        id: 1,
        avatar: 'https://randomuser.me/api/portraits/men/66.jpg',
        name: 'Tom Petters'
    },
    group: {
        id: 1,
        name: 'Laravel Developers'
    },
    attachments: [
        {
            id: 1,
            name: 'test.png',
            url: 'https://picsum.photos/1000',
            mime: 'image/png',
        },
        {
            id: 2,
            name: 'test2.png',
            url: 'https://picsum.photos/1000',
            mime: 'image/png',
        },
        {
            id: 2,
            name: 'MyDocument.docx',
            url: '#',
            mime: 'application/msword',
        }
    ],
    body: `
        <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Magni excepturi voluptas facere odio vel qui consectetur magnam nobis quia, fugiat, illo eos dolores officiis. Architecto autem nisi eligendi nulla tempore.</p>

        <p>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Magni excepturi voluptas facere odio vel qui consectetur magnam nobis quia, fugiat, illo eos dolores officiis. Architecto autem nisi eligendi nulla tempore.</p>
        `,
    created_at: '2023-11-19 15:12',
}

</script>

<template>
    <div class="overflow-auto">
        <PostItem :post="post1" />
        <PostItem :post="post2" />
    </div>
</template>

<style lang="scss" scoped>

</style>