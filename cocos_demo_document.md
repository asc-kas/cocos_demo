# cocos_document
打包工具说明文档


# ASCSDK文档(ASCSDK Cocos Document)

------
###简介(Introduction)
欢迎使用ASCSSDK，ASCSSDK是一款广告集成SDk，方便您快速的集成广告
Welcome to ASCSSDK. ASCSSDK is an advertising integration SDk that allows you to quickly integrate advertising
###  目录               (directory)
> * ASCSDK接入       (ASCSDK access)
> * ASCSDK工作流程   (ASCSDK The working process)
> * 广告示例         (AD sample)
> * 支付及其他示例   (Payment and other examples)
> * 统计功能         (statistics)
> * 打包             (Build)
### ASCSDK接入(ASCSDK Access)
> * 开发环境需求(Development environment requirements)：cocos2dx_3.10

###  广告示例(AD sample)

> * 无需多次加载的广告(No need to load multiple ads)
##### 横幅（Banner）

    void Start()
    {
        //初始化
        //initialize
        ASCSDKInterface::getInstance()->initSDK(this);
    }
    
    //展示或者隐藏横幅广告（广告是自动加载，在需要隐藏的时候调用HideBanner，需要展示的时候调用ShowBanner）
    //Display or Hide banner
    void HelloWorld::showBannerClickCallback(cocos2d::Ref* pSender){
    	CCLOG("Btn showBanner Clicked...ASC showBanner...");
    	ASCSDKInterface::getInstance()->showBanner();
    	btnShowBanner->setVisible(false);
    	btnHideBanner->setVisible(true);
    }
    void HelloWorld::hideBannerClickCallback(cocos2d::Ref* pSender){
    	CCLOG("Btn checkBanner Clicked...ASC hideBanner...");
    	ASCSDKInterface::getInstance()->hideBanner();
    	btnShowBanner->setVisible(true);
    	btnHideBanner->setVisible(false);
    }
    
> * 需要多次加载的广告 (Ads that require multiple loads: screenshots)
##### 插屏（Inters ）

    //展示广告
    //Display Inters
    void HelloWorld::showIntersClickCallback(cocos2d::Ref* pSender){
    	CCLOG("Btn showInters Clicked...ASC showInters...");
    	//该广告已经可以播放
        //getIntersFlag:The advertisement is already available
    	if (ASCSDKInterface::getInstance()->getIntersFlag()){
    	    //传入当前关卡若无则传入"0"
            //Pass in the current level and if not, pass in "0"
    		ASCSDKInterface::getInstance()->showInters(levelIndex);
    	}
    }

> * 需要多次加载且有回调的广告 (Ads that require multiple loads and callbacks)
##### 视频（Video ）
    //回调
    //video play callback
    void HelloWorld::OnPlayVideoResult(bool result)
    {
    	CCLOG("SDK视频回调...");
    	showLog(StringUtils::format("video result code = %d result = %d", result, result).c_str());
    	//TODO:result为视频回调结果，result为true时是播放成功，false为失败
    	//TODO: result for video callback as a result, the result is true is broadcast success, 
    	or false on failure
    }
    
    //展示广告
    //Display Video
    void HelloWorld::showVideoClickCallback(cocos2d::Ref* pSender){
    	CCLOG("Btn showVideo Clicked...ASC showVideo...");
    	//getVideoFlag:The advertisement is already available
    	if (ASCSDKInterface::getInstance()->getVideoFlag()){
    		ASCSDKInterface::getInstance()->showVideo();
    	}
    }

 
> * PS:根据游戏项目的需求手动设置横竖屏：AndroidManifest.xml文件里
PS: set the horizontal screen manually according to the requirements of the game project: Assect-> plugins-> android-> androidmanifest.xml file

     android:screenOrientation="portrait"

