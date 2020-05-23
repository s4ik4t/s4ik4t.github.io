---
title: Migrating from JUnit
---

Migrating from JUnit
--------------------

### Using Eclipse

The easiest way to convert your JUnit tests to TestNG is to use the Eclipse TestNG plug-in refactoring support. You will find a full description of its features in the [Eclipse section](eclipse.html#eclipse-quickfix).

### Asserts

Note that the class org.testng.Assert uses a different argument ordering than the ones used by JUnit. If you are porting code that uses JUnit's asserts, you might want to us a static import of that class:

import static org.testng.AssertJUnit.\*;

### Running JUnit Tests

TestNG can automatically recognize and run JUnit tests, so you can use TestNG as a runner for all your existing tests and write new tests using TestNG.

All you have to do is to put JUnit library on the TestNG classpath, so it can find and use JUnit classes, change your test runner from JUnit to TestNG in Ant and then run TestNG in "mixed" mode. This way you can have all your tests in the same project, even in the same package, and start using TestNG. This approach also allows you to convert your existing JUnit tests to TestNG incrementally.

#### Example - replacing JUnit Ant task with TestNG one

JUnit version:
```
<junit dir="${work.dir}" errorproperty="tests.failed" failureproperty="tests.failed" fork="true">
    <batchtest todir="${build.test.results.dir}">
        <fileset dir="${test.src.dir}">
            <include name="\*\*/\*Test.\*"/>
    </batchtest>
    <classpath>
        <path path="${run.test.classpath}"/>
    </classpath>
    <syspropertyset>
        <propertyref prefix="test-sys-prop."/>
        <mapper from="test-sys-prop.\*" to="\*" type="glob"/>
    </syspropertyset>
    <formatter type="xml"/>
    <jvmarg value="-ea"/>
    <jvmarg line="${run.jvmargs}"/>
</junit>
```
TestNG version:
```
<taskdef name="testng" classname="org.testng.TestNGAntTask" classpath="${run.test.classpath}"/>

<fileset id="mixed.tests" dir="${test.src.dir}">
    <include name="\*\*/\*Test.\*"/>
</fileset>

<testng mode="mixed" classfilesetref="mixed.tests" workingDir="${work.dir}" failureProperty="tests.failed" outputdir="${build.test.results.dir}">
    <classpath>
        <pathelement path="${build.test.classes.dir}"/>
        <pathelement path="${run.test.classpath}"/>
        <pathelement path="${junit.lib}"/>
    </classpath>
    <propertyset>
        <propertyref prefix="test-sys-prop."/>
        <mapper from="test-sys-prop.\*" to="\*" type="glob"/>
    </propertyset>
    <jvmarg line="${run.jvmargs}"/>
</testng>
```
### Related reading

*   [Here is the detailed report of a company that successfully converted a large codebase of JUnit 4 tests over to TestNG](https://www.opengamma.com/blog/2011/04/04/converting-opengamma-junit-testng).
*   [Mixed mode in TestNG](https://wiki.netbeans.org/TestNG_MixedMode).