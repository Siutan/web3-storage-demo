<script lang="ts">
    import {Web3Storage} from "web3.storage";

    let contractIdInput = "";
    let isError = false;
    let errorMessage = "";
    let querySuccess = false;
    let queryResult = {};

    function makeStorageClient() {
        return new Web3Storage({
            endpoint: undefined,
            token: import.meta.env.VITE_WEB3_TOKEN
        })
    }

    async function retrieve(cid) {
        const client = makeStorageClient()
        const res = await client.get(cid)
        console.log(`Got a response! [${res.status}] ${res.statusText}`)
        if (!res.ok) {
            isError = true;
            querySuccess = false;
            errorMessage = `Failed to get the following Contract ID: ${cid} <br> Code: ${res.status} <br> Reason: Contract ID is not valid`;
            throw new Error(`Failed to get the following Contract ID: ${cid}, Code: ${res.status}, Reason: Contract ID is not valid`)
        }

        // unpack File objects from the response
        const files = await res.files()
        for (const file of files) {
            const {lastModifiedDate, cid: cid1, size, name} = file;
            querySuccess = true;
            isError = false;
            queryResult = {
                lastModifiedDate,
                url: `<a target="_blank" href="https://dweb.link/ipfs/${cid}">https://dweb.link/ipfs/${cid}</a>`,
                cid: cid1,
                size: size,
                name: name
            }
            console.log(`${cid1} -- ${lastModifiedDate} -- ${size}`)
            console.log(file)
        }
    }
</script>

<div id="web3Form" class="form" on:submit|preventDefault={retrieve(contractIdInput)}>
    <form id="retrieve-form">
        <label for="contractIdInput">Enter CID of contract to retrieve</label>
        <input type="text" id="contractIdInput" name="contractID" bind:value={contractIdInput} required/>
        <input type="submit" value="Submit" id="submit"/>
    </form>
    {#if isError}
        <div class="error">
            <p class="error">{@html errorMessage}</p>
        </div>
    {/if}
    {#if querySuccess}
        <div>
            <div class="success">
                <p>Contract ID:</p>
                {@html queryResult.cid}
            </div>
            <div class="success">
                <p>Item URL:</p>
                {@html queryResult.url}
            </div>
            <div class="success">
                <p>Upload Date:</p>
                {@html queryResult.lastModifiedDate}
            </div>
            <div class="success">
                <p>Size of  item/s:</p>
                {@html queryResult.size} Bytes
            </div>
            <div class="success">
                <p>Size of  item/s:</p>
                {@html queryResult.name}
            </div>
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

    .error {
        color: #ff0000;
        font: bold 16px/1.5 sans-serif;
        padding: 16px;
        font-weight: 700;
    }
    .success {
        color: #00ff00;
        font: bold 16px/1.5 sans-serif;
        padding: 16px;
        font-weight: 700;
    }
</style>