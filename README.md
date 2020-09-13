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
DBpedia Spotlight是找出文本中哪些字詞是有在Dbpedia資料中的，並且從中找出來，這些找出來的字詞稱為Entity，也就是上圖的「李安」、「少年PI」與「雙子殺手」。
#### DBpedia Rest API (https://wiki.dbpedia.org/rest-api)
DBpedia Rest API則是將找到的Entity輸入後，會得到Property與Value，Property就是上面的關係，也就是「isDirectorof」；Value則是根據此Property所找到的Entity。以上圖來說，將「李安」這個Entity輸入到DBpedia Rest API後，會找到有一個叫「isDirectorof」的Property，他所對應的Value就有「少年PI」與「雙子殺手」這兩個Entity。
