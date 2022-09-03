---
layout: post
title: "Leverage custom html validation in react forms"
date: 2022-09-03 14:51:55 +0000
categories: git
toc: false
---

# Leverage custom html validation in react forms

There are many ways to use validation in react forms, including custom frameworks with validation. However html 5 brought about some inbuilt validation which are supported in browser.

There are several standard html validators; 
* required (The is the required tag which ensure that an input has data).
* minLength / maxLength (for strings)
* min / max (for numeric input)

The browsers allow custom validation using HTMLObjectElement.setCustomValidity() [see mozilla for more](https://developer.mozilla.org/en-US/docs/Web/API/HTMLObjectElement/setCustomValidity)

When the form is submitted the validation is checked and submit cancelled and the message displayed if any of the fields are invalid.

I put together a simple react app which can be viewed [on github](https://github.com/Cookiesworld/customvalidationexample/). In this example every time the input is changed the change handler runs and looks to see if the text includes the word 'test'. If it does then *e.currentTarget.setCustomValidity('')* is called to reset the validation. Otherwise the error message is set *e.currentTarget.setCustomValidity('Field must contain \'test\'');*

The form 
```
    <Form>
        <FormGroup >
            <Form.Label>Test custom input must contain test</Form.Label>
            <Form.Control required name="test" onChange={handleChange} value={validated} />
        </FormGroup>
        <button type="submit">Submit</button>
    </Form>
```
The handler
```
const handleChange = (e) => {
    setValidated(e.currentTarget.value);
    // custom validation logic here
    if (e.currentTarget.value.includes('test')) {
      e.currentTarget.setCustomValidity('')
    } else {
      e.currentTarget.setCustomValidity('Field must contain \'test\'');
    }
}
```

When the form is submitted without the correct validation an error is shown.
![](../assets/images/validationErrorExample.png) 