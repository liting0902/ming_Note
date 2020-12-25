//@ts-check
//Inside of settings.json, add the following:{ "javascript.implicitProjectConfig.checkJs": true }

import FirebaseMJS, {getDate_From_Firestore_TimeStamp} from '../firebase/FirebaseMJS.js';

/**
 * @class
 * @desc ming1 project ShopItem.jsx
 */
export class ShopItemInfo {
    /**@type {(ProductInfo|null|undefined)} */
    _productInfo;
    /**@type {string} */
    productId = 'XXXXXXXXXXXXX'
    //productInfo={productId:'mmmmmmmmmm'}
    /**@type {number} */
    amount = 0
    /**@type {number} */
    price = 0
    //productInfo
    /**@property {string}*/
    get productName() {
        if (!this._productInfo)
            return ''
        return this._productInfo.name
    }
    /**
     * fill _productInfo - from given product list
     * @param {ProductInfo[]} arrayProducts 
     */
    fill_ProductInfo_ByProductArray(arrayProducts) {
        if (arrayProducts.length === 0) {
            this.productInfo = null
            return;
        }
        // console.log(this.productId)
        //console.log(arrayProducts)
        let findProductInfo = arrayProducts.find((item) => {
            return item.productId === this.productId
        })
        //console.log('find---', findProductInfo)
        //if result not found, force to clear this._productInfo
        this.productInfo = null
        if (findProductInfo)
            this.productInfo = findProductInfo
            
        //console.log(this._productInfo)
    }
    /**@type {ProductInfo} - set this ShopItem's ProductInfo*/
    set productInfo(value) {
        this._productInfo = value
        if (value) {
            this.productId = value.productId
            this.price = value.price
        }

    }
    /**@type {(ProductInfo|null|undefined)} - return this ShopItem's ProductInfo*/
    get productInfo() {
        return this._productInfo
    }
}
/**@enum {string} */
export const ENUM_ProductCategory = {
    // soybean:"soybean",
    // duck:"duck",
    // chicken:"chicken",
    // beef:"beef",
    // pork:"pork",
    // others:"others",

    mainCourse:"mainCourse",
    meats:"meats",
    soups:"soups",
    stirFired:"stirFired",
    coldPlates:"coldPlates"
}
export const Map_ProductCategory = {
    // soybean:"豆類",
    // duck:"鴨肉類",
    // chicken:"雞肉類",
    // beef:"牛肉類",
    // pork:"豬肉類",
    // others:"其他類別",

    /**@type {string} */
    mainCourse:"主菜",
    /**@type {string} */
    meats:"肉類",
    /**@type {string} */
    soups:"湯",
    /**@type {string} */
    stirFired:"快炒",
    /**@type {string} */
    coldPlates:"冷盤"
}
/**
 * @class
 * @description firestore.Products/ProductList.jsx/ProductCard.jsx
 * */
export class ProductInfo {
    /**@prop {string} */
    productId = "null" //aaaaa-1
    /**@prop {string} */
    name = "null" //"白斬雞"
    /**@prop {number} */
    price = 0 //140
    /**@prop {?firebase.firestore.Timestamp} */
    fstsAddDateTime_server = null

    /**@prop {string} */
    category = "null"
    /**@prop {string} */
    tag = "#"

    /**@prop {string} */
    imgUrl = "./"
    /**@prop {string} */
    imgFileName = "default.png" //白斬雞.jpg
    /**@prop {?number} */
    autoNum = null
    /**@prop {?boolean} - in use or not */
    isActived = false
}

/**
 * @class
 * @description OrderInfo.OrderStatus
 */
export class OrderStatus {
    /**@type {boolean} */
    isPaid = false;
    /**@type {boolean} */
    isCompleted = false;
    /**@type {boolean} */
    isCanceled = false;
    /**@type {boolean} */
    isDelivery = false;
}
/**
 * @class
 * @description - OrderInfo.OrderLog by LogType
 */
export class OrderLog {
    /**@type {?firebase.firestore.Timestamp} */
    fstsLogDateTime_server = null
    /**@type {LogType} */
    logType = ''
    /**@type {string} */
    remarks = ''
}

/**
 * @enum {string}
 * @desc firestore.OrderInfo.LogType
 * */
export const LogType = {
    NewOrder: "NewOrder",
    PaidMoney: "PaidMoney",
    CancelOrder: "CancelOrder",
    DeliveryOrder: "DeliveryOrder",
    CompleteOrder: "CompleteOrder"
}