### 支付及其他示例(Payment and other examples)
> * 登入、登出、上传数据(Login, logout, upload data)、退出（exit）
##### 登入（Login）、登出（LogOut）、上传数据（upload data）

    //初始化回调
    //initialize callback
     void HelloWorld::OnInitSuc();
     
    //登入回调事件
    //login success callback
    void HelloWorld::OnLoginSuc(ASCLoginResult* result);
  
    //登出回调 
     //logout success callback
    void HelloWorld::OnSwitchLogin();

    //上传游戏数据（补单用，必须）
    //Upload game data must be in
    void HelloWorld::sendSubmitGameData(int type);
    
    //上传数据
    //upload data
    void HelloWorld::sendSubmitGameData(int type){
    	ASCExtraGameData* extraData = ASCExtraGameData::create();
    	extraData->dataType = type;
    	extraData->roleId = "role_01";
    	extraData->roleName = "role_01";
    	extraData->roleLevel = 10;
    	extraData->serverId = 10;
    	extraData->serverName = "server_01";
    	extraData->moneyNum = 1;
    	extraData->roleCreateTime = StringUtils::format("%d", getCurrentTime());
    	extraData->roleLevelUpTime = StringUtils::format("%d", getCurrentTime());
    	ASCSDKInterface::getInstance()->submitGameData(extraData);
    }
    //退出
    //exit
    void HelloWorld::onKeyReleased(EventKeyboard::KeyCode keyCode, Event* event)
    {
    	if (keyCode == EventKeyboard::KeyCode::KEY_BACK)
    	{
			//Call the exit confirmation box of the channel, return false, the SDK does not support, the game needs to use its own exit box
			if (ASCSDKInterface::getInstance()->isSupportExit()){
				ASCSDKInterface::getInstance()->sdkExit();
			}
			else{
				//you exit ui
			}
			sendSubmitGameData(5);//ASCExtraGameData TYPE_EXIT_GAME
    	}
    }

> * 支付（pay）
##### 支付（pay）
    //支付回调(pay callback)
    void HelloWorld::OnPayResult(int payCode,int result)
    {
    	CCLOG("SDK购买回调...");
    	showLog(StringUtils::format("pay result code = %d result = %d",payCode,result).c_str());
    	//TODO:payCode为购买道具的id，result为零时是购买成功，其他为失败
    	//TODO:payCode is the id of the purchase item, and the result is zero when the purchase is
    	successful and the other is failure.
    }
    
    //支付
    //pay
    void OnPayClick()
    {
        ASCPayParams data = new ASCPayParams();
        //游戏中商品ID（必须(Need)）
        //Product ID  in the game (Need)
        data.productId = "1";
        //游戏中商品名称，比如元宝，钻石...（必须(Need)）
        //The names of products in the game, such as yuan bao, diamonds...(Need)
        data.productName = "元宝";
        //游戏中商品描述（必须(Need)）
        //Product description in the game (Need)
        data.productDesc = "购买100元宝，赠送20元宝";
        //价格，单位为元
        //Price, in yuan
        data.price = 1;
        //购买数量,定值为1（必须(Need)）
        // purchase quantity, set as 1 (Need))
        data.buyNum = 1;
        //当前玩家身上剩余的虚拟币数量
        //The amount of virtual currency left over from the current player
        data.coinNum = 300;
        //当前角色所在的服务器ID
        //The server ID where the current role is located
        data.serverId = "10";
        //当前角色所在的服务器名称
        //The name of the server where the current role is located
        data.serverName = "地狱之恋";
        //当前角色ID
        //Current role ID
        data.roleId = "asc_24532452";
        //当前角色名称
        //Current role name
        data.roleName = "麻利麻利吼";
        //当前角色等级
        //Current role level
        data.roleLevel = 15;
        //游戏服的支付回调地址，用于接收支付回调通知
        //The payment callback address of the game server is used to receive payment callback notification
        data.payNotifyUrl = "";
        //当前角色的vip等级
        //The current role's VIP rating
        data.vip = "v15";
        //扩展数据， 支付成功回调通知游戏服务器的时候，会原封不动返回这个值
        //Extending the data, when a successful callback is paid to notify the game server, the value is returned intact
        data.extension="111";
	data.payNotifyUrl = "1111";
        ASCSDKInterface::getInstance()->pay(data);
    }
