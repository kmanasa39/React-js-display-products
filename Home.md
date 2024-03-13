
STEP 1. Creating Shipping Zone & Adding to Product:
One of the most important steps to capturing an order is determining the logistics of how you will ship your products. You need to answer questions like: Where will I ship? How much will I charge? Will it be a flat fee to place A etc...

For this example, we will have 3 shipping zones: United States, Mexico, Canada. We will be charging a flat rate (see below) for each zone. In order to set this up, you must navigate to the Shipping Tab within your dashboard and click Add Zone.

Now that you've added the shipping zones you wish to ship to, including price — no add these zones to your product. Each product can have different shipping zones, but for simplicity you shhould add all three zones to every product in the catalogue.

Add Zone to Product
Navigate to each individual product and click the enable zone button. This will enable a specific shipping option for the product. You can also add any extra shipping cost such as an amount for one order vs multiple orders.
This is an important step and must be completed before you can capture a checkout. As you will see further in this guide, the shipping option is needed for Chec to process and finalize an order. You also want to give customers a shipping price so they have a total amount due. In our checkout form, once the customer chooses their country – the shipping options available for that country will be presented.

Step 2. Add Checkout Button & Setup Route to Form
Next, add a checkout button to the cart modal where all your products are listed. Adding the button is pretty straight forward, but this button will have an onClick which will call a function with a few triggers - one of them is routing to your checkout form.

// *** [CartModal.js](http://cartmodal.js/) ***
<Button 
    floated='left' 
    size='big' 
    color='blue' 
    onClick={goToCheckout}
>
    Checkout
