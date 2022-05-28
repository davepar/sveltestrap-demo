<script context="module" lang="ts">
  /** @type {import('./index').Load} */
  export async function load({ fetch }) {
    const response = await fetch('./customers');
    return {
      status: response.status,
      props: {
        data: response.ok && (await response.json()),
      },
    };
  }
</script>

<script lang="ts">
  import { fade } from 'svelte/transition';
  import type { Customer } from './customers';
  import { Frequency, Region, FREQUENCY_OPTIONS, REGION_OPTIONS } from './customers';
  import {
    Dialog,
    DialogOverlay,
    DialogTitle,
    DialogDescription,
  } from "@rgossiaux/svelte-headlessui";

  export let data: {
    customers: Customer[];
  };

  let dialogOpen = false;

  function openDialog() {
    dialogOpen = true;
  }

  function closeDialog() {
    dialogOpen = false;
  }

  function saveCustomer() {
    data.customers.push(newCustomer);
    data = { ...data };
    dialogOpen = false;
  }

  const newCustomer = {
    name: '',
    region: null,
    frequency: null,
    newsletter: false,
  };
</script>

<svelte:head>
  <title>Svelte with Bootstrap and Svelte Headless UI</title>
  <meta name="description" content="A demo of the Bootstrap CSS library" />
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.0/dist/css/bootstrap.min.css"
  />
  <!-- Example of alternate Bootstrap theme:
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootswatch@4.5.2/dist/flatly/bootstrap.min.css"> -->
</svelte:head>

<div class="container">
  <div>
    <h1>Sample page for Svelte with Bootstrap CSS and Svelte Headless UI</h1>
    <p>Visit <a href="https://kit.svelte.dev">web.site.here</a> for more info</p>
  </div>
  <div class="mb-3">
    <button type="button" class="btn btn-primary" on:click={openDialog}>Add Customer</button>
  </div>
  <div>
    <table class="table table-bordered" style="width: inherit">
      <thead>
        <tr>
          <th scope="col">Name</th>
          <th scope="col">Region</th>
          <th scope="col">Frequency</th>
          <th scope="col">Newsletter</th>
        </tr>
      </thead>
      <tbody>
        {#each data.customers as customer}
          <tr>
            <td>{customer.name}</td>
            <td>{Region[customer.region]}</td>
            <td>{Frequency[customer.frequency]}</td>
            <td class="text-center">{#if customer.newsletter}&#10003;{/if}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>

<Dialog open={dialogOpen} on:close={closeDialog}>
  <div class="modal show" style="display: block">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <DialogTitle class="modal-title">Add customer</DialogTitle>
          <button type="button" class="btn-close" aria-label="Close" on:click={closeDialog}></button>
        </div>
        <div class="modal-body">

          <div class="mb-3">
            <label for="name" class="form-label">Name</label>
            <input type="text" class="form-control" id="name" placeholder="Full name" bind:value={newCustomer.name}>
          </div>

          <div class="mb-3">
            <label for="region" class="form-label">Region</label>
            <select class="form-select" id="region" bind:value={newCustomer.region}>
              {#each REGION_OPTIONS as { label, value }}
                <option {value}>{label}</option>
              {/each}
            </select>
          </div>

          <div class="mb-3">
            <p class="form-label">Frequency</p>
            {#each FREQUENCY_OPTIONS as { label, value }}
              <div class="form-check">
                <input class="form-check-input" type="radio" name="frequency" id={label} bind:group={newCustomer.frequency} {value}>
                <label class="form-check-label" for={label}>
                  {label}
                </label>
              </div>
            {/each}
          </div>

          <div class="form-check">
            <input class="form-check-input" type="checkbox" id="newsletter" bind:checked={newCustomer.newsletter} />
              <label class="form-check-label" for="newsletter">
                Newsletter
              </label>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" on:click={closeDialog}>Cancel</button>
          <button type="button" class="btn btn-primary" on:click={saveCustomer}>Save changes</button>
        </div>
      </div>
    </div>
  </div>
  <DialogOverlay class="modal-backdrop show" />
</Dialog>
