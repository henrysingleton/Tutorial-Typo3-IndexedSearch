# Typo3 Tutorial for Indexed Search Engine

This tutorial will show you how to display an Indexed Search Engine search form in the frontend of a Fluid-Powered Typo3 website. It will have a dedicated search results page, but the search form can be easily placed in any fluid template, including site-wide templates. This would let you have a search box in your site header, for example. 

## Requirements 

* Typo3 7.x.x
* [Indexed Search Engine 7.x.x](https://docs.typo3.org/typo3cms/extensions/indexed_search/) installed and active
* Your own "provider" extension

#### Some notes about your provider extension
This extension is where you will typically keep all your project-specific files, templates etc. It will be referred to as `your_extension` for the rest of the tutorial, so be sure to replace any instances of `your_extension` with your actual extension directory name. If you don't have your own provider extension, you can create one easily with the [Builder](https://typo3.org/extensions/repository/view/extension_builder) extension. 

## This tutorial will teach you how to:

* Configure the Indexed Search extension
* Create the form template and partial in Fluid
* Create TypoScript Lib file, so we can communicate with the Indexed Search extension in our Fluid template.

## Basic Configuration

This basic configuration simply enables the search indexer for your site. Without this, the indexer won't index any of your pages, and you won't get any results when you submit a search query. 

`your_extension\Configuration\TypoScript\setup.txt`

```TypoScript
config {
  ## Indexed Search
  # If the cache is disabled, no indexed_search records are created
  no_cache = 0
  # Enable the indexer
  index_enable = 1
  index_externals = 1
  index_metatags = 1
}
```

## Create the results page

A results page is required to show the search results after submitting a search query.  
To create this page: 

1. Log into the Typo3 backend backend and create a new page in your site. Call it "Search Results" or something like that. 
2. Create a Content Element inside your page. Choose Plugins -> "Indexed Search" as the type.
3. Once it's created, view the "Plugin" tab when editing the Content Element, and choose "Indexed Search (Extbase & Fluid based)" from the dropdown.
4. Save the Content Element, taking note of the Page ID, since we'll need it later.

## Create the search form Template

Create a template in `your_extension\Resources\Private\Templates\Search\Form.html` with the following content. This will load in the partial we will create next. 

```HTML
<f:render partial="Search/Form" arguments="{_all}" />
```

## Create the search form Partial

Create a new file in `your_extension\Resources\Private\Partials\Search\Form.html` with the following content. This was taken directly from the Indexed Search extension (`Indexed_Search/Resources/Private/Partials/Form.html`).   
*Remember to replace PAGEID in the `<f:form />` element with the page ID you took note of above.*

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
		<f:form.textfield placeholder="Search" name="search[sword]" value="{sword}" class="tx-indexedsearch-searchbox-sword" />
	</div>
</f:form>
```

## Create the TypoScript Object

Create a new TypoScript file in `your_extension\Configuration\TypoScript\Lib\lib.search.box.ts`.  
*Remember to replace `your_extension` with your extension directory name.*

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
        10 = EXT:your_extension/Resources/Private/Templates/
      }
      partialRootPaths {
        10 = EXT:your_extension/Resources/Private/Partials/
      }
      layoutRootPaths {
        10 = EXT:your_extension/Resources/Private/Layouts/
      }
    }
  }
}
```

Include this script in your TypoScript setup file. Note that you could also copy and paste the contents straight into your setup file but this keeps it separated.  
*Remember to replace `your_extension` with your extension directory name.*

 `your_extension\Configuration\TypoScript\setup.txt`  
```TypoScript
<INCLUDE_TYPOSCRIPT: source="FILE:EXT:your_extension/Configuration/TypoScript/Lib/lib.search.box.ts">
```

## Call the object in your Fluid template 

In any fluid template inside your provider extension, you should now be able to put in the following: 

```HTML
<f:cObject typoscriptObjectPath="lib.search.box"/>
```

The search form should be shown, and when you submit the form, you should be taken to the results page you created. 

## Next Steps

You can now easily customise the appearance of the form by editing `your_extension\Resources\Private\Partials\Search\Form.html`. 

## More Information

* [Search Index extension manual](https://docs.typo3.org/typo3cms/extensions/indexed_search/)
* [Indexed Search extension wiki page](https://wiki.typo3.org/De:Indexed_search)