/** 
 * @class
 * @description - from firestore.OrderInfo 
 */export class OrderInfo {
    /**@type {string} - firestore.Users.uid*/
    userId = "xxxxxxx"
    /**@type {(?firebase.firestore.Timestamp)} - order create datetime from firestore*/
    fstsCreateDateTime_server = null
    /**@type {?Date} - server time conver to client js datetime*/ 
    get jsdtCreateDateTime_server(){
        return (this.fstsCreateDateTime_server == null) ? null : this.fstsCreateDateTime_server.toDate();        
    }
    /**@property {string} */
    get sCreateDateTime() {
        return getDisplayTime(this.jsdtCreateDateTime_server)
    }
    /**@property {string} */
    get sOrderStatus() {
        let rtnStatus = "訂單成立,尚未付款"
        if (this.orderStatus.isPaid === true)
            rtnStatus = "已付款"
        if (this.orderStatus.isDelivery === true)
            rtnStatus = "已寄出"
        if (this.orderStatus.isCanceled === true)
            rtnStatus = "已取消"
        if (this.orderStatus.isCompleted === true)
            rtnStatus = "訂單完成(已送達)"
        // orderStatus = {
        //     isPaid: false,
        //     isCompleted: false,
        //     isCanceled: false,
        //     isDelivery: false
        // }
        return rtnStatus
    }
    /**@type {string} */
    orderId = "XXXXXXXX" // odke-20200717-0001
    /**@type {string} */
    orderAddress = 'XXXXXX'
    /**@type {ShopItemInfo[]} */
    shopItemList = []
    /**@property {string} - first shop product name with /.... */
    get orderProductName() {
        if (this.shopItemList.length == 0)
            return ''
        let shopItem_0 = this.shopItemList[0]

        if (this.shopItemList.length >= 2)
            return `${shopItem_0.productName} / .....`
        else
            return `${shopItem_0.productName}`

    }
    /**
     * get listProductId
     * @returns {string[]}
     */
    getShopItems_ProdId = function () {
        let list_productId = this.shopItemList.map((item) => {
            return item.productId
        })
        return list_productId
        //console.log(arr)
        //console.log(this.shopItemList)
    }
    /**
     * fill shopItem detail information
     * @param {ProductInfo[]} arrayProductInfo - distinct product list
     */
    fillShopItems(arrayProductInfo) {
        //console.log(arrayProductInfo)
        //console.log(this.shopItemList)
        this.totalPrice = 0;
        this.shopItemList = this.shopItemList.map((item) => {
            // convert to ShopItemInfo()
            let shopiteminfo = item
            if (item instanceof ShopItemInfo == false) {
                shopiteminfo = Object.assign(new ShopItemInfo(), item);
            }
            // fill shopiteminfo._productInfo
            shopiteminfo.fill_ProductInfo_ByProductArray(arrayProductInfo)
            // count OrderInfo.totalPrice
            this.totalPrice += (shopiteminfo.amount * shopiteminfo.price)
            //console.log(shopiteminfo)
            return shopiteminfo
        })
        //console.log(this.shopItemList)
    }
    /**@type {number} */
    totalPrice = 0
    /**@type {OrderStatus} */
    orderStatus = {
        isPaid: false,
        isCompleted: false,
        isCanceled: false,
        isDelivery: false
    }
    /**@type {OrderLog[]} */
    orderLog = []
    // {
    //     fstsLogDateTime_server: null,
    //     logType: '',
    //     remarks: ''
    // }

    /**@prop {UserData} */
    userData = new UserData();
    // {
    //     "dispalyName": null,
    //     "emailVerified": true,
    //     "userProfile": {
    //         "name": "HH",
    //         "address": "台北市萬華區寧波街11號XXXXXXXXXX"
    //     },
    //     "phoneNumber": "+886926923281",
    //     "uid": "s5hWIA4pdQN8NGjBmPekJoOodBi2",
    //     "photoURL": "https://lh3.googleusercontent.com/a-/AOh14GgufKuvpe34oq9gzvV9CppvPhWjISRrNeF4wbdOkw=s96-c",
    //     "providerId": "google.com",
    //     "email": "tristan829@gmail.com"
    // }

    
    convertDbFields(){
        if(!this.fstsCreateDateTime_server.toDate){
            function toDate2(){
                return getDate_From_Firestore_TimeStamp(this)
                
            }
            this.fstsCreateDateTime_server.toDate = toDate2;
        }
    }
    static getOrderInfo_FromDbFormat(db_orderInfo){
        let newOrderInfo = new OrderInfo();
        newOrderInfo = Object.assign(newOrderInfo, db_orderInfo)
        newOrderInfo.convertDbFields();
        return newOrderInfo
    }
}
/**@class 
 * @desc firestore.Users
*/
export class UserData{
    /**@prop {string} */
    dispalyName= null
    /**@prop {boolean} */
    emailVerified= false
    /**@prop {UserProfile} */
    userProfile= new UserProfile();
    // {
    //     name: "HH",
    //     address: "台北市萬華區寧波街11號XXXXXXXXXX"
    // }
    /**@prop {string} */
    phoneNumber= ""
    /**@prop {string} */
    uid= ""
    /**@prop {string} */
    photoURL= ""
    /**@prop {string} */
    //providerId= ""
    get providerId() {
        if(this.listProviderId.length === 0)
            return null;
        //find google providerId
        let findGoogle = this.listProviderId.find((item) => {
        
            if(item === UserData.ENUM_ProviderType.google)//'google.com')
                return true // return 'google.com'
        })
        if(findGoogle)// type string
            return UserData.ENUM_ProviderType.google//'google.com'
        else//undefined
            return UserData.ENUM_ProviderType.password//'password'
    }
    set providerId(value) {
        this.listProviderId = [value]
    }
    /**@prop {string} */
    email= ""
    /**
     * @function
     * @param {Array} AuthUserProviderData 
     */
    static ENUM_ProviderType={
        google:"google.com",
        password:"password"
    }
    static getListProviderId_ByAuthUserProviderData(AuthUserProviderData){
        let arrayData = AuthUserProviderData;
        let listProviderId = arrayData.map((item) => {
            return item.providerId;
        })
        return listProviderId;
    }
    /**@prop {string[]} */
    listProviderId = []
    
}
/**
 * @class
 * @desc firestore.Users.userProfile
 */
