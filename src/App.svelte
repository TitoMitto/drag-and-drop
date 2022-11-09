<script>
  let files = [{name: "BoomBox.jpg"}];
  let isDragging = false;

  function onDragOverArea(event) {
    isDragging = true;
  }

  function onLeaveDropArea(e) {
    isDragging = false;
  }

  function onDropFile(event) {
    isDragging = false;
    if (event.dataTransfer.items) {
      [...event.dataTransfer.items].forEach((item, i) => {
        if (item.kind === "file") {
          const file = item.getAsFile();
          files = [...files, file];
        }
      });
    } else {
      files = [...files, ...event.dataTransfer.files];
    }
  }

  function removeFile(index){
    files.splice(index, 1);
    files = [...files];
  }

  function pauseUpload(index){
    //files.splice(index, 1);
    //files = [...files];
  }

  function getImage(file) {
    let type = file.name.split(".")[1];

    switch(type){
      case "png":
        return "/png.png";
      case "jpg":
        return "/jpg.png";
      case "pdf":
        return "/pdf.png";
      case "svg":
        return "/svg.png";
      case "txt":
        return "/txt.png";
      case "zip":
        return "/zip.png";
      case "ai":
        return "/ai.png";
      default:
        return "/corrupted-file.png";
    }
  }

  $: hasFiles = files.length > 0;
</script>

<main class="h-screen  flex items-center bg-neutral-100 justify-center">
  <div
    on:dragover|preventDefault={onDragOverArea}
    on:dragleave={onLeaveDropArea}
    on:drop|preventDefault={onDropFile}
    class:bg-slate-100={isDragging || hasFiles}
    class:bg-white={!isDragging && !hasFiles}
    class:border-neutral-200={!isDragging}
    class:border-slate-500={isDragging}
    class=" w-80 border border-dashed   rounded-md border-spacing-4"
  >
    <div  class="flex w-80 h-80 flex-col flex-1 items-center justify-center py-5">
      <img class="h-14" src="/file.png" alt="File Icon" />
      <p class="font-semibold mt-2 text-blue-900">Proof of Payment</p>
      <p class="text-center text-neutral-500 mt-1">
        Upload or drag and drop <br /> an image
      </p>

      <button
        class:bg-slate-50={isDragging || hasFiles}
        class:border-slate-200={isDragging}
        class="px-4 py-2 mt-5 rounded-md flex items-center border-solid border-2 font-medium text-blue-900"
      >
        <img class="h-5 mr-2" src="/upload.png" alt="File Icon" />
        Upload
      </button>
    </div>
    <div class:flex-auto={hasFiles} class="w-full bg-gray-50">
      {#each files as file, index}
        <div class="p-3 flex flex-row items-center border-t">
          <div class="p-2 rounded-md border"><img src={getImage(file)} alt="file" class="h-10 "></div> 
          <span class="ml-5 flex-auto"> {file.name} </span> 
          <button class="border p-1 rounded mx-1" on:click={()=> pauseUpload(index)}> 
            <img src="/pause.png" class="h-3" alt=""> 
          </button>
          <button class="border p-1 rounded" on:click={()=> removeFile(index)}>
            <img src="/x.png" class="h-3" alt=""> 
           </button>
        </div>
      {/each}
    </div>
  </div>
</main>
