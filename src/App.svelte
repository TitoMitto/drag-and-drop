<script>
  let files = [{ name: "image_34.jpg" }, { name: "tito_mitto.png" },{ name: "Cho.svg" }];
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

  $: hasFiles = files.length > 0;
</script>

<main class="h-screen  flex items-center bg-neutral-100 justify-center">
  <div
    on:dragover|preventDefault={onDragOverArea}
    on:dragleave={onLeaveDropArea}
    on:drop|preventDefault={onDropFile}
    class:bg-slate-100={isDragging}
    class:bg-white={!isDragging}
    class:border-neutral-200={!isDragging}
    class:border-slate-500={isDragging}
    class:h-80={!hasFiles}
    class=" w-80 border-2 border-dashed   rounded-md border-spacing-4  flex flex-col"
  >
    <div class="flex flex-col flex-1 items-center justify-center py-5">
      <img class="h-14" src="/file.png" alt="File Icon" />
      <p class="font-semibold mt-2 text-blue-900">Proof of Payment</p>
      <p class="text-center text-neutral-500 mt-1">
        Upload or drag and drop <br /> an image
      </p>

      <button
        class:bg-slate-50={isDragging}
        class:border-slate-200={isDragging}
        class="px-4 py-2 mt-5 rounded-md flex items-center border-solid border-2 font-medium text-blue-900"
      >
        <img class="h-5 mr-2" src="/upload.png" alt="File Icon" />
        Upload
      </button>
    </div>
    <div class:flex-auto={hasFiles} class="w-full bg-red-400 ">
      {#each files as file}
        <div class="p-4 flex flex-row items-center">
          <img src="/file.png" alt="file" class="h-10 "> <span class="ml-5"> {file.name} </span>
        </div>
      {/each}
    </div>
  </div>
</main>
