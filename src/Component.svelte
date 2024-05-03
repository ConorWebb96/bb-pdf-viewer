<script>
  import { getContext } from "svelte"

  const { styleable } = getContext("sdk")
  const component = getContext("component")
  
  export let usage;
  export let dataProvider;
  export let url;
  export let name;
  export let width;
  export let height;

  $: files = dataProvider?.rows?.flatMap(obj => {
        const attachments = [];
        Object.keys(obj).forEach(key => {
            const value = obj[key];
            if (Array.isArray(value)) {
                attachments.push(...value.filter(att => att.extension === 'pdf' && att.url));
            } else if (typeof value === 'object' && value !== null && value.extension === 'pdf' && value.url) {
                attachments.push(value);
            }
        });
        return attachments;
    })
    .map(attachment => ({
        name: attachment.name,
        url: attachment.url
    }));
</script>

<div use:styleable={$component.styles}>
  {#if usage == "multi"}
     {#if files.length > 0}
       {#each files as pdfFile (pdfFile)}
           <iframe
             src={pdfFile.url}
             width={width ? width : 500}
             height={height ? height : 500}
             title={pdfFile.name}
           >
           </iframe>
       {/each}
     {:else}
       Sorry you have no pdf attachments in your selected dataprovider
     {/if}
  {:else}
    <iframe
      src={url}
      width={width ? width : 500}
      height={height ? height : 500}
      title={name}
    >
    </iframe>
  {/if}
</div>