# Tech skills

## 1, Git

### search resources

input the attributes in the search filed.

``in: name spring boot stars:> 3000``

```
in:description cloud languge:java pushed:>2020-01-01
```

## 2, eclipse

Eclipse Hot Keys:

### Maintenance

1) Open Resource (Suggestion Box)
-Open file(s) by suggested name. You can provide wildcard with *.
Ctrl + Shift + R

2) Open Outline (Suggestion Box)
-Navigates to the line in the code for the certain variable or method declaration that you are looking for. Very useful for searching for a method.
Ctrl + O

3) Clean Imports
Ctrl + Shift + O

4) Comment Method
-Adds a method comment to all methods if no method is selected
Ctrl + Alt + J

5) Refactor Name
-Refactors across all objects that use this variable
(Cursor in variable) > Alt + Shift + R

6) Generate Variable Methods
-If you have a lot of variables in a DTO that require Get and Set methods to be created, you can get quicker to the interface with the following:
(Alt + Shift + S) > Generate Getters and Setters...

### Tracing, Searching, and Navigating Code

1) Find/Replace
-In the Options section you want to have the following checked: Incremental, Wrap Search. You want to have unchecked: Case sensitive, Whole Word.
-If you are able to type without looking at the keyboard you will very quickly find what you are looking for with Incremental searching on.
Ctrl + F

2) Instance of String
Next Instance:
(Highlight String) > (Ctrl + K)
Previous Instance:
(Highlight String) > (Ctrl + Shift + K)

3) Global Search
-The File Search tab is the best tab, the other ones are garbage lol. It will throw an error when you finish searching.
	> Search > File... > File Search (Tab)
	Containing text: <variable>
	File name patterns: *

Drill Down:
4) Open Implementation
	i) (Ctrl + Left Click)
		-This will open the interface if it exists which is probably not what you are looking for.
	ii) (Ctrl + Hover) > Open Implementation > (Choose the object that implements the code not the interface)
		-You can open the implementation using this. It still gives you the option to open the interface as a choice though.
	
Drill Up:
5) Call Hierarchy

(Highlight a Method Name) > (Alt + Ctrl + H)
	-This will give you all objects that call this method, all objects that call the method that call this method, ... (Until it can't go up the hierarchy anymore)
6) set the show the varies scope
 	i)Ctrl + Shift + O
  ii)  windows-> preferences->java->Editor->Mark Occurences

<img src="https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHAvaW1nLmJsb2cuY3Nkbi5uZXQvMjAxNDA2MTgxMDE4MTczMTI/d2F0ZXJtYXJrLzIvdGV4dC9hSFIwY0RvdkwySnNiMmN1WTNOa2JpNXVaWFF2ZDI5dVpteDVNakF4TVE9PS9mb250LzVhNkw1TDJUL2ZvbnRzaXplLzQwMC9maWxsL0kwSkJRa0ZDTUE9PS9kaXNzb2x2ZS83MC9ncmF2aXR5L1NvdXRoRWFzdA==.jpg" alt="img" style="zoom:100%;" />



### Important Eclipse Views (Windows):

JAVA EE PERSPECTIVE
1) Package Explorer
	-Hides the library breakdown of the jar files for navigation.
	
2) Ant
	-Allows you to drag the build file into an interface controllable with double click instead of navigating the (right click) context menu on the build.xml

3) Console
	-Shows you messages from the glassfish server and the ant build. Also shows errors when running/debugging java code.

4) Markers
	-Show any Java errors that are appearing in Eclipse.
	-IMPORTANT: If an error is in eclipse that doesn't make sense you can DELETE THE ERROR from here.

5) Servers
	-Allows you a place to start the glassfish server without going to the command line. Glassfish will go down when you close eclipse though.
	
6) Synchronize
	-The most important view you know what this is.

7) History
	-The second most important window, allowing you to compare revisions.
	

### DEBUG PERSPECTIVE

1) Debug (Extremely Important)
	-This shows you any servers you have up and running and if you have attached a debugger to them.
	-IMPORTANT: This is effectively the debug stack in eclipse. You can navigate up and down the stack and through different threads. (You can debug multi-threaded code.)
	
