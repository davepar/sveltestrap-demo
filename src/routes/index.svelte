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
  import { Button, Table, Modal, ModalFooter, Form, FormGroup, Label, Input } from 'sveltestrap';
  import type { Customer } from './customers';
  import { Frequency, Region, FREQUENCY_OPTIONS, REGION_OPTIONS } from './customers';

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

  function toggle() {
    dialogOpen = !dialogOpen;
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
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.0/dist/css/bootstrap.min.css"
  />
  <!-- Example of alternate Bootstrap theme:
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootswatch@4.5.2/dist/flatly/bootstrap.min.css"> -->
</svelte:head>

<div class="container">
  <div>
    <h1>Sample page for Svelte with Sveltestrap</h1>
    <p>Visit <a href="https://kit.svelte.dev">web.site.here</a> for more info</p>
  </div>
  <div class="mb-3">
    <!-- <button type="button" class="btn btn-primary">Add Customer</button> -->
    <Button color="primary" on:click={openDialog}>Add Customer</Button>
  </div>
  <div>
    <Table bordered style="width: inherit">
      <thead>
        <tr>
          <th>Name</th>
          <th>Region</th>
          <th>Frequency</th>
          <th>Newsletter</th>
        </tr>
      </thead>
      <tbody>
        {#each data.customers as customer}
          <tr>
            <td>{customer.name}</td>
            <td>{Region[customer.region]}</td>
            <td>{Frequency[customer.frequency]}</td>
            <td class="text-center">{@html customer.newsletter ? '&#10003;' : ''}</td>
          </tr>
        {/each}
      </tbody>
    </Table>
  </div>
</div>

<Modal isOpen={dialogOpen} {toggle} header="Add customer" body>
  <Form>
    <FormGroup>
      <Label for="name">Name</Label>
      <Input type="text" id="name" bind:value={newCustomer.name} placeholder="Full name" />
    </FormGroup>

    <FormGroup>
      <Label for="region">Region</Label>
      <Input type="select" bind:value={newCustomer.region} id="region">
        {#each REGION_OPTIONS as { label, value }}
          <option {value}>{label}</option>
        {/each}
      </Input>
    </FormGroup>

    <FormGroup>
      <Label>Frequency</Label>
      {#each FREQUENCY_OPTIONS as { label, value }}
        <Input type="radio" bind:group={newCustomer.frequency} {label} {value} />
      {/each}
    </FormGroup>

    <FormGroup>
      <Input type="checkbox" label="Newsletter" bind:checked={newCustomer.newsletter} />
    </FormGroup>
  </Form>

  <ModalFooter>
    <Button color="primary" on:click={saveCustomer}>Save changes</Button>
    <Button color="secondary" on:click={closeDialog}>Cancel</Button>
  </ModalFooter>
</Modal>
