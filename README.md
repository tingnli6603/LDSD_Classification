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
<img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/direct_distance_algorithm.png" width="600">

(前後兩者的差異在於方向性，方向是從ra到rb或是從rb到ra。)

<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/direct_distance_sample.png" width="350"></div>


假設我們只要看其中一個property l1兩者之間的關係，從ra到rb的分母指的是ra在l1的關係下可以找到幾個value；分子則是在這些value下，是否有一個是rb，如果有為1，沒有則為0(表示兩者之間在此property下是沒有關係的)。而從rb到ra也是如此，只是反過來搜尋。那如果有多個i，也就是把所有Property加總起來而已。

> 分母如此設定的意義在於在不同關係(li)下，所產生的value數量可能不一樣，那兩者之間的連結強度也不同。假設ra跟rb在l1與l2都有連結，ra在l1的value有100個，而在l2的value只有5個，那兩者在l2的連結強度會大於l1。其實概念就是每個人的朋友可能有幾百個人，但能稱為好朋友的大概就那5個，因此如果兩個人在好朋友的關係下有連結，那強度會比朋友關係還來的強，距離也會較短。

### 間接距離
<img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/indirect_distance_algorithm.png" width="600">

(間接距離我們視為兩者是沒有差異的，因此計算完一個後乘2)

<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/indirect_distance_sample.png" width="350"></div>


也是先假設只有1個關係l1，就是搜尋兩個Entity ra與rb在l1的關係下可以找到那些value，分母的部分以ra找到的value總數為主；分子則是看ra在l1找到的value跟rb在l1找到的value相同的有幾個，如果數量越多表示兩者關係強度越強，如果完全沒關係就為0。一樣有越多li就是加總而已。

分類實作
--
### 新聞資料 http://mlg.ucd.ie/datasets/bbc.html
資料為BBC的新聞資料，總共有2,225篇。各類別數量:
| 類別 | 數量 |
| :---: | :---: |
| business | 510 |
| entertainment | 386 |
| politics | 417 |
| sport | 511 |
| tech | 401 |


### 分類想法
實作方法有點類似KNN的做法，我們會在每個類別都挑出幾個具有代表性的Entity，稱為類別Entities。分類方法就是將文本的Entity提取出來後，跟每個類別Entities計算LDSD，加總平均後看文本跟哪個類別的LDSD距離最小，即為該類別。

我們利用簡單的方法挑選類別Entities，是將每一個類別都隨機挑出80%文章作為代表文章，將80%文本內的所有Entity作為類別Entities，剩下的20%則為測試資料。

<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/ldsd_classification.png" width="600"></div>

1.單一text entity與每個business entity計算完LDSD後做加總平均表示單一text entity與business類別的語意距離。

2.所有text entity與business entities計算完後做加總平均，即代表這篇文章與這個類別的語意距離。

3.該文章與所有類別都計算完後，就可以選擇距離最小者作為分類類別。

### Property挑選
在不同的分類應用我們可以挑選不同的Property，而在這邊我們挑選常出現的兩個property使用，分別為dct:subject與gold:hypernym。

程式檔案介紹
--
#### get_news_entities.ipynb
我們透過https://github.com/dbpedia-spotlight/dbpedia-spotlight 可以自行架設DBpedia Spotlight服務。將所有文本放入服務後取得Entities。
#### get_news_entities_problem.ipynb
DBpedia Spotlight服務問題，輸入的文本長度不能太長，所以切割區塊放入。
#### split_news_class_test.ipynb
將所有文本依照80%、20%切割。
#### get_all_class_test_entities.ipynb
取得所有entity、各類別entities與將做為test data的文本放到同一個資料夾。
#### get_all_entities_property_value.ipynb
找到所有Entity在所選擇的兩個property底下的value，將所有資料都存下來，之後計算時只要查表即可。要注意DBpedia Rest API有流量的限制，時間內不可以存去太多次。
#### all_value_in_one.ipynb
將所有的Entity、Property與Value存放到同一個檔案，做為查找檔案。
#### folder_statistic.ipynb
各個檔案的統計資料。
#### ldsd_algorithm.ipynb
計算所有語意距離。
#### score_count/ answer_file_split.ipynb
將計算完分數的檔案切割，以便計算分數。
#### score_count/ score_count.ipynb
計算每一個類別的分類分數。

成效
--
| 類別 | Precision | Recall | F-measure |
| :---: | :---: | :---: | :---: |
| business | 0.6772 | 0.8431 | 0.7511 |
| entertainment | 0.8857 | 0.7949 | 0.8378 |
| politics | 0.9524 | 0.7143 | 0.8163 |
| sport | 0.8099 | 0.9515 | 0.875 |
| Tech | 0.8955 | 0.7407 | 0.8108 |