export class UserProfile {
    /**@type {string} */
    uid = "XXXXXXX"
    /**@type {string} */
    sendEmail = "XXXXXXX"
    /**@type {string} */
    name = "XXXXXXX"
    /**@type {string} */
    phoneNumber = "XXXXXXX"
    /**@type {string} */
    address = "XXXXXXX"
    // /**@type {string} */
    // address1 = "XXXXXXX"
    // /**@type {string} */
    // address2 = "XXXXXXX"
    // /**@type {string} */
    // address3 = "XXXXXXX"
    // /**@type {(1|2|3)} */
    // addressSelectNo = 1
    /**@function */
    // getSelectedAddress() {
    //     let propName = `address${this.addressSelectNo}`
    //     return this[propName]
    // }
}

/**
 * @typedef {Object} GlobalFlag
 * @prop {boolean} [__myFlag__]
 * @prop {firebase} [firebase]
 * @prop {import('../firebase/FirebaseMJS.js').default} [Firebase]
 * @prop {object} [app]
 * @typedef {Window & GlobalFlag} ExtendedWindow
 */

const fakeProductInfo1 = new ProductInfo()
fakeProductInfo1.productId = "ajnck"
fakeProductInfo1.name = "胖老爹美式炸雞,胖老爹美式炸雞,胖老爹美式炸雞,胖老爹美式炸雞"
fakeProductInfo1.price = 140
fakeProductInfo1.imgFileName = "白斬雞.jpg"
//fakeProductInfo1.imgUrl = "images/四季豆-d83dcdceb072b6cb1e7eb3cff1d5c656.jpg"
const fakeProductInfo2 = new ProductInfo()
fakeProductInfo2.productId = "okdsf"
fakeProductInfo2.name = "蔥爆牛肉"
fakeProductInfo2.price = 550
fakeProductInfo2.imgFileName = "蔥爆牛肉.jpg"
//fakeProductInfo2.imgUrl = "images/四季豆-d83dcdceb072b6cb1e7eb3cff1d5c656.jpg"
const fakeProductInfo3 = new ProductInfo()
fakeProductInfo3.productId = "fdsafe"
fakeProductInfo3.name = "蔥爆牛肉222"
fakeProductInfo3.price = 220
fakeProductInfo3.imgFileName = "四季豆.jpg"
//fakeProductInfo3.imgUrl = "images/蔥爆牛肉-c5015d40ca191cbb33002457f64a7cf7.jpg"
export {
    fakeProductInfo1,
    fakeProductInfo2,
    fakeProductInfo3
}

/**
 * 
 * @param {?Date} inDate 
 * @returns {string}
 */
function getDisplayTime(inDate) {
    if (!inDate)
        return 'noTime'
    else
        return `${inDate.getFullYear()}/${inDate.getMonth()+1}/${inDate.getDate()} ${inDate.getHours()}:${inDate.getMinutes()}:${inDate.getSeconds()}`
}