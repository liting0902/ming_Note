//@ts-check

import { UserData } from '../../js/dataDefine/index.js'
import {FIRESTORE_COLLECTION} from '../../js/firebase/FirebaseMJS'

/**@class */
class ProxyFormData {
    /**@prop {string} */
    iptAccount = ""
    /**@prop {string} */
    iptEmail = ""
    /**@prop {string} */
    iptName = ""
    /**@prop {string} */
    iptPhone = ""
    /**@prop {string} */
    iptAddress = ""
    /**@type {import('../../js/dataDefine/index.js').UserData} */
    userData = null//{}
    static userProfileConverter = {
        toFirestore: function (/**@type {ProxyFormData} */ proxyFormData) {
            return {
                // only write back key-userProfile
                userProfile: {
                    //sendEmail: proxyFormData.iptEmail,
                    name: proxyFormData.iptName,
                    //phoneNumber: proxyFormData.iptPhone,
                    address: proxyFormData.iptAddress,
                    // address1: proxyFormData.iptAddress1,
                    // address2: proxyFormData.iptAddress2,
                    // address3: proxyFormData.iptAddress3,
                    //selectAddress: proxyFormData.selectAddress,
                }
            }
        },
        fromFirestore: function (/**@type {any}} */snapshot, /**@type {any}}*/options) {
            
            const data = snapshot.data(options);
            
            let proxyFormData = new ProxyFormData();
            proxyFormData.userData = Object.assign(new UserData(),data)
            //load email + phone + userProfile
            proxyFormData.iptEmail = data.email;
            proxyFormData.iptPhone = defaultValue.iptPhone
            if (data.phoneNumber)
                proxyFormData.iptPhone = data.phoneNumber;
            if (data.userProfile) {
                //proxyFormData.iptEmail = data.userProfile.sendEmail
                proxyFormData.iptName = data.userProfile.name
                //proxyFormData.iptPhone = data.userProfile.phoneNumber
                proxyFormData.iptAddress = data.userProfile.address
                // proxyFormData.iptAddress1 = data.userProfile.address1
                // proxyFormData.iptAddress2 = data.userProfile.address2
                // proxyFormData.iptAddress3 = data.userProfile.address3
                // proxyFormData.selectAddress = data.userProfile.selectAddress
            }

            return proxyFormData
        }
    }
}
/**@enum {string}*/
export const ENUM_PhoneStatus = {
    noPhoneNubmer: "noPhoneNubmer",
    inputPhoneFailed: "inputPhoneFailed",
    inputVerifyCode: "inputVerifyCode",
    verifyFailed: "verifyFailed",
    verifiedOK: "verifiedOK",
}
/**@enum {string}*/
export const ENUM_LoginMethod = {
    google: "google",
    email: "email"
}
const defaultValue = {
    iptPhone: '+886',
    iptPhoneVerifyCode: '',
}

