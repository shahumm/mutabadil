<script>
  import dataTree from "$lib/data.json";

  function filterData(item) {
    if (typeof item === "object" && item !== null) {
      if (Array.isArray(item)) {
        return item.map((element) => filterData(element)).filter(Boolean);
      } else {
        if (item.hasOwnProperty("brand")) {
          return {
            brand: item.brand,
            company: item.company || "",
            image: item.image || "",
          };
        }

        const result = Object.fromEntries(
          Object.entries(item).map(([key, value]) => [key, filterData(value)])
        );

        return Object.fromEntries(
          Object.entries(result).filter(([_, value]) => value !== false)
        );
      }
    } else {
      return false;
    }
  }

  let searchQuery = "";
  let filteredData = filterData(dataTree[0]);

  $: if (searchQuery) {
    searchData();
  } else {
    filteredData = filterData(dataTree[0]);
  }

  function searchData() {
    filteredData = filterData(dataTree[0]);

    if (searchQuery) {
      const searchQueryLower = searchQuery.toLowerCase();
      let newFilteredData = {};

      Object.entries(filteredData).forEach(([key, value]) => {
        let isHeadingMatched = key.toLowerCase().includes(searchQueryLower);
        let subheadingMatches = {};

        if (typeof value === "object" && value !== null) {
          Object.entries(value).forEach(([subKey, subValue]) => {
            let isSubheadingMatched = subKey
              .toLowerCase()
              .includes(searchQueryLower);
            let titleMatches = {};

            if (typeof subValue === "object" && subValue !== null) {
              Object.entries(subValue).forEach(([titleKey, titleValue]) => {
                let isTitleMatched = titleKey
                  .toLowerCase()
                  .includes(searchQueryLower);

                let brandMatches = [];
                if (Array.isArray(titleValue)) {
                  titleValue = titleValue.filter((brand) => {
                    let match = brand.brand
                      .toLowerCase()
                      .includes(searchQueryLower);
                    if (match) brandMatches.push(brand);
                    return !match;
                  });
                }

                if (brandMatches.length > 0) {
                  titleMatches[titleKey] = [...brandMatches, ...titleValue];
                } else if (
                  isTitleMatched ||
                  isSubheadingMatched ||
                  isHeadingMatched
                ) {
                  titleMatches[titleKey] = titleValue;
                }
              });
            }

            if (
              isSubheadingMatched ||
              isHeadingMatched ||
              Object.keys(titleMatches).length > 0
            ) {
              subheadingMatches[subKey] =
                isSubheadingMatched || isHeadingMatched
                  ? subValue
                  : titleMatches;
            }
          });
        }

        if (isHeadingMatched || Object.keys(subheadingMatches).length > 0) {
          newFilteredData[key] = isHeadingMatched ? value : subheadingMatches;
        }
      });

      filteredData = newFilteredData;
    }
  }
</script>

<body>
  <main>
    <div class="searchSectionContainer">
      <section class="search-section">
        <div class="search-container">
          <h2>Boycott is the least we can do.</h2>
          <button class="action-button">How every effort counts ></button>
        </div>
      </section>
    </div>

    <section class="content-section">
      <div class="searchBar">
        <input
          type="text"
          id="searchInput"
          bind:value={searchQuery}
          placeholder="Find alternatives..."
        />
      </div>
      <div class="content-container">
        {#if filteredData && Object.keys(filteredData).length > 0}
          {#each Object.entries(filteredData) as [heading, subheadings]}
            <div>
              <h3>{heading}</h3>
              {#each Object.entries(subheadings) as [subheading, titles]}
                <div>
                  <h4>{subheading}</h4>
                  {#each Object.entries(titles) as [title, items]}
                    <div>
                      <h5>> {title}</h5>
                      <div class="cards-container">
                        {#each items as item, index}
                          <div class="card">
                            <img
                              class="product-image"
                              src={item.image}
                              alt="{item.brand} product"
                            />
                            <p class="brand-name">{item.brand}</p>
                            <p class="company-name">{item.company}</p>
                          </div>
                        {/each}
                      </div>
                    </div>
                  {/each}
                </div>
              {/each}
            </div>
          {/each}
        {:else}
          <p>No matching items found.</p>
        {/if}
      </div>
    </section>
  </main>
</body>

<style>
  .action-button {
    padding: 15px 20px;

    border: none;
    border-radius: 12px;
    background-color: #333;
    color: white;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s;
  }

  .action-button:hover {
    background-color: #555;
  }

  h3::first-letter,
  h4::first-letter,
  h5::first-letter {
    text-transform: capitalize;
  }

  .content-container h3 {
    font-size: 28px;
    font-weight: 600;
    padding-bottom: 5px;
  }

  .content-container h4 {
    font-size: 21px;
    font-weight: 500;
    padding-bottom: 5px;
  }

  .content-container h5 {
    font-size: 16px;
    font-weight: 500;
    padding-bottom: 20px;
  }

  body {
    margin: 0;
    padding: 0;
    display: flex;
    flex-direction: column;
    height: 100vh;
    background-color: #f0f0f0;
  }

  .searchSectionContainer {
    width: 100%;
    display: grid;
    place-items: center;
  }

  .search-section {
    display: flex;
    align-items: center;
    flex-direction: flex-start;
    height: 30vh;
    width: 63%;
  }

  .search-container h2 {
    padding: 20px 0px;
    font-weight: 600;
    font-size: 30px;
  }

  .content-section {
    overflow-y: auto;
    display: grid;
    place-items: center;
  }

  .content-section .searchBar {
    width: 500px;
    padding: 50px 0px;
    z-index: 10;
  }

  .search-container {
    width: 100%;
    max-width: 100%;
    padding: 20px;
    z-index: 10;
  }

  .content-container {
    width: 80%;
    max-width: 1200px;
    margin: auto;
    padding: 20px;
  }

  #searchInput {
    width: 100%;
    padding: 15px;
    border: 1.5px solid white;
    border-radius: 15px;
    color: white;
    background-color: transparent;
  }

  #searchInput::placeholder {
    color: rgb(218, 218, 218);
    font-weight: 300;
    font-size: 17px;
  }

  .cards-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 40px;
  }

  .card {
    background-color: rgb(57, 57, 57);
    border-radius: 20px;
    aspect-ratio: 1 / 1;
    overflow: hidden;

    display: flex;
    flex-direction: column;
    align-items: flex-start;
    justify-content: flex-end;
    position: relative;

    padding: 10px;
    opacity: 0.9;
    z-index: 10;
  }

  .card:last-child {
    margin-bottom: 70px;
  }

  .product-image {
    width: 70%;
    margin: 0 auto;
    align-self: center;
    max-height: 50%;
    object-fit: contain;
    position: absolute;
    top: 30px;
  }
  .card:before {
    content: "";
    width: 100%;
    padding-top: 100%;
    display: block;
  }

  .brand-name,
  .company-name {
    align-self: flex-start;
    margin-left: 10px;
  }

  .brand-name {
    color: white;
  }

  .company-name {
    color: rgb(193, 193, 193);
    margin-bottom: 5px;
    font-size: 14px;
  }

  @media (max-width: 1200px) {
    .cards-container {
      grid-template-columns: repeat(3, 1fr);
    }
  }

  @media (max-width: 900px) {
    .cards-container {
      grid-template-columns: repeat(2, 1fr);
    }
  }

  @media (max-width: 600px) {
    .cards-container {
      grid-template-columns: 1fr;
    }
  }
</style>
