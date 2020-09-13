LDSD實作新聞分類
==
鏈結資料語意距離(Linked Data Semantic Distance, LDSD)(來自於Measuring Semantic Distance on Linking Data and Using it for Resources Recommendations)是藉由建立好的知識圖譜(Ontology)，找到資料與資料之間的連結並計算該連結的距離，距離越長表示兩者越不相關，距離越短表示相關性越高。

知識圖譜(Ontology)
--
建立一個資料與資料透過關係而連結起來的鏈結資料。如下圖，「少年PI」與「雙子殺手」皆屬於「李安」導演的電影，因此兩部電影與李安導演就是透過「isDirectorof」所連結起來。而知識圖譜就是由大量的點與關係所建構起來的。
<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/ontology_sample.png" width="350"></div>

### Dbpedia(https://wiki.dbpedia.org/)
Dbpedia則是從維基百科的資料萃取結構化的資料，將大量的資料依照關係串連起來。主要使用Dbpedia中的兩個API，分別為Dbpedia Spotlight與DBpedia Rest API。

#### Dbpedia Spotlight (https://www.dbpedia-spotlight.org/)
DBpedia Spotlight是找出文本中哪些字詞是有在Dbpedia資料中的，並且從中找出來，這些找出來的字詞稱為 __Entity__ ，也就是上圖的「李安」、「少年PI」與「雙子殺手」。

#### DBpedia Rest API (https://wiki.dbpedia.org/rest-api)
DBpedia Rest API則是將找到的Entity輸入後，會得到Property與Value。 __Property就是上面的關係__ ，也就是「isDirectorof」； __Value則是根據此Property所找到的Entity__ 。以上圖來說，將「李安」這個Entity輸入到DBpedia Rest API後，會找到有一個叫「isDirectorof」的Property，他所對應的Value就有「少年PI」與「雙子殺手」這兩個Entity。

LDSD
--
<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/ldsd_algorithm.png"></div>
(來源: Measuring Semantic Distance on Linking Data and Using it for Resources Recommendations)

LDSD指的是兩個Entity之間的距離，公式如下。主要拆分成前半部分與後半部分，前半部為直接距離，後半部則為間接距離。
> ra與rb就是前述所說的Entity，也就是資料點；l代表Property，也就是兩點之間的關係是什麼，i則表示有多個Property，在兩個Entity之間他們可能由不同的關係所連接起來。

### 直接距離
<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/direct_distance_algorithm.png" width="600"></div>
(前後兩者的差異在於方向性，方向是從ra到rb或是從rb到ra。)
<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/direct_distance_sample.png" width="350"></div>

假設我們只要看其中一個property l1兩者之間的關係，從ra到rb的分母指的是ra在l1的關係下可以找到幾個value；分子則是在這些value下，是否有一個是rb，如果有為1，沒有則為0(表示兩者之間在此property下是沒有關係的)。而從rb到ra也是如此，只是反過來搜尋。那如果有多個i，也就是把所有Property加總起來而已。

> 分母如此設定的意義在於在不同關係(li)下，所產生的value數量可能不一樣，那兩者之間的連結強度也不同。假設ra跟rb在l1與l2都有連結，ra在l1的value有100個，而在l2的value只有5個，那兩者在l2的連結強度會大於l1。其實概念就是每個人的朋友可能有幾百個人，但能稱為好朋友的大概就那5個，因此如果兩個人在好朋友的關係下有連結，那強度會比朋友關係還來的強，距離也會較短。

### 間接距離
<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/indirect_distance_algorithm.png" width="600"></div>
(間接距離我們視為兩者是沒有差異的，因此計算完一個後乘2)
<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/indirect_distance_sample.png" width="350"></div>

也是先假設只有1個關係l1，就是搜尋兩個Entity ra與rb在l1的關係下可以找到那些value，分母的部分以ra找到的value總數為主；分子則是看ra在l1找到的value跟rb在l1找到的value相同的有幾個，如果數量越多表示兩者關係強度越強，如果完全沒關係就為0。一樣有越多li就是加總而已。