export default class cusModalUserProfile extends HTMLElement {
    /**
     * 
     * @param {HTMLElement} templateContent 
     * @param {import('../cusModalLogin3/cusModalLogin.js').plugins} plugins 
     */
    constructor(templateContent, plugins) {
        super();
        /** {Swal , Email_ResendPassword(...)} */
        this.plugins = plugins
        //this.plugins.Email_ResendPassword()
        
        // assign firebase
        this.firebase = null;
        this.db = null
        if (firebase)
            this.setFirebase(firebase)
            
        if (!templateContent)
            throw new Error('error, arg - templateContent not defined!')
        
        /**@typedef {sweetalert2} Swal */
        if (!Swal)
            throw new Error('error, please implement sweetalert2 first!')
        
        this.appendTemplate(templateContent);
        
        let proxyUI = {
            /**@prop {ENUM_PhoneStatus}*/
            enumPhoneStatus: ENUM_PhoneStatus.noPhoneNubmer,
            /**@prop {ENUM_LoginMethod}*/
            enumLoginMethod: ENUM_LoginMethod.google,
            divInputPhoneFailed_Text: '輸入電話失敗',
            divVerifyFailed_Text: '驗證失敗',
            divAccount_Show: false,
            isEmailVerified: true,
            isAccountOfGoogle: true, //not show btnResendPassword
        }
        let self = this
        this.proxyUI = new Proxy(proxyUI, {
            get: function (/**@type {any}*/ target, prop) {
                return target[prop]
            },
            set: function (/**@type {any}*/target, prop, value) {
                switch (prop) {
                    case 'enumPhoneStatus':
                        switch (value) {
                            case ENUM_PhoneStatus.noPhoneNubmer:
                                self.iptPhone.setDisabled(false)
                                self.btnVerifyNumber.setShow(true)
                                self.btnVerifyNumber.setDisabled(false)
                                self.btnDeleteNumber.setShow(false)
                                self.divInputPhoneFailed.setShow(false)
                                //-------divVerifyCode
                                self.divVerifyCode.setShow(false)
                                self.iptPhoneVerifyCode.value = defaultValue.iptPhoneVerifyCode
                                // self.iptPhoneVerifyCode.setDisabled(true)
                                // self.btnSendVerifyCode.setDisabled(true)
                                //-------divVerifyFailed
                                self.divVerifyFailed.setShow(false)
                                break;
                            case ENUM_PhoneStatus.inputPhoneFailed:
                                self.iptPhone.setDisabled(false)
                                self.btnVerifyNumber.setShow(true)
                                self.btnVerifyNumber.setDisabled(false)
                                self.btnDeleteNumber.setShow(false)
                                self.divInputPhoneFailed.setShow(true)
                                //-------divVerifyCode
                                self.divVerifyCode.setShow(false)
                                self.iptPhoneVerifyCode.value = defaultValue.iptPhoneVerifyCode
                                // self.iptPhoneVerifyCode.setDisabled(true)
                                // self.btnSendVerifyCode.setDisabled(true)
                                //-------divVerifyFailed
                                self.divVerifyFailed.setShow(false)
                                break;
                            case ENUM_PhoneStatus.inputVerifyCode:
                                self.iptPhone.setDisabled(true)
                                self.btnVerifyNumber.setShow(true)
                                self.btnVerifyNumber.setDisabled(true)
                                self.btnDeleteNumber.setShow(false)
                                self.divInputPhoneFailed.setShow(false)
                                //-------divVerifyCode
                                self.divVerifyCode.setShow(true)
                                self.iptPhoneVerifyCode.value = defaultValue.iptPhoneVerifyCode
                                self.iptPhoneVerifyCode.setDisabled(false)
                                self.btnSendVerifyCode.setDisabled(false)
                                //-------divVerifyFailed
                                self.divVerifyFailed.setShow(false)
                                break;
                            case ENUM_PhoneStatus.verifyFailed:
                                self.iptPhone.setDisabled(true)
                                self.btnVerifyNumber.setShow(true)
                                self.btnVerifyNumber.setDisabled(true)
                                self.btnDeleteNumber.setShow(false)
                                self.divInputPhoneFailed.setShow(false)
                                //-------divVerifyCode
                                self.divVerifyCode.setShow(true)
                                //self.iptPhoneVerifyCode.value = defaultValue.iptPhoneVerifyCode
                                self.iptPhoneVerifyCode.setDisabled(false)
                                self.btnSendVerifyCode.setDisabled(false)
                                //-------divVerifyFailed
                                self.divVerifyFailed.setShow(true)
                                break;
                            case ENUM_PhoneStatus.verifiedOK:
                                self.iptPhone.setDisabled(true)
                                self.btnVerifyNumber.setShow(false)
                                //self.btnVerifyNumber.setDisabled(true)
                                self.btnDeleteNumber.setShow(true)
                                self.divInputPhoneFailed.setShow(false)
                                //-------divVerifyCode
                                self.divVerifyCode.setShow(false)
                                self.iptPhoneVerifyCode.value = defaultValue.iptPhoneVerifyCode
                                // self.iptPhoneVerifyCode.setDisabled(true)
                                // self.btnSendVerifyCode.setDisabled(true)
                                //-------divVerifyFailed
                                self.divVerifyFailed.setShow(false)
                                break;
                            default:
                                break;
                        }
                        break;

                    case 'enumLoginMethod':
                        /**@type {HTMLElement} */
                        let iFaEnvelope = self.querySelector('#iFaEnvelope');
                        /**@type {HTMLElement} */
                        let iFaGoogle = self.querySelector('#iFaGoogle');
                        switch (value) {
                            case ENUM_LoginMethod.google:
                                iFaEnvelope.classList.add('d-none');
                                iFaGoogle.classList.remove('d-none');
                                break;
                            case ENUM_LoginMethod.email:
                                iFaEnvelope.classList.remove('d-none');
                                iFaGoogle.classList.add('d-none');
                                break;
                            default:
                                break;
                        }
                        break;
                    case "divInputPhoneFailed_Text":
                        self.divInputPhoneFailed.innerHTML = value;
                        //document.querySelector()
                        break;
                    case "divVerifyFailed_Text":
                        self.divVerifyFailed.innerHTML = value;
                        break;
                    case 'isEmailVerified':
                        /**@type {HTMLInputElement} */
                        let uiCkboxEmailVerified = self.querySelector('#uiCkboxEmailVerified');
                        uiCkboxEmailVerified.checked = value;
                        break;
                    case 'isAccountOfGoogle':
                        /**@type {HTMLInputElement} */
                        let uiCkboxIsAccountOfGoogle = self.querySelector('#uiCkboxIsAccountOfGoogle');
                        uiCkboxIsAccountOfGoogle.checked = value;
                        break;
                    case 'divAccount_Show':
                        let divAccount = self.querySelector('#formUserProfile #divAccount')
                        if (value === false)
                            divAccount.classList.add('d-none')
                        else
                            divAccount.classList.remove('d-none')
                        break;
                        // ...
                    default:
                        /**@type {string} - UI input value */
                        target[prop] = value;
                        break;
                }

                target[prop] = value;
                return true
            }
        })
        this.proxyUI.enumPhoneStatus = ENUM_PhoneStatus.verifiedOK;

    }
    connectedCallback() {
        
        //-----------Phone Verify renderRecaptcha
        this.recaptchaVerifier = null;
        this.verificationId = null;
        //this.renderRecaptcha('btnSendVerifyCode')
        this.renderRecaptcha('btnVerifyNumber')
        let self = this
        let authUser = this.firebase.auth().currentUser
        //set emailVerified UI -- warning icon + btnResendVerify show
        this.proxyUI.isEmailVerified = authUser.emailVerified;
        

        //check if google account
        /**@type {any[]}} */
        let findGoogleProvider = authUser.providerData.filter((/**@type {any}*/item) => {
            return item.providerId==='google.com'
        })
        this.proxyUI.isAccountOfGoogle = (findGoogleProvider.length>0)?this.proxyUI.isAccountOfGoogle = true:this.proxyUI.isAccountOfGoogle = false;
        
        this.btnSendVerifyCode.addEventListener('click', (e) => {
            e.preventDefault();
            console.log(this.verificationId)
            let sVerifyCode = self.iptPhoneVerifyCode.value
            let credential_phone = this.firebase.auth.PhoneAuthProvider.credential(this.verificationId, sVerifyCode);
            authUser.linkWithCredential(credential_phone)
                .then((/**@type {any}*/e) => {
                    self.proxyUI.enumPhoneStatus = ENUM_PhoneStatus.verifiedOK;
                    self.saveUserData({
                        phoneNumber: authUser.phoneNumber
                    })
                    self.proxyFormData.iptPhone = authUser.phoneNumber
                })
                .catch((/**@type {any}*/error) => {
                    let {
                        errCodeZHTW,
                        errMessageZHTW
                    } = self.getErrorMessageZHTW(error.code, error.message)
                    self.proxyUI.enumPhoneStatus = ENUM_PhoneStatus.verifyFailed
                    self.proxyUI.divVerifyFailed_Text = `${errCodeZHTW}, ${errMessageZHTW}`
                })
        })
        this.btnDeleteNumber.addEventListener('click', (e) => {
            let self = this
            console.log("LOG:: cusModalUserProfile -> connectedCallback -> authUser", authUser)
            authUser.unlink('phone').then(function () {
                // Auth provider unlinked from account
                // ...
                console.log('unlink phone OK!')
                self.proxyUI.enumPhoneStatus = ENUM_PhoneStatus.noPhoneNubmer
                console.log("LOG:: cusModalUserProfile -> connectedCallback -> authUser", authUser)
                self.saveUserData({
                    phoneNumber: null
                })
                self.proxyFormData.iptPhone = defaultValue.iptPhone

            }).catch(function (/**@type {any}*/error) {
                console.log("error -- LOG:: cusModalUserProfile -> connectedCallback -> error", error)
                // An error happened
                // ...
            });
        })
        // setTimeout(() => {
        //     this.renderRecaptcha('btnSendVerifyCode')
        // }, 2000);
        let btnResendEmailVerify = this.querySelector('#btnResendEmailVerify');
        btnResendEmailVerify.addEventListener('click', (e) => {
            let self = this
            authUser.sendEmailVerification().then(function () {
                // Email sent.
                self.plugins.Swal.fire('Email認證信已寄出', '請到信箱收信，並按下確認連結', 'success')
            }).catch(function (/**@type {any}*/error) {
                // An error happened.
                self.plugins.Swal.fire('發生錯誤', `${error.code},${error.message}`, 'error')
                console.log(error)
            });
        })
        this.btnResendPassword = this.querySelector('#btnResendPassword');
        this.btnResendPassword.addEventListener('click', () => {
            let self = this
            let emailAddress = this.proxyFormData.iptEmail;
            this.plugins.Email_ResendPassword(
                emailAddress,
                this.firebase,
                this.plugins.Swal,
                () => {
                    self.showModal(false)
                    //self.proxyUI.switchPage = ENUM_switchPage.radioSignin;
                }
            );
        })

        // let btnModifyPassword = this.querySelector('#btnModifyPassword');
        // btnModifyPassword.addEventListener('click', (e) => {
        //     let self = this
        //     let html = `
        //     <form id="formModifyPassword">
        //         <div>
        //             <span class="titleWhite">輸入舊密碼</span>
        //             <input id="swal-input1" type="password" class="swal2-input inputCheckValid" placeholder="英文字母或數字，至少8位數" pattern="^[0-9a-zA-Z]{8,}$" required autocomplete="current-password">
        //             <span></span>
        //         </div>
        //         <div>
        //             <span class="titleWhite">輸入新密碼</span>
        //             <input id="swal-input2" type="password" class="swal2-input inputCheckValid" placeholder="英文字母或數字，至少8位數" pattern="^[0-9a-zA-Z]{8,}$" required>
        //             <span></span>
        //         </div>
        //         <div>
        //             <span class="titleWhite">再次輸入新密碼</span>
        //             <input id="swal-input3" type="password" class="swal2-input inputCheckValid" placeholder="英文字母或數字，至少8位數" pattern="^[0-9a-zA-Z]{8,}$" required>
        //             <span></span>
        //         </div>
        //         <button id="btnValid" class="b-displayNone">Check Form Valid</button>
        //     </form>
        //     `;

        //     // html= '<input id="swal-input1" class="swal2-input">' +
        //     //     '<input id="swal-input2" class="swal2-input">',
        //     Swal.fire({
        //             title: '修改密碼',
        //             html: html,
        //             customClass: {
        //                 confirmButton: 'swalbtn'
        //             },
        //             // inputAttributes: {
        //             //     maxlength: 10,
        //             //     autocapitalize: 'off',
        //             //     autocorrect: 'on'
        //             // },
        //             focusConfirm: false,
        //             preConfirm: (e) => {
        //                 console.log('---------preConfirm-----------')
        //                 console.log('---------HTML5 validation-----------')
        //                 let btnValid = document.querySelector('#btnValid')
        //                 btnValid.click();

        //                 let ipt1 = document.querySelector('#formModifyPassword #swal-input1')
        //                 let ipt2 = document.querySelector('#formModifyPassword #swal-input2')
        //                 let ipt3 = document.querySelector('#formModifyPassword #swal-input3')
        //                 let isHTML5Valid = true;
        //                 if (ipt1.checkValidity() === false ||
        //                     ipt2.checkValidity() === false ||
        //                     ipt3.checkValidity() === false) {
        //                     Swal.showValidationMessage('');
        //                     return false
        //                 }
        //                 //-------other check
        //                 console.log('---------other check-----------')
        //                 if (ipt2.value !== ipt3.value) {
        //                     Swal.showValidationMessage('兩次輸入的新密碼必須一致');
        //                 }
        //                 // let form = document.querySelector('#formModifyPassword')
        //                 // console.log("LOG:: cusModalUserProfile -> connectedCallback -> form", form)
        //                 // let aa = document.querySelector('#formModifyPassword #swal-input1').checkValidity();
        //                 // console.log("LOG:: cusModalUserProfile -> connectedCallback -> aa", aa)
        //                 //Swal.showValidationMessage(''); //stop confirm process
        //                 return [
        //                     ipt1.value,
        //                     ipt2.value,
        //                     ipt3.value
        //                 ]
        //             },
        //             // onOpen:function(e){
        //             //     console.log('-------open------------')
        //             // },
        //             didRender: function (e) {
        //                 //-------- only for html5 form validation
        //                 let formModifyPassword = document.querySelector('#formModifyPassword');
        //                 formModifyPassword.addEventListener('submit', (e) => {
        //                     e.preventDefault()
        //                 })
        //             },
        //             showCancelButton: true,
        //             // inputValidator: (value) => {
        //             //     return 111
        //             //     console.log("LOG:: cusModalUserProfile -> connectedCallback -> value", value)
        //             //     if (!value) {
        //             //         return 'You need to write something!'
        //             //     }
        //             // }
        //         })
        //         .then((result) => {
        //             console.log("LOG:: cusModalUserProfile -> connectedCallback -> result", result)

        //         })
        // })

    }
    /**
     * @param {string} buttonID 
     */
    renderRecaptcha(buttonID) {
        let self = this
        // [START appVerifier]
        window.recaptchaVerifier = new this.firebase.auth.RecaptchaVerifier(buttonID, {
            'size': 'invisible',
            'callback': function (/**@type {any}*/response) {
                
                // reCAPTCHA solved, allow signInWithPhoneNumber.
                //window.signingIn = true;
                //updateSignInButtonUI();
                //var phoneNumber = getPhoneNumberFromUserInput();
                let appVerifier = window.recaptchaVerifier;
                let sPhoneNumber = self.iptPhone.value; //document.querySelector('#iptPhone').textContent;
                self.firebase.auth().signInWithPhoneNumber(sPhoneNumber, appVerifier)
                    .then(function (/**@type {any}*/confirmationResult) {
                        
                        //window.confirmationResult = confirmationResult;
                        self.verificationId = confirmationResult.verificationId
                        self.proxyUI.enumPhoneStatus = ENUM_PhoneStatus.inputVerifyCode;
                    }).catch(function (/**@type {any}*/error) {
                        self.proxyUI.enumPhoneStatus = ENUM_PhoneStatus.inputPhoneFailed;
                        self.proxyUI.divInputPhoneFailed_Text = `${error.code}, ${error.message}`
                        // Error; SMS not sent
                        console.error('Error during signInWithPhoneNumber', error);
                        // window.alert('Error during signInWithPhoneNumber:\n\n' +
                        //     error.code + '\n\n' + error.message);
                        //window.signingIn = false;
                        // updateSignInFormUI();
                        // updateSignInButtonUI();
                    });
            }
        });
        // [END appVerifier]
        // button start working
        window.recaptchaVerifier.render()
            .then(function (widgetId) {
                let recaptchaWidgetId = widgetId;
                //updateSignInButtonUI();
            })
            .catch((error) => {});
    }
    // onInputChange(e) {
    //     let thisElement = e.target;
    //     this.proxyFormData[e.target.id] = e.target.value;
    //     // console.log(e.target.id)
    //     // console.log(this.proxyFormData[e.target.id])
    // }

