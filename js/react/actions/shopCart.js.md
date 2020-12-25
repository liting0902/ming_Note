import {
    SET_productList,
    ADD_shopCart,
    DECREASE_shopCart,
    CLEAR_shopCart,
    ADD_uniqueId
} from '../actionTypes/index.js'

export function add_shopCart(amount, productInfo) {
    //console.log('add_uniqueId ',uniqueId)
    return {
        type: ADD_shopCart,
        //productId: productId,
        amount: amount,
        productInfo: productInfo
    };
}

export function decrease_shopCart( amount, productInfo) {
    //console.log('add_uniqueId ',uniqueId)
    return {
        type: DECREASE_shopCart,
        //productId: productId,
        amount: amount,
        productInfo: productInfo
    };
}
export function clear_shopCart() {
    //console.log('add_uniqueId ',uniqueId)
    return {
        type: CLEAR_shopCart,
    };
}