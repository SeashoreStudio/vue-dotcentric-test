# dotcentric-basket-test

This is a repo for the dotcentric basket test

## Known issues that I ran out of time for
* Removing all items from the cart I didn't create an empty cart message
* Updating the json file with the updated values. I had this in but being rusty on vue I couldn't work out the bug into why it wasn't updating the file. It was fetching the correct product but returning a 404 when trying to do a delete/put
* A decimal place issue when increasing quantity
* Add message when coming to maximum quantity of a product in the basket

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Run backend server

```sh
npm run backend
```

### Compile and Minify for Production

```sh
npm run build
```
