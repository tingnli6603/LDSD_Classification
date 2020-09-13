LDSD實作新聞分類
==
鏈結資料語意距離(Linked Data Semantic Distance, LDSD)(來自於Measuring Semantic Distance on Linking Data and Using it for Resources Recommendations)是藉由建立好的知識圖譜(Ontology)，找到資料與資料之間的連結並計算該連結的距離，距離越長表示兩者越不相關，距離越短表示相關性越高。

知識圖譜(Ontology):Dbpedia(https://wiki.dbpedia.org/)
--
建立一個資料與資料透過關係而連結起來的鏈結資料。如下圖，「少年PI」與「雙子殺手」皆屬於「李安」導演的電影，因此兩部電影與李安導演就是透過「isDirectorof」所連結起來。而知識圖譜就是由大量的點與關係所建構起來的。
<div align=center><img src="https://github.com/tingnli6603/LDSD_Classification/blob/master/readme_img/ontology_sample.png" width="350"></div>

