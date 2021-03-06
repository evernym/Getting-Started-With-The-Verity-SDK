
# Getting Started with the Verity SDK for Java

## Running the Example Java Application (Ubuntu 16.04)

These steps target the x86-64 architecture running Ubuntu 16.04. If you want to run this inside a fresh VM, install [vagrant](https://www.vagrantup.com/) and in an empty folder run:

```sh
vagrant init ubuntu/xenial64
vagrant up
vagrant ssh
```

1. You will need docker, Java 8 (JDK), Maven, libindy and python3-pip installed on your system

	```sh
	sudo apt-get update
	sudo apt-get install -y docker.io default-jdk maven python3-pip
	sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 68DB5E88
	sudo add-apt-repository "deb https://repo.sovrin.org/sdk/deb xenial master"
	sudo apt-get update
	sudo apt-get install -y libindy=1.9.0~1122
	```
	
2. You will need to be a Trust Anchor on the Sovrin Test Ledger to run this example. Use the `tools/new_did.py` script **in this repository** to create a new DID and then email the DID and VerKey to [support@sovrin.org](mailto:support@sovrin.org) asking for that DID to be written to the Staging Net. You should received a response within 12 hours on a weekday (often much sooner!).

	You will also need `tools/requirements.txt` from this repository. It's probably best to clone this repository and keep it handy.

	```
	git clone https://github.com/evernym/Getting-Started-With-The-Verity-SDK
	cd Getting-Started-With-The-Verity-SDK
	pip3 install -r tools/requirements.txt --user
	./tools/new_did.py
	```

	:exclamation: Don't send the seed! Keep it safe. You will need it to start the Mock Verity application and any other time you want to write to the Test Ledger.

3. One directory back from this repo (not inside the Getting-Started-With-The-Verity-SDK folder) create a new Java application with [maven](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html).
	
	```sh
	mvn archetype:generate \
		-DgroupId=com.mycompany.app \
		-DartifactId=example-app \
		-DarchetypeArtifactId=maven-archetype-quickstart \
		-DarchetypeVersion=1.4 \
		-DinteractiveMode=false
	cd example-app
	```

4. Copy `java/pom.xml` from this repository to the `pom.xml` of your application

        ```sh
        cp ../Getting-Started-With-The-Verity-SDK/java/pom.xml pom.xml
        ```

	This file is similar to the pom.xml file generated by maven, but it adds the Verity SDK JAR file as a dependency to your project and increases the target Java version to 1.8.
	
5. Copy `java/App.java` and `java/Listener.java` from this repository to `src/main/java/com/mycompany/app/.` of your example application.

        ```sh
        cp ../Getting-Started-With-The-Verity-SDK/java/*.java src/main/java/com/mycompany/app/.
        ```

6. Start the Mock Verity service locally with your Trust Anchor Seed created in step 2.

	```sh
	sudo docker pull evernymdev/mock-verity # Get latest version
	sudo docker run -itd --rm --network host evernymdev/mock-verity <YOUR_TRUST_ANCHOR_SEED>
	```

7. Provision against Mock Verity

	This is the process whereby your application registers and exchanges keys with the Verity application. You will need `tools/provision_sdk.py` from this repository. Also, make sure to replace \<WALLET\_KEY\_HERE\> with a new wallet password.

	```sh
        python3 ../Getting-Started-With-The-Verity-SDK/tools/provision_sdk.py --wallet-name verity-sdk-test-wallet http://localhost:8080 <WALLET_KEY_HERE> > verityConfig.json
	```
	
	As shown in the above command, the resulting config is copied to `example-app/verityConfig.json`
	
	Note: This wallet is only used to store the keys you use to communicate with Verity.
	
	:exclamation: If you restart Mock Verity at any point, you will need to provision again.


8. Build and run the example application.

	From inside the `example-app` directory:

	```sh
	mvn package
	mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
	```
	
9. The logs for the example application will print something like this:
	
	```
	Invite Details: {"sc":"MS-101","threadId":null,"s"...
	```
	
	Copy the invite details JSON object into any QR code generator.  [This one](https://www.qr-code-generator.com/) works well, but you will need to click on the "Text" tab. 

10. Download [Connect.Me](https://connect.me/) from the [App Store](https://itunes.apple.com/us/app/connect-me/id1260651672?mt=8) or the [Google Play Store](https://play.google.com/store/apps/details?id=me.connect&hl=en).

11. When you setup the app, make sure to check the "Use Staging Net" option at the bottom of the screen or this demo won't work.

	![Connect.Me Developer Mode Switch](https://i.postimg.cc/pTrdMszg/IMG-0116.png)

12. Scan the QR code with Connect.Me. You will then receive a Connection Invite, Challenge Question, Credential Offer, Credential and Proof Request in that order, demonstrating all of the protocols currently supported by the Verity SDK.

© 2013-2019, ALL RIGHTS RESERVED, EVERNYM INC.

