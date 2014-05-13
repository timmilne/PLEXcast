TPM 5/12/14

Sheesh,

To get this to compile, I had to make changes to the pom.xml file in the serenity-app directory.  Diff
pom.xml and pom - Orig.xml for all the differences.  The compiler-maven-plugin version needed to be
added to some of the other repo's poms to suppress warnings as well.

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