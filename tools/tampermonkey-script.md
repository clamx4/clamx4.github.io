### stackoverflow cookie prompt remover
```js
// ==UserScript==
// @name         Cookie prompt remover
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       You
// @match        https://stackoverflow.com/questions/*
// @match        https://webapps.stackexchange.com/questions/*
// @match        https://sapui5.hana.ondemand.com/*
// @icon         https://www.google.com/s2/favicons?sz=64&domain=stackoverflow.com
// @grant        none
// ==/UserScript==

const sleep = async function( millisecond ) {
    return new Promise( resolve => setTimeout( resolve, millisecond ) );
};

(function() {
    'use strict';
    const handleUi5Cookie = async() => {
        for (let i = 0; i < 100; i++) {
            const div = document.querySelector('div#sap-ui-blocklayer-popup');
            if (div !== null) {
                console.log('ui5 cookie div found', div);
                const dialogDiv = div.previousSibling;
                console.log(div);
                console.log(dialogDiv);
                div.style.visibility = 'hidden';
                dialogDiv.style.visibility = 'hidden';
                console.log('ui5 cookie div hidden');
                break;
            }
            console.log('ui5 cookie div not found');
            await sleep(50);
        }
    };
    const handleUi5Header = () => {
        const css = 'div.sapTntToolPageHeaderWrapper{display:none;}' +
              'div.sapUxAPObjectPageHeader{position:fixed;right:0px;}';
        addCss(css);
    };
    const handleStackOverflow = () => {
        const css = 'div#onetrust-consent-sdk{display:none;}';
        addCss(css);
    };
    const addCss = (cssString) => {
        const style = document.createElement('style');
        style.innerHTML = cssString;
        document.head.appendChild(style);
    };

    // Your code here...
    if (window.location.href.startsWith('https://stackoverflow.com/questions/') ||
        window.location.href.startsWith('https://webapps.stackexchange.com/questions')) {
        handleStackOverflow();
    }
    if (window.location.href.startsWith('https://sapui5.hana.ondemand.com')
        //&&
       //window.location.href.includes('/api/')
    ) {
        console.log('cookie remover for ui5 doc');
        handleUi5Cookie();
        handleUi5Header();
    }
})();
```

### diffchecker ad remove
```js
// ==UserScript==
// @name         diffchecker ad remove
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       You
// @match        https://www.diffchecker.com/
// @match        https://www.diffchecker.com/text-compare/
// @icon         https://www.google.com/s2/favicons?sz=64&domain=diffchecker.com
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    function addStyle() {
        const prefix1 = 'diff-checker_ads__';
        const prefix2 = 'ad-box_container__';
        // const prefixList = ['diff-checker_ads__', 'hide-print ad-box_container__'];

        try {
            const classNames = []
            const classSelectors = [];
            const adElement1 = document.querySelector(`[class^="${prefix1}"]`);
            classNames.push(adElement1.className);
            classSelectors.push(`.${adElement1.className}`);
            const adElement2 = document.querySelector(`[class^="hide-print ${prefix2}"]`);
            for (const className of adElement2.classList) {
                if (className.startsWith(prefix2)) {
                    classNames.push(className);
                    classSelectors.push(`.${className} *`);
                    break;
                }
            }
            // const styleText = `${classNames.map(x => '.' + x).join(',')} { display: none; }`;
            const styleText = `${classSelectors.join(',')} { display: none; height: 0; min-height: 0;}`;
            const style = document.createElement('style');
            style.textContent = styleText;
            document.head.appendChild(style);
            console.info(new Date().toISOString(), 'tamper monkey, adding style', styleText);
        } catch (e) {
            console.error('error when removing diffchecker.com ad', e);
        }
    }

    // Your code here...
    console.log('document.readyState', document.readyState)
    if (document.readyState === 'loading') {
        document.addEventListener("DOMContentLoaded", function() {
            console.log('doc loaded via DOMContentLoaded event handler')

            alert('not tested yet');
            console.log('doc loaded2')
            addStyle();
        });
    } else {
        console.log('loaded docu');
        addStyle();
    }

    window.onload = () => {
        //alert('window onload')
        console.log('window loaded')
        //debugger;
        //addStyle();
    }
})();
```
