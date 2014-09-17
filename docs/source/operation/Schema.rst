B5M站点配置说明
===================

collection集合
-------------------
B5M站点根据需求配置collection，分别定义如下：

* 商品相关

B5M对商品提供自动比价，比价之前来自每商家的商品被定义为Offer，比价之后聚合的商品被定义为Product，
Offer和Product均放入不同的collection，分别为b5mo和b5mp，类似的，商品的评论放入b5mc集合中。
爬虫抓来的商品数据，首先会放入一个离线的计算工具来计算比价信息，然后分别输出结果到b5mo和b5mp中，此外，
如果有评论信息，也会同时聚合。

b5mo的主要配置字段如下：
::

    <DocumentSchema>
      <Property name="DOCID" type="string" />
      <Property name="Url" type="string"/>
      <Property name="Title" type="string" />
      <Property name="Picture" type="string" />
      <Property name="OriginalPicture" type="string" />
      <Property name="Price" type="float" />
      <Property name="Brand" type="string" />
      <Property name="Source" type="string" />
      <Property name="Category" type="string" />
      <Property name="TargetCategory" type="string" />
      <Property name="OriginalCategory" type="string" />
      <Property name="Attribute" type="string" />
      <Property name="Content" type="string" />
      <Property name="uuid" type="string" />
      <Property name="olduuid" type="string" />
      <Property name="mobile" type="int32" />
      <Property name="PicPrpt" type="string" />
    </DocumentSchema>

    
    <IndexBundle>
      <Schema>
        <Property name="Title">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>

        <Property name="uuid">
          <Indexing filter="yes" multivalue="no" doclen="no" rankweight="0.1" />
        </Property>

        <Property name="Category">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>

        <Property name="Price">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>

        <Property name="Source">
          <Indexing filter="yes" multivalue="no" doclen="no" rankweight="0.1" />
        </Property>

        <Property name="mobile">
          <Indexing filter="yes" multivalue="no" doclen="no" rankweight="0.1" />
        </Property>
      </Schema>
    </IndexBundle>

    <MiningBundle>
      <Schema>
        <QueryRecommend>
          <QueryLog />
        </QueryRecommend>
        <Group>
          <Property name="TargetCategory" />
          <Property name="Source" />
        </Group>
    	<Attr>
          <Property name="Attribute" />
          <Exclude name="ISBN" />
        </Attr>
        <ProductRanking>
          <Score type="diversity" property="Source" />
          <Score type="merchant" property="Source" />
          <Score type="category" property="TargetCategory" weight="1" />
          <Score type="relevance" weight="0.01" />
        </ProductRanking>
        <SuffixMatch>
          <Property name="Title" />
          <TokenizeDictionary path="fmindex_dic" />
          <Incremental enable="no" />
          <FilterProperty name="TargetCategory" filtertype="group" />
          <FilterProperty name="Source" filtertype="group" />
          <FilterProperty name="Price" filtertype="numeric" />
        </SuffixMatch> 
      </Schema>
    </MiningBundle>


