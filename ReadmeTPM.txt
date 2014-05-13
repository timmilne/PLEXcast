TPM 5/12/14

How to do a manual build from command line with mvn

To get this to compile, I had to make changes to the pom.xml file in the plexcast-app directory.  Diff
pom.xml and pom - Orig.xml for all the differences.  In addition to the name and PLEXcast version changes,
the compiler-maven-plugin version needed to be added to some of the other repo's poms to suppress
warnings as well.

Added:

   <prerequisites>
      <maven>3.1.1</maven>
   </prerequisites>

Changed:

      <dependency>
         <groupId>android.support</groupId>
         <artifactId>compatibility-v4</artifactId>
         <version>19.0.1</version>
      </dependency>

to:

      <dependency>
         <groupId>android.support</groupId>
         <artifactId>compatibility-v4</artifactId>
         <version>19.1.0</version>
      </dependency>

changed:

	${version} to ${project.version}

from the command line ran: mvn versions:display-plugin-updates

to figure out that I need to add: <version>3.1</version> to

	<plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
               <source>1.6</source>
               <target>1.6</target>
            </configuration>
         </plugin>


Here are all the other notes that I took along the way:


TPM 5/5/14

Problems addressed today:
* Set the proxy for Android SDK Manager
* Realized that I need Android SDK 3.2 (not 4.4), but ended up installing all the SDKs
* I now have one remaining problem with the serenity-android app (see below, but resolved above)

Followed the instructions to build from source here: https://github.com/timmilne/serenity-android

* Environment variables (Windows key + Pause):
	- ANDROID_HOME: C:\Users\z018620\Dev\adt\sdk
	- JAVA_HOME: C:\Program Files\Java\jdk1.8.0.05
	- M2_Home: C:\Users\z018620\Dev\apache-maven-3.2.1
	- M2: %M2_HOME%\bin
	- MAVEN_HOME: C:\Users\z018620\Dev\apache-maven-3.2.1 (added while trying eclipse instructions)
	- Path: %M2%;%JAVA_HOME%\bin;%ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools (last two added
		while trying the eclipse instructions)

* ADK: Noticed this time reading the instruction I need Android SDK 3.2 (I tried 4.4 last night)
	- In Eclipse I tried adding: Window->Preferences->General->Network Settings and added the following:
		- http proxy: http://proxy-mdha.target.com:8080/
		- But that didn't work
	- In the Eclipse SDK Manager: Window->Android SDK Manager
		- I clicked Tools->Options and set:
		- HTTP Proxy Server to: http://proxy-mdha.target.com:8080/
		- Checked the box next to Force https:// sources to be fetched using http://
		- Now the SDK can pull what it needs
	- These settings are also picked up by: C:\Users\z018620\Dev\adt\SDK Manager.exe
		- Note: even though SDK Manager picked up the settings, pulling down addons for the adt
			does not pull them into Eclipse
		- Used this ADT SDK Manager to pull down Android SDK 3.2 and the Google APIs add on
		- I eventually added the Eclipse Marketplace plugin under the Help-> menu

