//@ts-check
//Inside of settings.json, add the following:{ "javascript.implicitProjectConfig.checkJs": true }
import React, { PureComponent } from 'react'
import BootstrapTable from 'react-bootstrap-table-next';
//import OrderList_css from './OrderList.css'
import Invoice from '../components/Invoice.jsx'
import PropTypes from 'prop-types'
import { OrderInfo, UserData, ShopItemInfo } from '../../../js/dataDefine/index.js'
// import { times } from 'lodash';

/**
 * 
 * @param {*} cell 
 * @param {*} row 
 * @param {*} rowIndex 
 * @param {*} formatExtraData 
 */
function dateFormatter1(cell, row, rowIndex, formatExtraData) {
    /**@type {Date} */
    let date1 = cell;
    let sDate = date1.toLocaleDateString()
    let sTime = date1.toLocaleTimeString()
    //console.log('cell--->',date1)
    return (
        <div>
            <div>{sDate}</div>
            <div>{sTime}</div>
        </div>
    );
}
const columns = [
    {
        dataField: 'jsdtCreateDateTime_server',
        text: '訂單日期',
        formatter: dateFormatter1
    }, {
        dataField: 'orderId',
        text: '訂單編號'
    }, {
        dataField: 'orderProductName',
        text: '名稱/規格'
    }, {
        dataField: 'totalPrice',
        text: '訂單總金額'
    }, {
        dataField: 'sOrderStatus',
        text: '訂單狀態'
    }];

const rowStyle = { backgroundColor: '#c8e6c9' };
// const products = [
//     {
//         id: 0,
//         name: 'name xxx 1',
//         price: 500
//     },
//     {
//         id: 1,
//         name: 'name xxx 2',
//         price: 250
//     }
// ]
const expandRow = {
    onlyOneExpanding: true,
    className: 'expandingRowBackground',
    renderer: (/**@type {any}*/row) => {
        let orderInfo = row
        // console.log("LOG: ~ file: ViewOrdersItem.jsx ~ line 70 ~ row.userData", row)
        // /**@type {import('../../dataDefine/index.js').UserData} */
        // let userData = new UserData();
        // userData = Object.assign(userData, row.userData)
        
        // //let totalPrice = row.totalPrice;
        // let {orderAddress,totalPrice} = row

        // /**@type {import('../../../dataDefine/index.js').UserProfile} */
        // let userInfo = new UserProfile();
        // userInfo = Object.assign(userInfo, row.userProfile)
        // let sendAddress = userInfo.address//.getSelectedAddress();
        return <div className="expandedInvoice">
            <Invoice orderInfo={orderInfo} IsOrderExisted={true}></Invoice>
        </div>
        // <Invoice orderInfo={orderInfo}></Invoice>
            
        // <div>
        //     <div>
        //         <label>姓名:</label>
        //         <span>{userData.userProfile.name}</span>
        //     </div>
        //     <div>
        //         <label>送貨地址:</label>
        //         <span>{orderAddress}</span>
        //     </div>
        //     <div>
        //         <label>電話:</label>
        //         <span>{userData.phoneNumber}</span>
        //     </div>
        //     <div>
        //         <label>訂單金額總計:</label>
        //         <span>{totalPrice}</span>
        //     </div>
        //     <div className="titleProductList">
        //         商品明細
        //     </div>
        //     <div>
        //         {row.shopItemList.map(function (/**@type {ShopItemInfo}*/item, /**@type {number}}*/index) {
        //             return <div key={index} className="productBox bd4">
        //                 <div>
        //                     <div className="imgContainer">
        //                         <img src={item._productInfo.imgUrl} alt="not found"></img>
        //                     </div>
        //                 </div>

        //                 <div className="boxProductName">
        //                     <span>{item._productInfo.name}</span>
        //                 </div>
        //                 <div className="boxProductUnitPrice">
        //                     <label>單價:</label>
        //                     <span>{item._productInfo.price}</span>
        //                 </div>
        //                 <div className="boxProductAmount">
        //                     <label>數量:</label>
        //                     <span>{item.amount}</span>
        //                 </div>
        //                 <div className="boxProductCountPrice">
        //                     <label>金額:</label>
        //                     <span>{item.amount * item._productInfo.price}</span>
        //                 </div>

        //             </div>
        //         })}
        //     </div>
        // </div>
    }
    ,
    parentClassName: (/**@type {Boolean}*/isExpanded, /**@type {any}*/row, /**@type {Number}*/rowIndex) => {
        return 'parentExpandFoo'
        // if (rowIndex > 2) return 'parent-expand-foo';
        // return 'parent-expand-bar';
    },
    showExpandColumn: true,
    expandHeaderColumnRenderer: (/**@type {any}*/{ isAnyExpands }) => {
        if (isAnyExpands) {
            return <b>-</b>;
        }
        return <b>+</b>;
    },
    expandColumnRenderer: (/**@type {any}*/{ expanded }) => {
        if (expanded) {
            return (
                <b>-</b>
            );
        }
        return (
            <b>...</b>
        );
    },

};

export default class ViewOrdersItem extends PureComponent {
    
    static propTypes = {
        //OrderInfo[]
        ['data-arrayOrder']: PropTypes.arrayOf(PropTypes.instanceOf(OrderInfo))
    }
    static defaultProps = {
        //data: []
    }
    render() {
        console.log('ViewOrdersItem.props-->', this.props)
        return (
            <div>
                <BootstrapTable
                    keyField='orderId'
                    data={this.props['data-arrayOrder']}
                    columns={columns}
                    expandRow={expandRow}
                />
            </div>
        )
    }
}
