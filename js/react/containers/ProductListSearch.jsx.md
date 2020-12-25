//@ts-check
//Inside of settings.json, add the following:{ "javascript.implicitProjectConfig.checkJs": true }
import React, { Component } from "react";
import PropTypes from "prop-types";
import { bindActionCreators } from "redux";
import { connect } from "react-redux";
import ShopCart from './ShopCart.jsx'
import { countAllItems_Price } from '../reducers/shopCart.js'
import './ProductListSearch.css'
//import { HashLink as Link } from 'react-router-hash-link';
import { load_productListAsync as load_productListAsync_act } from '../actions/productList.js'
//import arryProductInfo from './ProductInfo.json'
import CategoryCard from '../components/CategoryCard.jsx'

import { Map_ProductCategory, OrderInfo, ProductInfo } from '../../../js/dataDefine/index.js'
import FirebaseMJS, { FIRESTORE_COLLECTION } from '../../firebase/FirebaseMJS.js'
import PopInvoice from '../components/PopInvoice.jsx'

/**@enum {string} */
let ENUM_screenSize = {
    phone: "phone",
    desktop: "desktop"
}
export class App extends Component {
    state = {
        /**@prop {?ENUM_screenSize} */
        screenSize: null,//ENUM_screenSize.desktop,
        /**@prop {?boolean} */
        showbtnBottomCartButton: true,
        /**@prop {import('../../../firebase/FirebaseMJS.js').groupedCategory[]} */
        arrayGroupedCategories: [],
        /**@prop {?string} */
        orderAddress: '',

        orderInfo: new OrderInfo(),
        refPopInvoice: React.createRef(),

    }
    constructor(props) {
        super(props);
        this.refShopCart = React.createRef();
        this.refShopcartBox = React.createRef();
        this.refInsideShop = React.createRef();
        this.reStickyHeader = React.createRef();

        this.refBeef = React.createRef();

        //console.log(this.arrayGroupedCategories)
    }
    // static propTypes = {
    //     prop: PropTypes.number
    // }

    initLoad = (/**@type {any}*/e) => {
        function debounce(/**@type {any}*/fun, /**@type {Number} - miliseconds}*/delay) {
            return /**@this {any} */ function (/**@type {any}*/args) {
                let that = this
                let _args = args
                clearTimeout(fun.id)
                fun.id = setTimeout(function () {
                    fun.call(that, _args)
                }, delay)
            }
        }
        // let inputb = document.getElementById('iptDebounce')
        // let debounceAjax = debounce((/**@type {any}*/e) => {
        //     //console.log(e)
        // }, 500)
        // inputb.addEventListener('keyup', function (e) {
        //     debounceAjax(e.target.value)
        // })
        //----------------
        let debounceResize = debounce((/**@type {any}*/e) => {
            displayWindowSize();
        }, 500)

        function displayWindowSize() {
            // Get width and height of the window excluding scrollbars
            var w = document.documentElement.clientWidth;
            var h = document.documentElement.clientHeight;

            // Display result inside a div element
            document.getElementById("result").innerHTML = "Width: " + w + ", " + "Height: " + h;
            let window2 = /** @type {import('../../../js/dataDefine/index.js').ExtendedWindow} */ (window);
            //console.log(window2.app.viewSize())
        }
        window.addEventListener("resize", debounceResize);
        displayWindowSize();
    }
    test1 = () => {
        // let window2 = /** @type {import('../../dataDefine/index.js').ExtendedWindow} */ (window);
        // console.log(window2.app.viewSize())
        this.setState({ screenSize: ENUM_screenSize.phone })
        //let aa = document.querySelector('.navbar-expand-lg');
        //console.log(viewSize())
    }
    test2 = (/**@type {any}*/params) => {
        //this.setState({ screenSize: ENUM_screenSize.desktop })
        //$('#exampleModal2').modal('hide')
        console.log(this.props)
    }


