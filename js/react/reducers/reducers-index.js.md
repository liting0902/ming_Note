import {
    combineReducers
} from 'redux'
// import uniqueChildId from './uniqueChildId'
import shopCart from './shopCart'
import productList from './productList'
//import visibilityFilter from './visibilityFilter'

//console.log('todos==>', todos)
var combine = combineReducers({
    //uniqueChildId,
    shopCart,
    productList,
})
//console.log(combine)
export default combine