<template>
    <div>
        <table>
            <thead>
                <tr>
                    <th>View Name</th>
                    <th v-for="version in versions" :key="version">{{ version.commitId }}</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="view in views" :key="view">
                    <td>{{ view.viewName }}</td>

                    <td class="view-preview" v-for="version in versions" :key="version">
                        <img :src="(version.images[view.viewName] !== undefined) ? version.images[view.viewName].src : 'https://upload.wikimedia.org/wikipedia/commons/5/5f/Red_X.svg'" class="view-screenshot" />
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>

export default {
    data() {
        return {
            views: {},
            versions: {},
        };
    },
    mounted() {
        const imageFiles = require.context('/public/screenshots', true, /\.(jpe?g|png|gif|svg)$/);

        const views = {};
        const versions = {};

        imageFiles.keys().forEach((key) => {
            // extract semver from file path
            //const version = key.match(/^\.\/(.+?)\//)[1];
            const fileName = key.split('/').pop()
            const commitId = fileName.split('.')[0]
            const viewName = fileName.split('.')[1].split('--')[0]

            views[viewName] = {
                fileName: fileName,
                viewName: viewName,
            }

            // create image object
            const image = {
                src: imageFiles(key),
                name: fileName,
                commitId: commitId,
            };

            if (!versions[commitId]) {
                versions[commitId] = {
                    commitId: commitId,
                    images: {},
                };
            }
            versions[commitId].images[viewName] = image
        });

        this.versions = versions;
        this.views = views;
    },
};
</script>



<style>
table {
    width: 100%;
    border-collapse: collapse;
}

th,
td {
    padding: 8px;
    text-align: center;
    border-bottom: 1px solid #ddd;
}

.view-screenshot
{
    width: 400px;
    height: 200px;
    border: 2px solid #555;
}
</style>
  