    componentDidUpdate(/**@type {any}*/prevProps, /**@type {any}*/prevState, /**@type {any}*/snapshot) {
        if (prevState.screenSize != this.state.screenSize) {
            switch (this.state.screenSize) {
                case ENUM_screenSize.desktop:
                    this.refShopcartBox.current.appendChild(this.refShopCart.current)
                    //this.setState({ showbtnBottomCartButton: false })
                    $('#modalShopcart').modal('hide')
                    break;
                case ENUM_screenSize.phone:
                    this.refInsideShop.current.appendChild(this.refShopCart.current)
                    //this.setState({ showbtnBottomCartButton: true })
                    break;
                default:
                    break;
            }
        }



        // if (snapshot.notifyRequired) {
        //     this.updateAndNotify();
        //   }
    }
    componentDidMount() {
        let self = this
        if (!window.app.openModalShopCart)
            window.app.openModalShopCart = () => {
                $('#modalShopcart').modal('show')
            }
        //========detect screen size
        function debounce(/**@type {any}*/fun, /**@type {Number} - miliseconds*/delay) {
            return /**@this {any}*/function (/**@type {any}*/args) {
                let that = this
                let _args = args
                clearTimeout(fun.id)
                fun.id = setTimeout(function () {
                    fun.call(that, _args)
                }, delay)
            }
        }
        let debounceResize = debounce((/**@type {any}*/e) => {
            switchWindowSize();
        }, 500)

        function switchWindowSize() {
            let window2 = /** @type {import('../../../js/dataDefine/index.js').ExtendedWindow} */ (window);
            let size = window2.app.viewSize()
            let arrayLarge = ['xl', 'lg']
            let arraySmall = ['xs', 'sm', 'md']

            if (arrayLarge.includes(size)) {
                self.setState({ screenSize: ENUM_screenSize.desktop })
                //console.log(self)
                //self.setState({ aa: 55 })
                //console.log(self.state.aa)
            }
            else {
                //console.log(self)
                self.setState({ screenSize: ENUM_screenSize.phone })
                //self.setState({ aa: 55 })
            }
            //console.log(window2.app.viewSize())
        }
        window.addEventListener("resize", debounceResize);
        switchWindowSize();

        //------------- load categories
        //let self = this
        //fix issue, load categories can not be put in constructor
        if (window.app.arrayGroupedCategories)
            self.setState({ arrayGroupedCategories: window.app.arrayGroupedCategories });
        else {
            //async methods
            window.firebase.firestore().collection(FIRESTORE_COLLECTION.ProductInfo).get()
                .then((/**@type {any}*/querySnapshot) => {
                    let arrayFilteredDbProducts = querySnapshot.docs.filter((/**@type {any}*/item) => {
                        //console.log("LOG:: App -> constructor -> item", item)
                        return item.id !== "--AutoNum--"
                    })
                    return arrayFilteredDbProducts
                })
                .then((/**@type {any[]}*/arrayFilteredDbProducts) => {
                    let arrayDbProductInfo = arrayFilteredDbProducts.map((item) => {
                        //console.log("LOG:: App -> constructor -> item", item)

                        if (item.id !== "--AutoNum--")
                            return item.data()
                    })
                    return arrayDbProductInfo
                })
                .then((/**@type {any[]}*/arrayDbProducts) => {
                    // console.log("LOG:: App -> componentDidMount -> arrayDbProducts", arrayDbProducts)
                    //let window2 = /**@type {import('../../../../js/dataDefine/index.js').ExtendedWindow}*/ (window);
        
                    window.app.arrayProductInfo = arrayDbProducts.map((item) => {
                        return Object.assign(new ProductInfo(), item)
                    })
                    self.state.arrayGroupedCategories = FirebaseMJS.getProductInfo_GroupedItems_ByCategory(window._, arrayDbProducts);

                    /**@type {import('../../firebase/FirebaseMJS.js').groupedCategory[]} */
                    let arrayGroupedCategories = self.state.arrayGroupedCategories.map((/**@type {any}*/item) => {
                        // add React.createRef()
                        item.ref = React.createRef();// React.createRef() -- for scrollTo purpose
                        return item
                    })
                    window.app.arrayGroupedCategories = arrayGroupedCategories;
                    console.log('SET window.app.arrayGroupedCategories ---->', arrayGroupedCategories)
                    self.setState({ arrayGroupedCategories: arrayGroupedCategories });
                })
        }
        setTimeout(() => {
            reloadState_orderAddress();
        }, 500);
        setTimeout(() => {
            reloadState_orderAddress();
        }, 5000);
        function reloadState_orderAddress() {
            //authUser not Exist
            if (!window.firebase.auth().currentUser) {
                console.log('skip reloadState_orderAddress() - authUser not sign in.')
                return;
            }
            console.log(11111)
            //state.orderAddress already exist
            if (self.state.orderAddress)
                return;
            console.log(22222)
            //authUser Exist
            if (window.app.userData) {
                self.setState({ orderAddress: window.app.userData.userProfile.address })
                return
            }
            console.log(33333)
            //this.state.orderAddress == null
            if (!window.app.userData) {
                let authUser = window.firebase.auth().currentUser
                let self = this
                window.firebase.firestore().collection(FIRESTORE_COLLECTION.Users).doc(authUser.uid).get()
                    .then((snapshot) => {
                        console.log("LOG: ~ file: ProductListSearch.jsx ~ line 237 ~ .then ~ self", self)
                        let data = snapshot.data()
                        if (self)//initial = undefined??
                            self.setState({ orderAddress: data.userProfile.address })

                    })
            }
        }
    }
    
