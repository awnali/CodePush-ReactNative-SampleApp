## How to Integrate Code push with React Native (IOS & Android)

#### Running React-Native 

1. `npm install -g react-native-cli`
2. `react-native init SampleCodePushApp`
3. `react-native run-ios`

Verify if app is working, you should get following screen.

<center><img width="373" alt="IOS React App" src="https://user-images.githubusercontent.com/627361/29063869-491e6856-7c38-11e7-80b3-4bfda495f873.png"></center>

#### Install Code-Push

1. `npm install -g code-push-cli`
2. if you havn’t register, otherwise skip: `code-push register`
3.	 `code-push app add SampleApp-iOS ios react-native`
4.	`code-push app add SampleApp-Android android react-native`
5.	https://mobile.azure.com/users/[YOUR_USERNAME]/apps, go to this link and verify your apps are created successfully.
6.	`react-native link react-native-code-push`
7.	Enter the keys, when asked (to retrieve keys: `code-push deployment ls SampleApp-Ios -k`)
8.	Run application and verify everything is working


<center><img width="373" alt="IOS React App" src="https://user-images.githubusercontent.com/627361/29063869-491e6856-7c38-11e7-80b3-4bfda495f873.png"></center>

#### Prepare App for code push

 1. create new index.js on root as ios and android both will have same
 2. layout copy paste everything from index.ios.js to index.js replace
    the content of index.ios.js and index.android.js with `require('./index.js')`
 3. Run application again and verify everything is running as before.

<center><img width="373" alt="IOS React App" src="https://user-images.githubusercontent.com/627361/29063869-491e6856-7c38-11e7-80b3-4bfda495f873.png"></center>

###### Decouple code into components

Index.js should look like:

    export default class SampleCodePushApp extends Component {
        render() {
            return (
                <App/>
            );
	    }
    }
create new file at ./src/components/App.js and copy paste below code:

    import React from 'react'
    import {
        StyleSheet,
        Text,
        View
    } from 'react-native';
    
    class App extends React.Component {
        render(){
            return (
                <View style={styles.container}>
                    <Text style={styles.welcome}>
                        Welcome to React Native!
                    </Text>
                    <Text style={styles.instructions}>
                        To get started, edit index.ios.js
                    </Text>
                    <Text style={styles.instructions}>
                        Press Cmd+R to reload,{'\n'}
                        Cmd+D or shake for dev menu
                    </Text>
                </View>
            );
        }
    }
    
    const styles = StyleSheet.create({
        container: {
            flex: 1,
            justifyContent: 'center',
            alignItems: 'center',
            backgroundColor: '#F5FCFF',
        },
        welcome: {
            fontSize: 20,
            textAlign: 'center',
            margin: 10,
        },
        instructions: {
            textAlign: 'center',
            color: '#333333',
            marginBottom: 5,
        },
    });
    export default App;

Run app and verify everything is working as before.

<center><img width="373" alt="IOS React App" src="https://user-images.githubusercontent.com/627361/29063869-491e6856-7c38-11e7-80b3-4bfda495f873.png"></center>

DONE! let's jump back to code push.

#### Integrating Code-Push

For IOS your info.plist, NSExceptionDomains section should look like that:

    <dict>		
        <key>NSExceptionDomains</key>
        <dict>
            <key>codepush.azurewebsites.net</key>
            <dict>
                <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
            </dict>
            <key>codepush.blob.core.windows.net</key>
            <dict>
                <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
            </dict>
            <key>codepushupdates.azureedge.net</key>
            <dict>
                <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
            </dict>
            <key>localhost</key>
            <dict>
                <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
            </dict>
        </dict>
    </dict>
