配置样例
===================

collection集合
-------------------

* 商品

一个复杂的团购商品Tuan.xml配置样例字段如下，启用了Zambezi索引和Suffix索引
::

    <DocumentSchema>
      <Property name="DOCID" type="string" />
      <Property name="Url" type="string" />
      <Property name="WapUrl" type="string" />
      <Property name="WapBuyUrl" type="string" />
      <Property name="Source" type="string" />
      <Property name="SourceUrl" type="string" />
      <Property name="City" type="string" />
      <Property name="Category" type="string" />
      <Property name="OrigCategory" type="string" />
      <Property name="SubCategory" type="string" />
      <Property name="Characteristic" type="string" />
      <Property name="Destination" type="string" />
      <Property name="ThirdCategory" type="string" />
      <Property name="Major" type="string" />
      <Property name="Title" type="string" />
      <Property name="ShortTitle" type="string" />
      <Property name="Picture" type="string" />
      <Property name="OriginalPicture" type="string" />
      <Property name="TimeBegin" type="datetime" />
      <Property name="TimeEnd" type="datetime" />
      <Property name="Price" type="float" />
      <Property name="PriceOrign" type="float" />
      <Property name="Discount" type="float" />
      <Property name="Sales" type="int32" />
      <Property name="ProductName" type="string" />
      <Property name="SpendEndTime" type="datetime" />
      <Property name="Refund" type="int32" />
      <Property name="Reservation" type="int32" />
      <Property name="Tips" type="string" />
      <Property name="LimitDesc" type="string" />
      <Property name="OnSaleStatus" type="string" />
      <Property name="WapOrderUrl" type="string" />
      <Property name="BandName" type="string" />
      <Property name="MainName" type="string" />
      <Property name="MainTel" type="string" />
      <Property name="MainAddr" type="string" />
      <Property name="MainCoords" type="string" />
      <Property name="MainArea" type="string" />
      <Property name="MainDPShopId" type="string" />
      <Property name="MerchantName" type="string" />
      <Property name="MerchantArea" type="string" />
      <Property name="MerchantAddr" type="string" />
      <Property name="MerchantTel" type="string" />
      <Property name="MerchantCoords" type="string" />
      <Property name="MerchantDPShopId" type="string" />
      <Property name="MerchantOpenTime" type="string" />
      <Property name="MerchantTrafficInfo" type="string" />
      <Property name="Coupon" type="int32" />
      <Property name="Wifi" type="int32" />
      <Property name="itemcount" type="int32" />
      <Property name="GeoHash" type="string" />
      <Property name="ShopCoords" type="double" />
      <Property name="TFSPicture" type="string" />
      <Property name="Attribute" type="string" />
      <Property name="Detail" type="string" />
      <Property name="Subway" type="string" />
      <Property name="Landmarks" type="string" />
    </DocumentSchema>
    <IndexBundle>
    <ShardSchema>
      <ShardKey name="DOCID" />
      <DistributedService type="search" />
    </ShardSchema>
    <Schema>
        <Property name="Source">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="SourceUrl">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="City">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Category">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="10" />
        </Property>
        <Property name="OrigCategory">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="10" />
        </Property>
        <Property name="SubCategory">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.6" />
        </Property>
        <Property name="Characteristic">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Destination">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="ThirdCategory">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.4" />
        </Property>
        <Property name="Title">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.4" />
        </Property>
        <Property name="ShortTitle">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.4" />
        </Property>
        <Property name="ProductName">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="MainName">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="MainAddr">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="MerchantAddr">
          <Indexing filter="no" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="MainArea">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="MerchantArea">
          <Indexing filter="yes" multivalue="no" doclen="yes" analyzer="la_sia" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Price">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" range="yes" />
        </Property>
        <Property name="Discount">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" range="yes" />
        </Property>
        <Property name="MerchantTrafficInfo">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="MainCoords">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Sales">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Coupon">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Wifi">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Reservation">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="TimeBegin">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="itemcount">
          <Indexing filter="yes" multivalue="no" doclen="yes" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="GeoHash">
          <Indexing filter="yes" multivalue="yes" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="ShopCoords">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" range="yes" />
        </Property>
        <Property name="TFSPicture">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Attribute">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Subway">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
        <Property name="Landmarks">
          <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />
        </Property>
      </Schema>
      <ZambeziSchema reverse="y" poolSize="1024M" poolCount="16" vocabSize="32M" indexType="ATTR">
        <TokenizeDictionary path="attr_tokenize" />
        <VirtualProperty name="Combined" weight="0.8" poolSize="1024M" isAttrToken="yes">
          <SubProperty name="Title" />
          <SubProperty name="Source" />
          <SubProperty name="Category" />
          <SubProperty name="OrigCategory" />
          <SubProperty name="SourceUrl" />
          <SubProperty name="City" />
          <SubProperty name="SubCategory" />
          <SubProperty name="Characteristic" />
          <SubProperty name="Destination" />
          <SubProperty name="ThirdCategory" />
          <SubProperty name="ShortTitle" />
          <SubProperty name="ProductName" />
          <SubProperty name="MainName" />
          <SubProperty name="MainAddr" />
          <SubProperty name="MerchantAddr" />
          <SubProperty name="MerchantArea" />
          <SubProperty name="MainArea" />
          <SubProperty name="MerchantTrafficInfo" />
          <SubProperty name="Attribute" />          
        </VirtualProperty>
      </ZambeziSchema>
    </IndexBundle>

    <MiningBundle>
      <Schema>
        <QueryRecommend>
          <QueryLog />
        </QueryRecommend>
        <Group>
          <Property name="Source" />
          <Property name="Category" />
          <Property name="City" />
          <Property name="Category" />
          <Property name="OrigCategory" />
          <Property name="SubCategory" />
          <Property name="Characteristic" />
          <Property name="Destination" />
          <Property name="ThirdCategory" />
          <Property name="Price" />
          <Property name="Discount" />
          <Property name="MerchantArea" />
          <Property name="MerchantTrafficInfo" />
          <Property name="Coupon" />
          <Property name="Wifi" />
          <Property name="Subway" />
          <Property name="Landmarks" />
        </Group>
        <ProductRanking debug="y">
          <Score type="category" property="Category" weight="100" />
          <Score type="diversity" property="Source" />
          <Score type="popularity" weight="20">
              <Score property="Sales" weight="0.7" min="0" max="300" zoomin="100" />
              <Score property="TimeBegin" weight="0.3" min="1229904000" max="1419120000" />
          </Score>
          <Score type="zambezi" weight="100" min="0" max="3000" zoomin="1000" />
        </ProductRanking>
        <SuffixMatch>                                                                                                                                                                
          <Property name="Title" />
          <TokenizeDictionary path="title_pca" />
          <Normalizer padding="token" max_index_token="20" />
          <Incremental enable="no" />
        </SuffixMatch>
      </Schema>
    </MiningBundle>


