<script>
  import Map from './Map.svelte';
  import HouseRender from './HouseRender.svelte';

  const API_URL = "https://mongo-flex.onrender.com/"

  const randomPrice = () => Math.floor(Math.random()*1000+100)*100
  const dollarize = (n) => Math.floor(n/1000) + "," + (""+(1000+n%1000)).substr(1,3)
  let numHouses = 0
  let house = {
    name: "Villa",
    price: randomPrice(),
    coordinates: [40.5, 73.70],
    gridPieces: []
  }
  let similarHouses;

  let housePromise, numHousesPromise, similarHousesPromise;


  const init = () => {
    numHousesPromise = updateNumber();
    housePromise = randomHouse();
    similarHousesPromise = getSimilarHouses();
  }

  const getSimilarHouses = async () => {
    await fetch(API_URL+"similar-houses", {
      method: "POST",
      body: JSON.stringify(house),
      headers: {
        'Content-type': 'application/json; charset=UTF-8',
      }
    }).then(response => response.json()).then(response => similarHouses = response).finally(() => {
      
      similarHouses = similarHouses

    })

    return similarHouses

  }

  const selectHouse = (h) => {
    house = h
    similarHousesPromise = getSimilarHouses();

    console.log(h)
  }

  const randomHouse = async () => {
    await fetch(API_URL+"random-house")
		.then((response) => response.json())
		.then((h) => {
			house = h;
      house.price = randomPrice();
		})
    similarHousesPromise = getSimilarHouses()

    house = house

    return house
  }

  const updateNumber = async () => {
    await fetch(API_URL+"num-houses").then(response => response.text().then(res => numHouses = parseInt(res))).finally(()=>{
      numHouses = numHouses
    })

    return numHouses

  }

  const addMoreHouses = async () => {
    await fetch(API_URL+"populate")
    return updateNumber()
  }

  let loadPromise = init()
  init()
</script>

<main>
  <h1>Mongo</h1>
  <h3> Houses in Database: 
    {#await numHousesPromise}
      {numHouses}
    {:then nHouses} 
      {numHouses}
    {/await}
  
  </h3>
  <div>
    {#key house}
    <Map house={house.coordinates} />
    <HouseRender house={house} size={500}/>
    {/key}
    <h4> Name: {house.name} </h4>
    <h6> Price: ${dollarize(house.price)}</h6>
  </div>

  <div class="card">
    <button on:click={() => housePromise = randomHouse()}>
      Random House
    </button>  
    <button on:click={addMoreHouses}>
      Add More Houses
    </button>    
  </div>

  <h2> Similar Houses </h2>
  <div>
    {#key similarHouses}
    {#if similarHouses != null}
      <HouseRender house={similarHouses[0]} on:click={() => selectHouse(similarHouses[0])} />
      <HouseRender house={similarHouses[1]} on:click={() => selectHouse(similarHouses[1])}/>
      <HouseRender house={similarHouses[2]} on:click={() => selectHouse(similarHouses[2])}/>
    {/if}
    {/key}
  </div>

  
</main>