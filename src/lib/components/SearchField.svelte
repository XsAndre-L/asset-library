<script lang="ts">
  let {
    value = $bindable(""),
    placeholder = "",
    onkeydown,
  }: {
    value?: string;
    placeholder?: string;
    onkeydown?: (event: KeyboardEvent) => void;
  } = $props();
</script>

<div class="search-field">
  <input type="search" bind:value {placeholder} {onkeydown} />
</div>

<style>
  .search-field {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 0.25rem;
    border-radius: 10px;
    border: 1px solid var(--borderHoverColor);
    padding: 0.5rem 0.75rem;
    background: var(--surface-inset);
  }

  input {
    border: none;
    outline: none;
    background: transparent;
    color: var(--app-text);
    padding: 0;
    min-width: 7rem;
    flex: 1;
    font: inherit;
    line-height: 1.2;
  }

  input::-webkit-search-decoration,
  input::-webkit-search-cancel-button,
  input::-webkit-search-results-button,
  input::-webkit-search-results-decoration {
    -webkit-appearance: none;
  }
</style>
