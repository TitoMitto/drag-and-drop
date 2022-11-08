<script>
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
          console.log(`… file[${i}].name = ${file.name}`);
        }
      });
    } else {
      [...event.dataTransfer.files].forEach((file, i) => {
        console.log(`… file[${i}].name = ${file.name}`);
      });
    }
  }

</script>

<main class="h-screen  flex items-center bg-neutral-100 justify-center">
  <div
    on:dragover|preventDefault={onDragOverArea}
    on:dragleave={onLeaveDropArea}
    on:drop|preventDefault={onDropFile}
    class:bg-slate-100={isDragging}
    class:border-slate-500={isDragging}
    class="w-80 h-80 border-2 border-dashed  rounded-md border-spacing-4 p-10 flex flex-col  items-center justify-center  border-neutral-200 bg-white"
  >
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
</main>
