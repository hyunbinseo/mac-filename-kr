<script lang="ts">
  import { slide } from "svelte/transition";

  import "./lib/style.css";

  import BackhandIndexPointingRight from "./assets/emoji/1F449.svelte";
  import Badge from "./assets/Badge.svelte";

  import { downloadFiles, downloadItems } from "./lib/functions";

  const { VITE_GITHUB_URL } = import.meta.env;

  let badgeIsLoaded = false;

  let filesAreDraggedOver = false;

  const handleDragOver = (e: DragEvent) => {
    filesAreDraggedOver = true;
    e.dataTransfer.dropEffect = "copy";
  };

  const handleDragLeave = () => {
    filesAreDraggedOver = false;
  };

  const handleDrop = ({ dataTransfer: { items, files } }: DragEvent) => {
    if (items) {
      downloadItems(items);
    } else {
      downloadFiles(files);
    }
    // on:dragleave might not be called, even if files are downloaded
    // e.g. Chromium permission - Site wants to: Download multiple files
    filesAreDraggedOver = false;
  };

  let fileInput: HTMLInputElement;

  const handleFileChange = (e: Event) => {
    const input = e.target as HTMLInputElement;
    const { files } = input;
    downloadFiles(files);
    input.value = "";
  };
</script>

<div
  class="fixed inset-0 flex select-none flex-col bg-white pt-16 pb-12 transition-colors"
  class:bg-gray-50={!filesAreDraggedOver}
  class:bg-gray-300={filesAreDraggedOver}
  class:dark:bg-slate-900={!filesAreDraggedOver}
  class:dark:bg-slate-800={filesAreDraggedOver}
  on:dragover|preventDefault|stopPropagation={handleDragOver}
>
  <main
    class="mx-auto flex w-full max-w-7xl flex-grow flex-col justify-center px-4 sm:px-6 lg:px-8"
  >
    <div class="text-center">
      {#if !filesAreDraggedOver}
        <h1
          transition:slide
          class="text-2xl text-gray-800 dark:text-gray-200 md:text-3xl"
        >
          <span class="tracking-tighter">ㅍㅏㅇㅣㄹㅁㅕㅇ</span>
          <!-- SVG positioning from https://blog.prototypr.io/d44b3d7b26b4 -->
          <span
            class="relative top-0.5 inline-flex h-6 w-6 self-center md:h-7 md:w-7"
          >
            <!-- Emoji from https://github.com/sensadesign/sensaemoji -->
            <BackhandIndexPointingRight />
          </span>
          <span class="tracking-tight">파일명</span>
        </h1>
      {/if}
      <p
        class="mt-4 text-base"
        class:text-gray-500={!filesAreDraggedOver}
        class:text-gray-600={filesAreDraggedOver}
        class:dark:text-gray-400={!filesAreDraggedOver}
        class:dark:text-gray-300={filesAreDraggedOver}
      >
        {#if !filesAreDraggedOver}
          <input
            class="hidden"
            type="file"
            multiple
            bind:this={fileInput}
            on:change={handleFileChange}
          />
          <button
            type="button"
            class="text-indigo-600 hover:text-indigo-700 dark:text-indigo-400 dark:hover:text-indigo-300"
            on:click={() => fileInput?.click()}
          >
            파일을 선택하거나
          </button>
          이곳에 끌어다 놓으세요.
        {:else}
          끌어온 폴더와 파일을 이곳에 내려놓으세요.
        {/if}
      </p>
    </div>
  </main>
  {#if !filesAreDraggedOver}
    <footer
      transition:slide
      class="mx-auto w-full max-w-7xl flex-shrink-0 px-4 sm:px-6 lg:px-8"
    >
      <nav
        class="flex justify-center space-x-4 text-sm text-gray-500 dark:text-gray-400"
      >
        <a
          href="{VITE_GITHUB_URL}#readme"
          class="hover:text-gray-600 dark:hover:text-gray-300"
        >
          도구 소개
        </a>
        <span
          class="inline-block border-l border-gray-300"
          aria-hidden="true"
        />
        <a
          target="_blank"
          rel="noopener"
          href="{VITE_GITHUB_URL}/discussions/1"
          class="hover:text-gray-600 dark:hover:text-gray-300"
        >
          문의 제안
        </a>
        <span
          class="inline-block border-l border-gray-300"
          aria-hidden="true"
        />
        {#if !badgeIsLoaded}
          <Badge />
        {/if}
        <a
          target="_blank"
          rel="noopener"
          href="https://github.com/hyunbinseo/mac-filename-kr"
          class:hidden={!badgeIsLoaded}
        >
          <img
            width="82"
            height="20"
            alt="GitHub Repo stars"
            on:load={() => (badgeIsLoaded = true)}
            src="https://img.shields.io/github/stars/hyunbinseo/mac-filename-kr?style=social"
          />
        </a>
      </nav>
    </footer>
  {/if}
</div>

{#if filesAreDraggedOver}
  <div
    class="fixed inset-0"
    on:dragover|preventDefault|stopPropagation
    on:dragleave|preventDefault|stopPropagation={handleDragLeave}
    on:drop|preventDefault|stopPropagation={handleDrop}
  />
{/if}