b5mp的主要配置字段如下:
::

    <DocumentSchema>

      <Property name="DOCID" type="string" />
      <Property name="OldOfferIds" type="string" />
      <Property name="Url" type="string"/>
      <Property name="Title" type="string" />
      <Property name="Picture" type="string" />
      <Property name="OriginalPicture" type="string" />
      <Property name="Price" type="float" />
      <Property name="Brand" type="string" />
      <Property name="Source" type="string" />
      <Property name="Category" type="string" />
      <Property name="TargetCategory" type="string" />
      <Property name="OriginalCategory" type="string" />
      <Property name="Attribute" type="string" />
      <Property name="Content" type="string" />
      <Property name="Xihuan" type="int32" />
      <Property name="Fenxiang" type="int32" />
      <Property name="Guanzhu" type="int32" />
      <Property name="BuyCount" type="int32" />
      <Property name="itemcount" type="int32" />
      <Property name="Score" type="float" />
      <Property name="CommentCount" type="int32" />
      <Property name="mobile" type="int32" />
      <Property name="PicPrpt" type="string" />
    </DocumentSchema>

    
    <IndexBundle>
      <Schema>
        <Property name="Title">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.8" />
        </Property>
        <Property name="Price">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" range="yes" />
        </Property>
        <Property name="Brand">
          <Indexing filter="yes" multivalue="yes" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Source">
          <Indexing filter="yes" multivalue="yes" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="TargetCategory">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>
        <Property name="Category">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>
        <Property name="Attribute">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.2" />
        </Property>
        <Property name="Xihuan">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Fenxiang">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Guanzhu">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="BuyCount">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="itemcount">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="CommentCount">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Score">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="mobile">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <VirtualProperty name="Combined">
          <SubProperty name="Title" />
          <SubProperty name="Source" />
          <SubProperty name="Category" />
        </VirtualProperty>
      </Schema>
    </IndexBundle>
    
    <MiningBundle>
      <Schema>
        <QueryRecommend>
          <QueryLog />
        </QueryRecommend>
        <Group>
          <Property name="TargetCategory" />
          <Property name="Brand" />
          <Property name="Source" />
        </Group>
        <Attr>
          <Property name="Attribute" />
          <Exclude name="ISBN" />
        </Attr>
        <ProductRanking>
          <Score type="custom" weight="100" />
          <Score type="category" property="TargetCategory" weight="10" />
          <Score type="relevance" weight="0.001" />
          <Score type="diversity" property="Source" /> 
          <Score type="fuzzy" weight="0.1" />
          <Score type="popularity" weight="1">
            <Score property="itemcount" weight="1" />
            <Score property="Score" weight="0.8" />
            <Score property="Fenxiang" weight="0.5" />
            <Score property="Guanzhu" weight="0.5" />
            <Score property="Xihuan" weight="0.5" />
            <Score property="BuyCount" weight="0.5" />
            <Score property="_ctr" weight="0.5" />
            <Score property="DATE" weight="0.2" />
          </Score>
        </ProductRanking>
        <SuffixMatch>
          <Property name="Title" />
          <TokenizeDictionary path="product" />
          <Incremental enable="no" />
          <FilterProperty name="TargetCategory" filtertype="group" />
          <FilterProperty name="Source" filtertype="group" />
          <FilterProperty name="Brand" filtertype="group" />
          <FilterProperty name="Price" filtertype="numeric" />
        </SuffixMatch>
      </Schema>
    </MiningBundle>

    <RecommendBundle>
      <Schema>
        <User>
          <Property name="gender" />
          <Property name="age" />
          <Property name="area" />
        </User>
        <Track>
          <Event name="wish_list" />
          <Event name="own" />
          <Event name="like" />
          <Event name="favorite" />
        </Track>
      </Schema>
    </RecommendBundle>


.. note::
    b5mo和b5mp之间通过uuid字段进行关联


b5mc的配置字段如下:
::

    <DocumentSchema>

      <Property name="DOCID" type="string"/>

      <Property name="uuid" type="string"/>

      <Property name="DATE" type="datetime"/>

      <Property name="ComUrl" type="string"/>

      <Property name="ProdDocid" type="string"/>

      <Property name="ProdName" type="string"/>

      <Property name="Source" type="string"/>

      <Property name="UserName" type="string"/>

      <Property name="UsefulVoteTotal" type="int32"/>

      <Property name="UsefulVote" type="int32"/>
      
      <Property name="Content" type="string"/>
      <Property name="Advantage" type="string"/>
      <Property name="Disadvantage" type="string"/>

      <Property name="Title" type="string"/>
      <Property name="City" type="string"/>

      <Property name="Score" type="int32"/>
    </DocumentSchema>

    <IndexBundle>
      <Schema>
        <Property name="Title">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.8"/>
        </Property>

        <Property name="Content">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.2"/>
        </Property>

        <Property name="Advantage">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.2"/>
        </Property>

        <Property name="Disadvantage">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.2"/>
        </Property>

        <Property name="Source">
          <Indexing filter="yes" multivalue="no" doclen="no"/>
        </Property>

        <Property name="City">
          <Indexing filter="yes" multivalue="no" doclen="no"/>
        </Property>

        <Property name="ProdDocid">
          <Indexing filter="yes" multivalue="no" doclen="no"/>
        </Property>

        <Property name="uuid">
          <Indexing filter="yes" multivalue="no" doclen="no"/>
        </Property>

        <Property name="Score">
          <Indexing filter="yes" multivalue="no" doclen="no"/>
        </Property>

      </Schema>

    </IndexBundle>

    <MiningBundle>
      <Schema>
        <Group>
          <Property name="Score" />
          <Property name="Source" />
        </Group>
        <ProductRanking>
          <Score type="diversity" property="Source" />
        </ProductRanking>
        <Summarization>
          <DocidProperty name="DOCID"/>
          <UuidProperty name="uuid"/>
          <ContentProperty name="Content"/>
          <AdvantageProperty name="Advantage"/>
          <DisadvantageProperty name="Disadvantage"/>
          <TitleProperty name="Title"/>
          <ScoreProperty name="Score"/>
          <CommentCountProperty name="CommentCount"/>
          <OpinionProperty name="Opinion" />
          <OpinionWorkingPath path="./collection/b5mc/opinion_working/" />
          <OpinionSyncId name="b5m" />
        </Summarization>
      </Schema>
    </MiningBundle>


