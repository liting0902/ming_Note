import {SET_productList,REFRESH_productList, ADD_shopCart, ADD_uniqueId} from '../actionTypes'
import * as dataDefine from '../../dataDefine/index.js'

//productList:
const initState = [dataDefine.fakeProductInfo1, dataDefine.fakeProductInfo2,dataDefine.fakeProductInfo3,
//     {
//     productId: "ajnck",
//     name: "白斬雞",
//     price: 140,
//     imgFileName: "白斬雞.jpg",
// },
// {
//     productId: "okdsf",
//     name: "蔥爆牛肉",
//     price: 550,
//     imgFileName: "蔥爆牛肉.jpg",
// },
]

// Reducer
export default function reducer(state = initState, action = {}) {
    let newState = Object.assign({}, state);
    switch (action.type) {
        case REFRESH_productList:
            //console.log('reducer--', action.e1)
            
            newState=action.productList;//[]
            //newState.push(action.productList)//dataDefine.fakeProductInfo1
            return newState;
        // do reducer stuff
        default:
            return state;
    }
}

// Action Creators


// side effects, only as applicable
// e.g. thunks, epics, etc
// export function getWidget() {
//     return (dispatch) =>
//         get("/widget").then((widget) => dispatch(updateWidget(widget)));
// }