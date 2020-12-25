import React, { PureComponent } from "react";
import PropTypes from "prop-types";
import { bindActionCreators } from "redux";
import { connect } from "react-redux";
//import * as App_redux from "../../App_redux.js";
import * as shopCart_actions from '../actions/shopCart.js'
//import ProductCard_css from './ProductCard.css'
//import '../CategoryCard/CategoryCard.css'
import '../containers/ProductListSearch.css'

export default class ProductCard extends PureComponent {
    static propTypes = {
        id: PropTypes.string,
        dispatch: PropTypes.func,
        productInfo: PropTypes.object,
        //ref
        refShopcartBox:PropTypes.shape({ current: PropTypes.instanceOf(Element) })
        //PropTypes.instanceOf(Element)

    }
    constructor(/**@type {any}*/props) {
        super(props);
        //this.data = props.data;
        const { dispatch, refShopcartBox } = props
        this.boundActionCreators = bindActionCreators(shopCart_actions, dispatch)
        //notify parent add click happened.  need to scroll to shopcart bottom.
        
        this.refShopcartBox = refShopcartBox;

    }
    btnAddToCart = (/**@type {any}*/e) => {
        const { productInfo, dispatch } = this.props
        //console.log(this.props)
        //console.log('productInfo---> ', productInfo)
        this.boundActionCreators.add_shopCart(1, productInfo);
        setTimeout(() => {
            this.refShopcartBox.current.scroll(0,this.refShopcartBox.current.scrollHeight)
        }, 500);
        
    }
    render() {
        const { productInfo } = this.props
        return (
            <React.Fragment>
                <li className="bd1 productCard">
                    <figure>
                        <figcaption className="b-textCenter">
                            <div >{productInfo.name}</div>
                        </figcaption>
                        <div className="imgContainer bd2">
                            <img className="imgFood"
                                src={productInfo.imgUrl}
                                // src={imgProduct.default}
                                alt="image not found"
                            />
                        </div>
                        

                    </figure>
                    <div className="priceBottom bd3">
                        <div >NT$ {productInfo.price}</div>
                        <button className="addButton" onClick={this.btnAddToCart}>
                            <div>+</div>
                        </button>
                    </div>
                    <hr className="hrDot" />
                </li>
                
            </React.Fragment>
        );
    }
}
