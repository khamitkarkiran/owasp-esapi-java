While ESAPI security control reference implementations may perform the security checks and result in the security effects required by your organization and/or application, there may be a need to eliminate the ability of developers to deviate from your organization’s and/or application’s policies. High developer turnover may be an issue, for example. Another example would be to strongly enforce a coding standard.
The “extended” factory patterns refers to the addition of a new security control interface and corresponding implementation, which in turn calls ESAPI security control reference implementations and/or security control reference implementations that were replaced with your own implementations. The ESAPI locator class would be called in order to retrieve a singleton instance of your new security control, which in turn would call ESAPI security control reference implementations and/or security control reference implementations that were replaced with your own implementations.

For example:<todo: replace PHP with Java>
```
In the ESAPI locator class:
...
class ESAPI {
...
//not defined in ESAPI locator class
private static $adapter = null;
...
//new function
public static function getAdapter() {
if ( is_null(self::$adapter) ) {
require_once dirname(__FILE__).'/adapters/MyAdapter.php';
self::$adapter = new MyAdapter();
}
return self::$adapter;
}
//new function
public static function setAdapter($adapter) {
self::$adapter = $adapter;
}
```
In the new security control class’ interface:
```
...
//new interface
interface Adapter {
function getValidEmployeeID($eid);
function isValidEmployeeID($eid);
}
```
In the new security control class:
```
...
require_once dirname ( __FILE__ ) . '/../Adapter.php';
//new class with your implementation
class MyAdapter implements Adapter {
//for your new interface
function getValidEmployeeID($eid) {
//calls reference implementation
$val = ESAPI::getValidator();
//calls using hardcoded parameters
$val->getValidInput(
"My Organization's Employee ID",
$eid,
"EmployeeID", //regex defined in ESAPI config
4,
false
);
}

//for your new interface
function isValidEmployeeID($eid) {
try {
$this->getValidEmployeeID($eid);
return true;
} catch ( Exception $e ) {
return false;
}
}
```
Developers would call ESAPI in this example as follows:
```
...
$ESAPI = new ESAPI();
$adapter = ESAPI::getAdapter();
$adapter->isValidEmployeeID(1234);
... //no other ESAPI controls called directly
```
The UML for the above example is in the figure below.

Figure 4: Extended Factory Pattern Example <todo: picture>

Pros of taking this approach are the same as for the extended singleton pattern, and additionally include loose coupling between ESAPI and your own implementations, compared to the extended singleton pattern.

Cons include the need to maintain the modified ESAPI locator class (as new versions of ESAPI are released over time).