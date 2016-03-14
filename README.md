# Typo3 Tutorial for Indexed Search Engine
This tutorial help you to use the Indexed Search Engine 7.x.x

<strong>Before to start</strong><br>
I assum you have:
* Typo3 7.x.x
* Indexed Search Engine 7.x.x is installed

your own Ext in Typo3conf/Ext. All paths I use are related to this (Ext).

What it will happen:
* Configuration of the Indexed Search
* Creating Template and Partial in Fluid
* Creating TypoScript Lib to use it as object

## Minimal configuration
in: \Configuration\TypoScript\setup.txt
```TypoScript
config {
  ## Indexed Search
  # If the cache is disabled, no indexed_search records are created
  no_cache = 0
  index_enable = 1
  index_externals = 1
  index_metatags = 1
}
```
For more informations about the conf -> [Indexed Search Conf](https://wiki.typo3.org/De:Indexed_search)<br>
For more informations about the ext  -> [Indexed Search Doc](https://docs.typo3.org/typo3cms/extensions/indexed_search/latest/)

## Creating the page
Go in your Backend and create a new page.
Put inside a Content Element in Plugins -> Indexed Search (Extbase & Fluid based).<br>
Save the Page ID for later.

## Create the form as Template
in: \Resources\Private\Templates\Search\Form.html
```HTML
<f:render partial="Search/Form" arguments="{_all}" />
```

## Create the input form as Partial
in: \Resources\Private\Partials\Search\Form.html<br>
Take the ID of your page with the Plugin and set it in pageUid 
```HTML
<f:form pageUid="PAGEID" action="search" method="post" id="tx_indexedsearch" noCacheHash="true">
	<div class="tx-indexedsearch-hidden-fields">
		<f:form.hidden name="search[_sections]" value="0" />
		<f:form.hidden name="search[_freeIndexUid]" id="tx_indexedsearch_freeIndexUid" value="_" />
		<f:form.hidden name="search[pointer]" id="tx_indexedsearch_pointer" value="0" />
		<f:form.hidden name="search[ext]" value="{searchParams.ext}" />
		<f:form.hidden name="search[searchType]" value="{searchParams.searchType}" />
		<f:form.hidden name="search[defaultOperand]" value="{searchParams.defaultOperand}" />
		<f:form.hidden name="search[mediaType]" value="{searchParams.mediaType}" />
		<f:form.hidden name="search[sortOrder]" value="{searchParams.sortOrder}" />
		<f:form.hidden name="search[group]" value="{searchParams.group}" />
		<f:form.hidden name="search[languageUid]" value="{searchParams.languageUid}" />
		<f:form.hidden name="search[desc]" value="{searchParams.desc}" />
		<f:form.hidden name="search[numberOfResults]" value="{searchParams.numberOfResults}" />
		<f:form.hidden name="search[extendedSearch]" value="{searchParams.extendedSearch}" />
	</div>
	<div class="tx-indexedsearch-form">
		<f:form.textfield placeholder="Suche" name="search[sword]" value="{sword}" class="tx-indexedsearch-searchbox-sword" />
	</div>
</f:form>
```

## Create the TypoScript Object
in: \Configuration\TypoScript\Lib\lib.search.box.ts
```TypoScript
lib.search.box = COA
lib.search.box {

  10 = USER
  10 {

    userFunc = TYPO3\CMS\Extbase\Core\Bootstrap->run

    vendorName = TYPO3\CMS
    extensionName = IndexedSearch
    pluginName = Pi2
    controller = Search
    action = search

    view {
      templateRootPaths {
        10 = EXT:ext/Resources/Private/Templates/
      }
      partialRootPaths {
        10 = EXT:ext/Resources/Private/Partials/
      }
      layoutRootPaths {
        10 = EXT:ext/Resources/Private/Layouts/
      }
    }
  }
}
```
Load it in your Setup
in: \Configuration\TypoScript\setup.txt
```TypoScript
<INCLUDE_TYPOSCRIPT: source="FILE:EXT:Ext/Configuration/TypoScript/Library/lib.search.box.ts">
```
### Congrats!
Now you can render it in your template
```HTML
<f:cObject typoscriptObjectPath="lib.search.box"/>
```
