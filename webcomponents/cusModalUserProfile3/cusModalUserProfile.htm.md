<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>


    <style>
        html,
        body {
            background-color: black;
            color: white;
        }

        .colorBlack {
            color: black;
        }
    </style>
</head>

<body>


    <!--============================= Profile =============================-->
    <template>
        <form action="" id="formUserProfile" onsubmit="return false">
            <div class="modal fade colorBlack boxUserProfile" id="modalProfile" role="dialog"
                aria-labelledby="exampleModalLabel" aria-hidden="true">
                <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h5 class="modal-title titleWhite" id="exampleModalLabel">個人檔案</h5>
                            <button type="button" class="close titleWhite cancelHover" data-dismiss="modal"
                                aria-label="Close">
                                <span aria-hidden="true">&times;</span>
                            </button>
                        </div>
                        <div testArea></div>
                        <div class="modal-body">
                            <!-- ======== false=show buttons, true=hide buttons ======== -->                            
                            <input type="checkbox" class="uiCkbox-emailVerified b-displayNone" id="uiCkboxEmailVerified" title="Eamil Verified" checked>
                            <input type="checkbox" class="uiCkbox-isAccountOfGoogle b-displayNone" id="uiCkboxIsAccountOfGoogle" title="is Account Of Email" checked>
                            
                            <div id="divAccount" class="d-none">
                                <label for="iptAccount" class="titleWhite">帳號</label>
                                <input type="text" id="iptAccount" class="input fullWidth" readonly>
                            </div>
                            <div class="form-group inputField1">
                                <span class="titleWhite">帳號</span>
                                <input type="email" id="iptEmail" readonly>
                                <i class="fas fa-envelope emailColor" id="iFaEnvelope" title="Email Login"></i>
                                <i class="fab fa-google googleColor" id="iFaGoogle" title="Google Login"></i>
                                <i class="fas fa-exclamation-triangle emailWarning" id="iFaWarningEmailNotVerified" title="Email尚未通過驗證"></i>
                            </div>
                            <div class="form-group inputField1">
                                <span class="titleWhite">姓名</span>
                                <input type="text" id="iptName" bindIptRegisterEmail required autocomplete="name">
                            </div>
                            <div class="form-group inputField1">
                                <!-- <label for="iptPhone">電話</label> -->
                                <span class="titleWhite">電話</span>
                                <input type="text" id="iptPhone" placeholder="+886" isDisable>
                                <div style="display: inline-block;">
                                    <button id="btnVerifyNumber" type="button" class="btnLighten" isShow
                                        isDisable>驗證電話號碼</button>
                                </div>
                                <!-- <input id="btn2" type="button" name="submit" value="submit" /> -->
                                <button id="btnDeleteNumber" class="btnLighten" type="button" isShow>刪除電話號碼</button>
                            </div>
                            <div id="divInputPhoneFailed" class="divMessageFailed" isShow>
                                輸入電話失敗
                            </div>
                            <div id="divVerifyCode" class="form-group divVerifyCode inputField1" isShow>
                                <span class="titleWhite">輸入簡訊驗證碼</span>
                                <input type="text" id="iptPhoneVerifyCode" class="width30" isDisable>
                                <button id="btnSendVerifyCode" type="button" class="btnLighten" isDisable>送出驗證碼</button>
                                <!-- <input type="text" id="iptName" bindIptRegisterEmail required autocomplete="name"> -->
                            </div>
                            <!-- <div id="divVerifyCode" isShow>
                                <label for="iptPhoneVerifyCode">輸入簡訊驗證碼</label>
                                <input type="text" id="iptPhoneVerifyCode" class="input fullWidth" isDisable>
                                <button id="btnSendVerifyCode" type="button" class="btnLighten" isDisable>送出驗證碼</button>
                            </div> -->
                            <div id="divVerifyFailed" class="divMessageFailed" isShow>
                                驗證失敗
                            </div>

                            <div class="form-group inputField1">
                                <span class="titleWhite">預設地址</span>
                                <input type="text" id="iptAddress" bindIptAddress autocomplete="street-address">
                            </div>
                            <!-- <button style="display: inline-block;width: 100%;">ddd</button> -->
                            
                            <button type="button" id="btnResendEmailVerify" class="btnSave emailWarning">重寄Email認證信</button>
                            <button type="submit" id="btnSaveProfile" class="btnSave">儲存</button>
                            <!-- <button type="button" id="btnModifyPassword" class="btnSave">修改密碼(Email帳號)</button> -->
                            <button type="button" id="btnResendPassword" class="btnSave">重寄密碼</button>
                            <div class="footerHeight"></div>
                        </div>


                        <!-- ===== Address 1 ===== -->
                        <!-- <div class="input-group" id="iptgroup1">
                                <div class="input-group-prepend">
                                    <div class="input-group-text">
                                        <input type="radio" id="radioBtn_Address1" name="selectAddr" checked
                                            aria-label="Radio button for following text input">
                                    </div>
                                    <span class="input-group-text">(1)預設地址</span>
                                </div>
                                <input type="text" class="form-control" id="iptAddress1"
                                    aria-label="Text input with radio button">
                            </div> -->
                        <!-- ===== Address 2 ===== -->
                        <!-- <div class="input-group" id="iptgroup2">
                                <div class="input-group-prepend">
                                    <div class="input-group-text" style="background-color: lime;">
                                        <input type="radio" id="radioBtn_Address2"  name="selectAddr"
                                            aria-label="Radio button for following text input">
                                    </div>
                                    <span class="input-group-text" style="background-color: red;">(2)預設地址</span>
                                </div>
                                <input type="text" class="form-control" id="iptAddress2" style="background-color: blue;"
                                    aria-label="Text input with radio button">
                            </div> -->
                        <!-- ===== Address 3 ===== -->
                        <!-- <div class="input-group" id="iptgroup3">
                                <div class="input-group-prepend">
                                    <div class="input-group-text">
                                        <input type="radio" id="radioBtn_Address3" name="selectAddr"
                                            aria-label="Radio button for following text input">
                                    </div>
                                    <span class="input-group-text">(3)預設地址</span>
                                </div>
                                <input type="text" class="form-control" id="iptAddress3"
                                    aria-label="Text input with radio button">
                            </div> -->


                    </div>
                </div>
            </div>

        </form>

    </template>


</body>

</html>