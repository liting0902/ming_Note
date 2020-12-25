//@ts-check
//Inside of settings.json, add the following:{ "javascript.implicitProjectConfig.checkJs": true }
import React, { Component } from 'react'
// import {ENUM_switchIndexPage} from '../../../../pages/index/index.js'
import PropTypes from 'prop-types'
import { connect } from 'react-redux'
//import * as App_redux from '../../App_redux.js'
import * as shopCart_actions from '../actions/shopCart'
import { countAllItems_Price } from '../reducers/shopCart.js'
import { bindActionCreators } from 'redux'
import ShopItem from '../components/ShopItem.jsx'
// import './ShopCart.css'
//import '../../containers/ProductListSearch/ProductListSearch.css'
import { OrderInfo, ShopItemInfo, UserData, UserProfile } from '../../dataDefine/index.js'
import Swal from 'sweetalert2'
import { TypeOf, ENUM_TypeOf, getPlainObject } from '../../lib/dataKits.js'
import { ENUM_switchIndexPage } from '../../../pages/index/indexESM.js'
import PopInvoice from '../components/PopInvoice.jsx'

//let modalPopInvoice = <PopInvoice></PopInvoice>
class ShopCart extends Component {
    static propTypes = {
        ['data-orderAddress']: PropTypes.string,
        showInvoicePopModal:PropTypes.func,
    }
    state = {
        orderInfo: new OrderInfo(),
        refPopInvoice: React.createRef(),
    }
    constructor(/**@type {any}*/props) {
        super(props)
        const { dispatch } = props
        this.boundActionCreators = bindActionCreators(shopCart_actions, dispatch);
        console.log("LOG: ~ file: ShopCart.jsx ~ line 27 ~ ShopCart ~ constructor ~ this.boundActionCreators", this.boundActionCreators)
        //console.log('props.shopCart--> ',props.shopCart)
    }
    // shouldComponentUpdate = ()=>{
    //     return true
    // }
    /**
     * 
     * @param {ShopItemInfo[]} shopItems 
     */
    _getOrderInfo_FromShopItems = (shopItems) => {
        let newOrderIfno = new OrderInfo();
        shopItems.forEach((item) => {
            /**@type {ShopItemInfo} */
            let newItem = window._.cloneDeep(item)// { ...item }
            newItem._productInfo = null
            newOrderIfno.shopItemList.push(newItem);
        })
        return newOrderIfno
    }
    _getOrderInfo = () => {
        let orderInfo = this._getOrderInfo_FromShopItems(this.props.shopItemList);
        orderInfo.orderAddress = this.props['data-orderAddress'];
        orderInfo.totalPrice = this.props.AllItems_Price;

        return window.FirebaseMJS.getUser()
            .then((/**@type {any}*/user) => {
                //console.log('wwwww---', user)
                orderInfo.userId = user.uid
                //orderInfo.userProfile = user.userProfile
                orderInfo.userData = user;
                //get order select address
                let userData = new UserData();
                userData = Object.assign(userData, orderInfo.userData)
                // let userProf = new UserProfile();
                // userProf = Object.assign(userProf, orderInfo.userProfile)


                //console.log(userProf.getSelectedAddress())
                //console.log(orderInfo)
                return orderInfo;
            })


    }
    call_ShowInvoicePopModal = () => {
        let {showInvoicePopModal} = this.props
        
        this._getOrderInfo()
            .then((orderInfo) => {
                orderInfo.fillShopItems(window.app.arrayProductInfo)
                showInvoicePopModal(orderInfo)
            })
    }


    // CheckOutOrder = (/**@type {any}*/e) => {

    //     this._getOrderInfo()
    //         .then((orderInfo) => {
    //             // let enumType = TypeOf(orderInfo)
    //             // let toDb_orderInfo = (enumType == ENUM_TypeOf.objectClass)?recursiveToPlainObject(orderInfo): orderInfo;
    //             // console.log('toDb_orderInfo-->',toDb_orderInfo)
    //             return window.FirebaseMJS.addOrderInfo(orderInfo, window._)
    //         })
    //         .then(() => {
    //             return Swal.fire({
    //                 title: '訂單已成功送出',
    //                 text: '若要取消訂單，請至我的訂單頁面中查看',
    //                 icon: 'success',
    //                 confirmButtonText: '檢視我的訂單',
    //                 // denyButtonText: 'Cool',
    //                 cancelButtonText: '返回',
    //                 showConfirmButton: true,
    //                 // showDenyButton:true,
    //                 showCancelButton: true,

    //             })
    //         })
    //         .then((e) => {
    //             this.boundActionCreators.clear_shopCart();
    //             if (e.isConfirmed === true) {
    //                 window.app.switchIndexPage(ENUM_switchIndexPage.ViewOrders)
    //             }