</Button>
// *** [CartModal.js](http://cartmodal.js/) ***
const goToCheckout = e => {
    [history.push](http://history.push/)(`/checkout/${[props.cart.id](http://props.cart.id/)}`)
    [localStorage.setItem](http://localstorage.setitem/)('cart-id', [props.cart.id](http://props.cart.id/))
    [props.setModalOpen](http://props.setmodalopen/)(false)
    [props.setCheckout](http://props.setcheckout/)(true)
}
React Router has a history object that you can use to 'push' the customer to the page of your choice. In our case, the checkout page. Next, add the cart_id to the URL. The cart_id is needed for important SDK helper function calls, so adding it the URL makes it easy to access in the next component.

Add the cart-id to local storage as a trigger for the private route. If there's no cart-id in local storage - you can't route to this page. [props.setModalOpen](http://props.setmodalopen/)(false) is the trigger to close the modal and [props.setCheckout](http://props.setcheckout/)(true) is the trigger to NOT show the cart icon in the Nav (You want to hide the icon during checkout).

Setting up your Route
As previously mentioned, this is not a deep dive into React or React Router, but here's an overview on setting up some routes. First you need to install the proper dependencies:

// This package provides the core routing functionality for React Router

npm install react-router 

    - or -

yarn add react-router
// DOM bindings for React Router.

npm install react-router-dom

    - or -

yarn add react-router-dom
In the [index.js](http://index.js/) file you need to import the BrowserRouter:

// *** [index.js](http://index.js/) ***
import { BrowserRouter as Router } from "react-router-dom";

[ReactDOM.render](http://reactdom.render/)(
    <Router>
        <App />
    </Router>
,[document.getElementById](http://document.getelementbyid/)('root'));
Here you are wrapping the <Router> component around the <App /> component so everything in your <App /> component can access any routes you setup. All the routes for your app will be setup in the App /> component. If you take a look in the [App.js](http://app.js/) you'll notice a Route import:

import { Route } from 'react-router-dom'
This component will be used to render the <CheckoutContainer> component based on a few properties ...

// *** [App.js](http://app.js/) ***
<PrivateRoute 
    component={CheckoutContainer}
    path={`/checkout/:id`} 
    setCheckout={setCheckout}
    setModalOpen={setModalOpen}
    setReceipt={setReceipt}
/>
*** Note ** <PrivateRoute /> (check [PrivateRoute.js](http://privateroute.js/)) is a Higher Order Component created as sort of a middleware to allow logic to determine where a customer is routed*

You will notice the component prop which is set equal to the component that needs to be rendered. The path is what the URL will be for this route, and the rest of the props are passed along to be used in the <CheckoutContainer /> component. Here's another route setup for the homepage:

// *** [App.js](http://app.js/) ***
<Route exact path="/" render={props => {
    return (
        <ProductContainer 
            {...props}
            addToCart={addToCart}
            setCheckout={setCheckout}
        />
    )
}}/>
Because this isn't a 'Private Route', use the render prop which takes a function and returns your component. So anytime a customer hits the home page, they will be routed to our <ProductContainer /> component (component that is listing your products).

If you recall for the checkout button we pushed the customer to this path:

[history.push](http://history.push/)(`/checkout/${[props.cart.id](http://props.cart.id/)}`)
And in [App.js](http://app.js/), we have a route setup to that exact path:

<PrivateRoute 
    component={CheckoutContainer}
    path={`/checkout/:id`} 
the :id is just a foo name that is a variable for whatever text you put there. In our case we're setting [props.cart.id](http://props.cart.id/) equal to :id. This can later be accessed on the match object - [props.match.params.id](http://props.match.params.id/)

Cart ID [Commerce.js](http://commerce.js/)

The <CheckoutContainer /> Component
How you setup your React app and organize your components can vary. Choose to have a <CheckoutContainer /> component that will contain your form and other essential data necessary for checkout. One of the most important features of the [Commerce.js](http://commerce.js/) SDK is the checkout token. The checkout token is your key or access to all the information needed to capture a checkout. Further it has the live object which contains the most up date data in regards to items, shipping methods etc... Read more about the Checkout Token

In the <CheckoutContainer /> component you will be generating the checkout token. You will use the [commerce.checkout.generateToken](http://commerce.checkout.generatetoken/)() helper function and wrap this in an useEffect() - so that any time this component is loaded, you will always set the live object in state:

useEffect(() => {
    // *** [CheckoutContainer.js](http://checkoutcontainer.js/) ***

    /* *** Getting Checkout Token - Set Live Object in State *** */

    let cartId = [props.match.params.id](http://props.match.params.id/)
    [commerce.checkout.generateToken](http://commerce.checkout.generatetoken/)(cartId, { type: 'cart' })
        .then(res => {
            setLiveObject([res.live](http://res.live/))
            setTokenId([res.id](http://res.id/))
        })
        .catch(err => {
            [console.log](http://console.log/)(err)
        })

    [props.setCheckout](http://props.setcheckout/)(true)
},[])
The function takes the cartId (which is retrieved from the match object [props.match.params.id](http://props.match.params.id/)) and a second object that tells the type. Grab the checkout token in the response and store that in state along with the live object.

Because the live object contains so much valuable data, you can build triggers for certain UI based on the data changes. You also send this live object to your form in order to build the line_items object later needed for capture.

Now that these routes are setup and you can generate you checkout token - it is now time to build your form!

Step 3. Create Checkout Form
Before you create your form, it's good to determine what data is needed in order to process an order. There are four main properties needed: Customer (name, email etc...), Shipping (address, country etc...), Payment (card info, payment gateway), Fulfillment (whatever shipping method was selected). Head over to the docs to get a better look at the final object you'll be sending to the Chec dashboard.

*** Note ** There's also Billing but it holds the same 'sub-properties' as Shipping - so for this example we have logic setup to only include Billing if the customer's billing address is different than their shipping.*

Each main property is an object which contains more properties 'sub-properties' of data that needs to be collected. Take a look!

customer: {
    firstname: 'Van',                   
    lastname: 'Williams',
    email: '[van.doe@example.com](mailto:van.doe@example.com)',
  },
  shipping: {
    name: 'Van Williams',
    street: '123 Fake St',
    town_city: 'Nashville',
    county_state: 'TN',
    postal_zip_code: '94103',
    country: 'US',
  }
We will be building our form using Semantic UI components instead of the standard <input /> element. It is essentially the same implementation except you will see extra properties specific to the custom component provided by Semantic UI. Let's look at each main property:

Customer
Customer form fields 

// *** [CheckoutForm.js](http://checkoutform.js/) ***
<[Form.Input](http://form.input/)
    fluid
    name="firstname" 
    label='First Name'
    placeholder='John'
/>
<[Form.Input](http://form.input/) 
    fluid 
    name='lastname' 
    label='Last name' 
    placeholder='Smith'
/>
<[Form.Input](http://form.input/) 
    fluid 
    name='email'
    label='Email' 
    placeholder='[xyz@example.com](mailto:xyz@example.com)'
    type='email'  
/>
As you can see, the properties are the same like name, placeholder that you would use for a normal <input /> element.

Shipping
Shipping 

// *** [CheckoutForm.js](http://checkoutform.js/) ***
<[Form.Group](http://form.group/)>
    <[Form.Input](http://form.input/) 
        width={10} 
        name='street' 
        label='Address' 
        placeholder='122 Example St'   
    />
    <[Form.Select](http://form.select/)
        width={6} 
        name='country' 
        label='Select Country' 
        options={countries}
    />
</[Form.Group](http://form.group/)>
<[Form.Group](http://form.group/)>
    <[Form.Input](http://form.input/) 
        width={6} 
        name='town_city' 
        label='Town/City' 
        placeholder='Las Vegas' 
    />
    <[Form.Select](http://form.select/)
        width={6} 
        label='County/State/Province/Territory' 
        placeholder='Search ...'
        name='county_state' 
        search 
        fluid
        options={getCountryInfoShipping()}
    />
    <[Form.Input](http://form.input/)
        width={4} 
        type='number'
        name='postal_zip_code' 
        label='Zip/Postal' 
        placeholder='00000'
    />
</[Form.Group](http://form.group/)>
For dropdowns in Semantic UI there's an options prop that takes an array of objects of different options a customer can choose.

// *** [Countries.js](http://countries.js/) ***

export const countries = [
    {
    value: "CA",
    text: "Canada"
    },
    {
    value: "MX",
    text: "Mexico"
    },
    {
    value: "US",
    text: "United States"
    }
]
As the store owner and for this example we have already set up shipping for only three countries. These will be the only choices a customer can choose in order to ship. Once a customer chooses thier country it will trigger a different set of options based on that country. In order to achieve this I compiled a list of all territories/states/provinces for each country (see the North America folder under utils).

So, the options property for this dropdown ...

// *** [CheckoutForm.js](http://checkoutform.js/) ***

<[Form.Select](http://form.select/)
    width={6} 
    label='County/State/Province/Territory' 
    placeholder='Search ...'
    name='county_state' 
    search 
    fluid
    options={getCountryInfoShipping()}
/>
is set to a function - this function checks what Country was selected and returns the proper array for that country:

// *** [CheckoutForm.js](http://checkoutform.js/) ***

const getCountryInfoShipping = () => {

    /* *** Gives user proper options based on Shipping Country *** */

    if (shipCountry === 'MX') {
        return mexico
    }

    if (shipCountry === 'CA') {
        return canada
    }

    if (shipCountry === 'US') {
        return stateOptions
    }
}
Payment
// *** [CheckoutForm.js](http://checkoutform.js/) ***
<[Form.Group](http://form.group/) className='payment-radio'>
    <input
        name='gateway' 
        type='radio'
        value='test_gateway'
    />
    <label htmlFor="test_gateway">Test Gateway</label>
    <input
        name='gateway' 
        type='radio'
        value='stripe'
    />
    <label htmlFor="stripe">Credit Card</label>
</[Form.Group](http://form.group/)>
<[Form.Group](http://form.group/)>
    <[Form.Input](http://form.input/)
        name='number'
        type='number' 
        label='Credit Card Number' 
        placeholder='0000111100001111' 
    />
    <[Form.Input](http://form.input/)
        name='postal_billing_zip_code' 
        type='number'
        max='99999'
        label='Billing Zip' 
        placeholder='Enter Billing Zip Code'
    />
</[Form.Group](http://form.group/)>
<[Form.Group](http://form.group/)>
    <[Form.Select](http://form.select/) 
        width={3} 
        name='expiry_month' 
        fluid 
        options={monthOptions} 
        label='Month' 
    />
    <[Form.Select](http://form.select/) 
        width={3} 
        name='expiry_year' 
        fluid 
        options={yearOptions} 
        label='Year' 
    />
    <[Form.Input](http://form.input/) 
        width={3} 
        name='cvc'
        type='number'
        label='CVC' 
        placeholder='123'
    />
</[Form.Group](http://form.group/)>
These are all the fields needed to collect information about payment. We need to bring in arrays (monthOptions, yearOptions) for the options props in regards to month/year card expiration.

Fufillment
This is the last important piece of data you need to complete a checkout form. The customer needs to be able to select a shipping option. The shipping option is determined by country - remember our shipping zones?

For example: If the customer chooses Canada as their shipping country, then we run a function with that country code in order to give a shipping option to choose from. This option determines the price for shipping to your country. For this store - we are charging $8 dollars flate rate to ship to Canada.

Because we've separated the shipping options from the main form (where we are gathering all the other data), we have to pass a function via props to our form. This function is wrapped in a useEffect() and gets triggered every time a different country is selected.

useEffect(() => {
    // *** [CheckoutForm.js](http://checkoutform.js/) ***

    /* *** Takes the Shipping Country and updates shipping Options *** */
    [props.getShippingOptions](http://props.getshippingoptions/)(shipCountry)
}, [shipCountry])
Let's take a look at the getShippingOptions() function:

// *** [CheckoutContainer.js](http://checkoutcontainer.js/) ***
const getShippingOptions = (countrySymbol) => {
Build a single page checkout using [React.js](http://react.js/)

See live demo

About the author, Joseph Garcia

Joseph is a full-stack developer that loves learning new technologies and solving interesting problems. Starting out in Network Administration, Joseph now works full time in development. Joseph's favorite technology is React and favorite snack is Frosted Flakes!

GITHUBLINKEDIN

#CHECKOUT#REACT

This article might not necessarily be maintained. Please check the latest version of the documentation for up-to-date resources.

This guide explains the process and procedure to capture a checkout using [React.js](http://react.js/) & [Commerce.js](http://commerce.js/) (SDK)

** Note **

This guide is using v2 of the SDK

The Live Demo is best viewed on Desktop (responsiveness limited)

￼

Overview

You now have come to an important part of the eCommerce journey - payment processing and capturing an order. And just a quick recap: We added products to the Chec Dashboard and listed them on the site, We were then able to add chosen products to the cart, and now we want those items in the cart to be processed - get customer information and finalize payment; all with [Commerce.js](http://commerce.js/) and the SDK. The order will then be added to your Chec dashboard along with customer info and other important data. There's a lot of information packed in this guide, so let's dive in!

This guide will cover:

Create new shipping zone & add zone to product

Add checkout button & setup route to checkout form

Create Form

Handling Form Data/Validation/Errors

Handling Discount Code

Capturing Checkout & route to Thank You page (Thanks for Your Order)

* Extra *

How to implement Stripe as a payment gateway

This guide strictly utilizes functional React components and relies heavily on React Hooks and dynamic rendering. The purpose of this guide is to show how you can use the [Commerce.js](http://commerce.js/) SDK to build eCommerce functionality. We will not be going into detail around how to use React Hooks but will instead link to external resources that reference specific React features.

Requirements/Prerequisites

IDE of your choice (code editor)

NodeJS, or yarn → npm or yarn.

Some knowledge of Javascript & React

Bonus - Using React Hooks - specifically useState(), useEffect(), useContext()

Bonus - familiarity with React Router

Bonus - familiarity with React Hook Form

Bonus - familiarity with Stripe

Bonus - familiarity with the framework Semantic UI (react) library

Getting Started

STEP 1. Creating Shipping Zone & Adding to Product:

One of the most important steps to capturing an order is determining the logistics of how you will ship your products. You need to answer questions like: Where will I ship? How much will I charge? Will it be a flat fee to place A etc...

For this example, we will have 3 shipping zones: United States, Mexico, Canada. We will be charging a flat rate (see below) for each zone. In order to set this up, you must navigate to the Shipping Tab within your dashboard and click Add Zone.

Now that you've added the shipping zones you wish to ship to, including price — no add these zones to your product. Each product can have different shipping zones, but for simplicity you shhould add all three zones to every product in the catalogue.

Add Zone to Product

Navigate to each individual product and click the enable zone button. This will enable a specific shipping option for the product. You can also add any extra shipping cost such as an amount for one order vs multiple orders.
This is an important step and must be completed before you can capture a checkout. As you will see further in this guide, the shipping option is needed for Chec to process and finalize an order. You also want to give customers a shipping price