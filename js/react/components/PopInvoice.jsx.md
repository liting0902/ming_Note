import React, { Component } from 'react'
import PropTypes from 'prop-types'
import { OrderInfo } from '../../dataDefine/index.js'
import Invoice from './Invoice.jsx'
import {ENUM_switchIndexPage} from '../../../pages/index/indexESM.js'
import * as shopCart_actions from '../actions/shopCart'
import { bindActionCreators } from 'redux'
import './PopInvoice.css'
export default class PopInvoice extends Component {
    static propTypes = {
        orderInfo: PropTypes.instanceOf(OrderInfo).isRequired,
        dispatch:PropTypes.any.isRequired
        // userData:PropTypes.instanceOf(OrderInfo),
        // orderAddress:PropTypes.string,
        // totalPrice:PropTypes.number,

    }
    constructor(props) {
        super(props)
        this.refModal = React.createRef();
        this.boundActionCreators = bindActionCreators(shopCart_actions, props.dispatch);
    }
    componentDidMount() {
        //$(this.refModal.current).modal('show')
        //this.showModal(true)
    }
    showModal(isShow) {
        let sOption = (isShow === true)?'show':'hide';
        $(this.refModal.current).modal(sOption)
    }
    
    // _getOrderInfo = () => {
    //     let orderInfo = this._getOrderInfo_FromShopItems(this.props.shopItemList);
    //     orderInfo.orderAddress = this.props['data-orderAddress'];
    //     orderInfo.totalPrice = this.props.AllItems_Price;

    //     return window.FirebaseMJS.getUser()
    //         .then((/**@type {any}*/user) => {
    //             //console.log('wwwww---', user)
    //             orderInfo.userId = user.uid
    //             //orderInfo.userProfile = user.userProfile
    //             orderInfo.userData = user;
    //             //get order select address
    //             let userData = new UserData();
    //             userData = Object.assign(userData, orderInfo.userData)
    //             // let userProf = new UserProfile();
    //             // userProf = Object.assign(userProf, orderInfo.userProfile)


    //             //console.log(userProf.getSelectedAddress())
    //             //console.log(orderInfo)
    //             return orderInfo;
    //         })


    // }
    CheckOutOrder = (/**@type {any}*/e) => {
        window.FirebaseMJS.addOrderInfo(this.props.orderInfo, window._)
            .then(() => {
                return window.Swal.fire({
                    title: '訂單已成功送出',
                    text: '若要取消訂單，請至我的訂單頁面中查看',
                    icon: 'success',
                    confirmButtonText: '檢視我的訂單',
                    // denyButtonText: 'Cool',
                    cancelButtonText: '返回',
                    showConfirmButton: true,
                    // showDenyButton:true,
                    showCancelButton: true,
                    reverseButtons: true,

                })
            })
            .then((e) => {
                this.boundActionCreators.clear_shopCart();
                this.showModal(false)
                if(e.isConfirmed === true){
                    window.app.switchIndexPage(ENUM_switchIndexPage.ViewOrders)
                }
                
                //console.log(e)
                //     isConfirmed: true
                // isDenied: false
                // isDismissed: false  cancel
                // value: true
            })
            .catch((error) => {
                console.log("LOG: ~ file: PopInvoice.jsx ~ line 81 ~ PopInvoice ~ error", error)
                // console.dir(error)
                if(error.message.startsWith('regeneratorRuntime is not defined'))
                    console.error('need to require( @babel/polyfill )')
                return window.Swal.fire({
                    title: '訂單送出失敗',
                    text: `${error.message}`,
                    icon: 'error',
                    //confirmButtonText: '檢視我的訂單',
                    // denyButtonText: 'Cool',
                    cancelButtonText: '返回',
                    showConfirmButton: false,
                    // showDenyButton:true,
                    showCancelButton: true,

                })
            })
    }
    
    render() {
        let { userData, orderAddress, totalPrice, orderId, sOrderStatus, } = this.props.orderInfo
        
        //let jsdtCreateDateTime_server = (this.props.orderInfo.jsdtCreateDateTime_server) ? this.props.orderInfo.jsdtCreateDateTime_server.toLocaleString() : 'XXXXX';
        return (
            (
                <div className="boxPopInvoice">
                    {/* <button type="button" className="btn btn-primary" >Large modal</button> */}
                    {/* <button type="button" className="btn btn-primary" data-toggle="modal" data-target=".bd-example-modal-lg">Large modal</button> */}
                    <div ref={this.refModal} className="modal fade bd-example-modal-lg" tabIndex={99} role="dialog" aria-labelledby="myLargeModalLabel" aria-hidden="true">
                        <div className="modal-dialog modal-lg">
                            <div className="modal-content">
                                <div className="modal-header">
                                    <h5 className="modal-title" id="exampleModalLongTitle">購買明細</h5>
                                    <button type="button" className="close" data-dismiss="modal" aria-label="Close">
                                        <span aria-hidden="true">×</span>
                                    </button>
                                </div>
                                <div className="modal-body">
                                    <Invoice orderInfo={this.props.orderInfo} IsOrderExisted={false}></Invoice>
                                </div>
                                <div className="modal-footer">
                                    <div>
                                        <div className="divTotalPrice">訂單總金額: NT$ {totalPrice}</div>
                                        <div>
                                            <button type="button" className="btn btn-secondary" data-dismiss="modal">取消</button>
                                            <button type="button" className="btn btn-primary" onClick={this.CheckOutOrder}>訂單確認送出</button>
                                        </div>
                                    </div>

                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            )
        )
    }
}
