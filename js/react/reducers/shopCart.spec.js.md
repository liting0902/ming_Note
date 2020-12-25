import shopCart, {
  initState
} from "./shopCart.js";
import * as dataDefine from "../dataDefine/index.js";
import * as shopCart_act from "../actions/shopCart.js";

import _ from "lodash";

describe("shopCart reducer()", () => {
  const initialState = {
    shopItemList: [],
  };

  it("initialState should be eaual to initState", () => {
    expect(initialState).toEqual(initState);
  });

  it("should provide the initial state", () => {
    expect(shopCart(undefined, undefined)).toEqual(initialState);
  });

  // ProductInfo
  let product1 = new dataDefine.ProductInfo();
  product1.productId = "xad-1"; //aaaaa-1
  product1.name = "NAME-xad-1"; //"白斬雞"
  product1.price = 220; //140
  product1.imgFileName = "xad-1.jpg"; //白斬雞.jpg
  let product2 = new dataDefine.ProductInfo();
  product2.productId = "xad-2"; //aaaaa-1
  product2.name = "NAME-xad-2"; //"白斬雞"
  product2.price = 135; //140
  product2.imgFileName = "xad-2.jpg"; //白斬雞.jpg

  function GetNewShopItems() {
    let shotItem_1 = new dataDefine.ShopItem();
    shotItem_1.productInfo = product1;
    shotItem_1.productId = product1.productId;
    shotItem_1.amount = 1;
    let shotItem_2 = new dataDefine.ShopItem();
    shotItem_2.productInfo = product2;
    shotItem_2.productId = product2.productId;
    shotItem_2.amount = 1;
    return [shotItem_1,shotItem_2]
  }
  //ShopItem
  var newShopItems = GetNewShopItems();
  let shotItem_1 = newShopItems[0]
  let shotItem_2 = newShopItems[1]

  var act
  var preState
  var rtnState
  var expectState //answer
  //--------add item amount
  act = shopCart_act.add_shopCart(
    1,
    shotItem_1.productInfo
  );
  //answer
  expectState = _.cloneDeep(initState);
  expectState.shopItemList.push(shotItem_1) //first item

  //run
  preState = _.cloneDeep(initState);
  rtnState = shopCart(preState, act)
  // console.log(JSON.stringify(expectState, null, 4))
  // console.log(JSON.stringify(rtnState, null, 4))
  it("shopCart(reducer).ADD_shopCart ->  if shopItemList == [], then to [shopItem].", () => {
    expect(rtnState).toEqual(expectState);
  });

  //add the same item again...amount should be 2
  rtnState = shopCart(rtnState, act)
  expectState.shopItemList[0].amount = 2 //add same item, amount = 2 
  it("shopCart(reducer).ADD_shopCart ->  then shopItemList[0].amount++.", () => {
    expect(rtnState).toEqual(expectState);
  });

  //--------decrease item amount
  newShopItems = GetNewShopItems();
  shotItem_1 = newShopItems[0]
  shotItem_2 = newShopItems[1]
  
  //pre state , item1 amount == 2,item2 amount == 1
  preState = _.cloneDeep(initState);
  preState.shopItemList.push(shotItem_1);
  shotItem_1.amount ++ //==> 2
  preState.shopItemList.push(shotItem_2);
  //console.log(preState)
  //expect state
  newShopItems = GetNewShopItems();
  shotItem_1 = newShopItems[0]
  shotItem_2 = newShopItems[1]
  expectState = _.cloneDeep(initState);
  expectState.shopItemList.push(shotItem_1);//shotItem_1.amount ==1
  expectState.shopItemList.push(shotItem_2);
  //remove shotItem_1
  act = shopCart_act.decrease_shopCart(
    1,
    shotItem_1.productInfo
  );
  rtnState = shopCart(preState, act)
  // console.log(expectState)
  // console.log(rtnState)
  it("shopCart(reducer).DECREASE_shopCart ->  shopItem.amount == 2 ==> shopItem.amount == 1.", () => {
    expect(rtnState).toEqual(expectState);
  });

  newShopItems = GetNewShopItems();
  //shotItem_1 = newShopItems[0]
  shotItem_2 = newShopItems[1]

  expectState = _.cloneDeep(initState);
  expectState.shopItemList.push(shotItem_2);//only left shopItem2
  rtnState = shopCart(rtnState, act)
  it("shopCart(reducer).DECREASE_shopCart ->  if delete shopItem amount == 1, then remove this item from shopItemList.", () => {
    expect(rtnState).toEqual(expectState);
  });
//console.log(rtnState)
  // it('should handle CHECKOUT_FAILURE action', () => {
  //   expect(cart({}, {
  //     type: 'CHECKOUT_FAILURE',
  //     cart: 'cart state'
  //   })).toEqual('cart state')
  // })

  // it('should handle ADD_TO_CART action', () => {
  //   expect(cart(initialState, {
  //     type: 'ADD_TO_CART',
  //     productId: 1
  //   })).toEqual({
  //     addedIds: [1],
  //     quantityById: {
  //       1: 1
  //     }
  //   })
  // })

  // describe('when product is already in cart', () => {
  //   it('should handle ADD_TO_CART action', () => {
  //     const state = {
  //       addedIds: [1, 2],
  //       quantityById: {
  //         1: 1,
  //         2: 1
  //       }
  //     }

  //     expect(cart(state, {
  //       type: 'ADD_TO_CART',
  //       productId: 2
  //     })).toEqual({
  //       addedIds: [1, 2],
  //       quantityById: {
  //         1: 1,
  //         2: 2
  //       }
  //     })
  //   })
  // })
});