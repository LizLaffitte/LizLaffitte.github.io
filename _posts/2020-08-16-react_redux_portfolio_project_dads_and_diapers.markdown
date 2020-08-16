---
layout: post
title:      "React Redux Portfolio Project: Dads and Diapers"
date:       2020-08-16 12:21:37 -0400
permalink:  react_redux_portfolio_project_dads_and_diapers
---


### Inspiration
Dads and Diapers was inspired by my own experience as a new mom. Before having a child, I never once thought about changing tables, their availabilty or their condition. As my husband and I first started to venture out of the house with our new baby, we soon realized that changing tables are a rare commodity in mens restrooms. 

While the BABIES Act now requires changing tables in all publicly accessible, *federal* buildings, private businesses often fall short.

Dads & Diapers was built as a resource for parents of small children. It allows users to add listings of local businesses and review them based on the availability and condition of changing tables.

### Technologies

This app is build with a Rails API backend and a React/Redux frontend and is written in Ruby, JavaScript, HTML & CSS. It makes use of Redux DevTools for debugging, and React Router DOM for routing.

### Road Blocks
The biggest hurdle I came across when building Dads and Diapers was props persistence on refresh. I soon realized that while rendering a listing's show route, a refresh would clear the Redux store and the props passed to the Listing component would resolve to undefined.

This issue orginated from the way the Listing component (and its parent componen ListingCard) was passed a listing as a prop and where I was calling the action to populate the Redux store. 

```
 <Route path="/listings/:id" render={props => {
                             const listing = this.props.listings.find(listing => listing.id === props.match.params.id)
                        return <ListingCard {...props} listing={listing} />}
}/>

```
When users visit the route '/listings/:id', a variable listing is definied by finding a listing in the Redux store that has the same id as the parameter in the route. When a user refreshed the page, the ListingCard component code ran before the fetchListings() action was dispatched. This meant that the store was empty, there was no listing in the store that matched the params, and the listing prop resolved to undefined.

To mitigate this issue, I implemented two changes:

1. Placement of fetchListings()

In the current version, fetchListings() was called in the componentDidMount() lifestyle method of the MainContainer, ListingCard's grandparent component. The componentDidMount() method of a parent component does not fire until after its child components are mounted. 

This means that listings weren't added to the store until after the Listing and ListingCard components were mounted. I needed to call fetchListings() before Listing was mounted. I moved fetchListings() to the HeaderContainer component. HeaderContainer is completely mounted before MainContainer, meaning the store would be filled with data on refresh.

```
class App extends Component {

  render (){
    return (
      <Router>
        <div className="App">
          <HeaderContainer />
          <MainContainer />            
        </div>
      </Router>
    )
  }
}
```




2. Use of localStorage
As an additional failsafe, I made use of localStorage. When users click on a listing, an action is dispatched to save that listing in localStorage and in the store as currentListing. This way, if there is an issue with listings in the store, there is data available to display while the fetch's promises are being resolved. The route was transformed:

```

    findListing = (id) => {
        let listing = this.props.listings.find(listing => listing.id === id)
        if(listing) {
				    if (listing !== this.props.currentListing) {
              this.props.setCurrentListing(listing)
							}
            return listing
        } else {
            let listing = JSON.parse(localStorage.getItem('currentListing'))
            return listing
        }
    } 

 <Route path="/listings/:id" render={props => {
                             const listing = this.findListing(props.match.params.id)
                        return <ListingCard {...props} listing={listing} />}
                    }/>


```
Now when users visit the route '/lisings/:id`, findListing() is called. First we attempt to find a listing in the store whose id matches the params. If that listing is truthy, and is not saved as currentListing, it is saved as the currentListing and it is returned. Otherwise, we use the currentListing set in localStorage.

### Roadmap
I would like to incorporate a geocoding/maps API so that I can visually display the added listings on a map.  Once these features are complete, I will deploy the application on Heroku. 