Your App.js should look like:

    import React from 'react'
    import {
        StyleSheet,
        Text,
        View
    } from 'react-native';
    import CodePush from 'react-native-code-push'
    
    class App extends React.Component {
        constructor () {
            super();
            this.state = {
                logs: ''
            }
        }
        codePushStatusDidChange(status) {
            let msg = '';
            switch(status) {
                case CodePush.SyncStatus.CHECKING_FOR_UPDATE:
                    msg = ("Checking for updates.");
                    break;
                case CodePush.SyncStatus.DOWNLOADING_PACKAGE:
                    msg = ("Downloading package.");
                    break;
                case CodePush.SyncStatus.INSTALLING_UPDATE:
                    msg = ("Installing update.");
                    break;
                case CodePush.SyncStatus.UP_TO_DATE:
                    msg = ("Up-to-date.");
                    break;
                case CodePush.SyncStatus.UPDATE_INSTALLED:
                    msg = ("Update installed.");
                    break;
            }
            console.log(msg)
            setTimeout(() => {
                this.setState({
                    logs: msg
                });
            }, 2000);
        }
        codePushDownloadDidProgress(progress) {
            let msg = progress.receivedBytes + " of " + progress.totalBytes + " received.";
            console.log(msg);
            setTimeout(() => {
                this.setState({
                    logs: msg
                });
            }, 2000);
        }
        render(){
            return (
                <View style={styles.container}>
                    <Text style={styles.welcome}>
                        Welcome to React Native!
                    </Text>
                    <Text style={styles.instructions}>
                        To get started, edit index.ios.js
                    </Text>
                    <Text style={styles.instructions}>
                        Press Cmd+R to reload,{'\n'}
                        Cmd+D or shake for dev menu
                    </Text>
                    <Text style={styles.instructions}>
                        Version 1.2
                    </Text>
                    <Text style={styles.instructions}>
                        {this.state.logs}
                    </Text>
                </View>
            );
        }
    }
    
    const styles = StyleSheet.create({
        container: {
            flex: 1,
            justifyContent: 'center',
            alignItems: 'center',
            backgroundColor: '#F5FCFF',
        },
        welcome: {
            fontSize: 20,
            textAlign: 'center',
            margin: 10,
        },
        instructions: {
            textAlign: 'center',
            color: '#333333',
            marginBottom: 5,
        },
    });
    export default App = CodePush(App)

Run the app on ios, press cmd+D and click remote Debugging. After that open http://localhost:8081/debugger-ui and look into console. Whenever you’ll refresh your app, you should see something like:

<center><img width="1440" alt="screen shot 2017-08-08 at 2 49 35 pm" src="https://user-images.githubusercontent.com/627361/29068680-db50f346-7c48-11e7-9c77-a452b0a92627.png"></center>

Which means on launch your application is looking for code-push update.  Code push is integrated now but only for debug mode, lets see how we can release the app for IOS and Android.


Go to my another tutorial for [Release Build for IOS/Android](https://github.com/awnali/CodePush-ReactNative-SampleApp/blob/master/React-Native-Release-Tutorial.md)

If you followed the above release build tutorial then till now you should have release builds running on your device or simulator.

####  Update your app from code push

###### IOS

 1. Open your project in xcode and change the Build number from 1.0 to
    1.1, by clicking on name of project and then change the build version.
 2. Push the change to Code-Push with following command:  `code-push release-react SampleApp-Ios ios`
 3. After the push you can verify it at, https://mobile.azure.com/users/[USER_NAME]/apps/SampleApp-Ios
 4. Run application in Release mode and verify everything is working as expected.
 5. Now go back to application code and change text, once you change the text then push to code-push with following, `code-push release-react SampleApp-Ios ios`
 6. Come to your simulator again, relaunch the app you will see the text as Checking for update, Downloading Package and update installed.
 7. Now relaunch the app and you should see the change in your application without pushing your app to app store.
 8. Only Difference, if you want to run the same app on device, then you need to sign it with developer account. And then it will run on your device even.

##### Android

1. Push the latest code to code-push android app with following command:

    code-push release-react SampleApp-Android android
2. Release build for android,  for that go to [Release Build for IOS/Android](https://github.com/awnali/CodePush-ReactNative-SampleApp/blob/master/React-Native-Release-Tutorial.md)
3. Once you have the Release build, then change the text and push it to code-push.
4. Relaunch the app and you should see the update installed text.
5. Relaunch app and now you should see the change   