> * 分享、评价、礼包兑换 (Share and evaluate and Gift bag for)
##### 分享（Share）、评价（Evaluate）、礼包兑换(Gift bag for)、动态价格(Dynamic charging point)

    //分享
    //Share
    void Share()
    {
        ShareParams SP = new ShareParams();
        //分享的标题，最大30个字符
        //Share the title, up to 30 characters
        SP.title = "分享标题";
        //标题链接
        //The title link
        SP.titleUrl = "http://www.uustory.com/";
        //分享此内容显示的出处名称
        //Share the source name that this content shows
        SP.sourceName = "ASCSDK出处名称";
        //出处链接
        //The source link
        SP.sourceUrl = "http://www.uustory.com/";
        //内容，最大130个字符
        //Content, up to 130 characters
        SP.content = "分享内容";
        //链接，微信分享的时候会用到
        //Link, WeChat will be used when sharing
        SP.url = "http://www.uustory.com/";
        //图片地址
        //Picture address
        SP.imgUrl = "http://www.uustory.com/";
        //是否全屏还是对话框
        //Full screen or dialog box
        SP.dialogMode = true;
        //Notification的图标
        //The Notification icon
        SP.notifyIcon = 1;
        //notification的文字
        //Notification of the text
        SP.notifyIconText = "文字";
        //内容的评论，人人网分享必须参数，不能为空
        //Content comments, which must be Shared by renren, cannot be empty
        SP.comment = "评论，人人网分享必须参数，不能为空";
        ASCSDKInterface.getInstance().Share(SP);
    }
    
    //评价
    //evaluate
    void HelloWorld::getRateFlagDelay(float dt){
    	bool isHave = ASCSDKInterface::getInstance()->getRateFlag();
    	if (isHave){
    		btnRate->setVisible(true);
    	}
    	else{
    		btnRate->setVisible(false);
    	}
    }
    
    //兑换礼包(若cdkey为0，则调用sdk的输入界面)
    //Cash gift bag，If cdkey is 0, the input interface of the SDK is called
    void OnGetGiftInfoClick(string cdkey)
    {
        //cdkey is Exchange codes for gift bags,It's usually handed out in the background。
        ASCSDKInterface.getInstance().ExchangeGift(cdkey);
    }
    
    //礼包兑换回调实现
    //Gift bag for
    void HelloWorld::OnGetGiftResult(int propNumber,std::string msg){
    	CCLOG("SDK礼包回调...");
    	showLog(StringUtils::format("gift result propNumber = %d", propNumber).c_str());
    	//TODO: propNumber为礼包回调道具个数，msg为是礼包兑换消息
    	//TODO: PropNumber is the number of props for the gift package, MSG is the gift exchange
    	message.(if it is 0, the propNumber fails to be sent)
	//TODO: type 礼包返回道具的类型 The gift pack returns the type of item 
	if (type.compare("Golds")){
		//金币 == Golds
	}
	else if (type.compare("Diamonds")){
		//钻石 == Diamonds
	}
	else{
		//其他 == other 
	}
    }
    
    //动态价格回调
    //Dynamic charging point
    void HelloWorld::OnGetProductResult(cocos2d::ValueVector vv){
	CCLOG("SDK商品回调...");
	for (size_t i = 0; i < vv.size(); i++)
	{
		Value value = vv.at(i);
		cocos2d::ValueMap json = value.asValueMap();
		showLog(StringUtils::format("product result billingPoint = %d", json["billingPoint"].asInt()).c_str());
		showLog(StringUtils::format("product result price = %d", json["price"].asInt()).c_str());
		showLog(StringUtils::format("product result quantity = %d", json["quantity"].asInt()).c_str());

		price = json["price"].asInt();
	}
    }

### 注意事项(Matters needing attention)
如果您使用了第三方插件，请确认您的项目文件夹：Assect->Plugins->Android->AndroidManifest.xml文件里
application 下包含 android:name="com.asc.sdk.ASCApplication">

If you are using a third-party plug-in, make sure your project folder is in the Assect-> plugins-> androidb2 androidmanifest.xml file
Under the application contains the android: name = "com.asc.sdk.ASCApplication" >

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" 
        android:name="com.asc.sdk.ASCApplication">


###  统计功能(Statistics)

     //开始关卡
    //Starting level
    ASCSDKInterface.getInstance().startLevel(string num);

    //胜利关卡
    //Victory gate
    ASCSDKInterface.getInstance().finishLevel(string num);

    //失败关卡
    //Failure levels
    ASCSDKInterface.getInstance().failLevel(string num);
    
    //道具使用统计 item:道具名字，num：购买数量，price：需要的金币
    //Item: item name, num: number of items purchased, price: needed gold COINS
    ASCSDKInterface.getInstance().useBoosterInfo(string item, int num, int price);

    //自定义事件 eventName：自定义事件名字（由另一方实现）
    //Custom event eventName: custom eventName (implemented by the other party)
    ASCSDKInterface.getInstance().userCustomEvent(string eventName);
    
    //上传用户游戏参数，level->玩家当前通关最高关卡,buff->玩家当前道具总量,coin->玩家当前剩余金币数量
    //Upload user game parameters, level-> players currently pass the highest level of clearance, the total number of buff > players' current props, and the number of coin-> players' current remaining gold COINS
	ASCSDKInterface.getInstance().uploadUserInfo(string level, string buff, string coin);

###  打包(Build)
打包Android应用，在build settings中switch到Android平台，build即可。打包成功后接入渠道资源前调用视频广告、支付回调、退出、礼包兑换都会有弹窗提示，用于测试。
Package Android applications, switch to Android platform in build Settings, build。After successful packaging, video advertising, payment callback, exit and gift exchange will be called before access to channel resources for testing.


------



火星人互娱有限公司 [@huoxingren]     
2018 年 07月 31日    