2) Variables
	-Shows you all active variables in the current debug state.

3) Expressions
	-Allows you to watch a specific variable as you debug. (You are looking for a variable that becomes null at some point in time, add it to the expressions view. [Similar to VBA watch])

4) Breakpoints
	-KEEP THIS CLEAN. It's very important to be aware of which breakpoints you are tracing.
	-You can setup conditional break points in eclipse, that only trigger if a certain criteria is met. i.e. i == 100 where i is the iterator tracking which iteration of the loop you are in.
	-Allows you to quickly navigate back to key points in your code. Like having a bookmark.

Debug Hotkeys
1) Step Over
F6

2) Step Into
F5

3) Run
F8

4) Step Return
-I never use this
F7

#### Debug Shortcuts

1) Inspect
-IMPORTANT: Inspect is not showing you the result of the code you run, IT IS RUNNING THE CODE!
-But that means that you can alter the live state of the page you are debugging by inspecting dirty code that you write that doesn't match what you deployed.
-i.e. I have a String name = "Chris"; During debug I can write a line to say 'name = "Mayur"' and inspect that and it will change the name variable to Mayur without redeploying.
-This is very useful if you just want to check that a change will allow the code to proceed without deploying.

-Additionally the inspect functionality will allow you to run through the sub-routine code as if it were running WITHOUT CRASHING THE CODE. (As long as it's not volatile.)
-This means that if you know there is a null pointer that you are looking for coming soon, you can inspect the method and have it run and crash and be back where you started so you can drill down further, without having to reset the page state.
*You are not able to double inspect. You can't inpsect inside of code that is being inspected.

*For computationally heavy code such as BCrypt's exponentially calculated encrypted string that it returns, it will put a huge load on the debugger. If you set the breakpoint after that code it will be able to bypass it without much time.
-For example I have it set to 2^14 passes taking around 1 second in CPU time but takes around 14 seconds when debugging.

(Highlight the single line code you want to run) > (Right Click) > Inspect
OR
(Highlight the single line code you want to run) > (Ctrl + Shift + I)

2) Stop Local Debug As
-If you are using main or a JUnit, Debug as Java Application, you can kill the process with the following command
Ctrl + F2

3) Start Local Debug As
*UNTESTED
Alt + Shift + D, J

-I usually just click the drop down list beside the bug icon or the bug icon itself.



## 3, debug on the front end

1) open the Chrome back view -> click the resource

![image-20201204115410828](C:\Users\julie.yang\AppData\Roaming\Typora\typora-user-images\image-20201204115410828.png)

2) F5 refresh the page, the code will be shown on this filed

3) set the break point, refresh the page get the break point

4) F10 step by step, F8 go through the code to next break point.

5) in this path to change the xhtml file without redeploy to the server.

D:\wildfly\standalone\tmp\vfs\temp\temp824ceaabd940d3ab\content-22887c2fa1ad89c0\account 

#### Note: we have three solutions for solve the problems (get the message from global.prorerty or other global files)

- add the hidden element has the global message, then set attribute to the "date-fv-identical-message"

need to add some code the template.xhml->js part

- create a customer formValidation like the server.js or currency.js, it change the message by using the global.xhtml file

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://java.sun.com/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:c="http://java.sun.com/jstl/core"
	xmlns:a4j="http://richfaces.org/a4j"
    xmlns:rich="http://richfaces.org/rich">
 <script type="text/javascript">
//<![CDATA[
var SuretyCloud = {};

