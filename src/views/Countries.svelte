<script>
  import { afterUpdate, onMount } from 'svelte'
  export let title;
  title="Countries"
  let selected_country
  let countries = []
  let flag = []
  let loader = true
  let oldNewMap = {
    'UK': 'United Kingdom',
    'UAE': 'United Arab Emirates',
    'Taiwan': 'Taiwan*',
    'S. Korea': 'Korea, South',
    'North Macedonia': 'Macedonia',
    'Libyan Arab Jamahiriya': 'Libya',
    'Côte d\'Ivoire': 'Cote d\'Ivoire',
    'USA': 'US'
  }


  onMount(async () => {
    flag = await window.api('https://pomber.github.io/covid19/countries.json')
    flag = flag.data
    let global = await window.api('https://corona.lmao.ninja/v2/countries')
    countries = global.data
    for (let count in countries) {
      if (Object.keys(oldNewMap).includes(countries[count].country)) {
        countries[count].country = oldNewMap[countries[count].country]
      }
    }
    countries = countries
    loader = false
  })

  afterUpdate(() => {
    if (countries.length !== 0) {
      if (!window.$.fn.DataTable.isDataTable('#countries')) {
        window.$('#countries').DataTable({
          pageLength: 50,
          aaSorting: [[2, 'desc']]
        })
        window.$('.dataTables_length').addClass('bs-select')
        window.$('.dataTables_info').addClass('table-responsive')
      }
    }
  })

  function country_link (e) {
    let link = document.getElementById('cl')
    let count = e.target.getAttribute('country')
    link.href = '/country/' + count
    link.click()
  }

</script>

{#if loader}
  <div class="d-flex justify-content-center mt-5">
    <div class="spinner-border text-secondary" role="status" style="width: 5rem; height: 5rem;">
      <span class="sr-only">Loading...</span>
    </div>
  </div>
{:else}
  <a href={selected_country} class="d-none" id="cl"></a>
  <div class="container-fluid" >
    {#if countries !== 0}
      <h4 class="h4 text-center text-muted mt-5">Click on country to see more detail</h4>
      <table class="table table-responsive" id="countries">
        <thead class="mdb-color darken-3 white-text">
        <tr>
          <th scope="col">Flag</th>
          <th scope="col">Country</th>
          <th scope="col">Confirmed</th>
          <th scope="col">New Cases</th>
          <th scope="col">Deaths</th>
          <th scope="col">New Deaths</th>
          <th scope="col">Active</th>
          <th scope="col">Recovered</th>
          <th scope="col">Cases/1M pop</th>
          <th scope="col">Deaths/1M pop</th>
          <th scope="col">Critical</th>
          <th scope="col">Tests</th>
          <th scope="col">Tests/1M pop</th>
        </tr>
        </thead>
        <tbody>
        {#each countries as c}
          <tr on:click={country_link}>
            <td country={c.country}>{#if flag[c.country]}{flag[c.country]["flag"]}{:else}
              <img country={c.country} class="img-thumbnail" width="25" height="25" src="{c.countryInfo.flag}">{/if}</td>
            <th country={c.country} scope="row">{c.country}</th>
            <td country={c.country}>{c.cases}</td>
            <td country={c.country} class="text-danger">{#if c.todayCases}+{c.todayCases}{/if}</td>
            <td country={c.country}>{c.deaths }</td>
            <td country={c.country} class="text-danger">{#if c.todayDeaths}+{c.todayDeaths }{/if}</td>
            <td country={c.country}>{c.active}</td>
            <td country={c.country}>{c.recovered}</td>
            <td country={c.country}>{c.casesPerOneMillion}</td>
            <td country={c.country}>{c.deathsPerOneMillion}</td>
            <td country={c.country}>{c.critical}</td>
            <td country={c.country}>{c.tests}</td>
            <td country={c.country}>{c.testsPerOneMillion}</td>
          </tr>
        {/each}
        </tbody>
      </table>
    {/if}
  </div>
{/if}