    // componentWillUnmount(/**@type {any}*/e) {
    //     console.log('component will unmount--', e)
    // }
    scrollToCategory = (/**@type {any}*/e) => {
        e.preventDefault();
        // e.stopPropagation();
        //console.log(e.currentTarget)
        let dataHref = e.currentTarget.getAttribute('data-href')
        dataHref = dataHref.replace(/#/i, "");//.trimStart("#")
        /**@type {object}}} - category object*/
        let findCategory = this.state.arrayGroupedCategories.find((/**@type {Object} - category object*/item) => {
            if (item.category === dataHref)
                return item
        })
        let scrollTo_Ref = findCategory.ref

        //console.log(scrollTo_Ref)
        let scrollOffset_TopHeight = this.reStickyHeader.current.offsetHeight + window.app.navbar1.offsetHeight
        // let aa = document.querySelector('#beef');
        if (window.scroll) {
            e.preventDefault()

            //this.refBeef.current.scrollTop()
            let newTop = scrollTo_Ref.current.offsetTop - scrollOffset_TopHeight

            window.scroll({
                'behavior': 'smooth',
                'top': newTop
            })
        }

        //window.scrollTo(aa)
    }


    loadProducts = (/**@type {any}*/e) => {
        this.props.act_loadProducts();
    }
    handleInputChange = (e) => {
        // event.target 是當前的 DOM elment
        // 從 event.target.value 取得 user 剛輸入的值
        // 將 user 輸入的值更新回 state
        this.setState({ orderAddress: e.target.value });
    }
    showInvoicePopModal = (orderInfo) => {
        this.setState({ orderInfo: orderInfo }, (params) => {
            this.state.refPopInvoice.current.showModal(true);
        })
    }

    render() {
        this.state.arrayGroupedCategories
        // console.log("LOG:: App -> render -> this.arrayGroupedCategories5555", this.state.arrayGroupedCategories)
        const { productList } = this.props
        // let userAddress = '';
        // if (window.app.userData && window.app.userData.userProfile)
        //     userAddress = window.app.userData.userProfile.address


        // console.log("LOG: ~ file: ProductListSearch.jsx ~ line 273 ~ App ~ render ~ window.app.userData.userProfile", window.app.userData.userProfile)


        let arrayMap_ProductCategory = Object.entries(Map_ProductCategory);
        // console.log(arrayMap_ProductCategory);

        return (
            <main className="boxViewProductList">
                <article className="boxProductListSearch bd1">
                    <section>
                        {/* =============== HEADER ================== */}
                        <div className="boxDeliveryTimeAddress b-flexCenter inputField1 bd4">
                            <span>運送地址</span>
                            {/* <div>{this.state.orderAddress}</div> */}
                            <input type="text" placeholder="個人檔案中可以預設地址" defaultValue={this.state.orderAddress} onChange={this.handleInputChange}></input>
                        </div>
                        {/* <button onClick={this.showInvoicePopModal}>open Invoice</button> */}
                        <PopInvoice ref={this.state.refPopInvoice} orderInfo={this.state.orderInfo} dispatch={this.props.dispatch}></PopInvoice>
                        {/* ============ Category Buttons ============== */}
                        <ul className="b-flexCenter ulScrollButtons bd3" tabIndex={-1} ref={this.reStickyHeader}>
                            {/* <li className="Category">
                                    <div>牛肉</div>
                                </li> */}
                            {/*中英對照 item[0] = Object.Key 英文, item[1] = Object.value 中文  */}
                            {arrayMap_ProductCategory.map((item, index) => {
                                return <li className="btnCategory" key={index}>
                                    <div data-href={`#${item[0]}`} onClick={this.scrollToCategory}>{item[1]}</div>
                                </li>
                            })}
                        </ul>
                        {/* ============ Category - Product Cards ============== */}
                        <div className="boxCategoryCard bd2">
                            {this.state.arrayGroupedCategories.map((/**@type {any}*/item, /**@type {Number}*/index) => (
                                <CategoryCard className="listContainer row" forwardRef={this.refBeef} refShopcartBox={this.refShopcartBox} arrayGroupedCategories={item} key={index} dispatch={this.props.dispatch}></CategoryCard>
                            ))}
                        </div>
                        {/* ============== FOOTER ================ */}
                        <div className={`btn btn-primary d-lg-none bottomBuy ${this.state.showbtnBottomCartButton ? '' : 'displayNone'}`} data-toggle="modal" data-target="#modalShopcart">
                            {this.props.totalItemCount}項 --- 查看購物車 --- NT$ {this.props.AllItems_Price}
                        </div>
                        {/* Modal */}
                        <div className="modal fade " tabIndex={-1} id="modalShopcart">
                            <div className="modal-dialog">
                                <div className="modal-content">
                                    <div className="modal-header">
                                        {/* <h5 className="modal-title">Modal title</h5> */}
                                        <button type="button" className="close" data-dismiss="modal" aria-label="Close">
                                            <span aria-hidden="true">&times;</span>
                                        </button>
                                    </div>
                                    <div className="modal-body">
                                        {/* <p>Modal body text goes here.</p> */}
                                        <div ref={this.refInsideShop}>

                                        </div>
                                    </div>
                                    <div className="modal-footer">
                                        <button type="button" className="btn btn-secondary" data-dismiss="modal">Close</button>
                                        <button type="button" className="btn btn-primary">Save changes</button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </section>


                </article>
                {/* ============= 2.Aside (RIGHT) ============= */}
                <aside ref={this.refShopcartBox} className={`boxShopCart bd4 d-none d-lg-block`}>
                    <div ref={this.refShopCart}>
                        <ShopCart data-orderAddress={this.state.orderAddress} showInvoicePopModal={this.showInvoicePopModal}></ShopCart>
                    </div>
                </aside>
            </main>
        );
    }
}

const mapStateToProps = (/**@type {any}*/state) => {
    let { sumPrice, totalItemCount } = countAllItems_Price(state.shopCart.shopItemList)
    //console.log('state-> ',state)
    return {
        App_redux: state.App_redux,
        productList: state.productList,
        AllItems_Price: sumPrice,//countAllItems_Price(state.shopCart.shopItemList),
        totalItemCount: totalItemCount,
    };
};

const mapDispatchToProps = (/**@type {any}*/dispatch) => {
    //return bindActionCreators(App_redux, dispatch);
    return {
        dispatch: dispatch,
        act_loadProducts: () => {
            dispatch(load_productListAsync_act())
        }
    }
};

export default connect(mapStateToProps, mapDispatchToProps)(App);