    //             //console.log(e)
    //             //     isConfirmed: true
    //             // isDenied: false
    //             // isDismissed: false  cancel
    //             // value: true
    //         })
    //         .catch((error) => {
    //             console.error("LOG: ~ file: ShopCart.jsx ~ line 95 ~ ShopCart ~ error---")
    //             console.dir(error)
    //             if (error.message.startsWith('regeneratorRuntime is not defined'))
    //                 console.error('need to require( @babel/polyfill )')
    //             return Swal.fire({
    //                 title: '訂單送出失敗',
    //                 text: `${error.message}`,
    //                 icon: 'error',
    //                 //confirmButtonText: '檢視我的訂單',
    //                 // denyButtonText: 'Cool',
    //                 cancelButtonText: '返回',
    //                 showConfirmButton: false,
    //                 // showDenyButton:true,
    //                 showCancelButton: true,

    //             })
    //         })

    //     //console.log(orderInfo)
    //     //console.log(this.props.shopItemList)
    //     // this.props.shopItemList.forEach((item) => {
    //     //     let newItem = Object.assign({},item)
    //     //     newItem._productInfo = null
    //     //     console.log(item)
    //     //     console.log(newItem)
    //     // }
    //     // )
    //     //console.log(this.props.AllItems_Price)
    //     //let newOrder = new OrderInfo();
    //     //console.log(newOrder)
    // }


    render() {
        //console.log('---------- render ShopCart----------------')
        //const {getAllItem_PriceTotal} = this.props
        //console.log('getAllItem_PriceTotal--> ', getAllItem_PriceTotal)
        //getAllItem_PriceTotal();
        //console.log(getAllItem_PriceTotal)

        const { shopItemList, AllItems_Price, dispatch } = this.props
        //console.log('shopItemList-> ', shopItemList)
        return (
            <div>
                {/* <PopInvoice dispatch={dispatch}></PopInvoice> */}
                <PopInvoice ref={this.state.refPopInvoice} orderInfo={this.state.orderInfo} dispatch={dispatch}></PopInvoice>
                <div>
                    {/* <div>{this.props['data-orderAddress']}</div> */}
                    <div className="boxShopCard"><h1>購物車</h1></div>
                    <table id="tbShopcart" className="tableShopcart bd1">
                        <tbody>

                            {shopItemList.map((/**@type {ShopItemInfo}}*/item, /**@type {number}*/index) => (<ShopItem key={index} shopItem={item} dispatch={dispatch}></ShopItem>))}
                            {/* <tr>
                                <td>總計 (包括稅項)</td>
                                <td>NT$0.00</td>
                            </tr> */}
                            <tr>
                                <td>小計</td>
                                <td>NT$  {AllItems_Price}</td>
                            </tr>
                        </tbody>
                        <tfoot>
                            <tr>
                                <td colSpan={2}>
                                    <button className="btn btn-block btn-success" onClick={this.call_ShowInvoicePopModal}>結帳</button>
                                </td>
                            </tr>
                        </tfoot>
                    </table>
                    {/* <br />
                    <br /> */}
                    {/* <table className="table_shotItemList">
                        <tbody>
                            <tr>
                                <td>小計</td>
                                <td>NT$  {AllItems_Price}</td>
                            </tr>
                            {shopItemList.map((item, index) => (<ShopItem key={index} shopItem={item} dispatch={dispatch}></ShopItem>))}

                            <tr>
                                <td>服務費</td>
                                <td>NT$0.00</td>
                            </tr>
                            <tr>
                                <td>總計 (包括稅項)</td>
                                <td>NT$0.00</td>
                            </tr>
                            <tr>
                                <td>
                                    <button className="btn btn-success" onClick={this.CheckOutOrder}>結帳</button>
                                </td>
                            </tr>
                        </tbody>
                    </table> */}
                </div>

            </div>
        )
    }
}

const mapStateToProps = (/**@type {any}*/state) => {
    //console.log('shopCart -- mapStateToProps==> ', state)
    let { sumPrice, totalItemCount } = countAllItems_Price(state.shopCart.shopItemList)
    return {
        shopItemList: state.shopCart.shopItemList,
        //getAllItem_PriceTotal: state.shopCart.getAllItem_PriceTotal,
        AllItems_Price: sumPrice,//countAllItems_Price(state.shopCart.shopItemList)
        totalItemCount: totalItemCount,
    }
}

const mapDispatchToProps = (/**@type {any}*/dispatch) => {
    //return bindActionCreators(App_redux, dispatch);
    return {
        dispatch: dispatch
    }
}

export default connect(mapStateToProps, mapDispatchToProps)(ShopCart)
