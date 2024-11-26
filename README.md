  # Integrate Build Your Own

  ## Javascript functions

  Build Your Own should be integrated in an iframe and you have access to the article list for the design via Javascript.

  ### This is how it works in short.

  BYO sends an event, `byoLoaded`, to its parent window when all necessary assets are loaded.

  You can choose to activate a cart button in BYO by sending an `enableCart` event to BYO. When the cart is clicked it will post a list of all articles in the design to its parent window.

  Every time the article list is changed BYO posts a message `updateDesign` with the full list of articles.

  ### Wait for BYO to load

  BYO will post a message when ready to activate the cart function. This will happen before the full page is loaded.

  ```javascript
  'event': 'byoLoaded'
  ```

  This is an example how to listen for this:

  ```javascript
  window.addEventListener('message', handleLoad, true);
  ```

  ### Activate the cart function in BYO

  Post `enableCart` to the iframe via `window.postMessage`. BYO only accepts this call after the `byoLoaded` event is sent.

  Sample code:

  ```javascript
  document.querySelector('iframe').contentWindow.postMessage({event:"enableCart",data:{}}, "*");
  ```

  ### Listen for `updateCart` or `updateDesign` events and handle the article list according to your needs.

  `updateDesign` is sent every time the list is changed and doesn't need to be activated.

  `updateCart` is sent when the cart button in BYO is clicked.

  Sample code:

  ```javascript
  window.addEventListener('message', handleArticleList, true);
  ```

  Response from the event `cartUpdate`:

  ```javascript
  data.data = {
    designid : "123456",    // unique design id for the current shelf. Empty string if the design has not been saved
    name     : "design",    // the name of the design set by the user. Defaults to "design"
    items    :  [           // array with all the articles in the design
                  ean         : "7350038270467", // eancode for the item
                  article     : "SF20030-13-2", // article number for the item
                  quantity    : 1                 
    ]
  }
  ```

  ### All put together. All the code you need:

  ```javascript
  window.addEventListener('message', messageHandler, true); // use this if you are going to enable the cart button

  function messageHandler(event){
      if(event.data.event == "byoLoaded") {
          handleLoad(event)
      }
      if(event.data.event == "cartUpdate") {
          addToCart(event)
      }
      if(event.data.event == "designUpdate") {
          handleCart(event)
      }
  }

  function handleLoad(event) {
      // enable the button in BYO
      document.querySelector('iframe').contentWindow.postMessage({event:"enableCart",data:{}}, "*");
  }

  function addToCart(event) {
      let designid = event.data.data.designid;
      let designname = event.data.data.name;
      let cart = event.data.data.items;
      // add your code for handling user did an add to cart click
  }

  function handleCart(event) {
      let designid = event.data.data.designid;
      let designname = event.data.data.name;
      let cart = event.data.data.items;
      // add your code for handling article list changed in BYO
  }
  ```

  ## Embed Build Your Own

  Build Your Own can be embedded in an iframe.

  The URL for Build Your Own is `https://build-your-own.stringfurniture.com`

  For the best result the iframe should be set with `scrolling="no"` and a minimum width of 960px and minimum height of 730px. We recommend using the full width of the browser.

  Build Your Own takes the following arguments:

  | Argument        | Description                                                                                                             | Sample Value                                      |
  | --------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------- |
  | lan             | Language used in Build Your Own user interface.                                                                         | See side for list of available languages.         |
  | countrycode     | [2 letter ISO code](https://www.nationsonline.org/oneworld/country_code_list.htm) for country. Sets currency and units. | SE                                                |
  | guid            | Store GUID. New customers will be members of the store.                                                                 | sit_1234567890abcdef                              |
  | designtemplate* | Design id for a standard design. Build Your Own will be loaded with this design.                                        | Contact string for a list of design template ids. |

  \* = optional

  ### Languages

  - "sv" = Swedish
  - "en" = English
  - "ja" = Japanese
  - "de" = German
  - "es" = Spanish
  - "fr" = French