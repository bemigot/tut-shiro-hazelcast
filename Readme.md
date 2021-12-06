See https://stormpath.com/blog/hazelcast-support-apache-shiro

by Brian Demers (2016-07-27)

test
```
mvn test
```
run
```
mvn jetty:run -Djetty.port=9091
# in another terminal, so the output is not intermixed
mvn jetty:run -Djetty.port=9092
```

## step-2
added Hazelcast (with default built-in configuration)

Running the two servers above
we can see in the first terminal window
```
com.hazelcast.cluster.ClusterManager
INFO: [10.0.0.10]:5701 [dev]

Members [1] {
	Member [10.77.132.112]:5701 this
}
```
and in the second terminal window, after a pause:
```
Members [2] {
	Member [10.0.0.10]:5701
	Member [10.0.0.10]:5702 this
}
```
the first window is also updated, listing the new `:5702` Member

When the second Member goes down (on ^C), the 1st log shows
return to the initial status `Members [1] {..}`

## step-3
adds `hazelcast.xml` configuration file to show Hazelcast cluster re-configuration
in step-4: renaming `<group>` "dev" to something else, and changing port allocation.

A kind of mystery is a reference to `hazelcast-config-2.5.xsd` here,
since shiro-hazelcast:1.3.x pulls hazelcast:2.4 (2.4.1)

## Errata
Hazelcast 2.4 configuration code produces this misleading error message:
```
[Fatal Error] :6:3: The element type "hr" must be terminated by the matching end-tag "</hr>".
```
it doesn't seem affecting Hazelcast or this webapp functioning.

## Log sample
```
:00,002 o.a.shiro.web.servlet.OncePerRequestFilter: Filter 'ShiroFilter' not yet executed.  Executing now.
:00,009 o.a.shiro.mgt.DefaultSecurityManager: Context already contains a SecurityManager instance.  Returning.
:00,010 o.a.shiro.session.mgt.AbstractValidatingSessionManager: Attempting to retrieve session with key org.apache.shiro.web.session.mgt.WebSessionKey@4112edf5
:00,010 o.a.shiro.web.servlet.SimpleCookie: Found 'JSESSIONID' cookie value [6cc6cb72-3fcb-4110-a624-8b4a05feb3f2]
:00,028 o.a.shiro.session.mgt.AbstractValidatingSessionManager: Attempting to retrieve session with key org.apache.shiro.web.session.mgt.WebSessionKey@2541ce89
:00,031 o.a.shiro.session.mgt.AbstractValidatingSessionManager: Attempting to retrieve session with key org.apache.shiro.web.session.mgt.WebSessionKey@2541ce89
:00,038 o.a.shiro.session.mgt.AbstractValidatingSessionManager: Attempting to retrieve session with key org.apache.shiro.web.session.mgt.WebSessionKey@2541ce89
:00,041 o.a.shiro.session.mgt.AbstractValidatingSessionManager: Attempting to retrieve session with key org.apache.shiro.web.session.mgt.WebSessionKey@2541ce89
:00,048 o.a.shiro.subject.support.DelegatingSubject: attempting to get session; create = false; session is null = false; session has id = true
```
