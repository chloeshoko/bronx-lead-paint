<script>
  import { base } from '$app/paths';
  import DatabaseHeader from '$lib/components/DatabaseHeader.svelte';
  import Dashboard from '$lib/components/Dashboard.svelte';
  import BigNumber from '$lib/components/BigNumber.svelte';
  import Card from '$lib/components/Card.svelte';
  import MethodologyBox from '$lib/components/MethodologyBox.svelte';
  import LocatorMap from '$lib/components/LocatorMap.svelte';

  let { data } = $props();
  let building = data.building;

  function formatDate(dateStr) {
    const [year, month, day] = dateStr.split('-').map(Number);
    return new Date(year, month - 1, day).toLocaleDateString('en-US', {
      month: 'long',
      day: 'numeric',
      year: 'numeric',
    });
  }
</script>

<DatabaseHeader
  kicker="Tainted Home Tracker"
  headline={building.address}
>

  {#snippet graphic()}
    <LocatorMap
      longitude={building.lng}
      latitude={building.lat}
      zoom={13}
      dot={true}
      width={200}
      credit=" "
    />
  {/snippet}
  
  <Dashboard>
    <BigNumber number={`#${building.rank}`} label="Rank" />
    <BigNumber number={building.violationCount} label="Open violations" />
  </Dashboard>
  <a class="back" href="{base}/">
     ← Back to ranking
  </a>
</DatabaseHeader>

<div class="container">
  <div class="card-grid">
    {#each building.violations as violation (violation.violationId)}
      <Card>
        <p class="card-date">{formatDate(violation.date)}</p>
        <p>{violation.specificDescription || 'No description available'}</p>
      </Card>
    {/each}
  </div>

  <MethodologyBox>
    <p>
      The data on this page comes from the Department of Housing Preservation and Development
      <a href="https://data.cityofnewyork.us/Housing-Development/Housing-Maintenance-Code-Violations/wvxf-dwi5" target="_blank">via New York City's open data portal</a>.
    </p>
    <p>
      The citations published by the city were filtered to include only lead paint violations that city inspectors listed as unresolved. The data was filtered to only citations linked to addresses in the Bronx and then aggregated by address. Only addresses with five or more open violations were included in the ranking. The data is current as of March 2026.
    </p>
    <p>The code that executed the analysis is available as open source on GitHub at <a href="https://github.com/palewire/nyc-hpd-bronx-lead-paint-violations" target="_blank">github.com/palewire/nyc-hpd-bronx-lead-paint-violations</a>.</p>

    <p>The map tiles are provided by OpenFreeMap and OpenStreetMap contributors.</p>
  </MethodologyBox>
</div>

<style type="scss">
  .container {
    max-width: var(--max-width-wide);
    margin: 0 auto;
    padding: 0 var(--spacing-md);
  }

  :global(.row) {
    margin-top: var(--spacing-sm) !important;
    margin-bottom: var(--spacing-sm) !important;
    justify-content: flex-start !important;
  }

  @media (max-width: 767px) {
    :global(.row) {
      flex-direction: column;
    }
  }

  :global(.row .big-number .number) {
    font-size: 2.25rem;
  }

  :global(.row .big-number .label) {
    font-size: var(--font-size-sm);
  }

  .card-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: var(--spacing-md);
    margin: var(--spacing-lg) 0;
  }

  :global(p.card-date) {
    font-size: var(--font-size-sm);
    font-weight: var(--font-weight-bold);
    color: var(--color-accent) !important;
    text-transform: uppercase;
    letter-spacing: 0.03em;
    margin-bottom: var(--spacing-xs) !important;
  }

  a.back {
    display: inline-block;
    font-size: var(--font-size-xs);
    color: var(--color-text);
    text-decoration: none;
    text-transform: uppercase;
    &:hover {
      text-decoration: underline;
    }
  }
</style>