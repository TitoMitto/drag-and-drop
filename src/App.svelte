<script>
  "@hmr:keep-all";

  let supportedFileTypes = ["png", "jpg", "pdf", "svg", "txt", "zip", "ai"];
  let uploads = {};
  let fileList;
  let fileInput;
  let isDragging = false;

  function onDragOverArea(event) {
    isDragging = true;
  }

  function onLeaveDropArea(e) {
    isDragging = false;
  }

  async function onDropFile(event) {
    isDragging = false;
    if (event.dataTransfer.items) {
      let files = [];
      [...event.dataTransfer.items].forEach((item) => {
        if (item.kind === "file") {
          const file = item.getAsFile();
          files.push(file);
        }
      });
      onReceiveFiles(files);
    } else {
      onReceiveFiles(event.dataTransfer.files);
    }
    
  }

  function onReceiveFiles(files) {
    uploads = Object.assign(
      {},
      uploads,
      ...files.map((file) => ({
        [file.name]: {
          paused: false,
          uploaded: null,
          file,
        },
      }))
    );
    uploadFiles();
    console.log("JSSSSO ", uploads);
  }

  function removeFile(fileName) {
    delete uploads[fileName];
    uploads = { ...uploads };
  }

  function toggleUpload(index) {
    console.log("PAUSING ", index, uploads[index], uploads);
    uploads[index].paused = !uploads[index].paused;
    uploads[index].uploaded = null;
    uploads = uploads;
    console.log("AFTER ", index, uploads[index], uploads);
    if (!uploads[index].paused) {
      uploadFiles();
    }
  }

  function uploadFiles() {
    Object.entries(uploads).forEach((item) => {
      let upload = item[1];
      if (upload.uploaded == null && !upload.paused) {
        uploadFile(upload);
      }
    });
  }

  function uploadFile(upload) {
    uploads[upload.file.name].uploaded = false;
    uploads = uploads;
    setTimeout(() => {
      if (!uploads[upload.file.name].paused) {
        uploads[upload.file.name].uploaded = Date.now();
      }
    }, 2000);
  }

  function isSupportedFile(file) {
    let type = getFileType(file);
    return supportedFileTypes.includes(type);
  }

  function getFileType(file) {
    let type = file.name.split(".")[1];
    return type;
  }

  function getImage(file) {
    if (isSupportedFile(file)) {
      let type = getFileType(file);
      return `${type}.png`;
    }
    return "/corrupted-file.png";
  }

  function onPickFile() {
    fileInput.click();
  }

  $: hasFiles = uploads.length > 0;
  $: isUploadingFiles = Object.entries(uploads).some(
    (item) => !item[1].paused && !item[1].uploaded
  );

  $: if (fileList) {
    console.log(fileList);
    let files = [];
    for (let file of fileList) {
      files.push(file);
    }
    onReceiveFiles(files);
  }
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
    class:border-dashed={!hasFiles || isDragging}
    class=" w-80 border    rounded-md border-spacing-4"
  >
    {#if isUploadingFiles}
      <div
        class="flex w-80 h-80 flex-col flex-1 items-center justify-center py-5"
      >
        <svg
          class="inline mr-2 w-10 h-10 animate-spin text-slate-300 fill-blue-600"
          viewBox="0 0 100 101"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
            fill="currentColor"
          />
          <path
            d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
            fill="currentFill"
          />
        </svg>
      </div>
    {:else}
      <div
        class="flex w-80 h-80 flex-col flex-1 items-center justify-center py-5"
      >
        <img class="h-14" src="/file.png" alt="File Icon" />
        <p class="font-semibold mt-2 text-blue-900">Proof of Payment</p>
        <p class="text-center text-neutral-500 mt-1">
          Upload or drag and drop <br /> an image
        </p>

        <button
          class:bg-slate-50={isDragging || hasFiles}
          class:border-slate-200={isDragging}
          on:click={onPickFile}
          class="px-4 py-2 mt-5 rounded-md flex items-center border-solid border-2 font-medium text-blue-900"
        >
          <input
            type="file"
            class="hidden"
            bind:this={fileInput}
            bind:files={fileList}
            multiple
          />
          <img class="h-5 mr-2 " src="/upload.png" alt="File Icon" />
          Upload
        </button>
      </div>
    {/if}
    <div class:flex-auto={hasFiles} class="w-full bg-gray-50 rounded-b-md">
      {#each Object.entries(uploads) as [fileName, upload]}
        <div class="flex flex-row  border-t">
          <div
            class="p-3 flex flex-row items-center hover:bg-neutral-100 w-full m-2 rounded-lg"
          >
            <div class="p-2 rounded-md border">
              <img src={getImage(upload.file)} alt="file" class="h-10 " />
            </div>
            <span class="ml-5 flex-auto"> {fileName} </span>
            {#if upload.uploaded}
              <img
                src={isSupportedFile(upload.file)
                  ? "/checked.png"
                  : "/failed.png"}
                class="h-6"
                alt=""
              />
            {:else}
              <button
                class="border p-1 rounded mx-1 hover:bg-slate-100"
                on:click={() => toggleUpload(fileName)}
              >
                <img
                  src={upload.paused ? "/play.png" : "/pause.png"}
                  class="h-3"
                  alt=""
                />
              </button>
              <button
                class="border p-1 rounded hover:bg-slate-100"
                on:click={() => removeFile(fileName)}
              >
                <img src="/x.png" class="h-3" alt="" />
              </button>
            {/if}
          </div>
        </div>
      {/each}
    </div>
  </div>
</main>
