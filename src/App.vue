<script setup lang="ts">
import { onMounted } from 'vue'

import products from './assets/products.json'

/*
*********** START PART 2 ************
*/
interface Post {
  id: number
  title: string
  const: string | null
}

type NewPost = Omit<Post, 'id'> & { id?: number }

// const post: NewPost = { id: 3, title: 'test', const: 'nice' }

// console.log(post)

/*
*********** END PART 2 ************
*/


/*
*********** START PART FUNCTION 1 ************
*/
const watch = (
  obj: { [key: string | symbol]: any },
  getCallback: (key: string | symbol, value: any) => void | undefined,
  setCallback: (key: string | symbol, value: any) => void | undefined
) => {
  const proxy = new Proxy(obj, {
      get(obj, prop) {
        // No watch called, property doesn't exist
        if (!Object.hasOwn(obj, prop)) return

        getCallback(prop, obj[prop])

        return obj[prop]
      },
      set(obj, prop, value) {
        obj[prop] = value

        setCallback(prop, value)

        return true
      }
  })

  return proxy
}
/*
*********** END PART 1 FUNCTION ************
*/

onMounted(() => {
  /*
  *********** START PART 1 CALLING FUNCTION ************
  */
  const obj = {}

  const watchedObj = watch(
    obj,
    (key) => console.log('property accessed: ', key),
    (key, value) => console.log('property modified: ', key, value)
  )

  watchedObj.foo
  watchedObj.foo = true
  watchedObj.foo
  /*
  *********** END PART 1 CALLING FUNCTION ************
  */

  /*
  *********** START PART 3 ************
  */
  const outOfStockNotOnSaleUnder20: string[] = []

  let mostCommonlyUsedCategory = ''
  const categoriesArr: string[] = []

  let averagePriceSaleItems = 0
  let averagePriceSaleItemsString = ''
  let priceTotal = 0
  let productsLength = products.length

  let womensProductsOutOfStockByColor: { [key: string | symbol]: any } = {}

  // Gets the most frequent item in array
  const mostFrequentItemArray = ((array: string[]) => {
    return array.sort((a, b) =>
      array.filter(c => c === a).length - array.filter(c => c === b).length
    ).pop() || ''
  })

  products.forEach(({ in_stock, on_sale, price, name, categories, gender, color }) => {
    // Get rid of dollar sign and comma
    const priceNormalized = parseFloat(price.replace(/,/g, '').substring(1))

    // Not in stock, not on sale and price is less than 20
    if (!in_stock && !on_sale && priceNormalized < 20) {
      outOfStockNotOnSaleUnder20.push(name)
    }

    categoriesArr.push(...categories)

    priceTotal += priceNormalized

    // Female and out of stock
    if (gender === 'female' && !in_stock) {
      if (!Object.hasOwn(womensProductsOutOfStockByColor, color)) {
        womensProductsOutOfStockByColor[color] = 0
      } 
      
      womensProductsOutOfStockByColor[color]++
    }
  })

  mostCommonlyUsedCategory = mostFrequentItemArray(categoriesArr)

  averagePriceSaleItems = priceTotal / productsLength

  // Add dollar string formatting
  averagePriceSaleItemsString = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(averagePriceSaleItems)

  console.log(`1. Which products are out of stock, not on sale, and under $20? ${outOfStockNotOnSaleUnder20.join(', ')}`)
  console.log(`2. What is the most commonly used category? ${mostCommonlyUsedCategory}`)
  console.log(`3. What is the average price of sale items? ${averagePriceSaleItemsString}`)
  console.log(`4. How many women's products are out of stock, broken down by color? ${JSON.stringify(womensProductsOutOfStockByColor)}`)
  /*
  *********** End PART 3 ************
  */
})
</script>