.. note::
    b5mc和b5mp之间通过uuid字段进行关联，b5mc和b5mo之间通过ProdDocid字段进行关联

* 团购相关

团购也提供比价，但出于历史原因，并没有采用类似商品的Offer-Product映射关联，而是直接采用online
计算提供，与Offer对应的集合叫tuanm，与Product对应的集合叫tuana。

tuanm对应的配置:
::

    <DocumentSchema>

      <Property name="DOCID" type="string" />
      <Property name="SourceUrl" type="string" />
      <Property name="City" type="string" />
      <Property name="Url" type="string"/>
      <Property name="Title" type="string" />
      <Property name="Discount" type="float" />
      <Property name="Source" type="string" />
      <Property name="MerchantAddr" type="string" />
      <Property name="Content" type="string" />  
      <Property name="Sales" type="int32" />
      <Property name="TimeBegin" type="string" />
      <Property name="TimeEnd" type="string" />
      <Property name="PriceOrign" type="float" />
      <Property name="MerchantName" type="string" />
      <Property name="MerchantTel" type="string" />
      <Property name="MerchantArea" type="string" />
      <Property name="Price" type="float" />
      <Property name="Category" type="string" />
      <Property name="OriginalCategory" type="string" />
      <Property name="SubCategory" type="string" />
      <Property name="Picture" type="string" />
      <Property name="OriginalPicture" type="string" />
      <Property name="uuid" type="string" />	  
      

    </DocumentSchema>

    
    <IndexBundle>
      <Schema>
        <Property name="Title">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="uuid">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Price">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
      </Schema>
    </IndexBundle>


tuana对应的配置:
::

    <DocumentSchema>

      <Property name="DOCID" type="string" />
      <Property name="SourceUrl" type="string" />
      <Property name="City" type="string" />
      <Property name="Url" type="string"/>
      <Property name="Title" type="string" />
      <Property name="Discount" type="float" />
      <Property name="Source" type="string" />
      <Property name="MerchantAddr" type="string" />
      <Property name="Content" type="string" />
      <Property name="Sales" type="int32" />
      <Property name="TimeBegin" type="string" />
      <Property name="TimeEnd" type="string" />
      <Property name="PriceOrign" type="float" />
      <Property name="MerchantName" type="string" />
      <Property name="MerchantArea" type="string" />
      <Property name="MerchantTel" type="string" />
      <Property name="Price" type="float" />
      <Property name="Category" type="string" />
      <Property name="OriginalCategory" type="string" />
      <Property name="SubCategory" type="string" />
      <Property name="Picture" type="string" />
      <Property name="OriginalPicture" type="string" />
      <Property name="itemcount" type="int32" />

    </DocumentSchema>

    
    <IndexBundle>
      <Schema>
        <Property name="Title">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.8" />
        </Property>
        <Property name="Price">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" range="yes" />
        </Property>
        <Property name="PriceOrign">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Discount">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Sales">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Source">
          <Indexing filter="yes" multivalue="yes" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Category">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>
        <Property name="SubCategory">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>
        <Property name="City">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.2" />
        </Property>
        <Property name="MerchantArea">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.2" />
        </Property>
        <Property name="MerchantAddr">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="tok_divide, tok_unite" rankweight="0.2" />
        </Property>
        <Property name="MerchantName">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="tok_divide, tok_unite" rankweight="0.2" />
        </Property>
        <Property name="TimeBegin">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="TimeEnd">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="itemcount">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
      </Schema>
    </IndexBundle>


    <MiningBundle>
      <Schema>
        <QueryRecommend>
          <QueryLog />
        </QueryRecommend>
        <Group>
          <Property name="Category" />
          <Property name="SubCategory" />
          <Property name="Source" />
          <Property name="City" />
          <Property name="MerchantArea" />
          <Property name="Price" />
        </Group>
        <ProductRanking>
          <Score type="custom" weight="10" />
          <Score type="category" property="Category" weight="1" />
          <Score type="relevance" weight="0.01" />
        </ProductRanking>
        <SuffixMatch>                                                                                                                                                                
          <Property name="Title" />
          <TokenizeDictionary path="product" />
          <Incremental enable="no" />
        </SuffixMatch>
      </Schema>
    </MiningBundle>