    /**
     * 
     * @param {HTMLElement} templateContent 
     */
    appendTemplate(templateContent) {
        // const shadowRoot = this.attachShadow({mode: 'open'});
        // shadowRoot.appendChild(templateContent)
        this.appendChild(templateContent)
        //------------------
        /**@type {HTMLInputElement} */
        this.iptAccount = this.querySelector('#iptAccount');
        this.iptAccount.addEventListener('input', this.onInputChange.bind(this));
        /**@type {HTMLInputElement} */
        this.iptEmail = this.querySelector('#iptEmail');
        this.iptEmail.addEventListener('input', this.onInputChange.bind(this));
        /**@type {HTMLInputElement} */
        this.iptName = this.querySelector('#iptName');
        this.iptName.addEventListener('input', this.onInputChange.bind(this));
        // this.iptPhone = this.querySelector('#iptPhone');
        // this.iptPhone.addEventListener('input', this.onInputChange.bind(this));
        /**@type {HTMLInputElement} */
        this.iptAddress = this.querySelector('#iptAddress');
        this.iptAddress.addEventListener('input', this.onInputChange.bind(this));
        
        //======== Phone Number Verify=========
        /**@type {HTMLInputElement} */
        this.iptPhone = this.querySelector('#iptPhone');
        /**@type {HTMLElement} */
        this.btnVerifyNumber = this.querySelector('#btnVerifyNumber');
        /**@type {HTMLElement} */
        this.btnDeleteNumber = this.querySelector('#btnDeleteNumber');
        /**@type {HTMLElement} */
        this.divInputPhoneFailed = this.querySelector('#divInputPhoneFailed');
        /**@type {HTMLElement} */
        this.divVerifyCode = this.querySelector('#divVerifyCode');
        /**@type {HTMLInputElement} */
        this.iptPhoneVerifyCode = this.querySelector('#iptPhoneVerifyCode');
        /**@type {HTMLElement} */
        this.btnSendVerifyCode = this.querySelector('#btnSendVerifyCode');
        /**@type {HTMLElement} */
        this.divVerifyFailed = this.querySelector('#divVerifyFailed');

        /**
         * @this {HTMLElement | HTMLInputElement}
         * @param {boolean} value 
         */
        function setDisabled(value) {
            if (value)
                this.setAttribute('disabled', '');
            else
                this.removeAttribute('disabled');
        }

        /**
         * @this {HTMLElement | HTMLInputElement}
         * @param {boolean} value 
         */
        function setShow(value) {
            if (value)
                this.classList.remove('d-none')
            else
                this.classList.add('d-none')
        }
        
        let arrayElements_isDisable = this.querySelectorAll('[isDisable]');
        let arrayElements_isShow = this.querySelectorAll('[isShow]');
        // arrayElements_isDisable = arrayElements_isDisable.map((item) => {
        //     item.setDisabled = setDisabled
        //     return item
        // })
        arrayElements_isDisable.forEach((item) => {
            item.setDisabled = setDisabled
        })
        arrayElements_isShow.forEach((item) => {
            item.setShow = setShow
        })
        //this.iptPhone.setDisabled(true)
        // this.divVerifyFailed.setShow(true)

        /**@type {HTMLElement} */
        this.formUserProfile = this.querySelector('#formUserProfile');
        this.formUserProfile.addEventListener('submit', this.onFormSubmit.bind(this));

        let proxyFormData = new ProxyFormData();
        // let proxyFormData = {
        //     iptAccount: "",
        //     iptName: "",
        //     iptEmail: "",
        //     iptPhone: "",
        //     iptAddress1: "",
        //     iptAddress2: "",
        //     iptAddress3: "",
        // }

        /**@type {any}}*/
        let self = this
        this.proxyFormData = new Proxy(proxyFormData, {
            get: function (/**@type {any}*/target, prop) {
                return target[prop]
            },
            set: function (/**@type {any}*/target, prop, value) {
                switch (prop) {

                    default:
                        /**@type {string} - UI input value */
                        if (self[prop]) //userData === undefined
                            self[prop].value = value;
                        break;
                }

                target[prop] = value;

                //console.log(target, prop, value)
                return true;
            }
        });


    }
    /**
     * 
     * @param {HTMLElement} htmlElement 
     */
    appendTestArea(htmlElement) {
        let testArea = document.querySelector('[testArea]');
        testArea.appendChild(htmlElement)
        //console.log(aa)
    }
    showTestUI() {
        let uiCkboxEmailVerified = this.querySelector('#uiCkboxEmailVerified');
        uiCkboxEmailVerified.classList.remove('b-displayNone');
    }

