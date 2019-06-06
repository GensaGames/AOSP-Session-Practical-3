# AOSP-Session-Practical-3



1. For setting up environment to work with AOSP as new vendor, we need to setup working directory. Create directory, by copying it from the existing default linaro venor changes. Make sure, u set up terminal with building scripts. 

'''
mkdir -p $ANDROID_BUILD_TOP/vendor/globallogic/product && \
cp -R $ANDROID_BUILD_TOP/vendor/linaro/hikey960 $ANDROID_BUILD_TOP/vendor/globallogic/product
'''

2. To add new vendor working folder, you need to add link to the 'device-vendor.mk' from the '/vendor/globallogic-new/product/hikey960/' in device mk file 'device/linaro/hikey/hikey960' 'device-hikey960.mk'. By using following command. Check patch source in case you need to add this manually. 

cd $ANDROID_BUILD_TOP/device/linaro/hikey && \
git apply 0001-GL-Stub-Linking-sample-Vendor-changes-with-Device.-N.patch && \
cd -


3. Now you can check build one more time, by running make command in root folder of source. To make sure, new changes successfully linked. 

'''
make -j8
'''

4. Once you have working directory, you can use 'vendor/globallogic/' to add new modules, libraries, etc. All that source, should be linked with main vendor Mk file in '/vendor/globallogic-new/product/hikey960/' 'device-vendor.mk'. First step, will be to add Overlay settings, to existing Android configuration. See AOSP documentation for more details https://source.android.com/setup/develop/new-device#use-resource-overlays. Check patch source in case you need to add this manually. 

'''
cd $ANDROID_BUILD_TOP/vendor/globallogic/product/hikey960 && \
git apply 0001-GL-Stub-Link-Adding-new-overlay-configuration-to-the.patch && \
cd -
'''

5. Now you can check build again, and flash new build. In this overlay we have changed few options, one of them it's behavior of long press power button. In previous build by long press power button you should expect global button selection. After changes above, it should be changed to power off dialog with confirmation. 

'''
make -j8 && \
cd $ANDROID_BUILD_TOP/device/linaro/hikey/installer/hikey960 && \
./flash-all.sh && cd -
'''

6. Next step would be adding custom changes in existing application in AOSP Settings. We will add notification panel to the Settings app, by using following patch.  Check patch source in case you need to add this manually. 

'''
cd $ANDROID_BUILD_TOP/packages/apps/Settings && \
git apply 0001-GL-Stub-Added-local-stub-vendor-changes.patch
'''

7. In order to apply changes in this case (of high level application changes or Android framework) we don't need to make complete rebuild. First of all launch board, and run next commands. 

'''
adb root && adb remount
'''

8. Now you should have rights to use adb commands with syncing module source directly in device. 

'''
cd $ANDROID_BUILD_TOP/packages/apps/Settings && \
mmm && adb sync && \
adb shell am force-stop com.android.settings && \
adb shell am start -n com.android.settings/.Settings
'''

9. If you have done everything right, you should have new changes in Settings with red notification panel. Now most interesting part, try to experiment with default application and their configuration. Try to change other values in System configuration from the file 'frameworks/base/core/res/res/values/config.xml'.