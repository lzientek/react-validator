

React Validation Framework
==========================

Validating fields on UI (client side validation) a lot simpler.

***

Validating fields in React is complex because of one-way binding and stores often has to be burdened.
With this, you just need to wrap your field component that needs to be validated. This also supports material-ui components.




***Install***

npm i react-validation-framework --save


***Features***
- Supports components that have specific errorText prop like in case of material-ui components
- Supports consolidated validation of a group of fields. This is acheived by specifying the group name as prop to the Validation component.
Using ref validity of a paritcular filed at any point can be found.
- If a component is not supported out of box, then support for your new/custom component can be added easily
- Error Text style can be customized

***Basic Usage***


    <Validation
      group={<optional>}
      validator={<array of validators>}
      onChangeCallback={<optional, default is 'onChange'>}
      valueProp={<optional, default is 'value'>}
      defaultValueProp={<optional, default is 'onChange'>}
      closures={<object>}
      errorStyle={<optional>} >
        <YourComponent />
    </Validation>


***Real Code Example***

    import {Validation, fieldValidatorCore} from "react-validation-framework";
    import validator from "validator";
    <Validation group="myGroup1"
    		    closures={{area}}
		        validators={[
				{
		         validator: (val) => !validator.isEmpty(val),
		         errorMessage: "Cannot be left empty"
		        }, {
	             validator: (val) => validator.isNumeric(val),
	             errorMessage: "Should be a numeric number"
	            }, {
	             validator: (val) => {
	               if (parseInt(val) > 100){
	                   return false;
	               } else {
	                   return true;
	               }
	             errorMessage: "Must be any number less than 100"
                }}]}>
                <TextField value={this.state.value}
                           className={styles.inputStyles}
                           style={{width: "100%"}}
                           onChange={
                            (evt)=>{
                              console.log("you have typed: ", evt.target.value);
			                  console.log("this value is defined in differenet scope and hence added to closures prop", area);
                            }
                           }/>
    </Validation>


***Notes***

1- Validation accepts an array of validators functions, each with their respective error messages.
The order is important and the field is validated as per the order. For validator function third-party library can be used like - [validator](github.com/chriso/validator.js),
as like above, which has whole bunch of well tested regex, like isEmpty, isEmail,
etc or we can supply our own function for specific case to validate.

2- “group” prop can be added to define the group in which the filed belongs.
Later the group name can be used to find whether a group of filed is valid or not like this :

    handleSubmit(){
        let checkFieldTestResult = fieldValidatorCore.checkGroup("myGroup1");
        if (checkFieldTestResult.isValid){
          console.log("All fields with Gropu prop value as myGroup1 is valid"
        } else {
          console.log("Some of fields with group as "myGroup1" are invalid");
          console.log("Field which are invalid are ", checkFieldTestResult.inValidComponents);
          console.log("Fields which are valid are ", checkFieldTestResult.validComponents
        }
      }


  or simply add a ref to the Validation tag and call the isValid method to find if the field is valid.
  
  3- To add a new component

Signature - 
`fieldValidatorCore.addSupport(name, getValueFromChangeEvent, changeCallBackCaller, errorPropName)`

**name** - string - Tag name of you component

**getValueFromChangeEvent** - function - (arg)=>{}

**changeCallBackCaller** - function - (callback, args)=>{}

**errorPropName**- string - if your component has a prop for errorText, ex. some* *material-ui components have "errorText"

add the component before your page component mounts, like

    componentWillMount(){
        fieldValidatorCore.addSupport("TextField", (args)=>{
          return args[0].target.value;
        }, (callback, args)=>{
          callback(args[0]);
        }, "errorText");
      }

4- (with 2.3.0), If any other prop other than value prop of your field component has value derived from upper scope/closure, ex - 'area' in onChange, it is better to added those closures in a prop called closures, otherwise, if the value changes you field componenet will still be having old value
 

    <Validation group="ga1" closures={{area}} validators={...}>
      <TextField className={styles.inputStyles}
    	     value={foo}
    	     onChange={
    	      (evt)=>{
    		fooFun(area, evt.target.value)
    	      }
    	     }
    	     onKeyUp={
    		(evt)=>{
    		  if (evt.keyCode === enterKeyCode){
    		    barFun(area, evt.target.value)
    		  }
    
    		}
    	       }
      />
    </Validation> 