SuretyCloud.global = {
	numInv: '#{global.numInv}',
	timeInv: '#{global.timeInv}',
	currencyInvKey: '#{global.currencyInvKey}',
	currencyInvLong: '#{global.currencyInvLong}',
	currencyInv: '#{global.currencyInv}',
	dateInvAdult: '#{global.dateInvAdult}',
	dateInvKey: '#{global.dateInvKey}',
	bidDtTmeNull: '#{global.bidDtTmeNull}',
	einInvShort: '#{global.einInvShort}',
	einInvLong: '#{global.einInvLong}',
	einInv: '#{global.einInv}',
	expMonthNull: '#{global.expMonthNull}',
	expDateInvalid: '#{global.expDateInvalid}',
	numDashInv: '#{global.numDashInv}',
	numInvKey: '#{global.numInvKey}',
	percentageInv: '#{global.percentageInv}',
	percentageInvMsg: '#{global.percentageInvMsg}',
	positiveNoValidation: '#{global.positiveNoValidation}',
	prcntValueBase: '#{global.prcntValueBase}',
	phoneInv: '#{global.phoneInv}',
	phoneInvShort: '#{global.phoneInvShort}',
	phoneInvLong: '#{global.phoneInvLong}',
	zipInv: '#{global.zipInv}',
	zipNull: '#{global.zipNull}',
	zipInvShortUS: '#{global.zipInvShortUS}',
	zipInvLongUS: '#{global.zipInvLongUS}',
	zipInvUS: '#{global.zipInvUS}',
	zipInvShortCA: '#{global.zipInvShortCA}',
	zipInvLongCA: '#{global.zipInvLongCA}',
	zipInvCA: '#{global.zipInvCA}',
	zipInvKey: '#{global.zipInvKey}',
	selectZip: '#{global.selectZip}',
	selectFromList: '#{global.selectFromList}',
	passInvShort: '#{global.passInvShort}',
	passInvUpper: '#{global.passInvUpper}',
	passInvLower: '#{global.passInvLower}',
	passInvDigit: '#{global.passInvDigit}',
	ssnInvStart: '#{global.ssnInvStart}',
	ssnInvShort: '#{global.ssnInvShort}',
	ssnInvLong: '#{global.ssnInvLong}',
	ssnInv: '#{global.ssnInv}',
	thousandInv: '#{global.thousandInv}',
	thousandInvLong: '#{global.thousandInvLong}',
	aLimitInv: '#{global.aLimitInv}',
	sLimitInv: '#{global.sLimitInv}',
	grossVal: '#{global.grossVal}',
	fnlPrcVal: '#{global.fnlPrcVal}',
	amtBankLineVal: '#{global.amtBankLineVal}',
	fullBankLineVal: '#{global.fullBankLineVal}',
	creditLineVal: '#{global.creditLineVal}',
	amtCreditLineVal: '#{global.amtCreditLineVal}',
	antStarDtInvl: '#{global.antStarDtInvl}',
	antCmplDtInv1: '#{global.antCmplDtInv1}',
	subBrkrNotExist: '#{global.subBrkrNotExist}',
	emailInv: '#{global.emailInv}',
};
//]]>
</script>
</html>
```

```js
$(document).ready(function() {
	FormValidation.Validator.currency = {
			 html5Attributes: {
				 sign: "sign",
				 greaterThanZero: "greaterThanZero",
				 greaterThanValue: "greaterThanValue",
				 lessThanValue: "lessThanValue",
				 message: "message"
			 },
		init: function(validator, $field, options) {
			var $form = validator.getForm();
			
			//Run the focus code if this is the active element.
			var activeEle = document.activeElement;
			if (activeEle && $field[0].id == activeEle.id) {
				currencyFocus($field[0]);
			}
			
			//Attach additional events
			var fieldName = $field.attr('data-fv-field');
			$form.on('blur', '[name="' + fieldName + '"]', function() { currencyBlur($field[0]); })
			$form.on('focus', '[name="' + fieldName + '"]', function(event) { currencyFocus($field[0]); });
			$form.on('keypress', '[name="' + fieldName + '"]', function(event) { return currencyKeypress(event, $field[0]); });
		},
		
		destroy: function(validator, $field, options) {
			var $form = validator.getForm();
			
			//Remove additional events
			var fieldName = $field.attr('data-fv-field');
			$form.off('blur', '[name="' + fieldName + '"]');
			$form.off('focus', '[name="' + fieldName + '"]');
			$form.off('keypress', '[name="' + fieldName + '"]');
		},
	
		validate: function(validator, $field, options) {
			var value = $field.val();
			if (value === '') {
				return true;
			}
			//THIS IS BEING CALLED BY THE TEMPLATE BACKING PROCESSING THE AJAX EVENT
        	//START - CHECK DATE TIME INVALID
            var eleId = $field[0].id;
            var currencyMsgId = eleId + "Msg";
			
			var currencyMsg = document.getElementById(currencyMsgId);
			
			if (currencyMsg != null) {
				var currencyMsgExist = currencyMsg.innerText != "";
				
				if (currencyMsgExist) {
					var tempMessage = currencyMsg.innerText;
					//dateMsg.innerText = "";
					
					return {
						valid: false,
						message: tempMessage
					};
				}
			}
			//END - CHECK CURRENCY INVALID
			
			var lengthFail = false;
			
			var eleVal = value.replace(/[,]/g, "");
			
			var decInd = eleVal.indexOf(".");
			
			if (decInd > -1) {
				if (decInd > 10 || eleVal.length > 12) {
					lengthFail = true;
				}
			} else {
				if (eleVal.length > 9) {
					lengthFail = true;
				}
			}

			if (lengthFail) {
				return {
					valid: false,
					message: SuretyCloud.global.currencyInvLong
				};
			}
			
			var eReg = /^([0-9]{1,3})((,[0-9]{3})*|([0-9]{3})*)(\.[0-9]{0,2})?$/;
            
			if (options.sign == "both") {
				eReg = /^[-+]?([0-9]{1,3})((,[0-9]{3})*|([0-9]{3})*)(\.[0-9]{0,2})?$/;
			} 
			
			var match = eReg.exec(value);
			if (match == null) {
				return {
					valid: false,
					message: SuretyCloud.global.currencyInv
				};
			}
			
			minVal = 0;
			var eleVal1 = parseFloat(value);
			if (options.greaterThanZero == "both") {
				minVal = 1;
			if(eleVal1 < minVal) {
				return {
					valid: false,
					message: SuretyCloud.global.positiveNoValidation
				};
			}
			}
			
```

```javascript
$(document).ready(function() {
	FormValidation.Validator.server = {
		init: function(validator, $field, options) {
			var $form = validator.getForm();
			var fieldName = $field.attr('data-fv-field');
			
			$form.on('change', '[name="' + fieldName + '"]', function() { serverChange($field[0]); });
		},
		
		destroy: function(validator, $field, options) {
			var $form = validator.getForm();
			
			//Remove additional events
			var fieldName = $field.attr('data-fv-field');
			$form.off('change', '[name="' + fieldName + '"]');
		},
	
		validate: function(validator, $field, options) {
			var value = $field.val();
			if (value === '') {
				return true;
			}
			//THIS IS BEING CALLED BY THE TEMPLATE BACKING PROCESSING THE AJAX EVENT
        	//START - CHECK DATE TIME INVALID
            var eleId = $field[0].id;
            var msgId = eleId + "Msg";
			
			var msgEle = document.getElementById(msgId);
			
			if (msgEle != null) {
				var msg = msgEle.innerText;
				
				if (msg != "") {
					return {
						valid: false,
						message: msg
					};
				}
			}
			//END - CHECK DATE TIME INVALID

			return true;
		}
	};
	
	function serverChange(ele) {
		var eleId = ele.id;
		var msgId = eleId + "Msg";
		
		var msgEle = document.getElementById(msgId);
		
		if (msgEle != null) {
			msgEle.innerText = "";
			$(ele).closest("form").formValidation('revalidateField', ele.name);
		}
	}
});
```

server.js.

in the html file using it like this

```html
<div class="col-xs-11 col-md-7">
			<h:selectOneMenu id='bndFrmNme' styleClass='form-control' value='#{bondLimitsBacking.bondLimitsDTO.baiOverlayDTO.templateFormId}'
				data-fv-server="true">
				<f:selectItems value='#{bondLimitsBacking.bondLimitsDTO.templateFormAddEditSelectList}'/>
			</h:selectOneMenu>
			<h:outputText value="#{global.bndFrmNull}" styleClass='fvAttribute' id='bndFrmNme-Null'/>
			<div style='display: none'>
				<rich:message id='bndFrmNmeMsg' for='bndFrmNme' errorClass='componentError'/>
			</div>
		</div>
```

