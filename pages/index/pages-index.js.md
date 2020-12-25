
#### import [[firebase-FirebaseMJS.js]] {Email_ResendPassword,FIRESTORE_COLLECTION } from '../../js/firebase/FirebaseMJS.js'

#### import [[pages-indexESM.js]]{ENUM_switchIndexPage} from './indexESM.js'
import _ from 'lodash'
#### import {[[dataDefine-index.js]]UserData} from '../../js/dataDefine/index.js'


#### import [[cusModalLogin.js]]cusModalLogin from '../../webcomponents/cusModalLogin3/cusModalLogin.js'

#### import [[cusModalUserProfile.js]]cusModalUserProfile from '../../webcomponents/cusModalUserProfile3/cusModalUserProfile.js'

#### import [[fullPageScroll.js]]cusFullPageScroll from '../../webcomponents/cusFullPageScroll/fullPageScroll.js'

#### firebase Config -> [[firebaseProj.config.json]] = require('../../projectConfig/firebaseProj.config.json') 

#### function switchIndexPage -> switch two react pages

#### firebase.auth().onAuthStateChanged() -> get login information for display

#### proxyUserMenuDropdown -> loging dropdown toggle

####proxyMainPageUI - >pageStatic & pageReact toggle

#### get [[fullPageScroll.htm]] [[cusModalLogin.js]] [[cusModalUserProfile.htm]] and use[[useComponent3.js]] to manifest  three webComponents

#### array_MenuHrefs navBar scrolling to target DIV


