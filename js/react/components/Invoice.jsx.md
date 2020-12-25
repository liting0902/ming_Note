import React, { Component } from 'react'
import './Invoice.css'
import {OrderInfo, ShopItemInfo} from '../../dataDefine/index.js'
import PropTypes from 'prop-types'

class InvoiceShopItem extends Component{
    static propTypes={
        shopItemInfo:PropTypes.instanceOf(ShopItemInfo)
    }
    render(){
        /**@type {ShopItemInfo} */
        let shopItemInfo = this.props.shopItemInfo
        if(shopItemInfo._productInfo===null)
            throw new Error('shopItemInfo._productInfo is null! try method OrderInfo.fillShopItem()')
        return <tr>
        <td><div className="imgContainer bd2"><img src={shopItemInfo._productInfo.imgUrl} /></div></td>
        <td><div className="rowProductName">{shopItemInfo._productInfo.name}</div></td>
        <td>{shopItemInfo.amount}</td>
        <td>{shopItemInfo.price}</td>
        <td>{shopItemInfo.amount*shopItemInfo.price}</td>
    </tr>
    }
}

export default class Invoice extends Component {
    static propTypes={
        orderInfo:PropTypes.any,//PropTypes.instanceOf(OrderInfo)
        IsOrderExisted:PropTypes.bool,
        // userData:PropTypes.instanceOf(OrderInfo),
        // orderAddress:PropTypes.string,
        // totalPrice:PropTypes.number,
        
    }
    render() {
        let {IsOrderExisted} = this.props;
        let {userData, orderAddress, totalPrice, orderId, sOrderStatus,shopItemList} = this.props.orderInfo
        console.log("LOG: ~ file: Invoice.jsx ~ line 28 ~ Invoice ~ render ~ shopItemList", shopItemList)
        let jsdtCreateDateTime_server = (this.props.orderInfo.jsdtCreateDateTime_server)?this.props.orderInfo.jsdtCreateDateTime_server.toLocaleString():'XXXXX';
        return (
            <div className="boxInvoice b-flexCenter">
                <table className="tableHeader">
                    <tbody>
                        <tr className={(IsOrderExisted)?'':'d-none'}>
                            <td>訂單編號： {orderId}</td>
                            <td>訂單成立日期 : {jsdtCreateDateTime_server}</td>
                        </tr>
                        <tr>
                            <td>姓名: {userData.userProfile.name}</td>
                            <td><span className={(IsOrderExisted)?'':'d-none'}>訂單狀態: {sOrderStatus}</span></td>
                        </tr>
                        <tr>
                            <td>電話: {userData.phoneNumber}</td>
                            <td></td>
                        </tr>
                        <tr>
                            <td>送貨地址: {orderAddress}</td>
                            <td>訂單總金額:NT$ {totalPrice}</td>
                        </tr>
                    </tbody>
                </table>
                <table className="tableItems bd1">
                    <thead>
                        <tr>
                            <th>圖片</th>
                            <th>名稱/規格</th>
                            <th>數量</th>
                            <th>單價</th>
                            <th>金額</th>
                        </tr>
                    </thead>
                    <tbody>
                        {shopItemList.map((/**@type {ShopItemInfo}*/shopItemInfo, index) => {
                            return <InvoiceShopItem key={index} shopItemInfo={shopItemInfo}></InvoiceShopItem>
                        }
                        )}
                        {/* <tr>
                            <td><div className="imgContainer bd2"><img src="http://fakeimg.pl/300x250/" /></div></td>
                            <td><div className="rowProductName">【火鍋好物】龍鳳蛋餃|88g克/Box盒XXXadfasdfasdfasdfasdfasdfdsafasdfsadfsdafasdfsdaf</div></td>
                            <td>15</td>
                            <td>10</td>
                            <td>150</td>
                        </tr>
                        <tr>
                            <td><div className="imgContainer bd2"><img src="http://fakeimg.pl/300x250/" /></div></td>
                            <td><div className="rowProductName">【火鍋好物】龍鳳蛋餃|88g克/Box盒XXXadfasdfasdfasdfasdfasdfdsafasdfsadfsdafasdfsdaf</div></td>
                            <td>15</td>
                            <td>10</td>
                            <td>150</td>
                        </tr>
                        <tr>
                            <td><div className="imgContainer bd2"><img src="http://fakeimg.pl/300x250/" /></div></td>
                            <td><div className="rowProductName">【火鍋好物】龍鳳蛋餃|88g克/Box盒XXXadfasdfasdfasdfasdfasdfdsafasdfsadfsdafasdfsdaf</div></td>
                            <td>15</td>
                            <td>10</td>
                            <td>150</td>
                        </tr>
                        <tr>
                            <td><div className="imgContainer bd2"><img src="http://fakeimg.pl/300x250/" /></div></td>
                            <td><div className="rowProductName">【火鍋好物】龍鳳蛋餃|88g克/Box盒XXXadfasdfasdfasdfasdfasdfdsafasdfsadfsdafasdfsdaf</div></td>
                            <td>15</td>
                            <td>10</td>
                            <td>150</td>
                        </tr> */}
                    </tbody>
                </table>
            </div>
        )
    }
}

function getDateString_correctTimeZone(inDate, formatNo) {
    // correct timezone time -> inDate.getXXXX()
    let yy = inDate.getFullYear();
    let mm = inDate.getMonth() + 1;
    let dd = inDate.getDate();
    let MM = String(mm).padStart(2, '0')
    let DD = String(dd).padStart(2, '0')    
    switch (formatNo) {
        case 1:
            return `${yy}/${MM}/${DD} `;
        default:
            return ''
    }
}