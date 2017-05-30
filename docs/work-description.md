## Crawling and Generating Lucene Index

#### Step. 1 - Crawl all web Urls from AOL dataset
There are **1,632,797** URLs in AOL dataset and we have crawled **1,051,483** wchich is **64.4%** of the total number of URLs. Remaining URLs are either dead, broken or moved permanently to another URL. List of all the URLs can be found [here](https://drive.google.com/a/virginia.edu/file/d/0B8ZGlkqDw7hFNkc0c0p1OVF2YTA/view).

We have stored the crawled data in one xml file and the format of the file looks like below.

```xml
<?xml version="1.0" encoding="utf-8"?>
<crawledData>
   <page id="45" url="http://www.google.com/">
		 <anchor>Null</anchor>
		 <content>Search Images Maps Play YouTube News Gmail Drive More »Web History | Settings | Sign in × Try a fast, secure browser with updates built in. Yes, get Chrome now  Advanced searchLanguage toolsAdvertising ProgramsBusiness Solutions+GoogleAbout Google© 2016 - Privacy - Terms</content>
   </page>
   <page id="455" url="http://www.apple.com/">
		 <anchor>Null</anchor>
		 <content>Open Menu Close Menu Apple Shopping Bag Apple Mac iPad iPhone Watch TV Music Support Search apple.com Shopping Bag iPad Pro Super. Computer. NowÂ inÂ twoÂ sizes. Learn more Watch the film Watch the keynote iPhone SE A big step for small. Learn more Watch the keynote Apple Watch You. At a glance. Learn more Watch the keynote March Event 2016 Watch the keynote Apple and Education. Create more a-ha moments. iPhone 6s. 3D Touch. 12MP photos. 4K video. One powerful phone. Apple tv. The future of television is here. Macbook. Light. Years ahead. Better together. Shop our collection of curated accessories. AC Wall Plug Adapter Recall Program Apple Footer Shop and Learn Open Menu Close Menu MaciPadiPhoneWatchTVMusiciTunesiPodAccessoriesGift Cards Apple Store Open Menu Close Menu Find a StoreGenius BarWorkshops and LearningYouth ProgramsApple Store AppRefurbishedFinancingReuse and RecyclingOrder StatusShopping Help For Education Open Menu Close Menu Apple and EducationShop for College For Business Open Menu Close Menu iPhone in BusinessiPad in BusinessMac in BusinessShop for Your Business Account Open Menu Close Menu Manage Your Apple IDApple Store AccountiCloud.com Apple Values Open Menu Close Menu EnvironmentSupplier ResponsibilityAccessibilityPrivacyInclusion and DiversityEducation About Apple Open Menu Close Menu Apple InfoJob OpportunitiesPress InfoInvestorsEventsHot NewsContact Apple More ways to shop: Visit an Apple Store, call 1-800-MY-APPLE, or find a reseller. United States Copyright Â© 2016 Apple Inc. All rights reserved. Privacy Policy Terms of Use Sales and Refunds Legal Site Map</content>
	</page>
</crawledData>
```

#### Step. 2 - Creating Lucene Index
  * Create the lucene index from all crawled data by running [Indexer.java](https://github.com/wasiuva/Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/index/Indexer.java).
  * For AOL dataset, our lucene index can be found [here](https://drive.google.com/a/virginia.edu/file/d/0B8ZGlkqDw7hFMGZkVF9FSUtqMW8/view?usp=sharing).

## Generating Pre-requisite Data

#### Step. 1 - Generate Language Models

 * Generate hierarchical language models based on DMOZ dataset and corresponding crawled webpages. These language models are stored in a directory by maintaining hierarchy. To generate the hierarchical language models, run the [LanguageModelGenerator.java](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/model/LanguageModelGenerator.java) program.
 * This is required for cover query generation which is the core part of this hierarchical privacy presercing IR model.

#### Step. 2 - Generate User Search Logs

 * User search logs are a list of user submitted query, query timestamp and their corresponding clicked document.
 * Top N user search logs are generated from AOL dataset to evaluate the model. Top users mean the users with maximum search query with at least one clicked document.
 * User search logs can be generated by running [SearchLogBuilder.java](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/searchlog/SearchLogBuilder.java) program.

#### Step. 3 - Generate AOL Dictionary

 * Two type of AOL dictionary is required, one using document frequency of the words and the other one using total term frequency of the words. The first one is required for *TF-IDF* calculation and the second is required to generate the reference model.
 * To generate AOL dictionary, [AOLDictionary.java](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/preprocessing/AOLDictionary.java) program can be used.
 * AOL dictionary is generated based on the URLs we have crawled from AOL dataset.

#### Step. 4 - Generate DMOZ Dictionary

 * This is required to generate the reference model. 
 * To generate the DMOZ dictionary, [DMOZDictionary.java](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/preprocessing/DMOZDictionary.java) program can be used.
 * The dictionary will be created from the crawled webpages related to different DMOZ categories. Dictionary will contain words along with their total term frequency.

#### Step. 5 - Generate Reference Model

 * Reference model is required for smoothing purpose at different times.
 * To generate reference model, [ReferenceModel.java](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/user/ReferenceModel.java) program can be used.
 * Reference model is created over all AOL and DMOZ crawled dataset.

#### Step. 6 - Generate AOL Term Index

 * AOL term index is required to compute **Normalized Mutual Information** between true user query and its cover queries.
 * To generate the AOL term index, [TermIndex.java](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/preprocessing/TermIndex.java) program can be used.

## Setup Input Variables

To run the model, we need to set values of some input variables in the [settings.txt](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/settings.txt) file. The file looks like below.

```
number of cover queries = 2
client side re-ranking = off
lucene AOL index directory = lucene-AOL-index/
users search log directory = data/user_search_logs/
language models directory = data/language_models/
reference model file = data/Reference-Model
AOL dictionary file = data/AOL-Dictionary
term index file = data/Term-Index
number of threads = 4
```

## Evaluating Hierarchical Privacy-Preseving-IR Model

The entire evaluation procedure on a single user works as follows.
 * Load the reference model, language models, AOL dictionary and term index.
 * Load user judgements to measure search effectiveness (Mean Average Precision).
 * Generate **k** cover queries for each user query using hierarchical language models.
 * Each user query along with the **k** cover queries are submitted to the search engine. Search effectiveness is measured only for the true user query.
 * [Evaluate.java](https://github.com/wasiuva/Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/eval/Evaluate.java) program follows the above mentioned steps.

## Run Full Pipeline for N Users
 
 * Run [MultiThread.java](https://github.com/wasiahmad/Hierarchical-Privacy-Preserving-IR/blob/master/src/edu/virginia/cs/eval/MultiThread.java) program with all the input variables set in the **settings.txt** file.
 