    /**
     * 
     * @param {firebase} inFirebase 
     */
    setFirebase(inFirebase) {
        // console.log(inFirebase)
        this.firebase = inFirebase;
        this.db = this.firebase.firestore();
        //this.setAuth_getRedirectResult();
    }

    /**
     * @function - save partial of userData to firestore
     * @param {any} userData 
     */
    saveUserData(userData) {
        let uid = this.proxyFormData.iptAccount
        this.db.collection(FIRESTORE_COLLECTION.Users).doc(uid).set(userData, {
            merge: true
        })
    }
    /**
     * 
     * @param {boolean} isShow 
     */
    showModal(isShow) {
        //let modalLogin2 = document.querySelector('#modalLogin2');
        //console.log(modalLogin2)
        //modalLogin2.assign();

        let options = {
            show: isShow,
            focus: true,
            keyboard: true
        }
        let strShow = null;
        if (isShow == true)
            strShow = 'show';
        else
            strShow = 'hide'
        $('#modalProfile').modal(strShow)
        //console.log($('#modalLogin'))
    }
    onFormSubmit(/**@type {any}*/e) {
        e.preventDefault();

        let uid = this.proxyFormData.iptAccount
        
        this.db.collection(FIRESTORE_COLLECTION.Users).doc(uid).withConverter(ProxyFormData.userProfileConverter).set({
            ...this.proxyFormData
        }, {
            merge: true
        })
        this.showModal(false)
    }
    onInputChange(/**@type {any}*/e) {
        let thisElement = e.target;
        this.proxyFormData[e.target.id] = e.target.value;
        // console.log(e.target.id)
        // console.log(this.proxyFormData[e.target.id])
    }
    