* JDK: already installed 1.7 and 1.8, and pointed maven to 1.8 with JAVA_HOME above
	(1.7 didn't solve the issue)

* Maven: http://maven.apache.org/download.cgi
	- Command prompt to verify install: mvn --version

* maven-android-sdk-deploy: https://github.com/timmilne/maven-android-sdk-deployer
	- Open command prompt in C:\Users\z018620\Documents\Git\maven-android-sdk-deployer
	- Run: mvn install -P 3.2
	- Everything was a success

* serenity-android
	- Open a command prompt in C:\Users\z018620\Dev\serenity-android
	- mvn clean install
	- Made it past the REST tests...
	- Now failing on the menu drawer
		- Looks like the error message is indicating API level 16
		- Back to SDK Manager and installed SDK Platform and Google APIs for 4.1.2 (API 16)
		- Then back to maven-android-sdk-deploy for: mvn install -P 4.1
		- Success
	- Back here for another mvn clean install
	- Made it past the subtitle converter
	- Now failing on the Plexapp Android Client (see below)


[INFO] ------------------------------------------------------------------------
[INFO] Building Plexapp Android Client 1.7.3d
[INFO] ------------------------------------------------------------------------
Downloading: http://kingargyle.github.com/repo/android/support/compatibility-v4/
19.0.1/compatibility-v4-19.0.1.pom
Downloading: https://android-rome-feed-reader.googlecode.com/svn/maven2/releases
/android/support/compatibility-v4/19.0.1/compatibility-v4-19.0.1.pom
Downloading: http://4thline.org/m2/android/support/compatibility-v4/19.0.1/compa
tibility-v4-19.0.1.pom
Downloading: http://repo.maven.apache.org/maven2/android/support/compatibility-v
4/19.0.1/compatibility-v4-19.0.1.pom
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO]
[INFO] PlexApp Google TV Client Parent ................... SUCCESS [  0.208 s]
[INFO] Plexapp REST Library .............................. SUCCESS [  7.097 s]
[INFO] Android MenuDrawer (Parent) ....................... SUCCESS [  0.016 s]
[INFO] Android MenuDrawer ................................ SUCCESS [  6.287 s]
[INFO] subtitle-converter ................................ SUCCESS [  2.707 s]
[INFO] Plexapp Android Client ............................ FAILURE [  1.827 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 19.502 s
[INFO] Finished at: 2014-05-05T10:57:25-06:00
[INFO] Final Memory: 42M/339M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project serenity-app: Could not resolve depend
encies for project us.nineworlds.serenity:serenity-app:apk:1.7.3d: Failed to col
lect dependencies at android.support:compatibility-v4:jar:19.0.1: Failed to read
 artifact descriptor for android.support:compatibility-v4:jar:19.0.1: Could not
transfer artifact android.support:compatibility-v4:pom:19.0.1 from/to android-ro
me-feed-reader-repository (https://android-rome-feed-reader.googlecode.com/svn/m
aven2/releases): sun.security.validator.ValidatorException: PKIX path building f
ailed: sun.security.provider.certpath.SunCertPathBuilderException: unable to fin
d valid certification path to requested target -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e swit
ch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please rea
d the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyReso
lutionException
[ERROR]
[ERROR] After correcting the problems, you can resume the build with the command

[ERROR]   mvn <goals> -rf :serenity-app

	- This looks like a 19.0.1 dependency error
		- so back to SDK Manager
		- Platform tools and build tools for 19 are already installed
		- and maven: mvn install -P 4.4
		- Here the Google APIs was missing, but maven didn't want the x86 in the path
		- Back to SDK Manager and installed it, then renamed it to remove the x86
			C:\Users\z018620\Dev\adt\sdk\add-ons\addon-google_apis-google-19
		- another: mvn install -P 4.4
		- Finally, the GDK was missing
		- Back to SDK Manager and installed it
		- final: mvn install -P 4.4
		- Success!
	- Back for another run with mvn clean install
	- Same error as above
	- Looking at the notes from the "Using Eclipse to Build" section: https://github.com/timmilne/serenity-android
		- Am I running the right compatibility?


Seems like I should try the following combination:
* JDK 18 (JAVA_HOME): 	C:\Program Files\Java\jdk1.8.0_05
* ADK Manager: 		SDK 4.3 (API 18)
* maven: 		mvn clean install -P 4.4
			mvn install -P 4.3
			mvn clean install -P 3.2

	- I did, but ended up with the same error...

Now lets try installing all SDK platforms and all maven levels
* JDK 18 (JAVA_HOME): 	C:\Program Files\Java\jdk1.8.0_05
* ADK Manager: 		4.2, 4.0.3, 4.0, 3.1, 3.0, 2.3.3, 2.2, 2.1, 1.6, 1.5
* maven: 		mvn clean install

	- I did, but ended up with the same error...


TPM 5/13/14

As noted above, this error was resolved by changing the android.compatibility from 19.0.1 in the
plexcast-app .pom file:

      <dependency>
         <groupId>android.support</groupId>
         <artifactId>compatibility-v4</artifactId>
         <version>19.0.1</version>
      </dependency

to (19.1.0):

      <dependency>
         <groupId>android.support</groupId>
         <artifactId>compatibility-v4</artifactId>
         <version>19.1.0</version>
      </dependency


TPM 5/13/14

Other fun things I found.  You can sideload a compile apk from here:
	https://github.com/NineWorlds/serenity-android/wiki/Downloads

While trying to follow the Eclipse instructions, in addition to the environment variables listed above,
I did the following:

Eclipse in Android SDK (Juno)

http://stackoverflow.com/questions/17283486/where-is-eclipse-marketplace-in-android-sdk

Try this...
1.	Go to Help,
2.	Then click Install New Software,
3.	Press Add,
4.	Name = "your option" and Location = "http://download.eclipse.org/releases/juno" then press OK,
5.	Select Marketplace client from General Tools section
6.	Accept all the terms and conditions then press install.
7.	Then restart the ADT.

Once installed: Help->Eclipse Marketplace…
Search for m2eclipse
And the package Maven Integration for Eclipse WTP (Juno) contains: m2e-wtp, maven, wtp, m2e, m2eclipse

According to the serenity-android, I only need m2e, m2eclipse and an m2e-android (that wasn’t listed).

Not sure if I need m2e-android…


I also tried converting some eclipse projects into maven projects:

	http://wiki.eclipse.org/Converting_Eclipse_Java_Project_to_Maven_Project

importing a maven project into eclipse

	http://stackoverflow.com/questions/2061094/importing-maven-project-into-eclipse

With not much luck.