.. note::
    tuana和tuanm之间通过uuid字段进行关联


* 票务相关

票务采用和商品一致的映射关系，对应的collection分别叫ticketo和ticketp。

ticketo对应的配置：
::

    <DocumentSchema>

      <Property name="DOCID" type="string" />
      <Property name="Url" type="string"/>
      <Property name="PlayProfile" type="string" />
      <Property name="PlayPicture" type="string" />
      <Property name="SeatPicture" type="string" />
      <Property name="PlayAddress" type="string" />
      <Property name="TicketPrice" type="float" />
      <Property name="LowestTicketPrice" type="float" />
      <Property name="Source" type="string" />
      <Property name="Category" type="string" />
      <Property name="PlayCity" type="string" />
      <Property name="PlayName" type="string" />
      <Property name="PlayTime" type="datetime" />
      <Property name="uuid" type="string" />

    </DocumentSchema>

    
    <IndexBundle>
      <Schema>
        <Property name="PlayName">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="uuid">
          <Indexing filter="yes" multivalue="no" doclen="no" rankweight="0.1" />
        </Property>
        <Property name="TicketPrice">
          <Indexing filter="yes" multivalue="yes" doclen="no" rankweight="0.1" />
        </Property>
        <Property name="LowestTicketPrice">
          <Indexing filter="yes" multivalue="no" doclen="no" rankweight="0.1" />
        </Property>
        <Property name="PlayTime">
          <Indexing filter="yes" multivalue="yes" doclen="no" rankweight="0.1" />
        </Property>
        <Property name="Source">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>

      </Schema>
    </IndexBundle>


ticketp对应的配置：
::

    <DocumentSchema>

      <Property name="DOCID" type="string" />
      <Property name="Url" type="string"/>
      <Property name="PlayProfile" type="string" />
      <Property name="PlayPicture" type="string" />
      <Property name="SeatPicture" type="string" />
      <Property name="PlayAddress" type="string" />
      <Property name="TicketPrice" type="float" />
      <Property name="LowestTicketPrice" type="float" />
      <Property name="Source" type="string" />
      <Property name="Category" type="string" />
      <Property name="PlayCity" type="string" />
      <Property name="PlayName" type="string" />
      <Property name="PlayTime" type="datetime" />
      <Property name="itemcount" type="int32" />
    </DocumentSchema>

    
    <IndexBundle>
      <Schema>
        <Property name="PlayProfile">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="tok_divide, tok_unite" rankweight="0.2" />
        </Property>
        <Property name="PlayAddress">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="TicketPrice">
          <Indexing filter="yes" multivalue="yes" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="LowestTicketPrice">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Source">
          <Indexing filter="yes" multivalue="yes" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Category">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>
        <Property name="PlayCity">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>
        <Property name="PlayTime">
          <Indexing filter="yes" multivalue="yes" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="PlayName">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="tok_divide, tok_unite" rankweight="0.8" />
        </Property>
        <Property name="itemcount">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
      </Schema>
    </IndexBundle>

    
    <MiningBundle>
      <Schema>
        <QueryRecommend>
          <QueryLog />
        </QueryRecommend>
        <Group>
          <Property name="Category" />
          <Property name="Source" />
          <Property name="PlayTime" />
          <Property name="PlayCity" />
          <Property name="PlayAddress" />
        </Group>
        <SuffixMatch>                                                                                                                                                                
          <Property name="PlayName" />
          <TokenizeDictionary path="fmindex_dic" />
          <Incremental enable="no" />
        </SuffixMatch>
      </Schema>
    </MiningBundle>

    <RecommendBundle>
      <Schema>
        <User>
          <Property name="gender" />
          <Property name="age" />
          <Property name="area" />
        </User>
        <Track>
          <Event name="wish_list" />
          <Event name="own" />
          <Event name="like" />
          <Event name="favorite" />
        </Track>
      </Schema>
    </RecommendBundle>

.. note::
    ticketo和ticketp之间通过uuid字段进行关联



* 色界相关

色界和逛街共享同一个collection，因此collection名字为guang。色界的数据在送去索引前，会先送到一个
离线服务里去重，去重后的结果才进行索引；与此同时，去重之前的结果系统并不删除，而是直接归档放入guango collection。

