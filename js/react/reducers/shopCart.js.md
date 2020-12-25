import {SET_productList, ADD_shopCart,DECREASE_shopCart,CLEAR_shopCart, ADD_uniqueId} from '../actionTypes/index.js'
import * as dataDefine from '../../dataDefine/index.js'
import _ from 'lodash'
import { clear_shopCart } from '../actions/shopCart.js';
// Action Creators


// state object = {
//     productId: "axc-1",
//     amount: 3,
// }
// shopCart
export const initState = {
    shopItemList:[
        // shopItem
    ]
}

// Reducer
export default function reducer(state = initState, action = {}) {
    let newState = {};
    switch (action.type) {
        case ADD_shopCart: {
            let reusult = state.shopItemList.find((element, index, array) => {
                if (element.productId === action.productInfo.productId) return element;
                else return false;
            });
            
            // this shopItem not exist
            if(reusult===undefined){
                //add cart
                let newShopItem = new dataDefine.ShopItemInfo();
                //console.log('>>>>>PPP>>>>>>'+newShopItem.productId)
                //console.log(action.productInfo)
                newShopItem.amount = action.amount;
                newShopItem.productInfo = action.productInfo;
                // newShopItem = {
                //     ...newShopItem,
                //     //productId:action.productId,
                //     amount:action.amount,
                //     productInfo:action.productInfo,
                // }
                //console.log('-------newShopItem--->',newShopItem )
                //console.log('newShopItem--> ', newShopItem)
                // newShopItem.productId
                // let newBuy = {
                //     productId:action.productId,
                //     amount:action.amount,
                // }
                newState = _.cloneDeep(state)
                let newArray = [...state.shopItemList, newShopItem]
                newState.shopItemList = newArray
                //console.log('json--> ', JSON.stringify(state,null,4))
                //newState = Object.assign({},state, {shopItemList:state.shopItemList.push(newBuy)});// [...state.shopItemList, newBuy]
            }
            // already exist shopItem
            else{
                reusult.amount++
                //newState = state.map((item)=>{return item})
                //debugger;
                // let newArrayItemList = [state.shopItemList]
                // newState = Object.assign({},state, {shopItemList:newArrayItemList});
                // console.log('newState-> ', newState)
                let newArray = [...state.shopItemList]
                //let newReusult = Object.assign({},reusult)
                //newReusult.amount++
                //debugger;
                newState = _.cloneDeep(state)
                newState.shopItemList = newArray;
                //console.log('newState-> ', newState)
            }
            //debugger
            return newState;
        }
        case DECREASE_shopCart:{
            //let newArray = [...state.shopItemList]
            let findItem = state.shopItemList.find((item, index, array) => {
                if (item.productId === action.productInfo.productId) return item;
                else return false;
            });
            //found item
            if(findItem!==undefined){
                var newItemList;
                
                if(findItem.amount===1){
                    //ready to remove findItem
                    newItemList = state.shopItemList.filter(item=>{
                        return(item.productId !== findItem.productId)//return true
                    })
                }
                else{
                    findItem.amount--;
                    newItemList = [...state.shopItemList]
                }
                
                return{
                    ...state,
                    shopItemList:newItemList
                }
            }

        }
        case CLEAR_shopCart:{
            return{
                ...state,
                shopItemList:[]
            }

        }
        // do reducer stuff
        default:
            return state;
    }
}

export const countAllItems_Price = (shopItemList=[{},{}])=>{
    //console.log('shopItemList-> ', shopItemList)
    let sumPrice = 0;
    /**@type {Number} sum of all items amount */
    let totalItemCount = 0;
    shopItemList.forEach((/**@type {dataDefine.ShopItemInfo}*/shopItem, index)=>{
        totalItemCount+=shopItem.amount;
        let shopItemPrice = countItemPrice(shopItem);
        //shopItem.amount * shopItem.productInfo.price;
        sumPrice+=shopItemPrice
    });
    //let window2 = /** @type {import('../../dataDefine/index.js').ExtendedWindow} */ (window);
    window.app.setShopItemCount(totalItemCount)
    let result = {
        sumPrice,
        totalItemCount
    }
    return result;
}
//
// let a = new dataDefine.ShopItem();
// let prod = new dataDefine.ProductInfo();
// //prod.price
export const countItemPrice = (shopItem) =>{
    return shopItem.amount * shopItem.productInfo.price
}

