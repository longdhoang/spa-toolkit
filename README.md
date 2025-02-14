
/*

Design considerations

0.A SPA is a javascript application that interact with users via the HTML DOM document. HTML DOM document is actually application, doing the work.
        SPA injects the data, and composes the DOM document. The building blocks are the DOM nodes.
        HTML scripts are ideal for static component. For dynamic content components, it's all in memory dom node objects.
        Text strings are always embedded as a text node.


1. script on demand loading
        getD3Insance() {
        ...
        if  (D3 == 'undefined') {
                add script tag to document
        }
        setTimeout

        return D3
        }

2. the more the number of CSS classes, the longer it takes for the browser to render

3. identify browser's reflow behavior

4. design layout in such away that browser reflow impact is just on the node.

5. OOP
      Web application contains at least one widget object, which might have one or more child widget objects, and so on and on
      Widget has a globally unique ID, so it can be examined anytime.
      Widget has states 
      Widget has properties
      Widget always has application data, which could be emptry, {}, that might be visual or not visual. 
      Widget has updaters
      Widget has validators
      Widget has helpers
      Widget has view, which is a container of one or more physical HTML nodes rendered as specified by CSS  rules
      Widget can have a parent widget
      Widget can have child widget(s)

      React FiberNode tree is overkilled, JSX generates fatter DOM images due to fragmentation from javascript expression interpolation. Change detection are exhaustive,
      and serialized, hence updates is effectively serialized.

      Widget tree is leaner, change detection and update is perform case by case, and limited to locally, less prone for race condition.

6. Convention over configuration
      a. naming
      b. data field name should be consistent among UI, API payload, to database column - keep it simple, less to manage
                Hibernate to reverse engineering from db to target JAVA POJO
                JAVA POJO to JSON
                JSON camelcase to hypenated from
                and vice versa
        e.g: FIRST_NAME ->  firstName -> first-name

7. Reuse
        a. Widget hierarchy via prototype - prototype is a beautiful concept.
        b. A widget is well defined, and complete, i.e. it is functional by itself, and means exactly one thing
                  at all time.

8. Exploit javascript strength
        a. function is first class
        b. function has constructor, usually itself
        c. function has prototype
        d. prototype value is a physical object, not abstract declaration
        e. use closure wisely
        f. arrow function to establish closure correctly, binding is tedious.
        g. object augmentation.
        h. function is implied to be either a function or to be instantiated/duplicated/created as a new instance that
           would behave as a smart object


9. Execution context
        a. browser does the work
        b. application talks to the browser using WEB API, HTML DOM API, SVG API, etc
        c. application processing logic should not take less than 100ms, 
            longer than that, using setTimeout to yield the thread, and comeback later, until done.

10. Asynchronous
        a. callback should belong to widget as a behavior, not using arrow function, maintenance is over convenience.
        b. make sure to handle error accordingly, or whether chaining is safe
        c. let the browser handle DOM document updates via message/event queue, to achieve concurrency
        d. AJAX
            i. XhrXMLRequest, if you can, for proper error handling - DNS, TCP/IP, HTTP, server host, then API


11. Inter widgets communications
        a. Producer/subsciber
        b. Centralized mechanisms for maintenance, and exposure.
        c. callback passed to widget constructor, or a repository at global scope.

12. Caching
        a. dictionaries, constants
        b. API data fetch, if cached, make sure they need to be updated timely.


13. Typescript
    Eventually, the application will grow, and Typescript is the only option to help writing complex codes effectively - 
    static type checking goes a long way.


14. Development
        a. observe the picture carefully - what is the problem, or what is the ask.
        b. identify details - do I understand the problem, or the need?
        c. ask questions until there is no more questions, i.e. brainstorm sessions
        d. repeat a, b, and c
                until you have a minimum viable list of functional requirements and physical artifacts.
        e. proof of concept, one requirement or featue at a time
                repeat a, b, c, e until you cover the requirement list
        f. once POC/feasibility is confirmed, incrementally deliver the features.
        

15. Messures of success
        1. SPA advantages - advanced programming techniques and pleasant user experiences - thich client/thin server, 
            as oppose to thin client/thick server, utilising user laptop resources.
        2. Preserve the best of html page browsing
                a. Back button
                b. Book marks
        3. Print to PDF - WYSIWYG.


16. If using React for SPA, and want to improve search engine presence, here is a viable alternate for hydrate
        1. NextJs or Remix, "use client" through out, generate static html for landing page as preview.
        2. Client side rootRender(), then replace current DOM from static html with the new DOM from FiberRootNode

*/




//============================= Widget PROTOTYPES ====================
//
'use strict';
var SpaToolKit = SpaToolKit || {}; 

SpaToolKit.handlers = {};

SpaToolKit.Widget = function(options) {
        this.init(options);
	this.create();
	return this;
}


