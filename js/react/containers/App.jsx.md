
import React, { PureComponent } from 'react'
import {
    //BrowserRouter as Router,
    HashRouter as Router,
    Switch,
    Route,
    Link,
    //IndexRoute,
    // IndexRedirect,
    //DefaultRoute,

    Redirect,

} from "react-router-dom";
import { createBrowserHistory, createHashHistory } from "history";
//import { browserHistory } from 'react-router'
import ViewOrders from './ViewOrders.jsx'
import ProductListSearch from './ProductListSearch.jsx'
import './App.css'
export default class App extends PureComponent {
    constructor(/**@type {any}*/props) {
        super(props);
        this.props = props
        /**@type {any} */
        let history = createHashHistory(this.props)
        window.app.pushUrl = history.push
        window.app.history = history
        
        
        
    }
    OnClickMe = () => {
        //console.log('AAAA')
        window.app.history.push('/ViewOrders')
        
        
    }
    // SearchDashboard = (e) => {
    //     console.log('SearchDashboard')
    // }

    render() {
        return (
            // history={browserHistory}
            <Router >
                <div>
                    {/* <button onClick={this.OnClickMe}>Click Me</button>
                    <ul>
                        <li>
                            <Link to="/">App2</Link>
                        </li>
                        <li>
                            <Link to="/ViewOrders">ViewOrders</Link>
                        </li>
                    </ul>
                    <hr /> */}

                    {/* <DefaultRoute handler={this.SearchDashboard} /> */}
                    <Switch>
                        {/* <IndexRoute to="/" component={App2}/> */}
                        {/* <Redirect from="/" to="App2" /> */}
                        {/* <IndexRedirect to="/" /> */}
                        {/* <Route exact path="/">
                            <App2 />
                        </Route> */}
                        {/* <Redirect from="/" to="searchDashboard" /> */}
                        {/* <IndexRoute component={App2}/> */}
                        <Route
                            exact
                            path="/"
                            render={() => {
                                // return (
                                //     this.state.isUserAuthenticated ?
                                //         <Redirect to="/App2" /> :
                                //         <Redirect to="/ViewOrders" />
                                // )
                                return <Redirect to="/ProductListSearch" />
                            }}
                        />
                        <Route
                            exact
                            path="/page-react"
                            render={() => {
                                // return (
                                //     this.state.isUserAuthenticated ?
                                //         <Redirect to="/App2" /> :
                                //         <Redirect to="/ViewOrders" />
                                // )
                                return <Redirect to="/ProductListSearch" />
                            }}
                        />
                        <Route path="/ProductListSearch">
                            <ProductListSearch />
                        </Route>
                        <Route path="/ViewOrders">
                            <ViewOrders />
                        </Route>
                        {/* <Route path="/dashboard">
                            <Dashboard />
                        </Route> */}
                        {/* <Route path="/AA" render={(props) =>
                            <ButtonToNavigate2 {...props}
                                title="Navigate elsewhere" />
                        } >

                        </Route> */}
                    </Switch>
                </div>
            </Router>

        )
    }
}
