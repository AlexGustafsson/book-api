# Book API for Node JS
### A fully async API written in ES6 for fetching books from various sources
***
![npm badge](https://img.shields.io/npm/v/book-api.svg)

### Setting up

##### Installing

```
npm install book-api
```

##### Quickstart

###### CLI

```Bash
# Install
> npm install -g book-api
# Use
> book-api --help

book-api <query>

Search for the query

Options:
  --help                Show help                                      [boolean]
  --version             Show version number                            [boolean]
  --fetch-all           Fetch all search results                       [boolean]
  --search-all-sources  Search all sources                             [boolean]
  --search-results, -n  Number of search results to include. Is not guaranteed
                        to be honoured                                  [number]
  --parallel-queries    The number of queries to process simultaneously
                                                                    [default: 3]
  --minimum-delay       The minimum number of milliseconds to wait between
                        requests                                  [default: 100]
  --maximum-delay       The maximum number of milliseconds to wait between
                        requests                                 [default: 5000]
  --output, -o          Path to the output file
  --debug, -d           Run in debugging mode                          [boolean]

```

###### Library

```JavaScript
const {Akademibokhandeln, Adlibris} = require('book-api');

const source = new Adlibris();

// Search for books
source.search('Test Driven Development')
.then(books => {
  source.fetch(books[0]).then(book => {
    console.log(JSON.stringify(book, null, 2));
  });
});
```

```
{
  "marketPrices": [
    {
      "value": 443,
      "currency": "sek",
      "source": "Adlibris"
    }
  ],
  "isbn": "9780321146533",
  "cover": {
    "url": "https://s2.adlibris.com/images/824962/[...]"
  },
  "images": [
    {
      "url": "https://s2.adlibris.com/images/824962/[...]"
    }
  ],
  "title": "Test Driven Development",
  "authors": [
    "Kent Beck"
  ],
  "sources": [
    {
      "url": "https://www.adlibris.com/se/bok/[...]",
      "name": "Adlibris"
    }
  ],
  "formfactor": "paperback",
  "description": "Quite simply, test-driven development is meant to eliminate fear in [...]",
  "categories": [
    "Datorer & IT",
    "Affärstillämpningar",
    "Programmering",
    "Programvaruteknik"
  ],
  "published": "2002-11-01T00:00:00.000Z",
  "publisher": "Addison-Wesley Educational Publishers Inc",
  "pages": 240,
  "language": "en",
  "weight": "418 gram"
}
```

### Documentation

The documentation is currently a bit sparse. For more information, refer to the source, tests and issues on GitHub.

#### Methods

The module exposes the following

```JavaScript
const {
  Akademibokhandeln, // Source
  Adlibris, // Source
  sources, // Enumerable array of the sources above
  search, // Convenience method for searching for queries
  searchAll // Convenience method for searching for multiple different queries
} = require('book-api');
```

#### Convenience methods

```JavaScript
/**
* Search asynchronously for a query using sensible source priority.
* @param {String} query - The query to search for.
* @param {Object} options - Optional options.
* @param {Boolean} options.fetchAll - Fetch all search results. Defaults to false.
* @param {Boolean} options.searchAllSources - Search all sources. Defaults to false.
* @param {Number} options.searchResults - Number of search results to include. Is not guaranteed to be honoured. Defaults to 0 (predefined).
* @returns {Array} Array of books.
*/
async function search(query, options = {}) {
  ...
}

/**
* Search asynchronously for a query using sensible source priority.
* @param {Array} queries - Array of queries to search for.
* @param {Object} options - Optional options.
* @param {Boolean} options.fetchAll - Fetch all search results. Defaults to false.
* @param {Boolean} options.searchAllSources - Search all sources. Defaults to false.
* @param {Number} options.searchResults - Number of search results to include. Is not guaranteed to be honoured. Defaults to 0 (predefined).
* @param {Number} options.parallelQueries - The number of queries to process simultaneously. requests.
* @returns {Array} Array of books.
*/
async function * searchAll(queries, options = {}) {
  ...
}

// Example usage
for await (const batch of searchAll(['test', 'queries'])) {
  console.log(`Got batch of ${batch.length}`);
  console.log(batch);
  console.log('Waiting between batches');
  await wait(minimumDelay, maximumDelay);
}
```

#### Book schema

Each book generated by this API follows the format as explained in `src/book.js` as shown below.

```JavaScript
class Book {
  constructor() {
    // The cost of the book - always available
    // Each item is a price:
    // price.value: null // The amount of the currency
    // price.currency: null // The currency of the price
    // price.source: null // The source's name
    this.marketPrices = [];

    // The International Standard Book Number (ISBN) - always available
    this.isbn = null;

    // Cover image - always available. As large as possible
    // cover.url : null // An url to the image
    this.cover = null;

    // Images - always available. All images found (including the cover itself)
    // image.url : null // An url to the image
    this.images = [];

    // Title of the book - always available
    this.title = null;

    // Array of authors of the book - always available, but can be empty
    this.authors = [];

    // Sources where the book is found - always available
    // Each item is a source:
    // source.url : null // URL to the book on its respective site
    // source.name : null // The source's name
    this.sources = [];

    // Formfactor of the book, such as paperback - always available but could be null
    this.formfactor = null;

    // Description of the book - available in part for Adlibris, in full by fetching.
    // Available after fetching for Akademibokhandeln
    this.description = null;

    // Category of the book - available after fetching
    this.categories = [];

    // The date (or year) the book was published - always available for Adlibris,
    // available after fetching for Akademibokhandeln
    this.published = null;

    // The publisher of the book - available after fetching
    this.publisher = null;

    // The number of pages in the book - available after fetching
    this.pages = null;

    // The language of the book (ISO 639-1) - always available for Adlibris,
    // available after fetching for Akademibokhandeln - both sources can however be 'unknown'
    this.language = null;

    // Weight of the book - available after fetching for Adlibris,
    // not available for Akademibokhandeln
    this.weight = null;
  }
}
```

### Contributing

Any contribution is welcome. If you're not able to code it yourself, perhaps someone else is - so post an issue if there's anything on your mind.

###### Development

Clone the repository:
```
git clone https://github.com/AlexGustafsson/book-api.git && cd book-api
```

Set up for development:
```
npm install
```

Follow the conventions enforced:
```
npm run lint
npm run test
npm run coverage
npm run check-duplicate-code
```

### Disclaimer

_Although the project is very capable, it is not built with production in mind. Therefore there might be complications when trying to use the API for large-scale projects meant for the public. The API was created to easily fetch information about books programmatically and as such it might not promote best practices nor be performant. This project is not in any way affiliated with Akademibokhandeln or Adlibris._
