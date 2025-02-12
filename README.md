# spa-toolkit
Web Single Page Application Guide



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

      React FiberNode tree is overkilled, JSX generates fatter DOM images due to interpolation. Change detection are exhaustive,
      and serialized, hence updates.

      Widget tree is leaner, change detection and update is perform case by case, and limited to locally, less prone for race condition.

6. Convention over configuration
      a. naming
      b. data field name should be consistent among UI, API payload, to database column - keep it simple, less to manage
                Hibernate to reverse engineering from db to target JAVA POJO
                JAVA POJO to JSON
                JSON camelcase to hypenated from
                and vice versa

7. Reuse
        a. Widget hierarchy via prototype - prototype is a beautiful concept.

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


16. If using React, alternate for hydrate
        1. Generate static as preview.
        2. Client side rootRender(), then replace current DOM with the new DOM.


























