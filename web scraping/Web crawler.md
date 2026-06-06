## web crawling tutorial 

Here I have designed a workflow diagram defining how does web scraping really looks.

For web crawling technique I have used the most basic approach i.e. DDGS + BS4

We will be using a VLM instead of LLM or make a hybrid approach to direct which model to be used for the task.

DDGS is for DuckDuckGo search and BS4 is BeautifulSoap

We have used this to know the main concept like how the things are really working.

BeautifulSoap is used to parse HTML/XML and extract elements like text, images, metadata, and embedded media references.

DuckDuckGo search libraries such as DDGS/ duckduckgo-search can retrieve text, image, video and news search results.

Use this when you need fast search result and for complex pages we will use other framework.

So according to the Architecture I have provided we will know each of the layer's hidden work.

The 1st one, user's query understanding:

for example the user provided a query as : "explain the agentic orchestration with diagrams"

so from this we will extract the query type, domain type, freshness, and source quality.

{

     "query\_type" : text\_only | image\_only | video needed | mixed, --in this case it will be mixed--

     "domain\_type" : programming | science | legal | medical | finance | research, --in this case programming--

     "freshness" : latest | old/historic --in this case latest--

     "source\_quality" : official | academic | news | general --in this case official--

}

if user have uploaded some file then it will by default redirect to that part of workflow i.e. RAG system of retrieval.

now the layer 2, DuckDuckGo search :

this layer is not for data extraction, rather than a site finder.

it should return in a format like,

{
     "url" : --site url over here--

     "result\_type" : text | image | video | --in a form of tuple--

     "searh\_rank" : it should rank the extraction quality on the basis of given query.

     "source/domain" : official | academic | news | general

}

Then the layer 3, URL filtering +  source Ranking :

It should retrieve the sites we should set hardcoded for own safety, its good to by default set only trusted sites. like -

\*you can use these sites too --

Programming → official docs, GitHub, package docs
Science → papers, university pages, journals
Legal → official government/legal sources
Medical → WHO, NIH, hospitals, medical journals
News → trusted publishers, date-sensitive
Finance → official exchange/company/filing/news sources
Research → arXiv, papers, Semantic Scholar-like pages

Then the layer 4, Robots + safety check :

Before that, we should check if the site allowed crawling or not. In python we have predefined library for that, urllib.robotparser.RobotFileParser

which checks whether the a user agent may fetch a URL according to the site's robots.txt file.

This layer should handle --

* robots.txt check
* rate limiting
* timeout
* retry policy
* user-agent
* domain cooldown
* max pages per query

Then the layer 5, BeautifulSoup Extractor :

it should extract :

1. main text
2. paragraphs
3. lists
4. tables if simple
5. links
6. image URLs
7. video/embed URLs
8. meatadata
9. author/date if available
10. canonical URL

\*BeautifulSoap is built for navigating, searching and modifying parsed HTML/XML trees.

\*BeautifulSoap can not understand video content by itself we need a thumbnail embedding or VLM for transcript extraction.

Then the layer 6, Media decision layer :

The main thing is never to see and download every image/video from the site, cause that will make the process very noisy and slow also use of heavy storage. Always have a decision logic for the task whether to download or not.

It would be better to store in this format :

{

     ALWAYS : \[ extract text,

         extract media candidates as metadata

              ],

     IMAGE : \[ user asks for image,

        query asks for diagram/chats/figures/visuals/photos.

               test result is weak,

        page contains important explanatory images,

        image caption/title,

              ]

     VIDEO : \[ user asks for video,

    	    video is tutorial/explanation,

        page text is not enough,

        transcript/thumbnails/title if relevant,

}

So this will tell the VLM how to perform the task, it's more like a skill for the VLM to perform his work.

so the till now the overall workflow is like :  

HTML page --> Extract text --> Extract image/video candidates -> Score media relevance --> Only store useful media embeddings

Now after we have completed the extraction all the required data we have to store it in the vector DB which will be then carried for answer retrieval by the VLM.

you can use simple RAG system or HYBRID RAG system or even your own retrieval strategy.

If you consider my strategy, then a more faster retrieval strategy would be the best one.

it should sore these data-

::::::::::::::::::

{

     embeddings

     content type 

     text/chunks

     source URL

     local file path if downloaded

     metadata

}

for the image and video it will store under a single folder -> ../media/image/ or ../media/video/

But the main thing is the vector should store like Qdrant stores named vectors, meaning one point can have different vector spaces for text, image and video or other modalities.

The arrangement structure should look like -

.json/

{

    "id" : "source\_chunk\_id",

    "vectors" : {

    		"text" : "......",

    		"image" : ".......",

    		"video" : ",,,,,,,,",

    	    },

    "payload" : {

    		"query":".....",

    		"URL" : ".......",

    		"title" : "pages\_title",

    		"content\_type" : "image|text|video|link",

    		"text" : '.....',

    		"media" : "image/video URL",

    		"local\_path" : "media got downloaded",

    		"source\_domain" : "domain name",

    		"timestamp" : "when did the crawling happened",

    		"rank" : "content's rank in float"

    	   }

}

Damn !!

Now connect the vector DB to the VLM to retrieve answer with your own kind of skill being described. like how should the answer should be looking and extracted. describe all those things to a skill.md file or direct inject the SYSTEM\_PROMPPT to the VLM and enjoy!!!
So we have completed the overall web crawling using DDGS + BS4. There are other ways but for less computing this techinique is acceptable. 

Other ways like crawl4ai integration that makes things more easier. We will talk about that in another chapter.

So this was the theory to build your own web crawler. In the next tutorial we will be building the web crawler full integration.

Hope this was informative for you, if any question or correction on my side then you can contact me, 
G-mail : metamorphtech.uk@gmail.com



"Please correct me if any information is incorrect, and feel free to share your thoughts or suggestions on this topic.

