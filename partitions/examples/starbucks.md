# Starbucks Store Location

## What you'll need {docsify-ignore}
* node/npm
* git
* AWS CLI
* Graphql Playground

## What we will accomplish {docsify-ignore}

* Deploy Partitions
* Seed geo fenced data
* Execute queries by proximity

After, this example you will have everything you need for various "Near me" functionalities.  Hotels near me, diners near me, bathrooms near me, etc.

## Embark {docsify-ignore}

Pull down the Partitions example branch in a directory of your choice.

```bash
git clone https://github.com/SkylarWapato/partitions-example.git
```

Navigate to the repo

```bash
cd partitions-example
```

Checkout the store locator branch

```bash
git checkout starbuck-store-location
```

Install dependencies

```bash
npm install
```

Deploy Partitions.

```bash
npm run deploy
```

Seed sample data.  This might take a few minutes.  There are nearly 7000 stores being seeded.  Also, if a request should fail, visit the ```seed.js``` file.  There will be instructions on how to pick up where you left off.

```bash
node seed
```

Once this command is finished you can begin to query geo fenced data.  Let's see what the Starbucks situation is in San Francsico.  Make the following query with a client of your choice.

<pre><code class="language-graphql">
{
  starbucksByLocation(latitude: 37.7749, longitude: -122.4194, proximity: 10){
    id
    storeId
    address
    distance
  }
}
</code></pre>

The proximity argument is measured in kilometers.  They pack their towns pretty tight in the Bay Area so we are getting a few results from neighboring cities.  Let's refine our search.

<pre><code class="language-graphql">
{
  starbucksByLocation(latitude: 37.7749, longitude: -122.4194, proximity: 1){
    id
    storeId
    address
    distance
  }
}
</code></pre>

There we go, we have results wholly in San Fran now.
Still, it is pretty cluttered.  Let's refine our request once more to only get results that I am willing to walk to.

<pre><code class="language-graphql">
{
  starbucksByLocation(latitude: 37.7749, longitude: -122.4194, proximity: 0.5){
    id
    storeId
    address
    distance
  }
}
</code></pre>

There we go!.

Just pass your whatever device your working on's location data and Partitions will take care of the rest.

Play with it, query your hometown or your favorite city.


| WARNING: The states are stored in alphabetical order and only go through Ohio.  Keep this in mind as you play with the queries ! |
| --- |

Running

```bash
npm run remove
```

Will tear down all AWS resources created by this example.