    // } //-----------------------------------------
    /**
     * 
     * @param {string} uid 
     */
    loadDbProfile(uid) {
        let self = this
        let authUser = this.firebase.auth().currentUser
        this.proxyFormData.iptAccount = uid
        // 畫面UI資料以auth()為準
        if (authUser) {
            console.log("LOG:: cusModalUserProfile -> loadDbProfile -> authUser", authUser)
            this.proxyFormData.iptAccount = uid;
            this.proxyFormData.iptEmail = authUser.email;
            this.proxyFormData.iptPhone = authUser.phoneNumber
            //==== phone status
            this.proxyUI.enumPhoneStatus = (authUser.phoneNumber) ? ENUM_PhoneStatus.verifiedOK : ENUM_PhoneStatus.noPhoneNubmer;
            //==== signin provider type
            let listProviderId = UserData.getListProviderId_ByAuthUserProviderData(authUser.providerData);
            let fakeUser = new UserData();
            fakeUser.listProviderId = listProviderId;
            let providerId = fakeUser.providerId;
            this.proxyUI.enumLoginMethod = (providerId === UserData.ENUM_ProviderType.google)?ENUM_LoginMethod.google:ENUM_LoginMethod.email;
            // /**@type {Array} */
            // let findGoogleProvider = authUser.providerData.filter((item) => {
            //     return item.providerId === 'google.com' // google.com password
            // })
            // if (findGoogleProvider.length > 0)
            //     this.proxyUI.enumLoginMethod = ENUM_LoginMethod.google;
            // else
            //     this.proxyUI.enumLoginMethod = ENUM_LoginMethod.email;
        }

        //newAA.proxyFormData.iptAccount = 'RZN9NipaymMkYA8Pr4a8Tg5gvz13'

        //var self = this.firebase
        this.db.collection(FIRESTORE_COLLECTION.Users).doc(uid).withConverter(ProxyFormData.userProfileConverter).get()
            .then((/**@type {any}*/e) => {
                let userInfo = e.data();
                if (userInfo) {
                    self.proxyFormData.iptName = userInfo.iptName
                    self.proxyFormData.iptAddress = userInfo.iptAddress
                }
                

            })

    }
    /**
     * 
     * @param {string} errCode 
     * @param {string} errMessage 
     */
    getErrorMessageZHTW(errCode, errMessage) {
        console.log("LOG:: cusModalUserProfile -> getErrorMessageZHTW -> errCode", errCode)
        let errCodeZHTW;
        let errMessageZHTW = errMessage;
        switch (errCode) {
            case 'auth/invalid-phone-number':
                errCodeZHTW = '電話號碼格式錯誤'
                break;
            case 'auth/invalid-verification-code':
                errCodeZHTW = '驗證碼輸入錯誤'
                break;
            case 'auth/provider-already-linked':
                errCodeZHTW = '此電話號碼已存在'
                break;
            case 'auth/credential-already-in-use':
                errCodeZHTW = '此電話號碼已被使用'
                break;
            case 'auth/missing-phone-number':
                errCodeZHTW = errCode;
                break;
            case 'auth/too-many-requests':
                errCodeZHTW = errCode;
                break;
            default:
                errCodeZHTW = errCode;
                errMessageZHTW = errMessage;
                break;
        }
        return {
            errCodeZHTW,
            errMessageZHTW
        }
        // auth/invalid-phone-number, TOO_SHORT
        // auth/invalid-phone-number, TOO_LONG        
        // auth/invalid-verification-code, The SMS verification code used to create the phone auth credential is invalid. Please resend the verification code sms and be sure use the verification code provided by the user.        
        // auth/provider-already-linked, User can only be linked to one identity for the given provider.
        // case 'auth/missing-phone-number':
        // case 'auth/too-many-requests':

        //address bug
    }

}