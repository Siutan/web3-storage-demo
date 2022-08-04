<script lang="ts">
    import {Web3Storage} from 'web3.storage';

    let uploadState = "";
    let files;
    let contractID;
    let finished = false;

    async function handleSubmit() {
        let filesArr = Array.from(files);
        await storeWithProgress(filesArr);
    }

    function makeStorageClient() {
        return new Web3Storage({
            endpoint: undefined,
            token: import.meta.env.VITE_WEB3_TOKEN
        })
    }

    async function storeWithProgress(files) {
        // show the root cid as soon as it's ready
        const onRootCidReady = cid => {
            contractID = cid;
            uploadState = "Uploading files with Contract ID: <br>" + cid;

        }

        // when each chunk is stored, update the percentage complete and display
        // const totalSize = files.map(f => f.size).reduce((a, b) => a + b, 0)
        // let uploaded = 0

        const onStoredChunk = size => {
            console.log("onStoredChunk", size);
            // uploaded += size
            // let pct = totalSize / uploaded * 100
            checkStatus(contractID);

        }
        // makeStorageClient returns an authorized Web3.Storage client instance
        const client = makeStorageClient()

        // client.put will invoke our callbacks during the upload
        // and return the root cid when the upload completes
        return client.put(
            files,
            {onRootCidReady, onStoredChunk})
    }

    //TODO: error handling
    function onError(err) {
        console.log(err);
    }

    async function checkStatus(cid) {
        const client = makeStorageClient()
        const uploadStatus = await client.status(cid)
        console.log(uploadStatus.pins);

        if (uploadStatus) {
            // language=HTML
            uploadState = `Finished uploading files, your Contract ID (cid) is: ${contractID} <br>
            you can view it <a target="_blank" href="https://dweb.link/ipfs/${contractID}">HERE</a>`
            finished = true;
        }
    }
</script>

<div id="web3Form" class="form" on:submit|preventDefault={handleSubmit}>
    <form id="upload-form">
        <label for="filePicker">Pick files to store</label>
        <input type="file" id="filePicker" name="fileList" bind:files multiple required/>
        {#if files}
            <h2>Selected files:</h2>
            {#each Array.from(files) as file}
                <p>{file.name} ({file.size} bytes) </p>
            {/each}
        {/if}
        <input type="submit" value="Submit" id="submit"/>
    </form>

    {#if contractID}
        <div id="uploadState">
            {#if !finished}
                <div class="loader">Loading...</div>
            {/if}
            {@html uploadState}
        </div>
    {/if}
</div>

<style>
    form {
        width: 500px;
        padding: 16px;
        max-width: 100%;
        display: block;
        margin: 0 auto;
        color: #333;
    }

    label {
        display: block;
        padding: 32px 0 8px;
        font-weight: 700;
    }

    input[type=submit] {
        border-radius: 20px;
        display: block;
        padding: 4px 16px;
        font-weight: 700;
        font-size: 16px;
        margin-top: 32px;
    }

    .loader,
    .loader:before,
    .loader:after {
        border-radius: 20%;
        width: 2.5em;
        height: 2.5em;
        -webkit-animation-fill-mode: both;
        animation-fill-mode: both;
        -webkit-animation: load7 1.8s infinite ease-in-out;
        animation: load7 1.8s infinite ease-in-out;
    }

    .loader {
        color: #ffffff;
        font-size: 10px;
        margin: 80px auto;
        position: relative;
        text-indent: -9999em;
        -webkit-transform: translateZ(0);
        -ms-transform: translateZ(0);
        transform: translateZ(0);
        -webkit-animation-delay: -0.16s;
        animation-delay: -0.16s;
    }

    .loader:before,
    .loader:after {
        content: '';
        position: absolute;
        top: 0;
    }

    .loader:before {
        left: -3.5em;
        -webkit-animation-delay: -0.32s;
        animation-delay: -0.32s;
    }

    .loader:after {
        left: 3.5em;
    }

    @-webkit-keyframes load7 {
        0%,
        80%,
        100% {
            box-shadow: 0 2.5em 0 -1.3em;
        }
        40% {
            box-shadow: 0 2.5em 0 0;
        }
    }

    @keyframes load7 {
        0%,
        80%,
        100% {
            box-shadow: 0 2.5em 0 -1.3em;
        }
        40% {
            box-shadow: 0 2.5em 0 0;
        }
    }


</style>