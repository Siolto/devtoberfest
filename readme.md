# Flexible Programming Model (FPM) Integration

## 1. Make the App FPM ready

1. Extend the Component from `sap/fe/core/AppComponent` insteead of `sap/ui/core/UIComponent`

2. Extend the App Controller from `sap/fe/core/PageController` instead of `sap/ui/core/mvc/Controller`

3. Adapt the `manifest.json`:
    - Add routing to the `manifest.json`
    ```json
    "routing": {
        "routes": [
            {
                "pattern": ":?query:",
                "name": "mainPage",
                "target": "mainPage"
            }
        ],
        "targets": {
            "mainPage": {
                "type": "Component",
                "id": "mainPage",
                "name": "sap.fe.core.fpm",
                "options": {
                    "settings": {
                        "viewName": "sap.ui.core.tutorial.odatav4.view.App",
                        "entitySet": "People",
                        "navigation": {}
                    }
                }
            }
        }
    }
    ```

    - remove the `rootView` from the `manifest.json`

4. Add the required libraries you need to use the FPM:

```yaml
framework:
  name: SAPUI5
  version: "1.118.1"
  libraries:
    - name: sap.fe.macros
    - name: sap.fe.templates
    - name: sap.f
    - name: sap.m
    - name: sap.ui.core
    - name: sap.ui.layout
    - name: themelib_sap_horizon
```

5. Add the flexibility capabilities to the app

```html
data-sap-ui-flexibilityServices='[{"connector": "LocalStorageConnector"}]'
```

5. For better performance let's build the resources with the help of the `ui5-middleware-serveframework` middleware

```bash
npm i -D ui5-middleware-serveframework
```
```yaml
server:
  customMiddleware:
    - name: ui5-middleware-serveframework
        afterMiddleware: compression
```

Your App should now be ready for the FPM to be used!

## 2. Use the Building Blocks/Macros Table

1. Remove all the coding from the `App.controller.js` leaving only the blank controller skeleton. Your controller should now look like this:

```javascript
sap.ui.define([
	"sap/fe/core/PageController"
], function (PageController) {
	"use strict";

	return PageController.extend("sap.ui.core.tutorial.odatav4.controller.App", {});
});

```
2. Remove all the coding from the `App.view.xml` inside of the `content` aggregation of the `Page` control. Your view should now look like this:

```xml
<mvc:View controllerName="sap.ui.core.tutorial.odatav4.controller.App"
	displayBlock="true"
	xmlns="sap.m"
	xmlns:mvc="sap.ui.core.mvc"
	xmlns:macros="sap.fe.macros">
	<Shell>
		<App busy="{appView>/busy}"
			class="sapUiSizeCompact">
			<pages>
				<Page title="{i18n>peoplePageTitle}">
					<content>
					</content>
				</Page>
			</pages>
		</App>
	</Shell>
</mvc:View>
```
Add the table building block to the content aggregation:

```xml
<content>
	<macros:Table metaPath="@com.sap.vocabularies.UI.v1.LineItem"
		id="peopleTable" />
</content>
```

3. Test the app. It should now work with a Building Block control with no additional coding

4. Add the mockserver to the app to experience the whole capabilities of the Building Blocks

```bash
npm i -D @sap-ux/ui5-middleware-fe-mockserver
```
```yaml
- name: sap-fe-mockserver
      mountPath: /
      afterMiddleware: compression
      configuration:
        watch: true
        services:
          - urlPath: '/sap/opu/odata/sap/'
            metadataPath: './webapp/localService/metadata.xml'
            mockdataPath: './webapp/localService/mockdata'
```
```html
data-sap-ui-oninit="module:sap/ui/core/ComponentSupport"
```

## 3. Add an Object Page with the help of the Fiori Tools

1. Add another route in the `manifest.json`

```json
"routing": {
    "routes": [
        {
            "pattern": ":?query:",
            "name": "mainPage",
            "target": "mainPage"
        },
        {
            "pattern": "/People({key}):?query:",
            "name": "detailPage",
            "target": "detailPage"
        }
    ],
    "targets": {
        "mainPage": {
            "type": "Component",
            "id": "mainPage",
            "name": "sap.fe.core.fpm",
            "options": {
                "settings": {
                    "viewName": "sap.ui.core.tutorial.odatav4.view.App",
                    "entitySet": "People",
                    "navigation": {
                        "People": {
                            "detail": {
                                "route": "detailPage"
                            }
                        }
                    }
                }
            }
        },
        "detailPage": {
            "type": "Component",
            "id": "detailPage",
            "name": "sap.fe.templates.ObjectPage",
            "options": {
                "settings": {
                    "entitySet": "People",
                    "navigation": {}
                }
            }
        }
    }
}
```

2. Add the Flexible Column Layout (FCL) with the help of the Fiori Tools Extension

3. Extend the Object Page with the help of the Fiori Tools `Page Map``