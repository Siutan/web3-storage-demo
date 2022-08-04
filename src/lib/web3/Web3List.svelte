<script>
    import { Web3Storage } from 'web3.storage';
    let iList = [];
    let pog = ""

    function makeStorageClient() {
        return new Web3Storage({
            endpoint: undefined,
            token: import.meta.env.VITE_WEB3_TOKEN
        })
    }

    async function listUploads () {
        const client = makeStorageClient()
        for await (const upload of client.list()) {
            console.log(upload)
            let uploadJson = {
                cid: upload.cid,
                size: upload.dagSize,
                created: upload.created
            }
            iList.push(uploadJson)
            pog = "lmaoooo"
        }
        console.log(iList)
    }

    // Notes:
    // card onClick handler is implemented as an inline function due to the restrictions of opening tabs and windows in browsers,
    // for future reference because i'll forget: https://stackoverflow.com/questions/4907843/open-a-url-in-a-new-tab-and-not-a-new-window

</script>

<div>
    {#await listUploads()}
    <h2>Retrieving List of Uploaded Items</h2>
    {:then e}
        <div class="">
            {#each iList as {cid, size, created}}
                <div class="card" on:click={() => { // Refer to notes in script section
                    let url = `https://${cid}.ipfs.dweb.link/`
                    window.open(url, '_blank').focus();
                }}>
                    <p><strong>Contract ID:</strong> {cid}</p>
                    <p><strong>Size:</strong> {size} Bytes</p>
                    <p><strong>Date Created:</strong> {created}</p>
                </div>
            {/each}
        </div>
    {:catch error}
    <p style="color: red">{error.message}</p>
    {/await}
</div>

<style>
    .card {
        border: 2px solid black;
        padding: 10px;
        margin: 10px;
        border-radius: 15px;
    }
    .card:hover {
        background-color: #f0f0f0;
    }
</style>