guang的主要字段配置:
::

    <DocumentSchema>

      <Property name="DOCID" type="string" />
      <Property name="UserType" type="string"/>
      <Property name="UserId" type="string" />
      <Property name="UserName" type="string" />
      <Property name="Name" type="string" />
      <Property name="Content" type="string" />
      <Property name="Text" type="string" />
      <Property name="Img" type="string" />
      <Property name="Url" type="string" />
      <Property name="LabelIds" type="string" />
      <Property name="LabelNames" type="string" />
      <Property name="ShareType" type="string" />
      <Property name="ShareSourceType" type="string" />
      <Property name="ShareSourceName" type="string" />
      <Property name="ShareSourceUrl" type="string" />
      <Property name="PriceStart" type="float" />
      <Property name="PriceEnd" type="float" />
      <Property name="CategoryId" type="string" />
      <Property name="CategoryName" type="string" />
      <Property name="SplId" type="string" />
      <Property name="SplName" type="string" />
      <Property name="BrandId" type="string" />
      <Property name="BrandName" type="string" />
      <Property name="LikeCount" type="int32" />
      <Property name="CommentCount" type="int32" />
      <Property name="IsEssence" type="int8" />
      <Property name="IsHide" type="int8" />
      <Property name="CommentPreview" type="string" />
      <Property name="ShareDocid" type="string" />
      <Property name="ShareUuid" type="string" />
      <Property name="UserPreview" type="string" />
      <Property name="SpuName" type="string" />
      <Property name="IsTop" type="int8" />
      <Property name="IsRecommand" type="int8" />
      <Property name="OrderSeq" type="int8" />
      <Property name="VisitCount" type="int32" />
      <Property name="CreateTime" type="datetime" />
      <Property name="EssenceTime" type="datetime" />
      <Property name="RecommendTime" type="datetime" />
      <Property name="TopTime" type="datetime" />
      <Property name="GMemCount" type="int32" />
    </DocumentSchema>

    
    <IndexBundle>
      <Schema>
        <Property name="Name">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.8" />
        </Property>
        <Property name="Content">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.2" />
        </Property>
        <Property name="UserId">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="UserName">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="BrandName">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="ShareType">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="LikeCount">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="VisitCount">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="IsEssence">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="IsHide">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="IsTop">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="OrderSeq">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="IsRecommand">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="LabelNames">
          <Indexing filter="yes" multivalue="yes" doclen="no" />
        </Property>
        <Property name="PriceStart">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="PriceEnd">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="CreateTime">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="EssenceTime">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="RecommendTime">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="TopTime">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
        <Property name="GMemCount">
          <Indexing filter="yes" multivalue="no" doclen="no" />
        </Property>
      </Schema>
      <Parameter>
          <Sia refreshsearchcache="n" refreshcacheinterval="600" topknum="1000" />
      </Parameter>
    </IndexBundle>

    
    <MiningBundle>
      <Schema>
        <Group>
          <Property name="ShareSourceName" />
          <Property name="ShareType" />
          <Property name="LabelNames" />
        </Group>
        <ProductRanking debug="n">
          <Score type="relevance" weight="0.001" />
          <Score type="random" weight="1" />
          <Score type="diversity" property="ShareSourceName" />
          <Score type="popularity" weight="2">                                                                                                                                       
            <Score property="IsTop" weight="1" />
            <Score property="TopTime" weight="0.8" />
            <Score property="IsRecommand" weight="0.7" />
            <Score property="RecommendTime" weight="0.6" />
            <Score property="LikeCount" weight="0.5" />
            <Score property="VisitCount" weight="0.2" />
            <Score property="OrderSeq" weight="0.2" />
          </Score>
        </ProductRanking>
        <TDT enabletdt="no" enablesemantic="yes" >
          <TokenizeDictionary path="guang" />
        </TDT>
        <SuffixMatch>
          <Property name="Content" />
          <TokenizeDictionary path="sejie_dic" />                                                                                                                                  
          <Incremental enable="no" />
          <FilterProperty name="IsHide" filtertype="numeric" />
          <FilterProperty name="ShareType" filtertype="group" />
        </SuffixMatch>
      </Schema>
      <Parameter>
        <DocumentMiningPara cron="0 24 * * *"/>
        <ProductRankingPara cron="0 * * * *"/>
      </Parameter>
    </MiningBundle>