SpaToolKit.Widget.prototype.init = function(options) {
	const self = this;
	this.defaultOptions = 
	{
		baseWidgetType: 'widget',
		widgetType: 'widget',
		id: '',			// UUID - to generate other ids

				// id, name are used to generates id, name for child elements
		formName: '',
		name: undefined, // 'generic-widget',		// should be uniqe within a form, if belong to one
		hasLabel: true,
		printLabel: true,
		hasInput: true,
		hasOthers: false,
		hasErrorMessageBox: false,
		readOnly: false,
		asJSON: false,
		labelOnLeft: true,
		landscape: true,
		report: false,
		onupdate: function() {},
		onclick: function() {},
		singleProperty: true,    // true: basic types, false: object
							     // ideally, parent widget subscribe to child widget onchange
								 // child widget should never worry about larger context
								 // This is useful in case of multiple boxes
		//nestedWidget: true,	 	// if object, is nested, by design, nesting 1-1 with JSON data model
		arrayValue: false,		 // for getValue(), the tag accept multiple values, comma separated
		//singleValue: true,		 // not needed, vs arrayValue
		value: null,
		
		/*  value is determined by create() - if it's basic type, or array, or object, or nested object
		 *  create(), populate(), and getValue() should be implemented or overriden in tandem
				checked: false, 
				selected: false,
				val: {
					bool: false,
					num: 0,
					str: '',
					obj: {},
					vector: []
				}
		*/

		css: '',
		moreCss: {},		// a collenction   e.g. moreCss['btc-address'] = true
					//			moreCss['widget']
		states: { value: {}, uiStates: {} },
					// register call backs
		subscribers: { 'click': function() {}, 'update': function() {}},
		tag: 'input',
		tagType: 'text',
		tagAttrs: {
					'class': null,
					name: null,				// by widget type, and application data model relationship
												// if not single property widget, name should be 'value'
					formid: 'formid',
					id: 'id',		
					placeholder: '',
					pattern: null,
					required: true,
					spellcheck: false,
					onchange: this.onchange.bind(this),
					onkeydown: this.onkeydown.bind(this)
		}

	}
	this.mergedOptions = Object.assign({}, this.defaultOptions, options);
	this.mergedOptions.instanceId = SpaToolKit.nextUUID();
        this.value = {};
        this.origValue = {};
        this.valid = true;
        this.dirty = false;
        this.complete = false;
        this.childWidgets = {};			// list as in value object, nested	
}

SpaToolKit.Widget.prototype.getValue = function(latest) {
    for (var w in this.childWidgets) {
        // should check whether it is a subobject or part of a top level object
        this.value[this.mergedOptions.name][this.childWidgets[w].mergedOptions.name] = this.childWidgets[w].getValue();				
    }
    // ... getValue
    
	return SpaToolKit.dup(this.value);
}	

SpaToolKit.Widget.prototype.onChildChange = function(e, child) {
}
SpaToolKit.Widget.prototype.onchange = function(e) {
}
SpaToolKit.Widget.prototype.populate = function(dataObj) {

	for (var w in this.childWidgets) {
		this.childWidgets[w].populate(dataObj);				
	}
    // ... set value
	this.origValue[this.mergedOptions.name] = data;
	this.dirty = false;
	this.validate();
}
SpaToolKit.Widget.prototype.clear = function() {
	for (var w in this.childWidgets) {
		this.childWidgets[w].clear();				
	}
    // ... clear
}
SpaToolKit.Widget.prototype.reset = function() {
	for (var w in this.childWidgets) {
		this.childWidgets[w].reset();				
	}
    // .. reset
}

SpaToolKit.Widget.prototype.validate = function(dataObj) {
	for (var w in this.childWidgets) {
		this.valie &= this.childWidgets[w].validate();				
	}
     
    // ... validate
	return this.valid;
}
SpaToolKit.Widget.prototype.isValid = function(dataObj) {
	return this.valid;
}
SpaToolKit.Widget.prototype.isDirty = function(dataObj) {
	return this.dirty;
}
SpaToolKit.Widget.prototype.onkeydown = function(e) {
}
SpaToolKit.Widget.prototype.create = function() {


        let widget = {};
        // .....

		this.dom.appendChild(widget);

        // ......
		
}

SpaToolKit.Widget.prototype.addChildWidget = function(whichBox, childWidgetDomNode) {
}

SpaToolKit.Widget.prototype.focus = function() {
}

SpaToolKit.Widget.prototype.hide = function() {
}

SpaToolKit.Widget.prototype.show = function() {
}

SpaToolKit.Widget.prototype.disable = function(children) {
    for (var w in this.childWidgets) {
        this.childWidgets[w].disable();
    }
    // .. disable
}

SpaToolKit.Widget.prototype.update = function() {
}
SpaToolKit.Widget.prototype.destroy = function() {
}
SpaToolKit.Widget.prototype.getState = function() {
}












