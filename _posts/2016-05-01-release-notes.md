---
bg: dark
color: white
fa-icon: file-text
---

# Release Notes

## PowerMock 1.6.5 ##

  * We have a new core committer [Arthur Zagretdinov](https://github.com/thekingnothing), big welcome!
  * Experimental support for Mockito 2.x. See [getting started guide](https://github.com/jayway/powermock/wiki/GettingStarted) to get started.
  * PowerMockRunner now processes JUnit Rules correctly (issue [427](https://github.com/jayway/powermock/issues/427))
  * Added support for `@TestSubject` in EasyMock API (issue [605](https://github.com/jayway/powermock/issues/605))
  * PowerMockRunner run tests defined in super class (issue [352](https://github.com/jayway/powermock/issues/352))

## PowerMock 1.4.5 ##

  * Removed PowerMock specific classes to support mocking of signed classes in the EasyMock extension API since nowadays EasyMock supports this out of the box. This makes PowerMock less dependent on a specific EasyMock version and fixes and patches in EasyMock automatically applies to PowerMock in the future as well.
  * Upgraded the TestNG module to TestNG 5.13.1
  * Added the "toThrow" method to the stubber API. You can now do e.g. "stub(method("methodName")).toThrow(new Exception());" ([issue 182](https://code.google.com/p/powermock/issues/detail?id=182))
  * Fixed an issue that was introduced in version 1.4 when support for partial mocking of instance methods in final system classes not having a default constructor was added. The bug caused an javassist.CannotCompileException exception when mocking certain classes such as java.lang.Console ([issue 272](https://code.google.com/p/powermock/issues/detail?id=272))
  * Deprecated "andReturn" in the stubber API and added "toReturn" instead. The API now reads e.g. "stub(method("methodName")).toReturn(new Object());"

### Non backward compatible changes ###
  * Whitebox#setInternalStateFromContext now support field matching strategies. By default  all context fields that matches a field in the target class or instance is copied to the instance. If the strategy is changed from MATCHING to STRICT then an exception will be thrown if the context contains a field that cannot be copied to the target. Note that this change may cause backward incompatibility if you're currently using setInternalStateFromContext in your code

## PowerMock 1.3.7 ##

  * Support for Mockito 1.8.4 and new experimental bootstrap using JUnit 4.7+
  * It's now possible to bootstrap PowerMock using a JUnit Rule instead of the RunWith annotation. This allows you to use other JUnit runners than the PowerMock one (PowerMockRunner) while still benefiting from PowerMock's functionality. It's still experimental but we encourage everyone to try it out. It'll probably be the default way of bootstrapping PowerMock in the future. See [examples](https://github.com/jayway/powermock/tree/master/modules/module-test/mockito/junit4-rule-xstream/src/test/java/org/powermock/modules/test/junit4/rule/xstream) in subversion and the [wiki page](PowerMockRule) for more info.
  * Upgraded the Mockito extension to use Mockito 1.8.4
  * Fixed so that a "mock name" is set in the Mockito extension API. Fixes NullPointerException when e.g. toString is invoked on a mock. ([issue 239](https://code.google.com/p/powermock/issues/detail?id=239))
  * Added support for suppressing all constructors in a class using suppress(constructorsDeclaredIn(X.class))
  * Added support for suppressing all constructors and methods in a class suppress(everythingDeclaredIn(X.class))

## PowerMock 1.3.6 ##

  * Support for Mockito 1.8.3 and EasyMock 2.5.2.
  * Upgraded the Mockito extension to use Mockito 1.8.3
  * Added support for Mockito annotations @Spy, @Captor and @InjectMocks
  * Mockito extension now supports real partial mocking for private (and private final) methods (both static and instance methods) using PowerMockito.doReturn(..), PowerMockito.doThrow(..) etc
  * Added support for MockSettings and Answers when creating class or instance mocks in Mockito extension, e.g:
> ` FinalClass mock = mock(FinalClass.class, Mockito.RETURNS_MOCKS); `
  * Upgraded the EasyMock extension to use EasyMock class extensions 2.5.2
 
### (Possibly) Non-backward compatible changes ###
  * Fixed a bug in the Wildcard matcher which resulted in that classes located in packages containing "junit.", "java.", "sun." in their name was never prepared for test ([issue 225](https://code.google.com/p/powermock/issues/detail?id=225)).
  * The PowerMock API extension to EasyMock is not backward compatible with EasyMock class extension versions prior to 2.5.2 because of internal changes in this project.

## PowerMock 1.3.0 ##

PowerMock 1.3 is one of biggest releases to date and there's been lots of changes both internally and externally.

  * PowerMock now changes the context class-loader to the MockClassloader which means that you don't need to use @PowerMockIgnore as often as before. This solves many issues with frameworks that creates new instances using reflection like log4j, hibernate and many XML frameworks. If you've been using PowerMockIgnore in your test you may need to remove it (or update the ignored package list) otherwise the test might fail.
  * Test classes are now always prepared for test automatically. This means that you can use suppressing constructors and mock final system classes more easily since you don't have to prepare the actual test class for test. In some cases this change is not backward compatible with version 1.2.5. These cases are when you've tried to suppress a constructor but have forgot to prepare the test class for test as well (which should be quite rare).
  * Fixed a major issue with the prepare for test algorithm. Before classes were accidentally prepared for test automatically if the fully qualified name of the class started with the same fully qualified name as a class that were prepared for test. This has now been resolved. This may lead to backward incompatibility issues in cases where tests didn't prepare all necessary artifacts for test.
  * @PowerMockIgnore now accept wildcards, this means that if you want to ignore all classes in the "com.mypackage" package you now have to specify `@PowerMockIgnore("org.mypackage.*")` (before you did `@PowerMockIgnore("org.mypackage."`)).
  * @PrepareForTest now accepts wildcards in the fullyQualifiedName attribute, e.g. you can now do `@PrepareForTest(fullyQualifiedName={"*name*"})` to prepare all classes in containing "name" in its fully-qualified name. If you haven't been preparing packages for test earlier then this should cause no problem unless you've forgot to prepare all necessary artifacts for test.
  * PowerMock now supports mocking instance methods of final system classes (such as java.lang.String). To do this you need to prepare the class that invokes the method of the system class. Note that partial mocking of instance methods in final system classes doesn't yet work if a constructor needs to be invoked on the mock.
  * Upgraded the Mockito extension to use Mockito 1.8
  * Mocking of static methods in final system classes now works
  * When using the PowerMock Mock annotation with Mockito the method names (previously used for partial mocking) are ignored. They are no longer needed, just use PowerMockito.spy instead.
  * Support for private method expectations using `PowerMockito.when(..)`
  * You can now mock construction of new objects using the Mockito extension. To do so use `PowerMockito#whenNew(..)`, e.g. `whenNew(MyClass.class).withArguments(myObject1, myObject2).thenReturn(myMock)`. Verification can be done with `PowerMockito#verifyNew(..), e.g. verifyNew(MyClass.class).withArguments(myObject1, myObject2)`.
  * Supports suppressing constructors, fields and methods
  * Supports verification of private methods (for both static and instance methods). Use `verifyPrivate(..).invoke(..)`, e.g. `verifyPrivate(myObject, times(2)).invoke("myMethod", argument1, argument2)`.
  * Supports "verifyNoMoreInteractions" and "verifyZeroInteractions" for both instance mocks, class mocks and new instance mocks
  * Supports, doAnswer, doThrow, doCallRealMethod, doNothing and doReturn for void methods defined in final classes, final void methods and static methods (use the PowerMockito version of each method). Note that if a method is a private void method you should still use `PowerMockito#when`.
  * Classes prepared for test in parent test classes are now automatically prepared for test as well. I.e. if Test Case A extends Test Case B and A prepares X.class and B prepares Y.class then both X and Y will be prepared for test. Before only X was prepared for test.
  * Upgraded the JUnit runner to JUnit 4.7 and also implemented support for JUnit 4.7 rules.
  * Annotation support has been integrated in the test runners so there's no need to specify the AnnotationEnabler listener using `@PowerMockListener(AnnotationEnabler.class)`. It works both for the EasyMock and Mockito extension API's.
  * Added a PowerMock classloading project which can be used to execute any Runnable or Callable in a different classloader. The result of a callable operation will be cloned to the the callers classloader.
  * Implemented a more fluent API for suppression, replacing and stubbing (`org.powermock.api.support.membermodification.MemberModifier`). It uses matcher methods defined in `org.powermock.api.support.membermodification.MemberMatcher`. 
  * PowerMock and PowerMockito now supports stubbing methods (including private methods). Use PowerMock#stub or PowerMockito#stub to accomplish this.
  * PowerMock and PowerMockito now supports proxying methods, including private methods using replace(..), e.g. `replace(method(MyClass.class, "methodToProxy")).with(myInvocationHandler)`. Every call to the "methodToProxy" method will be routed to the supplied invocation handler instead.
  * PowerMock and PowerMockito now supports duck typing of static methods, including static private methods using replace(..), e.g. `replace(method(MyClass.class, "methodToDuckType")).with(method(MyOtherClass.class, "methodToInvoke"))`. Every call to the "methodToDuckType" method will be routed to the "methodToInvoke" method instead.    

### (Possibly) Non-backward compatible changes ###

  * Removed `mockStatic(Class<?> type, Method method, Method... methods)`, `mockStaticPartial`, `mockPartial`, `mock(Class<T> type, Method... methods)` & `mock(Class<T> type, Method... methods)` from the Mockito extension API. You should use `PowerMockito.spy` instead
  * Verification behavior of static methods in the Mockito extension API has changed. Before you did `verifyStatic(MyClass.class); MyClass.methodToVerify(argument);`, now you do `verifyStatic(); MyClass.methodToVerify(argument);`. You can also pass a verification mode when verifying static methods: `verifyStatic(times(2)); MyClass.methodToVerify(argument);`. This change is NOT backward compatible with version 1.2.5.

### Deprecation ###
  * Deprecated `Whitebox#getInternalState(Object object, String fieldName, Class<?> where, Class<T> type)`. Use `"Whitebox.<Type> getInternalState(Object object, String fieldName, Class<?> where)"` instead.
  * Deprecated `org.powermock.core.classloader.annotations.Mock`, you should now use the Mock annotation in respective extension API instead. For EasyMock this is `org.powermock.api.easymock.annotation.Mock` and for Mockito it's `org.mockito.Mock`.
  * Deprecated `org.powermock.api.easymock.powermocklistener.AnnotationEnabler` and `org.powermock.api.mockito.powermocklistener.AnnotationEnabler`. You should just remove them because they're now integrated with the test runner.
  * MockPolicy: Deprecated setSubstituteReturnValues, getSubstituteReturnValues and addSubtituteReturnValue in `MockPolicyInterceptionSettings`, you should now use setMethodsToStub, getStubbedMethods and stubMethod instead
  * Deprecated all suppressMethod, suppressField and suppressConstructor methods in the EasyMock extension API. You should now use PowerMock#suppress instead. See the new suppression API above.

### Removed deprecations ###
  * Removed the deprecated classes "org.powermock.PowerMock" and "org.powermock.Whitebox"

For minor changes see [change log](https://github.com/jayway/powermock/blob/master/changelog.txt).


