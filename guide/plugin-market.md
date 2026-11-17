# PostgreSQL 插件市场
用户进入PostgreSQL实例的插件市场，在插件管理中选择指定数据库，会展示当前实例支持安装的插件列表，支持按需筛选、安装、卸载、升级。

## 注意事项

* `bool_plperl` 依赖 `plperl`，需要先安装 `plperl` 后再安装 `bool_plperl`。
* `bool_plperlu` 依赖 `plperlu`，需要先安装 `plperlu` 后再安装 `bool_plperlu`。

## 操作说明

![插件市场操作界面](/upgsql/images/upgsql-plugin-market-operation-01.png)

* 安装插件：筛选状态为未安装的插件，根据需要点击安装操作按钮，将插件安装到目标数据库中。
  * 注意：插件列表中展示安装该插件是否需要重启，若插件涉及到参数修改，安装/升级会默认自动重启实例，建议在业务低峰期操作。
* 升级插件：筛选状态为已安装的插件，若最新可用版本比当前版本更高，则支持进行安装操作。
* 卸载插件：筛选状态为已安装的插件，点击卸载按钮卸载目标插件。
  * 注意：卸载插件需要谨慎操作，可能导致数据丢失，以及业务不可用。部分插件涉及PG底层业务，不支持卸载。

## 插件列表及使用教程

按插件能力划分为 10 个分类。

| 插件类型 | 插件个数 | 文档跳转链接 |
| --- | ---: | --- |
| 空间与地理 | 13 | [查看文档](#空间与地理) |
| 向量与 AI | 6 | [查看文档](#向量与-ai) |
| 检索与文本 | 12 | [查看文档](#检索与文本) |
| 时序与消息 | 13 | [查看文档](#时序与消息) |
| 数据类型与分区 | 24 | [查看文档](#数据类型与分区) |
| 跨库与联邦 | 18 | [查看文档](#跨库与联邦) |
| 运维与监控 | 26 | [查看文档](#运维与监控) |
| 安全与合规 | 7 | [查看文档](#安全与合规) |
| 过程语言 | 26 | [查看文档](#过程语言) |
| 其他扩展 | 6 | [查看文档](#其他扩展) |

### 空间与地理

用于地理位置数据的存储、索引、距离与网格计算，以及路径规划与地址标准化。 本类共 13 个插件。

<div id="spatial-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [postgis](#postgis) | PostGIS 是 PostgreSQL 的空间数据库扩展，为数据库增加几何对象类型、空间索引与空间分析函数，用于存… | 否 |
| [postgis_topology](#postgis_topology) | postgis_topology 是 PostGIS 的拓扑扩展，把几何数据组织为共享边的拓扑模型（节点、边、面），… | 否 |
| [postgis_raster](#postgis_raster) | PostGIS 栅格扩展，提供栅格数据类型与函数（影像、DEM、气象格点等），支持切片、波段运算与栅格/矢量联合分析… | 否 |
| [postgis_tiger_geocoder](#postgis_tiger_geocoder) | postgis_tiger_geocoder 是 PostGIS 内置的美国地理编码扩展，基于美国人口普查局 TIG… | 否 |
| [address_standardizer](#address_standardizer) | 地址标准化扩展（PostGIS 生态），把一行地址文本按规则拆分为门牌号、街道、城市、邮编等结构化字段，是地理编码的… | 否 |
| [address_standardizer_data_us](#address_standardizer_data_us) | 地址标准化扩展的美国数据集，为 address_standardizer 提供美国地址所需的词典与规则表数据。 | 否 |
| [cube](#cube) | cube 提供多维立方体（cube）数据类型，可表示点与超矩形，用于多维数据的存储、包含/相交判断与距离计算，常见于… | 否 |
| [earthdistance](#earthdistance) | earthdistance 提供两种计算地球表面大圆距离的方式，基于经纬度的 `earth_distance` 函数… | 否 |
| [pgrouting](#pgrouting) | pgRouting 在 PostGIS 之上提供路径规划与网络分析函数，最短路径、K 最短路径、旅行商、可达区域等，… | 是 |
| [ip4r](#ip4r) | IPv4/IPv6 地址与网段专用类型，提供比内置 inet/cidr 更丰富的包含、范围与索引支持，适合 IP 归… | 否 |
| [q3c](#q3c) | Q3C 天球索引插件，为天文坐标（RA/Dec）提供立方体球面索引与锥形搜索、多边形搜索能力，用于星表等海量天文学数… | 否 |
| [pg_sphere](#pg_sphere) | pg_sphere（PGSphere）提供球面数据类型与函数，球面点、圆、多边形及其包含/相交/距离运算，并支持 G… | 否 |
| [imgsmlr](#imgsmlr) | 图像相似度扩展，把图像特征表示为签名（signature）类型，用 GiST 索引做以图搜图式的相似度检索。 | 否 |

<div id="postgis"></div>

#### postgis

PostGIS 是 PostgreSQL 的空间数据库扩展，为数据库增加几何对象类型、空间索引与空间分析函数，用于存储和查询地理位置数据（点、线、面、栅格等）。UDB PostgreSQL 已内置该插件，创建后即可在 SQL 中使用空间能力。

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
```

**使用示例**

```sql
-- 1. 创建带空间列的表（4326 为 WGS84 经纬度坐标系）
CREATE TABLE poi (
  id   bigserial PRIMARY KEY,
  name text,
  geom geometry(Point, 4326)
);

-- 2. 写入数据
INSERT INTO poi (name, geom) VALUES
  ('人民广场', ST_GeomFromText('POINT(121.4752 31.2329)', 4326)),
  ('外滩',     ST_GeomFromText('POINT(121.4903 31.2417)', 4326));

-- 3. 创建空间索引并收集统计信息
CREATE INDEX idx_poi_geom ON poi USING gist (geom);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="postgis_topology"></div>

#### postgis_topology

postgis_topology 是 PostGIS 的拓扑扩展：把几何数据组织为共享边的拓扑模型（节点、边、面），保证相邻要素边界一致、不重叠、无缝隙，适合行政区划、地块、路网等需要严格拓扑关系的场景。

```sql
CREATE EXTENSION IF NOT EXISTS postgis_topology;
```

**使用示例**

```sql
-- 1. 安装依赖与插件
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology;

-- 2. 创建拓扑（SRID 4326 为经纬度坐标系）
SELECT CreateTopology('city_topo', 4326);

-- 3. 为业务表添加拓扑几何列（layer 会自动创建）
CREATE TABLE parcels (id bigserial PRIMARY KEY, name text);
SELECT AddTopoGeometryColumn('city_topo', 'public', 'parcels', 'topo_geom', 'POLYGON');

-- 4. 把普通几何写入为拓扑几何
ALTER TABLE parcels ADD COLUMN geom geometry(Polygon, 4326);
INSERT INTO parcels (name, geom) VALUES
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="postgis_raster"></div>

#### postgis_raster

PostGIS 栅格扩展：提供栅格数据类型与函数（影像、DEM、气象格点等），支持切片、波段运算与栅格/矢量联合分析。

```sql
CREATE EXTENSION IF NOT EXISTS postgis_raster;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_raster;

CREATE TABLE dem (id bigserial PRIMARY KEY, rast raster);

-- 生成一个小栅格做示范（实际可用 raster2pgsql 导入 GeoTIFF）
INSERT INTO dem (rast)
VALUES (ST_AddBand(ST_MakeEmptyRaster(10, 10, 0, 0, 1, -1, 0, 0, 4326), '32BF'::text, 0));

-- 统计栅格波段信息
SELECT rid, (ST_SummaryStats(rast)).*
FROM (SELECT rid, rast FROM dem) t;

-- 取某点的栅格值
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="postgis_tiger_geocoder"></div>

#### postgis_tiger_geocoder

postgis_tiger_geocoder 是 PostGIS 内置的美国地理编码扩展：基于美国人口普查局 TIGER 数据，提供地址与坐标的双向转换（地址 → 经纬度、经纬度 → 地址）以及地址标准化能力。

```sql
CREATE EXTENSION IF NOT EXISTS postgis_tiger_geocoder;
```

**使用示例**

```sql
-- 1. 安装依赖与插件
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_tiger_geocoder;

-- 2. 查看是否已装载 TIGER 数据（未装载时 geocode 无结果）
SELECT count(*) FROM tiger.county;
SELECT count(*) FROM tiger.edges;

-- 3. 地址转坐标
SELECT (g).rating,
       (g).norm_address,
       ST_AsText((g).geomout) AS location,
       (g).addy AS matched_address
FROM (SELECT geocode('1 Market St, San Francisco, CA') AS g) t;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="address_standardizer"></div>

#### address_standardizer

地址标准化扩展（PostGIS 生态）：把一行地址文本按规则拆分为门牌号、街道、城市、邮编等结构化字段，是地理编码的前置步骤。

```sql
CREATE EXTENSION IF NOT EXISTS address_standardizer;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS address_standardizer;
CREATE EXTENSION IF NOT EXISTS address_standardizer_data_us;

SELECT * FROM standardize_address(
  'us_lex', 'us_gaz', 'us_rules',
  '1 Market St, San Francisco, CA 94105'
);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="address_standardizer_data_us"></div>

#### address_standardizer_data_us

地址标准化扩展的美国数据集：为 address_standardizer 提供美国地址所需的词典与规则表数据。

```sql
CREATE EXTENSION IF NOT EXISTS address_standardizer_data_us;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="cube"></div>

#### cube

cube 提供多维立方体（cube）数据类型，可表示点与超矩形，用于多维数据的存储、包含/相交判断与距离计算，常见于多维相似度检索与推荐场景。

```sql
CREATE EXTENSION IF NOT EXISTS cube;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS cube;

CREATE TABLE items (
  id   bigserial PRIMARY KEY,
  name text,
  pos  cube
);

-- 1. 写入三维特征向量（例如三个打分维度）
INSERT INTO items (name, pos) VALUES
  ('A', cube(1, 2, 3)),
  ('B', cube(1.2, 2.1, 2.8)),
  ('C', cube(8, 1, 1));

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="earthdistance"></div>

#### earthdistance

earthdistance 提供两种计算地球表面大圆距离的方式：基于经纬度的 `earth_distance` 函数族，以及基于 cube 的点距离方式。常用于「附近的人 / 附近的店」这类地理距离检索。

```sql
CREATE EXTENSION IF NOT EXISTS earthdistance;
```

**使用示例**

```sql
-- 1. 安装依赖与插件（顺序不能颠倒）
CREATE EXTENSION IF NOT EXISTS cube;
CREATE EXTENSION IF NOT EXISTS earthdistance;

CREATE TABLE shops (
  id  bigserial PRIMARY KEY,
  name text,
  lat double precision,
  lon double precision
);

INSERT INTO shops (name, lat, lon) VALUES
  ('人民广场店', 31.2329, 121.4752),
  ('外滩店',     31.2417, 121.4903),
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="pgrouting"></div>

#### pgrouting

pgRouting 在 PostGIS 之上提供路径规划与网络分析函数：最短路径、K 最短路径、旅行商、可达区域等，用于路网、配送与物流场景。

> 使用前需将 `pgrouting` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pgrouting;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS pgrouting;

-- 1. 建路网边表（id, source, target, cost, reverse_cost, geom）
CREATE TABLE ways (
  id     bigserial PRIMARY KEY,
  source bigint,
  target bigint,
  cost   double precision,
  reverse_cost double precision,
  geom   geometry(LineString, 4326)
);
CREATE INDEX idx_ways_source ON ways (source);
CREATE INDEX idx_ways_target ON ways (target);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="ip4r"></div>

#### ip4r

IPv4/IPv6 地址与网段专用类型：提供比内置 inet/cidr 更丰富的包含、范围与索引支持，适合 IP 归属、黑白名单、网段检索。

```sql
CREATE EXTENSION IF NOT EXISTS ip4r;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS ip4r;

CREATE TABLE ip_blocks (id bigserial PRIMARY KEY, net ip4r, owner text);
INSERT INTO ip_blocks (net, owner) VALUES ('10.0.0.0-10.0.255.255', 'internal'), ('1.1.1.0/24', 'cdn');
CREATE INDEX idx_ip_blocks_net ON ip_blocks USING gist (net);
ANALYZE ip_blocks;

-- 查询某 IP 属于哪个网段
SELECT owner FROM ip_blocks WHERE net >>= '10.0.1.5'::ip4;
-- 网段相交
SELECT * FROM ip_blocks WHERE net && '1.1.0.0/16'::ip4r;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="q3c"></div>

#### q3c

Q3C 天球索引插件：为天文坐标（RA/Dec）提供立方体球面索引与锥形搜索、多边形搜索能力，用于星表等海量天文学数据。

```sql
CREATE EXTENSION IF NOT EXISTS q3c;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="pg_sphere"></div>

#### pg_sphere

pg_sphere（PGSphere）提供球面数据类型与函数：球面点、圆、多边形及其包含/相交/距离运算，并支持 GiST 索引，面向天文与球面几何检索。

```sql
CREATE EXTENSION IF NOT EXISTS pg_sphere;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

<div id="imgsmlr"></div>

#### imgsmlr

图像相似度扩展：把图像特征表示为签名（signature）类型，用 GiST 索引做以图搜图式的相似度检索。

```sql
CREATE EXTENSION IF NOT EXISTS imgsmlr;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回空间与地理插件一览](#spatial-index)

### 向量与 AI

向量检索与排序、机器学习、化学信息学等 AI 相关能力。 本类共 6 个插件。

<div id="vector-ai-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [vector](#vector) | pgvector，向量数据类型与 HNSW / IVFFlat 近似最近邻索引，用于 AI 场景的语义检索、以图搜图… | 否 |
| [vectorscale](#vectorscale) | 基于 pgvector 的 DiskANN 索引扩展（StreamingDiskANN），用磁盘驻留索引降低大向量集… | 是 |
| [pgml](#pgml) | PostgresML，在数据库内完成机器学习训练与推理（模型训练、预测、嵌入生成），可直接用 SQL 调用模型。 | 是 |
| [pgroonga](#pgroonga) | 基于 Groonga 的全文检索扩展，支持中日韩等全语言分词与高速全文索引，可对 `text`、`jsonb`、数组… | 是 |
| [pgroonga_database](#pgroonga_database) | PGroonga 的数据库管理模块，用于查看 Groonga 侧数据库状态、执行维护命令（如 vacuum），需与 … | 是 |
| [rdkit](#rdkit) | RDKit 化学信息学扩展，提供分子结构与指纹数据类型及函数，可在库内做化合物相似度检索与子结构匹配（化学/医药研发… | 否 |

<div id="vector"></div>

#### vector

pgvector：向量数据类型与 HNSW / IVFFlat 近似最近邻索引，用于 AI 场景的语义检索、以图搜图与推荐。（扩展名为 `vector`）

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE items (id bigserial PRIMARY KEY, item text, embedding vector(3));
INSERT INTO items (item, embedding) VALUES ('苹果','[1,1,0]'), ('香蕉','[1.2,0.8,0]'), ('猫','[0,0.4,6]');

-- 精确最近邻
SELECT item, embedding <-> '[1,1,0]' AS distance FROM items ORDER BY embedding <-> '[1,1,0]' LIMIT 5;

-- 近似索引 + 查询期召回参数
CREATE INDEX idx_items_hnsw ON items USING hnsw (embedding vector_cosine_ops) WITH (m=16, ef_construction=64);
SET hnsw.ef_search = 100;
SELECT item, 1 - (embedding <=> '[1,1,0]') AS cosine_similarity FROM items ORDER BY embedding <=> '[1,1,0]' LIMIT 5;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回向量与 AI插件一览](#vector-ai-index)

<div id="vectorscale"></div>

#### vectorscale

基于 pgvector 的 DiskANN 索引扩展（StreamingDiskANN），用磁盘驻留索引降低大向量集的内存占用，适合千万级向量的低成本检索。

> 使用前需将 `vectorscale` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS vectorscale;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS vector;
CREATE EXTENSION IF NOT EXISTS vectorscale;

CREATE TABLE items (id bigserial PRIMARY KEY, embedding vector(768));
-- 写入数据后建立 DiskANN 索引
CREATE INDEX idx_items_diskann ON items USING diskann (embedding vector_cosine_ops);

SELECT * FROM items ORDER BY embedding <=> (SELECT embedding FROM items LIMIT 1) LIMIT 10;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回向量与 AI插件一览](#vector-ai-index)

<div id="pgml"></div>

#### pgml

PostgresML：在数据库内完成机器学习训练与推理（模型训练、预测、嵌入生成），可直接用 SQL 调用模型。

> 使用前需将 `pgml` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pgml;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgml;

-- 训练（示意，参数以实例版本为准）
SELECT * FROM pgml.train(
  project_name  => 'demo',
  task          => 'classification',
  relation_name => 'public.training_data',
  y_column_name => 'label',
  algorithm     => 'xgboost'
);

SELECT pgml.predict('demo', ARRAY[1.0, 2.5, 3.1]) AS prediction;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回向量与 AI插件一览](#vector-ai-index)

<div id="pgroonga"></div>

#### pgroonga

基于 Groonga 的全文检索扩展：支持中日韩等全语言分词与高速全文索引，可对 `text`、`jsonb`、数组等类型建立 Groonga 索引。

> 使用前需将 `pgroonga` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pgroonga;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgroonga;

CREATE TABLE docs (id bigserial PRIMARY KEY, content text);
CREATE INDEX idx_docs_content ON docs USING pgroonga (content);

INSERT INTO docs (content) VALUES ('云数据库 PostgreSQL 中文全文检索'), ('另一个不相关的文档');

SELECT * FROM docs WHERE content &@ '全文检索';
SELECT * FROM docs WHERE content &@~ 'PostgreSQL OR 云数据库';
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回向量与 AI插件一览](#vector-ai-index)

<div id="pgroonga_database"></div>

#### pgroonga_database

PGroonga 的数据库管理模块：用于查看 Groonga 侧数据库状态、执行维护命令（如 vacuum），需与 pgroonga 一起使用。

> 使用前需将 `pgroonga_database` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pgroonga_database;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回向量与 AI插件一览](#vector-ai-index)

<div id="rdkit"></div>

#### rdkit

RDKit 化学信息学扩展：提供分子结构与指纹数据类型及函数，可在库内做化合物相似度检索与子结构匹配（化学/医药研发场景）。

```sql
CREATE EXTENSION IF NOT EXISTS rdkit;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS rdkit;

-- 1. 由 SMILES 生成分子
SELECT mol_from_smiles('c1ccccc1') AS mol;

-- 2. 指纹与相似度（Tanimoto）
SELECT tanimoto_sml(
  morganbv_fp(mol_from_smiles('c1ccccc1')),
  morganbv_fp(mol_from_smiles('c1ccccc1O'))
) AS tanimoto;

-- 3. 子结构匹配
SELECT substruct(mol_from_smiles('c1ccccc1'), mol_from_smiles('c1ccccc1O')) AS is_substructure;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回向量与 AI插件一览](#vector-ai-index)

### 检索与文本

全文检索、中文分词、模糊匹配与字符串相似度计算。 本类共 12 个插件。

<div id="fulltext-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [pg_trgm](#pg_trgm) | pg_trgm 通过三元组（trigram）匹配来计算文本相似度，提供相似度函数、相似度运算符以及支持模糊匹配的 G… | 否 |
| [pg_bigm](#pg_bigm) | pg_bigm 使用二元语法（2-gram）建立 GIN 索引来加速模糊匹配，支持 `LIKE '%关键词%'` 这… | 是 |
| [zhparser](#zhparser) | zhparser 是中文分词扩展，为 PostgreSQL 全文检索提供中文分词能力（基于 SCWS），配合 `ts… | 是 |
| [pg_jieba](#pg_jieba) | 结巴中文分词扩展，为全文检索提供 jieba 中文分词，词库准确度高，适合中文关键词检索。 | 是 |
| [rum](#rum) | rum 是一个基于 GIN 改进的索引访问方法，专为全文检索场景设计，在索引中额外保存词位等附加信息，从而加速相关度… | 否 |
| [unaccent](#unaccent) | unaccent 是一个文本搜索词典，用于在分词与检索时移除字符上的变音符号（重音），例如把 "café" 归一为 … | 否 |
| [dict_int](#dict_int) | dict_int 是一个全文检索词典模板，用于控制被索引/检索的整数，可以限制整数词位的最大长度、或直接丢弃过长的整… | 否 |
| [dict_xsyn](#dict_xsyn) | 扩展同义词词典模板（内置 contrib），在分词时为词位附加同义词（如把 bike 映射为 bicycle、cyc… | 否 |
| [fuzzystrmatch](#fuzzystrmatch) | fuzzystrmatch 提供字符串相似度与发音匹配函数，Soundex、Metaphone（含 Double M… | 否 |
| [pg_similarity](#pg_similarity) | 字符串相似度扩展，提供多种相似度算法（Levenshtein、Jaro、Jaro-Winkler、Cosine、Di… | 否 |
| [smlar](#smlar) | 数组相似度扩展，为任意一维数组计算相似度（可配置相似函数与阈值），常与 GiST 索引配合做「近似去重 / 相似标签… | 否 |
| [tsearch2](#tsearch2) | 文本检索兼容包，为 8.3 之前的全文检索函数提供兼容层，用于老应用迁移过渡。 | 否 |

<div id="pg_trgm"></div>

#### pg_trgm

pg_trgm 通过三元组（trigram）匹配来计算文本相似度，提供相似度函数、相似度运算符以及支持模糊匹配的 GIN / GiST 索引操作符类，可显著加速 `LIKE '%关键词%'`、`ILIKE`、正则匹配和"相似字符串"检索。

```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

**使用示例**

```sql
-- 1. 计算相似度
SELECT similarity('postgresql', 'postgres'),          -- 0.6 左右
       word_similarity('postgres', 'postgresql database');

-- 2. 相似度检索：找出与目标串比较像的名称
SELECT name, similarity(name, '上海科技') AS sim
FROM company
WHERE name % '上海科技'
ORDER BY name <-> '上海科技'
LIMIT 10;

-- 3. 模糊匹配加速：为文本列建立 GIN 三元组索引
CREATE EXTENSION IF NOT EXISTS pg_trgm;        -- 先装插件
CREATE INDEX idx_company_name_trgm ON company USING gin (name gin_trgm_ops);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="pg_bigm"></div>

#### pg_bigm

pg_bigm 使用二元语法（2-gram）建立 GIN 索引来加速模糊匹配，支持 `LIKE '%关键词%'` 这类前后都带通配符的查询，对中日文等无空格分词的语言尤其友好。与 pg_trgm（3-gram）相比，它在短关键词与中文场景下检索更快。

> 使用前需将 `pg_bigm` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_bigm;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_bigm;

CREATE TABLE docs (id bigserial PRIMARY KEY, title text, body text);

-- 1. 建立 GIN 二元语法索引
CREATE INDEX idx_docs_body_bigm ON docs USING gin (body gin_bigm_ops);
ANALYZE docs;

-- 2. 模糊匹配：前后都带通配符也能命中索引
SELECT id, title FROM docs WHERE body LIKE '%数据库性能%';
SELECT id, title FROM docs WHERE body LIKE '%性能优化%';

-- 3. 相似度检索与排序
SELECT title, bigm_similarity(title, '云数据库性能优化') AS sim
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="zhparser"></div>

#### zhparser

zhparser 是中文分词扩展，为 PostgreSQL 全文检索提供中文分词能力（基于 SCWS）。配合 `tsvector`/`tsquery` 使用，可实现中文关键词检索、相关度排序与高亮，比 `LIKE` 模糊匹配更准确高效。

> 使用前需将 `zhparser` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS zhparser;
```

**使用示例**

```sql
-- 1. 创建插件
CREATE EXTENSION IF NOT EXISTS zhparser;

-- 2. 创建中文全文检索配置（词性映射按业务需要增减）
CREATE TEXT SEARCH CONFIGURATION chinese (PARSER = zhparser);
ALTER TEXT SEARCH CONFIGURATION chinese ADD MAPPING FOR n,v,a,i,e,l WITH simple;

-- 3. 分词效果验证
SELECT to_tsvector('chinese', '云数据库 PostgreSQL 支持中文全文检索');
SELECT to_tsquery('chinese', '数据库 & 检索');

-- 4. 建表并建立 GIN 全文索引
CREATE TABLE articles (
  id      bigserial PRIMARY KEY,
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="pg_jieba"></div>

#### pg_jieba

结巴中文分词扩展：为全文检索提供 jieba 中文分词，词库准确度高，适合中文关键词检索。

> 使用前需将 `pg_jieba` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_jieba;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_jieba;

CREATE TEXT SEARCH CONFIGURATION jiebacfg (PARSER = jieba);
ALTER TEXT SEARCH CONFIGURATION jiebacfg ADD MAPPING FOR n,v,a,i,e,l WITH simple;

SELECT to_tsquery('jiebacfg', '云数据库');

CREATE TABLE docs (id bigserial PRIMARY KEY, content text, tsv tsvector);
INSERT INTO docs (content, tsv)
VALUES ('云数据库支持中文检索', to_tsvector('jiebacfg', '云数据库支持中文检索'));
CREATE INDEX idx_docs_tsv ON docs USING gin (tsv);

SELECT * FROM docs WHERE tsv @@ to_tsquery('jiebacfg', '中文检索');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="rum"></div>

#### rum

rum 是一个基于 GIN 改进的索引访问方法，专为全文检索场景设计：在索引中额外保存词位等附加信息，从而加速相关度排序、短语查询，以及「全文检索 + 时间排序」的组合查询。代价是索引构建与写入比 GIN 更慢。

```sql
CREATE EXTENSION IF NOT EXISTS rum;
```

**使用示例**

```sql
-- 1. 创建插件
CREATE EXTENSION IF NOT EXISTS rum;

-- 2. 建表并维护 tsvector
CREATE TABLE articles (
  id         bigserial PRIMARY KEY,
  title      text,
  content    text,
  created_at timestamp,
  tsv        tsvector
);

CREATE TRIGGER tsvectorupdate
BEFORE INSERT OR UPDATE ON articles
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="unaccent"></div>

#### unaccent

unaccent 是一个文本搜索词典，用于在分词与检索时移除字符上的变音符号（重音），例如把 "café" 归一为 "cafe"，从而让带重音的拉丁文本也能被按无重音关键词检索。

```sql
CREATE EXTENSION IF NOT EXISTS unaccent;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS unaccent;

-- 1. 先看函数效果
SELECT unaccent('café'), unaccent('naïve'), unaccent('Müller');

-- 2. 配置检索：在 simple 配置上叠加 unaccent
CREATE TEXT SEARCH CONFIGURATION fr_unaccent (COPY = simple);
ALTER TEXT SEARCH CONFIGURATION fr_unaccent
  ALTER MAPPING FOR hword, hword_part, word WITH unaccent, simple;

-- 3. 带重音与不带重音的文本分词结果一致
SELECT to_tsvector('fr_unaccent', 'Un café à Paris');
SELECT to_tsvector('fr_unaccent', 'Un cafe a Paris');

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="dict_int"></div>

#### dict_int

dict_int 是一个全文检索词典模板，用于控制被索引/检索的整数：可以限制整数词位的最大长度、或直接丢弃过长的整数，避免文档编号、时间戳等长数字进入索引造成索引膨胀与无效匹配。

```sql
CREATE EXTENSION IF NOT EXISTS dict_int;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS dict_int;

-- 1. 创建基于 dict_int 模板的词典
CREATE TEXT SEARCH DICTIONARY intdict (
  TEMPLATE = dict_int,
  MAXLEN   = 4,
  REJECTLONG = true
);

-- 2. 挂到检索配置上（示例：simple 配置）
CREATE TEXT SEARCH CONFIGURATION intcfg (COPY = simple);
ALTER TEXT SEARCH CONFIGURATION intcfg
  ALTER MAPPING FOR asciiword, word WITH intdict, simple;

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="dict_xsyn"></div>

#### dict_xsyn

扩展同义词词典模板（内置 contrib）：在分词时为词位附加同义词（如把 bike 映射为 bicycle、cycle），用于全文检索的同义词召回。

```sql
CREATE EXTENSION IF NOT EXISTS dict_xsyn;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="fuzzystrmatch"></div>

#### fuzzystrmatch

fuzzystrmatch 提供字符串相似度与发音匹配函数：Soundex、Metaphone（含 Double Metaphone）用于「发音相近」匹配，Levenshtein 用于「编辑距离」计算，适合姓名去重、录入纠错、疑似重复数据识别。

```sql
CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;

-- 1. 发音编码与匹配度（英文姓名场景）
SELECT soundex('Anne'), soundex('Ann'), difference('Anne', 'Ann');

-- 2. 编辑距离
SELECT levenshtein('kitten', 'sitting') AS dist,          -- 3
       levenshtein('数据库', '数据仓') AS dist_cn;         -- 1（按字符计算）

-- 3. 带阈值的快速版本
SELECT levenshtein_less_equal('kitten', 'sitting', 2);    -- 若超过 2 会直接返回较大值

-- 4. Double Metaphone
SELECT dmetaphone('Smith'), dmetaphone_alt('Smyth');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="pg_similarity"></div>

#### pg_similarity

字符串相似度扩展：提供多种相似度算法（Levenshtein、Jaro、Jaro-Winkler、Cosine、Dice、Jaccard、Monge-Elkan 等）及对应运算符，用于模糊匹配与去重。

```sql
CREATE EXTENSION IF NOT EXISTS pg_similarity;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="smlar"></div>

#### smlar

数组相似度扩展：为任意一维数组计算相似度（可配置相似函数与阈值），常与 GiST 索引配合做「近似去重 / 相似标签」检索。

```sql
CREATE EXTENSION IF NOT EXISTS smlar;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS smlar;

SET smlar.type = 'cosine';
SET smlar.threshold = 0.7;

CREATE TABLE docs (id bigserial PRIMARY KEY, tags text[]);
CREATE INDEX idx_docs_tags ON docs USING gist (tags _text_sml_ops);
ANALYZE docs;

SELECT id, tags, smlar(tags, ARRAY['postgres','database']) AS sim
FROM docs
WHERE tags % ARRAY['postgres','database']
ORDER BY sim DESC LIMIT 10;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

<div id="tsearch2"></div>

#### tsearch2

文本检索兼容包：为 8.3 之前的全文检索函数提供兼容层，用于老应用迁移过渡。

```sql
CREATE EXTENSION IF NOT EXISTS tsearch2;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回检索与文本插件一览](#fulltext-index)

### 时序与消息

时序数据、消息队列、ID 生成与基数估算等应用型能力。 本类共 13 个插件。

<div id="timeseries-queue-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [timescaledb](#timescaledb) | 时序数据库扩展，提供 hypertable（按时间自动分区）、连续聚合、压缩与保留策略，是时序指标、IoT、监控数据… | 是 |
| [pgmq](#pgmq) | 轻量消息队列扩展，在 PostgreSQL 内实现类似 SQS 的消息队列（发送、读取、删除、可见性超时），适合任务… | 否 |
| [pg_net](#pg_net) | 异步 HTTP 客户端扩展，在数据库内以异步方式发起 HTTP(S) 请求（不阻塞事务），常用于触发 Webhook… | 是 |
| [pg_graphql](#pg_graphql) | 为 PostgreSQL 自动生成 GraphQL 接口，基于数据库 schema 反射出 GraphQL 查询能力… | 否 |
| [pg_jsonschema](#pg_jsonschema) | JSON Schema 校验扩展，在数据库内对 jsonb 数据按 JSON Schema 校验，可用于写入前约束与… | 否 |
| [pgjwt](#pgjwt) | JWT 扩展，在数据库内生成与校验 JSON Web Token，可用于把简单的鉴权/会话能力下沉到数据库。 | 否 |
| [pg_hashids](#pg_hashids) | 短 ID 混淆扩展，把整型 ID 编码为不可枚举的短字符串（Hashids 算法），并在库内可反解，适合避免暴露自增… | 否 |
| [pg_uuidv7](#pg_uuidv7) | UUIDv7 生成扩展，生成按时间有序的 UUID（前 48 位为毫秒时间戳），既保持 UUID 的分布式特性，又具… | 否 |
| [snowflake](#snowflake) | 雪花 ID 扩展，在库内生成全局趋势递增的 64 位 ID（时间戳 + 机器位 + 序列位），适合分布式系统主键。 | 否 |
| [sequential_uuids](#sequential_uuids) | sequential_uuids（Sequential UUID generators）提供顺序化的 UUID 生成… | 否 |
| [hll](#hll) | HyperLogLog 扩展，用极小内存估算大基数集合的基数（近似 distinct count），并支持集合合并（… | 否 |
| [topn](#topn) | Top-N 近似聚合类型，用固定大小的 sketch 结构在有限内存内估算「出现次数最多的若干项」，适合大基数维度的… | 否 |
| [count_distinct](#count_distinct) | 提供替代 `COUNT(DISTINCT ...)` 的聚合实现，通过 HashAggregate 显著降低大表去重… | 否 |

<div id="timescaledb"></div>

#### timescaledb

时序数据库扩展：提供 hypertable（按时间自动分区）、连续聚合、压缩与保留策略，是时序指标、IoT、监控数据的常用方案。

> 使用前需将 `timescaledb` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS timescaledb;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS timescaledb;

-- 1. 建普通表并转为 hypertable（时间列必须有索引/主键包含）
CREATE TABLE conditions (
  time        timestamptz NOT NULL,
  device_id   int,
  temperature numeric
);
SELECT create_hypertable('conditions', 'time', chunk_time_interval => interval '1 day');

-- 2. 写入与时间范围查询
INSERT INTO conditions
SELECT g, (g % 10), random() * 30
FROM generate_series(now() - interval '7 days', now(), interval '1 minute') g;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="pgmq"></div>

#### pgmq

轻量消息队列扩展：在 PostgreSQL 内实现类似 SQS 的消息队列（发送、读取、删除、可见性超时），适合任务分发与异步处理。

```sql
CREATE EXTENSION IF NOT EXISTS pgmq;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgmq;

SELECT pgmq.create('my_queue');
SELECT pgmq.send('my_queue', '{"task":"sync"}'::jsonb);

-- 读取一条消息（可见性超时 30 秒）
SELECT * FROM pgmq.read('my_queue', 30, 1);

-- 处理完成归档
SELECT pgmq.archive('my_queue', (SELECT msg_id FROM pgmq.read('my_queue', 30, 1) LIMIT 1));
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="pg_net"></div>

#### pg_net

异步 HTTP 客户端扩展：在数据库内以异步方式发起 HTTP(S) 请求（不阻塞事务），常用于触发 Webhook、调用外部 API。

> 使用前需将 `pg_net` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_net;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_net;

-- 异步发起 POST 请求
SELECT net.http_post(
  url     := 'https://example.com/webhook',
  headers := '{"Content-Type": "application/json"}'::jsonb,
  body    := '{"event":"db_change"}'::jsonb
) AS request_id;

-- 稍后查看响应
SELECT * FROM net._http_response ORDER BY created DESC LIMIT 5;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="pg_graphql"></div>

#### pg_graphql

为 PostgreSQL 自动生成 GraphQL 接口：基于数据库 schema 反射出 GraphQL 查询能力，便于前端直接通过 GraphQL 访问数据。

```sql
CREATE EXTENSION IF NOT EXISTS pg_graphql;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_graphql;

-- 用 SQL 执行 GraphQL 查询
SELECT graphql.resolve($$
  {
    accountCollection(first: 5) {
      edges { node { id email } }
    }
  }
$$);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="pg_jsonschema"></div>

#### pg_jsonschema

JSON Schema 校验扩展：在数据库内对 jsonb 数据按 JSON Schema 校验，可用于写入前约束与数据质量检查。

```sql
CREATE EXTENSION IF NOT EXISTS pg_jsonschema;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_jsonschema;

-- 1. 校验一个 jsonb 是否符合 schema
SELECT jsonb_matches_schema(
  '{"type":"object","properties":{"age":{"type":"integer","minimum":0}},"required":["age"]}'::json,
  '{"age": 18}'::jsonb
) AS valid;

-- 2. 结合 CHECK 约束拦截非法数据
CREATE TABLE profile (
  id int PRIMARY KEY,
  data jsonb CHECK (jsonb_matches_schema('{"type":"object","required":["age"]}'::json, data))
);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="pgjwt"></div>

#### pgjwt

JWT 扩展：在数据库内生成与校验 JSON Web Token，可用于把简单的鉴权/会话能力下沉到数据库。

```sql
CREATE EXTENSION IF NOT EXISTS pgjwt;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgjwt;

-- 1. 生成 token（示例密钥请替换为强随机值）
SELECT sign('{"role":"app","uid":1234}'::json, '<密钥>') AS token;

-- 2. 校验 token
SELECT verify('<上一步生成的 token>', '<密钥>') AS payload;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="pg_hashids"></div>

#### pg_hashids

短 ID 混淆扩展：把整型 ID 编码为不可枚举的短字符串（Hashids 算法），并在库内可反解，适合避免暴露自增主键。

```sql
CREATE EXTENSION IF NOT EXISTS pg_hashids;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_hashids;

SELECT id_encode(12345) AS short_id;                      -- 默认 salt
SELECT id_decode(id_encode(12345)) AS original_id;

-- 自定义 salt 与最短长度
SELECT id_encode(12345, 'my-secret-salt', 8, 'abcdefghijklmnopqrstuvwxyz');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="pg_uuidv7"></div>

#### pg_uuidv7

UUIDv7 生成扩展：生成按时间有序的 UUID（前 48 位为毫秒时间戳），既保持 UUID 的分布式特性，又具备良好的索引局部性。

```sql
CREATE EXTENSION IF NOT EXISTS pg_uuidv7;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_uuidv7;

CREATE TABLE events (
  id uuid PRIMARY KEY DEFAULT uuid_generate_v7(),
  info text
);

INSERT INTO events (info) SELECT 'e' || g FROM generate_series(1, 1000) g;
SELECT id, info FROM events ORDER BY id DESC LIMIT 3;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="snowflake"></div>

#### snowflake

雪花 ID 扩展：在库内生成全局趋势递增的 64 位 ID（时间戳 + 机器位 + 序列位），适合分布式系统主键。

```sql
CREATE EXTENSION IF NOT EXISTS snowflake;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS snowflake;

CREATE TABLE orders (
  id   bigint PRIMARY KEY DEFAULT snowflake.next_id(),
  info text
);

INSERT INTO orders (info) SELECT 'o' || g FROM generate_series(1, 100) g;
SELECT id, info FROM orders ORDER BY id DESC LIMIT 5;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="sequential_uuids"></div>

#### sequential_uuids

sequential_uuids（Sequential UUID generators）提供顺序化的 UUID 生成函数，让 UUID 的低位随序列或时间递增，从而在使用 UUID 主键时保持良好的索引局部性，避免随机 UUID 造成的索引膨胀与写入放大。

```sql
CREATE EXTENSION IF NOT EXISTS sequential_uuids;
```

**使用示例**

```sql
-- 1. 时间戳模式：作为表主键默认值（推荐）
CREATE TABLE orders (
  id         uuid PRIMARY KEY DEFAULT uuid_time_nextval(),
  created_at timestamptz DEFAULT now(),
  amount     numeric(12,2)
);

INSERT INTO orders (amount)
SELECT (random() * 1000)::numeric(12,2) FROM generate_series(1, 1000);
SELECT * FROM orders ORDER BY id DESC LIMIT 3;

-- 2. 序列模式：基于自建序列生成顺序 UUID
CREATE SEQUENCE uuid_seq;
SELECT uuid_sequence_nextval('uuid_seq'::regclass, 256, 256);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="hll"></div>

#### hll

HyperLogLog 扩展：用极小内存估算大基数集合的基数（近似 distinct count），并支持集合合并（union），适合 UV 统计。

```sql
CREATE EXTENSION IF NOT EXISTS hll;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS hll;

CREATE TABLE uv_day (day date, users hll);

-- 每天写一行：把当天用户聚合为 hll
INSERT INTO uv_day
SELECT date_trunc('day', created_at)::date, hll_add_agg(hll_hash_text(user_id::text))
FROM visits GROUP BY 1;

-- 区间 UV 用并集估算
SELECT #hll_union_agg(users) AS uv_estimate
FROM uv_day WHERE day >= current_date - 30;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="topn"></div>

#### topn

Top-N 近似聚合类型：用固定大小的 sketch 结构在有限内存内估算「出现次数最多的若干项」，适合大基数维度的 Top-N 统计。

```sql
CREATE EXTENSION IF NOT EXISTS topn;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS topn;

CREATE TABLE site_log (url text);
-- 插入数据后统计访问量 Top 10
SELECT topn(t.topn_agg, 10) AS top_urls
FROM (
  SELECT topn_add_agg(url) AS topn_agg FROM site_log
) t;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

<div id="count_distinct"></div>

#### count_distinct

提供替代 `COUNT(DISTINCT ...)` 的聚合实现：通过 HashAggregate 显著降低大表去重计数的内存与时间开销。

```sql
CREATE EXTENSION IF NOT EXISTS count_distinct;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS count_distinct;

-- 对比：大表上去重计数
SELECT count_distinct(user_id) FROM events;

-- 传统写法（大表容易内存溢出/变慢）
-- SELECT count(DISTINCT user_id) FROM events;

-- 也可以与 GROUP BY 组合
SELECT date_trunc('day', created_at) AS d, count_distinct(user_id)
FROM events GROUP BY 1 ORDER BY 1;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回时序与消息插件一览](#timeseries-queue-index)

### 数据类型与分区

扩展数据类型、分区管理、数组与位图，以及索引访问方法。 本类共 24 个插件。

<div id="data-types-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [pg_partman](#pg_partman) | pg_partman 是分区表管理扩展，用于自动化「创建分区 + 维护分区」，按时间或 ID 区间自动建分区、按保留… | 是 |
| [pg_pathman](#pg_pathman) | pg_pathman 是高性能分区管理扩展，支持 HASH 与 RANGE 分区，提供函数式的分区创建 / 绑定 /… | 是 |
| [hstore](#hstore) | hstore 提供键值对（key-value）数据类型，可在单个列中保存一组文本键值，适合属性不固定、字段经常增减的… | 否 |
| [ltree](#ltree) | ltree 提供标签树（label tree）数据类型，用点号分隔的标签路径（如 `top.middle.leaf`… | 否 |
| [citext](#citext) | citext 提供大小写不敏感的字符串类型，写入与比较时自动忽略大小写差异，从而让唯一约束、等值查询、索引都按「不敏… | 否 |
| [isn](#isn) | isn 提供国际标准的编号数据类型，ISBN（图书）、EAN13、UPC、ISMN、ISSN 等，数据在写入时按标准… | 否 |
| [seg](#seg) | 线段 / 区间数据类型扩展（内置 contrib），提供 seg 类型表示浮点区间与线段，支持范围比较与 GiST … | 否 |
| [intarray](#intarray) | intarray 提供整数数组的专用函数与操作符，包含、相交、并集、差集、排序去重、下标查找等，并支持 GIN 索引… | 否 |
| [intagg](#intagg) | intagg 提供整数数组的聚合与枚举函数，把多行整数聚合成一个数组、把数组展开成多行，该功能目前已由内置的 `ar… | 否 |
| [aggs_for_arrays](#aggs_for_arrays) | aggs_for_arrays 提供一批对数组做统计计算的函数（均值、极值、中位数、众数、分位数、直方图等），把一批… | 否 |
| [roaringbitmap](#roaringbitmap) | roaringbitmap 提供 Roaring Bitmap 数据类型与位图运算函数，用于对大量整数集合做交集、并… | 否 |
| [bloom](#bloom) | Bloom 过滤索引访问方法（内置 contrib），用签名文件索引支持多列任意组合的等值查询，空间占用小，适合多列… | 否 |
| [btree_gin](#btree_gin) | btree_gin 为常见标量类型（int、text、timestamp、uuid 等）提供 GIN 操作符类，使 … | 否 |
| [btree_gist](#btree_gist) | btree_gist 为常见标量类型（int、text、timestamp、uuid 等）提供 GiST 操作符类，… | 否 |
| [uuid-ossp](#uuid-ossp) | UUID 生成扩展（内置 contrib），提供多种 UUID 生成函数（v1、v3、v4、v5）。 | 否 |
| [xml2](#xml2) | XML 处理扩展（内置 contrib），提供 XPath 查询与 XSLT 转换函数，用于在库内做 XML 解析与… | 否 |
| [lo](#lo) | 大对象维护扩展（内置 contrib），提供对大对象（Large Object）的查看与维护能力，用于排查与清理 l… | 否 |
| [old_snapshot](#old_snapshot) | 旧快照工具扩展（内置 contrib），配合 old_snapshot_threshold 参数，查看事务使用的旧快… | 否 |
| [timetravel](#timetravel) | 时间旅行函数（内置 contrib），提供对表历史版本的访问能力（仅支持特定存储实现），属实验性历史功能。 | 否 |
| [refint](#refint) | 参照完整性触发器函数（内置 contrib），提供检查引用完整性的触发器函数，属于早期替代外键的方案。 | 否 |
| [tcn](#tcn) | 触发变更通知扩展（内置 contrib），数据变更时通过 LISTEN/NOTIFY 向客户端推送变更通知，用于实现… | 否 |
| [autoinc](#autoinc) | 自增触发器函数（内置 contrib），提供 autoinc() 触发器函数用于自动填充序列值，属于早期替代方案。 | 否 |
| [insert_username](#insert_username) | 审计字段触发器函数（内置 contrib），提供 insert_username() 触发器函数，在插入/更新时自动… | 否 |
| [moddatetime](#moddatetime) | 修改时间触发器函数（内置 contrib），提供 moddatetime() 触发器函数，在 UPDATE 时自动把… | 否 |

<div id="pg_partman"></div>

#### pg_partman

pg_partman 是分区表管理扩展，用于自动化「创建分区 + 维护分区」：按时间或 ID 区间自动建分区、按保留策略删除或归档历史分区，避免人工维护分区表。支持 PostgreSQL 原生声明式分区。

> 使用前需将 `pg_partman` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_partman SCHEMA partman;
```

**使用示例**

```sql
-- 1. 建父表（原生分区表，主键需包含分区键）
CREATE TABLE public.measurements (
  city_id   int  not null,
  logdate   date not null,
  peaktemp  int,
  PRIMARY KEY (city_id, logdate)
) PARTITION BY RANGE (logdate);

-- 2. 交给 pg_partman 管理：按天分区，提前建 4 个
SELECT partman.create_parent(
  p_parent_table => 'public.measurements',
  p_control      => 'logdate',
  p_interval     => 'daily',
  p_premake      => 4
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="pg_pathman"></div>

#### pg_pathman

pg_pathman 是高性能分区管理扩展，支持 HASH 与 RANGE 分区，提供函数式的分区创建 / 绑定 / 拆分 / 合并能力，并为分区表生成更优的查询计划（含分区裁剪与运行时追加）。适合在较低大版本上使用分区表。

> 使用前需将 `pg_pathman` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_pathman;
```

**使用示例**

```sql
-- 1. HASH 分区：按 id 分 4 个区
CREATE TABLE hash_test (id int primary key, val int);
SELECT create_hash_partitions('hash_test', 'id', 4);

-- 2. RANGE 分区：按日期每 7 天一个分区，先建 4 个
CREATE TABLE range_test (logdate date not null, val int);
SELECT create_range_partitions('range_test', 'logdate', '2026-01-01'::date, interval '7 days', 4);

-- 3. 查看分区清单
SELECT * FROM pathman_partition_list WHERE parent = 'range_test'::regclass;

-- 4. 追加 / 分裂 / 合并范围分区
SELECT add_partition('range_test', '2026-02-01'::date);
SELECT split_partition('range_test', 'range_test_1', '2026-01-04'::date);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="hstore"></div>

#### hstore

hstore 提供键值对（key-value）数据类型，可在单个列中保存一组文本键值，适合属性不固定、字段经常增减的业务数据（如扩展属性、标签、配置项）。

```sql
CREATE EXTENSION IF NOT EXISTS hstore;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS hstore;

CREATE TABLE products (
  id     bigserial PRIMARY KEY,
  name   text,
  attrs  hstore
);

-- 1. 写入
INSERT INTO products (name, attrs) VALUES
  ('手机',  'brand=>UCloud, color=>黑色, storage=>256G'),
  ('笔记本', 'brand=>UCloud, color=>银色, memory=>16G');

-- 2. 建 GIN 索引（加速包含与键存在查询）
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="ltree"></div>

#### ltree

ltree 提供标签树（label tree）数据类型，用点号分隔的标签路径（如 `top.middle.leaf`）表示层级数据，并提供查询路径包含、祖先/后代判断、最近公共祖先等函数与 GiST 索引支持，适用于组织架构、商品类目、菜单等树形结构。

```sql
CREATE EXTENSION IF NOT EXISTS ltree;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS ltree;

CREATE TABLE categories (
  id   bigserial PRIMARY KEY,
  name text,
  path ltree
);

-- 1. 写入层级数据
INSERT INTO categories (name, path) VALUES
  ('科学',     'top.science'),
  ('物理',     'top.science.physics'),
  ('天文',     'top.science.astro'),
  ('文学',     'top.literature');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="citext"></div>

#### citext

citext 提供大小写不敏感的字符串类型：写入与比较时自动忽略大小写差异，从而让唯一约束、等值查询、索引都按「不敏感」语义工作，避免到处写 `lower(col)`。

```sql
CREATE EXTENSION IF NOT EXISTS citext;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS citext;

-- 1. 用 citext 建表：用户名唯一且大小写不敏感
CREATE TABLE users (
  id       bigserial PRIMARY KEY,
  username citext UNIQUE,
  email    citext
);

INSERT INTO users (username, email) VALUES ('Alice', 'Alice@Example.com');
-- 再插入 'alice' 会被唯一约束拦截
INSERT INTO users (username, email) VALUES ('alice', 'alice@example.com');
-- ERROR: duplicate key value violates unique constraint "users_username_key"

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="isn"></div>

#### isn

isn 提供国际标准的编号数据类型：ISBN（图书）、EAN13、UPC、ISMN、ISSN 等。数据在写入时按标准校验（含校验位），并按规范化格式存储与输出，适合图书、零售等编号强校验场景。

```sql
CREATE EXTENSION IF NOT EXISTS isn;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS isn;

CREATE TABLE books (
  id     bigserial PRIMARY KEY,
  title  text,
  isbn   isbn13,
  issn   issn
);

-- 1. 标准格式写入（含校验位）
INSERT INTO books (title, isbn) VALUES
  ('PostgreSQL 实战', '978-7-121-12345-6'),
  ('数据库系统概念',  '9787111123456');

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="seg"></div>

#### seg

线段 / 区间数据类型扩展（内置 contrib）：提供 seg 类型表示浮点区间与线段，支持范围比较与 GiST / B-tree 索引。

```sql
CREATE EXTENSION IF NOT EXISTS seg;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS seg;

CREATE TABLE ranges (id bigserial PRIMARY KEY, r seg);
INSERT INTO ranges (r) VALUES ('[1,10]'), ('[5,20]'), ('[30,40]');
CREATE INDEX idx_ranges_r ON ranges USING gist (r);

-- 与 [8,12] 相交的区间
SELECT * FROM ranges WHERE r && '[8,12]'::seg;
-- 包含 5 的区间
SELECT * FROM ranges WHERE r @> '5'::seg;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="intarray"></div>

#### intarray

intarray 提供整数数组的专用函数与操作符：包含、相交、并集、差集、排序去重、下标查找等，并支持 GIN 索引，可显著加速整数数组字段的检索与去重统计。

```sql
CREATE EXTENSION IF NOT EXISTS intarray;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS intarray;

CREATE TABLE user_tags (
  user_id bigint PRIMARY KEY,
  tags    int[]
);

INSERT INTO user_tags VALUES
  (1, ARRAY[10, 20, 30, 30]),
  (2, ARRAY[20, 40]),
  (3, ARRAY[30, 50]);

-- 1. 建 GIN 索引，加速包含/相交查询
CREATE INDEX idx_user_tags ON user_tags USING gin (tags gin__int_ops);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="intagg"></div>

#### intagg

intagg 提供整数数组的聚合与枚举函数：把多行整数聚合成一个数组、把数组展开成多行。该功能目前已由内置的 `array_agg()` 与 `unnest()` 覆盖，属于兼容性保留插件。

```sql
CREATE EXTENSION IF NOT EXISTS intagg;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS intagg;

CREATE TABLE order_items (order_id int, product_id int);
INSERT INTO order_items VALUES (1, 100), (1, 101), (1, 102), (2, 200), (2, 201);

-- 1. 聚合：每个订单的商品 ID 数组
SELECT order_id, int_array_aggregate(product_id) AS products
FROM order_items
GROUP BY order_id;

-- 2. 枚举：把数组展开为多行
SELECT order_id, int_array_enum(int_array_aggregate(product_id)) AS product_id
FROM order_items
GROUP BY order_id;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="aggs_for_arrays"></div>

#### aggs_for_arrays

aggs_for_arrays 提供一批对数组做统计计算的函数（均值、极值、中位数、众数、分位数、直方图等）。把一批数值放在一行的数组列里整体计算，比逐行聚合快得多，适合指标、采样、日志等高密度数值场景。

```sql
CREATE EXTENSION IF NOT EXISTS aggs_for_arrays;
```

**使用示例**

```sql
-- 1. 用数组列存放一批采样值
CREATE TABLE metrics (
  id      bigserial PRIMARY KEY,
  name    text,
  samples float8[]
);

INSERT INTO metrics (name, samples)
VALUES ('cpu', ARRAY(SELECT random() * 100 FROM generate_series(1, 10000)));

-- 2. 一次算出均值、极值、中位数
SELECT name,
       array_to_mean(samples)   AS mean,
       array_to_min(samples)    AS min,
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="roaringbitmap"></div>

#### roaringbitmap

roaringbitmap 提供 Roaring Bitmap 数据类型与位图运算函数，用于对大量整数集合做交集、并集、差集与基数统计。相比整数数组，位图在存储与计算上都更紧凑高效，常用于标签筛选、人群圈选、UV 去重等场景。

```sql
CREATE EXTENSION IF NOT EXISTS roaringbitmap;
```

**使用示例**

```sql
-- 1. 创建插件与带位图列的表
CREATE EXTENSION IF NOT EXISTS roaringbitmap;
CREATE TABLE tags (id integer, bitmap roaringbitmap);

-- 2. 写入：数组构建 + 聚合构建
INSERT INTO tags SELECT 1, rb_build(ARRAY[1,2,3,4,5,6,7,8,9,200]);
INSERT INTO tags SELECT 2, rb_build_agg(e) FROM generate_series(1, 100) e;

-- 3. 位图运算：交集
SELECT rb_and(a.bitmap, b.bitmap) AS intersect_users
FROM (SELECT bitmap FROM tags WHERE id = 1) a,
     (SELECT bitmap FROM tags WHERE id = 2) b;

-- 4. 圈选规模统计（基数）
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="bloom"></div>

#### bloom

Bloom 过滤索引访问方法（内置 contrib）：用签名文件索引支持多列任意组合的等值查询，空间占用小，适合多列条件组合不固定的检索。

```sql
CREATE EXTENSION IF NOT EXISTS bloom;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS bloom;

CREATE TABLE logs (id bigserial PRIMARY KEY, user_id int, action text, ip text);

CREATE INDEX idx_logs_bloom ON logs
  USING bloom (user_id, action, ip) WITH (length = 80, col1 = 4, col2 = 4, col3 = 4);
ANALYZE logs;

-- 以下等值组合查询均可命中该索引
SELECT count(*) FROM logs WHERE user_id = 100;
SELECT count(*) FROM logs WHERE user_id = 100 AND action = 'login';
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="btree_gin"></div>

#### btree_gin

btree_gin 为常见标量类型（int、text、timestamp、uuid 等）提供 GIN 操作符类，使 GIN 索引也能索引这些类型。典型用途是建立「多列 GIN 索引」，把标量过滤条件与数组 / JSONB 条件放在同一个索引里。

```sql
CREATE EXTENSION IF NOT EXISTS btree_gin;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS btree_gin;

CREATE TABLE articles (
  id        bigserial PRIMARY KEY,
  title     text,
  tags      text[],
  author_id int,
  created_at timestamptz
);

-- 1. 普通 B-tree 方案：标量列用 B-tree，数组列用 GIN（需要两个索引）
-- CREATE INDEX idx_articles_author ON articles (author_id);
-- CREATE INDEX idx_articles_tags   ON articles USING gin (tags);

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="btree_gist"></div>

#### btree_gist

btree_gist 为常见标量类型（int、text、timestamp、uuid 等）提供 GiST 操作符类，使标量列与范围类型、几何类型可以共用一个 GiST 索引。最典型的用法是配合 EXCLUDE 约束实现「同一房间的时间段不能重叠」这类排他约束。

```sql
CREATE EXTENSION IF NOT EXISTS btree_gist;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS btree_gist;

-- 1. 排他约束：同一房间的时间段不允许重叠
CREATE TABLE bookings (
  id      bigserial PRIMARY KEY,
  room_id int       NOT NULL,
  period  tsrange   NOT NULL,
  EXCLUDE USING gist (room_id WITH =, period WITH &&)
);

-- 2. 正常插入
INSERT INTO bookings (room_id, period)
VALUES (101, '[2026-01-01 09:00, 2026-01-01 11:00)');

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="uuid-ossp"></div>

#### uuid-ossp

UUID 生成扩展（内置 contrib）：提供多种 UUID 生成函数（v1、v3、v4、v5）。

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

SELECT uuid_generate_v4() AS random_uuid;
SELECT uuid_generate_v1() AS time_based_uuid;

CREATE TABLE t_uuid (id uuid PRIMARY KEY DEFAULT uuid_generate_v4(), info text);
INSERT INTO t_uuid (info) VALUES ('a'), ('b');
SELECT * FROM t_uuid;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="xml2"></div>

#### xml2

XML 处理扩展（内置 contrib）：提供 XPath 查询与 XSLT 转换函数，用于在库内做 XML 解析与转换。

```sql
CREATE EXTENSION IF NOT EXISTS xml2;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS xml2;

-- XPath 查询
SELECT xpath('/book/title/text()', '<book><title>PostgreSQL</title></book>'::xml) AS titles;

-- 用 xpath_table 把 XML 拆成行
SELECT * FROM xpath_table('id', 'xmldoc', 'documents', '/book/title', '1=1') AS t(id int, title text);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="lo"></div>

#### lo

大对象维护扩展（内置 contrib）：提供对大对象（Large Object）的查看与维护能力，用于排查与清理 lo 数据。

```sql
CREATE EXTENSION IF NOT EXISTS lo;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS lo;

-- 查看数据库中的大对象及其归属
SELECT l.oid, l.loid, t.relname, a.attname
FROM pg_largeobject_metadata l
LEFT JOIN lo ON true
LIMIT 20;

SELECT count(*) AS lo_count FROM pg_largeobject_metadata;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="old_snapshot"></div>

#### old_snapshot

旧快照工具扩展（内置 contrib）：配合 old_snapshot_threshold 参数，查看事务使用的旧快照信息。

```sql
CREATE EXTENSION IF NOT EXISTS old_snapshot;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="timetravel"></div>

#### timetravel

时间旅行函数（内置 contrib）：提供对表历史版本的访问能力（仅支持特定存储实现），属实验性历史功能。

```sql
CREATE EXTENSION IF NOT EXISTS timetravel;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="refint"></div>

#### refint

参照完整性触发器函数（内置 contrib）：提供检查引用完整性的触发器函数，属于早期替代外键的方案。

```sql
CREATE EXTENSION IF NOT EXISTS refint;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="tcn"></div>

#### tcn

触发变更通知扩展（内置 contrib）：数据变更时通过 LISTEN/NOTIFY 向客户端推送变更通知，用于实现轻量的实时刷新。

```sql
CREATE EXTENSION IF NOT EXISTS tcn;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS tcn;

CREATE TABLE items (id bigserial PRIMARY KEY, name text);

CREATE TRIGGER trg_items_tcn AFTER INSERT OR UPDATE OR DELETE ON items
  FOR EACH ROW EXECUTE PROCEDURE tcn('items_changed');

-- 客户端执行 LISTEN items_changed; 后，下面的写入会推送通知
INSERT INTO items (name) VALUES ('a');
UPDATE items SET name = 'b' WHERE id = 1;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="autoinc"></div>

#### autoinc

自增触发器函数（内置 contrib）：提供 autoinc() 触发器函数用于自动填充序列值，属于早期替代方案。

```sql
CREATE EXTENSION IF NOT EXISTS autoinc;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="insert_username"></div>

#### insert_username

审计字段触发器函数（内置 contrib）：提供 insert_username() 触发器函数，在插入/更新时自动写入当前用户名。

```sql
CREATE EXTENSION IF NOT EXISTS insert_username;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS insert_username;

CREATE TABLE docs (id bigserial PRIMARY KEY, content text, created_by text);

CREATE TRIGGER trg_docs_ins BEFORE INSERT ON docs
  FOR EACH ROW EXECUTE PROCEDURE insert_username('created_by');

INSERT INTO docs (content) VALUES ('hello');
SELECT * FROM docs;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

<div id="moddatetime"></div>

#### moddatetime

修改时间触发器函数（内置 contrib）：提供 moddatetime() 触发器函数，在 UPDATE 时自动把指定列更新为当前时间。

```sql
CREATE EXTENSION IF NOT EXISTS moddatetime;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS moddatetime;

CREATE TABLE docs (id bigserial PRIMARY KEY, content text, updated_at timestamptz);

CREATE TRIGGER trg_docs_upd BEFORE UPDATE ON docs
  FOR EACH ROW EXECUTE PROCEDURE moddatetime(updated_at);

INSERT INTO docs (content) VALUES ('v1');
UPDATE docs SET content = 'v2' WHERE id = 1;
SELECT id, content, updated_at FROM docs;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回数据类型与分区插件一览](#data-types-index)

### 跨库与联邦

跨库/跨实例访问、逻辑复制、变更捕获与文件、日志外部数据源。 本类共 18 个插件。

<div id="fdw-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [dblink](#dblink) | dblink 允许在一个数据库会话中连接并查询另一个 PostgreSQL 数据库（可以是同实例的其他库，也可以是其… | 否 |
| [postgres_fdw](#postgres_fdw) | postgres_fdw 是 PostgreSQL 官方外部数据包装器，可把远端 PostgreSQL 的表映射为本… | 否 |
| [mysql_fdw](#mysql_fdw) | mysql_fdw 让 PostgreSQL 可以直接读写 MySQL / MariaDB 中的数据，把远端 MyS… | 否 |
| [oracle_fdw](#oracle_fdw) | Oracle 外部数据包装器，把远端 Oracle 表映射为本地外部表，在 PostgreSQL 中直接查询 Ora… | 否 |
| [tds_fdw](#tds_fdw) | TDS 外部数据包装器，把 SQL Server / Sybase 的表映射为本地外部表，在 PostgreSQL … | 否 |
| [clickhouse_fdw](#clickhouse_fdw) | ClickHouse 外部数据包装器，把远端 ClickHouse 表映射为本地外部表，便于把 OLTP 与 OLA… | 否 |
| [log_fdw](#log_fdw) | 日志外部数据包装器，把服务端日志文件映射为外部表，用 SQL 查询/统计日志（错误、慢查询等），无需登录服务器。 | 否 |
| [file_fdw](#file_fdw) | 文件外部数据包装器（内置 contrib），把服务器上的 CSV / 文本文件当作外部表查询，也可配合 COPY 用… | 否 |
| [plproxy](#plproxy) | PL/Proxy 是面向水平拆分（分库分表）的代理语言，代理节点上的函数通过 `LANGUAGE plproxy` … | 是 |
| [pglogical](#pglogical) | pglogical 提供逻辑复制能力，可按表/按库做发布订阅、跨大版本复制、多主与双向复制，是迁移与双写场景的常用工… | 是 |
| [pglogical_origin](#pglogical_origin) | 兼容性占位扩展，从 PostgreSQL 9.4 升级的场景中保留该扩展名，正常使用 pglogical 时无需关注… | 否 |
| [pgl_ddl_deploy](#pgl_ddl_deploy) | DDL 自动部署扩展，基于 pglogical 逻辑复制，把发布端的 DDL 变更自动同步到订阅端，避免逻辑复制场景… | 是 |
| [decoderbufs](#decoderbufs) | 逻辑解码输出插件（Debezium 使用），以 Protocol Buffers 格式输出 WAL 变更，供 Deb… | 是 |
| [wal2json](#wal2json) | 逻辑解码输出插件，把 WAL 变更以 JSON 格式输出给订阅端，用于 CDC（变更数据捕获）、实时同步到 Kafk… | 是 |
| [zombodb](#zombodb) | ZomboDB 扩展，把 Elasticsearch 作为 PostgreSQL 的索引后端，兼顾事务一致性（数据在… | 是 |
| [http](#http) | HTTP 客户端扩展（pgsql-http），在 SQL 中直接发起同步 HTTP(S) 请求（GET/POST/H… | 否 |
| [gzip](#gzip) | gzip 压缩函数扩展（pgsql-gzip），在库内直接对 bytea 数据做 gzip / gunzip 压缩与… | 否 |
| [amcheck](#amcheck) | 索引与表一致性校验扩展（内置 contrib），用于检查 B-tree 索引是否损坏、是否存在违反约束的堆表数据。 | 否 |

<div id="dblink"></div>

#### dblink

dblink 允许在一个数据库会话中连接并查询另一个 PostgreSQL 数据库（可以是同实例的其他库，也可以是其他实例）。它适合临时跨库取数、跨库比对与轻量数据同步。

```sql
CREATE EXTENSION IF NOT EXISTS dblink;
```

**使用示例**

```sql
-- 1. 创建插件
CREATE EXTENSION IF NOT EXISTS dblink;

-- 2. 建立命名连接（建议 host 写实例实际地址/内网地址）
SELECT dblink_connect(
  'conn1',
  'host=<目标地址> port=5432 dbname=<目标库> user=root password=<密码>'
);

-- 3. 查询远程表（必须给出列定义）
SELECT *
FROM dblink('conn1', 'SELECT id, name FROM public.customers WHERE id < 100')
     AS t(id int, name text);

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="postgres_fdw"></div>

#### postgres_fdw

postgres_fdw 是 PostgreSQL 官方外部数据包装器，可把远端 PostgreSQL 的表映射为本地「外部表」，像访问本地表一样查询（并可写入）。相比 dblink，它支持下推、批量抓取与统计信息收集，是跨库 / 跨实例访问的首选方式。

```sql
CREATE EXTENSION IF NOT EXISTS postgres_fdw;
```

**使用示例**

```sql
-- 1. 创建插件
CREATE EXTENSION IF NOT EXISTS postgres_fdw;

-- 2. 定义远端实例
CREATE SERVER remote_pg
FOREIGN DATA WRAPPER postgres_fdw
OPTIONS (host '<目标地址>', port '5432', dbname '<目标库>', fetch_size '1000');

-- 3. 定义账号映射（本地 root -> 远端账号）
CREATE USER MAPPING FOR root
SERVER remote_pg
OPTIONS (user 'root', password '<密码>');

-- 4. 批量导入远端表（推荐）
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="mysql_fdw"></div>

#### mysql_fdw

mysql_fdw 让 PostgreSQL 可以直接读写 MySQL / MariaDB 中的数据：把远端 MySQL 表映射为本地外部表，便于异构数据库之间的数据同步、迁移与联合查询。

```sql
CREATE EXTENSION IF NOT EXISTS mysql_fdw;
```

**使用示例**

```sql
-- 1. 创建插件
CREATE EXTENSION IF NOT EXISTS mysql_fdw;

-- 2. 定义远端 MySQL 实例
CREATE SERVER mysql_svr
FOREIGN DATA WRAPPER mysql_fdw
OPTIONS (host '<MySQL地址>', port '3306');

-- 3. 定义账号映射
CREATE USER MAPPING FOR root
SERVER mysql_svr
OPTIONS (username '<MySQL账号>', password '<密码>');

-- 4. 批量导入 MySQL 中的表
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="oracle_fdw"></div>

#### oracle_fdw

Oracle 外部数据包装器：把远端 Oracle 表映射为本地外部表，在 PostgreSQL 中直接查询 Oracle 数据，用于迁移与数据互通。

```sql
CREATE EXTENSION IF NOT EXISTS oracle_fdw;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS oracle_fdw;

CREATE SERVER ora_svr FOREIGN DATA WRAPPER oracle_fdw
  OPTIONS (dbserver '//<oracle地址>:1521/<service>');

CREATE USER MAPPING FOR root SERVER ora_svr
  OPTIONS (user '<oracle用户>', password '<密码>');

CREATE FOREIGN TABLE public.ora_users (
  id   numeric,
  name varchar(100)
) SERVER ora_svr OPTIONS (schema 'APP', table 'USERS');

SELECT * FROM public.ora_users LIMIT 10;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="tds_fdw"></div>

#### tds_fdw

TDS 外部数据包装器：把 SQL Server / Sybase 的表映射为本地外部表，在 PostgreSQL 中查询这些数据源。

```sql
CREATE EXTENSION IF NOT EXISTS tds_fdw;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS tds_fdw;

CREATE SERVER mssql_svr FOREIGN DATA WRAPPER tds_fdw
  OPTIONS (servername '<SQLServer地址>', port '1433', database '<库名>');

CREATE USER MAPPING FOR root SERVER mssql_svr
  OPTIONS (username '<账号>', password '<密码>');

CREATE FOREIGN TABLE public.mssql_orders (
  id int, amount numeric
) SERVER mssql_svr OPTIONS (table_name 'dbo.orders', row_estimate_method 'showplan_all');

SELECT * FROM public.mssql_orders LIMIT 10;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="clickhouse_fdw"></div>

#### clickhouse_fdw

ClickHouse 外部数据包装器：把远端 ClickHouse 表映射为本地外部表，便于把 OLTP 与 OLAP 数据联合查询。

```sql
CREATE EXTENSION IF NOT EXISTS clickhouse_fdw;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="log_fdw"></div>

#### log_fdw

日志外部数据包装器：把服务端日志文件映射为外部表，用 SQL 查询/统计日志（错误、慢查询等），无需登录服务器。

```sql
CREATE EXTENSION IF NOT EXISTS log_fdw;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS log_fdw;

-- 1. 列出日志文件，选择 FILEID
SELECT * FROM log_fdw.list_postgres_log_files();

-- 2. 把指定日志文件映射为外部表
SELECT log_fdw.create_foreign_table('postgres_log', '<FILEID>');

-- 3. 用 SQL 分析日志
SELECT log_time, error_severity, left(message, 120) AS msg
FROM postgres_log
WHERE error_severity IN ('ERROR','FATAL')
ORDER BY log_time DESC
LIMIT 20;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="file_fdw"></div>

#### file_fdw

文件外部数据包装器（内置 contrib）：把服务器上的 CSV / 文本文件当作外部表查询，也可配合 COPY 用于导入导出与日志分析。

```sql
CREATE EXTENSION IF NOT EXISTS file_fdw;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS file_fdw;

CREATE SERVER file_svr FOREIGN DATA WRAPPER file_fdw;

-- 用 CSV 文件建外部表（请替换为实例上实际可读的文件路径）
CREATE FOREIGN TABLE csv_demo (
  id   int,
  name text
) SERVER file_svr OPTIONS (filename '<CSV 文件绝对路径>', format 'csv', header 'true');

SELECT * FROM csv_demo LIMIT 10;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="plproxy"></div>

#### plproxy

PL/Proxy 是面向水平拆分（分库分表）的代理语言：代理节点上的函数通过 `LANGUAGE plproxy` 声明的规则，把调用转发到后端数据节点执行。支持 CLUSTER 模式（按哈希分区路由）与 CONNECT 模式（直连指定数据库）。

> 使用前需将 `plproxy` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS plproxy;
```

**使用示例**

```sql
-- 1. 创建插件（代理节点与数据节点都建议安装）
CREATE EXTENSION IF NOT EXISTS plproxy;

-- 2. 定义集群分区（代理节点上）
CREATE OR REPLACE FUNCTION plproxy.get_cluster_partitions(cluster_name text)
RETURNS SETOF text AS $$
BEGIN
  IF cluster_name = 'mycluster' THEN
    RETURN NEXT '<数据节点1>:5432';
    RETURN NEXT '<数据节点2>:5432';
    RETURN NEXT '<数据节点3>:5432';
    RETURN NEXT '<数据节点4>:5432';
    RETURN;
  END IF;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="pglogical"></div>

#### pglogical

pglogical 提供逻辑复制能力：可按表/按库做发布订阅、跨大版本复制、多主与双向复制，是迁移与双写场景的常用工具。

> 使用前需将 `pglogical` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pglogical;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pglogical;

-- 发布端
SELECT pglogical.create_node(node_name := 'provider1', dsn := 'host=<本机地址> port=5432 dbname=<库> user=root password=<密码>');
SELECT pglogical.replication_set_add_all_tables('default', ARRAY['public']);

-- 订阅端
SELECT pglogical.create_node(node_name := 'subscriber1', dsn := 'host=<订阅端地址> port=5432 dbname=<库> user=root password=<密码>');
SELECT pglogical.create_subscription(
  subscription_name := 'sub1',
  provider_dsn      := 'host=<发布端地址> port=5432 dbname=<库> user=root password=<密码>'
);

SELECT * FROM pglogical.show_subscription_status();
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="pglogical_origin"></div>

#### pglogical_origin

兼容性占位扩展：从 PostgreSQL 9.4 升级的场景中保留该扩展名，正常使用 pglogical 时无需关注。

```sql
CREATE EXTENSION IF NOT EXISTS pglogical_origin;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="pgl_ddl_deploy"></div>

#### pgl_ddl_deploy

DDL 自动部署扩展：基于 pglogical 逻辑复制，把发布端的 DDL 变更自动同步到订阅端，避免逻辑复制场景下结构不一致。

> 使用前需将 `pgl_ddl_deploy` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pgl_ddl_deploy;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="decoderbufs"></div>

#### decoderbufs

逻辑解码输出插件（Debezium 使用）：以 Protocol Buffers 格式输出 WAL 变更，供 Debezium 捕获 PostgreSQL 变更。

> 使用前需将 `decoderbufs` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS decoderbufs;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="wal2json"></div>

#### wal2json

逻辑解码输出插件：把 WAL 变更以 JSON 格式输出给订阅端，用于 CDC（变更数据捕获）、实时同步到 Kafka/数仓等场景。

> 使用前需将 `wal2json` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS wal2json;
```

**使用示例**

```sql
-- 1. 参数：wal_level = logical（需要重启生效）
-- 2. 创建逻辑复制槽（使用 wal2json 插件）
SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');

-- 3. 产生变更后读取
CREATE TABLE IF NOT EXISTS t_demo (id int primary key, v text);
INSERT INTO t_demo VALUES (1, 'hello');

SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL);

-- 4. 用完删除复制槽
SELECT pg_drop_replication_slot('test_slot');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="zombodb"></div>

#### zombodb

ZomboDB 扩展：把 Elasticsearch 作为 PostgreSQL 的索引后端，兼顾事务一致性（数据在 PG）与 ES 的检索、聚合能力。

> 使用前需将 `zombodb` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS zombodb;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="http"></div>

#### http

HTTP 客户端扩展（pgsql-http）：在 SQL 中直接发起同步 HTTP(S) 请求（GET/POST/HEAD 等），用于把外部接口数据拉进数据库或推送数据。

```sql
CREATE EXTENSION IF NOT EXISTS http;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS http;

-- GET 请求
SELECT status, content_type, (content::jsonb ->> 'origin') AS origin
FROM http_get('https://httpbin.org/get');

-- POST 请求（JSON 体）
SELECT status, content
FROM http_post('https://httpbin.org/post', '{"hello":"world"}', 'application/json');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="gzip"></div>

#### gzip

gzip 压缩函数扩展（pgsql-gzip）：在库内直接对 bytea 数据做 gzip / gunzip 压缩与解压，用于降低大字段存储与传输体积。

```sql
CREATE EXTENSION IF NOT EXISTS gzip;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS gzip;

CREATE TABLE payloads (id bigserial PRIMARY KEY, data bytea);

-- 压缩写入
INSERT INTO payloads (data)
VALUES (gzip(convert_to(repeat('重复内容-abcdefg', 1000), 'UTF8')));

-- 读取时解压
SELECT convert_from(gunzip(data), 'UTF8') AS payload FROM payloads WHERE id = 1;
SELECT pg_size_pretty(sum(octet_length(data))) AS compressed_size FROM payloads;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

<div id="amcheck"></div>

#### amcheck

索引与表一致性校验扩展（内置 contrib）：用于检查 B-tree 索引是否损坏、是否存在违反约束的堆表数据。

```sql
CREATE EXTENSION IF NOT EXISTS amcheck;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS amcheck;

-- 校验单个索引（可选 heapallindexed 做更彻底的堆校验）
SELECT bt_index_check('public.idx_orders_created_at'::regclass);
SELECT bt_index_check('public.orders_pkey'::regclass, heapallindexed => true);

-- 新版本：校验堆表是否与约束一致
-- SELECT * FROM verify_heapam(relation => 'public.orders');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回跨库与联邦插件一览](#fdw-index)

### 运维与监控

性能监控、锁与页面诊断、表与索引整理、任务调度与回归测试。 本类共 26 个插件。

<div id="ops-monitor-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [pg_stat_statements](#pg_stat_statements) | pg_stat_statements 用于追踪服务器执行过的所有 SQL 语句的规划与执行统计，可以按总耗时、平均耗… | 是 |
| [pg_stat_kcache](#pg_stat_kcache) | 内核级资源统计扩展，采集每条语句在操作系统层面的 CPU（user/sys）与读写字节数，与 pg_stat_sta… | 是 |
| [pg_wait_sampling](#pg_wait_sampling) | 等待事件采样扩展，周期性采样会话的等待事件，形成等待事件的直方图统计，用于判断瓶颈在锁、IO 还是 CPU。 | 是 |
| [pg_profile](#pg_profile) | 负载画像与报告扩展，定期采集负载快照（等待事件、语句统计、表/索引 IO 等），支持生成两个时间点之间的对比报告，类… | 否 |
| [pg_proctab](#pg_proctab) | 操作系统进程视图扩展，把数据库后端进程与操作系统进程信息（CPU、内存、IO）关联起来，便于定位具体会话的资源消耗。 | 否 |
| [pg_permissions](#pg_permissions) | 权限盘点扩展，把当前库中对象权限导出为可比较的快照视图，用于核对「实际权限」与「期望权限」是否一致。 | 否 |
| [pgstattuple](#pgstattuple) | pgstattuple 提供表和索引的物理统计信息，活元组、死元组、空闲空间占比等，是判断「表 / 索引是否膨胀」的… | 否 |
| [pg_buffercache](#pg_buffercache) | pg_buffercache 提供查看共享缓冲区（shared buffers）使用情况的视图与函数，哪些表/索引的… | 否 |
| [pg_freespacemap](#pg_freespacemap) | pg_freespacemap 用于查看表的空闲空间映射（FSM），每个数据块中有多少可用空间，有助于判断更新后空间… | 否 |
| [pgrowlocks](#pgrowlocks) | pgrowlocks 用于查看指定表中当前的行锁情况，哪些行被锁、由哪个事务持有、锁模式是什么、是否有多事务（mul… | 否 |
| [pg_prewarm](#pg_prewarm) | pg_prewarm 可以把表或索引的数据主动加载到操作系统缓存或 PostgreSQL 共享缓冲区，用于实例重启后… | 是 |
| [pg_repack](#pg_repack) | pg_repack 用于在线消除表与索引膨胀，以重建表的方式回收空闲空间，相比 CLUSTER 或 VACUUM F… | 否 |
| [pg_squeeze](#pg_squeeze) | pg_squeeze 用于在线重整膨胀的表与索引，通过建立表的压缩副本再切换，回收死元组与空闲空间，过程中不阻塞读写… | 是 |
| [hypopg](#hypopg) | hypopg 提供虚拟索引能力，在不真正创建索引的前提下，让优化器按「假设存在的索引」生成执行计划，快速验证加索引是… | 否 |
| [pg_hint_plan](#pg_hint_plan) | pg_hint_plan 允许在 SQL 中以特殊的注释形式给出执行计划提示（扫描方式、连接方式、连接顺序、并行度等… | 是 |
| [pageinspect](#pageinspect) | 底层页面检查扩展（内置 contrib），直接读取数据页与索引页内容（页面头、行指针、元组数据），用于深度排障与页面… | 否 |
| [pg_visibility](#pg_visibility) | 可见性映射检查扩展（内置 contrib），查看表的可见性映射（VM）与页级可见性信息，用于分析 VACUUM 效果… | 否 |
| [pg_walinspect](#pg_walinspect) | WAL 检查扩展（内置 contrib），直接读取 WAL 记录内容（插入、更新、检查点、事务提交等），用于复制/恢… | 否 |
| [pg_logicalinspect](#pg_logicalinspect) | pg_logicalinspect 是 PostgreSQL 18 新增的内置模块，用于查看**逻辑解码快照文件**… | 否 |
| [pg_surgery](#pg_surgery) | 数据修复工具扩展（内置 contrib），在表数据损坏导致无法访问时，直接修改元组可见性等底层标记，用于应急抢救数据… | 否 |
| [adminpack](#adminpack) | PostgreSQL 管理辅助函数包（内置 contrib），提供日志文件读写、数据库/表大小等管理函数，属早期维护… | 否 |
| [pgagent](#pgagent) | pgAgent 作业调度器，把作业定义保存在数据库中，由 pgAgent 守护进程按计划执行 SQL/脚本任务，是 … | 否 |
| [pg_cron](#pg_cron) | pg_cron 是基于 cron 的作业调度插件，语法与 Linux crontab 一致，插件在数据库内以后台进程… | 是 |
| [pgtap](#pgtap) | 数据库单元测试框架，用 SQL 编写断言（`ok()`、`is()` 等）测试表结构、函数与数据结果，并可输出 TA… | 否 |
| [plpgsql_check](#plpgsql_check) | PL/pgSQL 静态检查扩展，在编译期检查函数中的语法、对象引用与类型问题（比默认的「调用时才报错」更早发现问题）… | 否 |
| [pldbgapi](#pldbgapi) | PL/pgSQL 调试服务端支持，与 pldebugger 客户端配合，为存储过程/函数提供断点、单步与变量查看能力… | 是 |

<div id="pg_stat_statements"></div>

#### pg_stat_statements

pg_stat_statements 用于追踪服务器执行过的所有 SQL 语句的规划与执行统计，可以按总耗时、平均耗时、执行次数、扫描行数等维度定位慢 SQL 与高消耗语句，是数据库性能分析的基础工具。

> 使用前需将 `pg_stat_statements` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

**使用示例**

```sql
-- 1. 执行次数最多的语句
SELECT calls, rows,
       round(mean_exec_time::numeric, 2) AS mean_ms,
       left(query, 60) AS query
FROM pg_stat_statements
ORDER BY calls DESC
LIMIT 10;

-- 2. 平均耗时最高（排除执行次数过少的偶发语句）
SELECT calls,
       round(mean_exec_time::numeric, 2) AS mean_ms,
       round(total_exec_time::numeric, 2) AS total_ms,
       left(query, 80) AS query
FROM pg_stat_statements
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_stat_kcache"></div>

#### pg_stat_kcache

内核级资源统计扩展：采集每条语句在操作系统层面的 CPU（user/sys）与读写字节数，与 pg_stat_statements 配合定位 CPU/IO 热点。

> 使用前需将 `pg_stat_kcache` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_kcache;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_kcache;

-- 与 pg_stat_statements 关联，查看 CPU 消耗最高的语句
SELECT s.queryid, left(s.query, 60) AS query,
       k.exec_user_time, k.exec_system_time, k.plan_user_time,
       k.reads, k.writes
FROM pg_stat_kcache k
JOIN pg_stat_statements s USING (queryid)
ORDER BY k.exec_user_time + k.exec_system_time DESC
LIMIT 10;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_wait_sampling"></div>

#### pg_wait_sampling

等待事件采样扩展：周期性采样会话的等待事件，形成等待事件的直方图统计，用于判断瓶颈在锁、IO 还是 CPU。

> 使用前需将 `pg_wait_sampling` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_wait_sampling;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_wait_sampling;

-- 等待事件分布（Top 20）
SELECT event_type, event, count
FROM pg_wait_sampling_profile
ORDER BY count DESC LIMIT 20;

-- 指定语句/进程的等待明细
SELECT * FROM pg_wait_sampling_profile WHERE pid = 12345 ORDER BY count DESC;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_profile"></div>

#### pg_profile

负载画像与报告扩展：定期采集负载快照（等待事件、语句统计、表/索引 IO 等），支持生成两个时间点之间的对比报告，类似 AWR。

```sql
CREATE EXTENSION IF NOT EXISTS pg_profile;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_profile;

-- 1. 采集一份快照（建议用 pg_cron 定时执行）
SELECT take_sample();

-- 2. 查看已生成的报告
SELECT * FROM show_reports();

-- 3. 生成两个快照之间的报告
SELECT * FROM get_report(1, 2);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_proctab"></div>

#### pg_proctab

操作系统进程视图扩展：把数据库后端进程与操作系统进程信息（CPU、内存、IO）关联起来，便于定位具体会话的资源消耗。

```sql
CREATE EXTENSION IF NOT EXISTS pg_proctab;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_permissions"></div>

#### pg_permissions

权限盘点扩展：把当前库中对象权限导出为可比较的快照视图，用于核对「实际权限」与「期望权限」是否一致。

```sql
CREATE EXTENSION IF NOT EXISTS pg_permissions;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pgstattuple"></div>

#### pgstattuple

pgstattuple 提供表和索引的物理统计信息：活元组、死元组、空闲空间占比等，是判断「表 / 索引是否膨胀」的权威手段，也常用于评估是否需要 VACUUM、pg_repack 或表重建。

```sql
CREATE EXTENSION IF NOT EXISTS pgstattuple;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgstattuple;

-- 1. 表膨胀率（未使用空间占比）
SELECT table_len,
       tuple_count,
       dead_tuple_count,
       free_space,
       round(1.0 - tuple_len::numeric / nullif(table_len, 0), 4) AS bloat_ratio
FROM pgstattuple('public.orders');

-- 2. 死元组占比（判断是否需要 VACUUM）
SELECT round(dead_tuple_len::numeric / nullif(table_len, 0), 4) AS dead_ratio
FROM pgstattuple('public.orders');

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_buffercache"></div>

#### pg_buffercache

pg_buffercache 提供查看共享缓冲区（shared buffers）使用情况的视图与函数：哪些表/索引的数据常驻内存、缓存命中分布如何、有多少脏页，用于排查「缓存不够」还是「访问模式不合理」。

```sql
CREATE EXTENSION IF NOT EXISTS pg_buffercache;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_buffercache;

-- 1. 汇总：共享缓冲区整体使用情况
SELECT * FROM pg_buffercache_summary();

-- 2. 每个对象占用多少缓冲区（缓存占用排行）
SELECT c.relname,
       count(*)                                   AS buffers,
       pg_size_pretty(count(*) * 8192)            AS cached_size,
       round(avg(b.usagecount), 2)                AS avg_usage_count,
       count(*) FILTER (WHERE b.isdirty)          AS dirty_buffers
FROM pg_buffercache b
JOIN pg_class c ON c.relfilenode = b.relfilenode
WHERE b.relfilenode <> 0
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_freespacemap"></div>

#### pg_freespacemap

pg_freespacemap 用于查看表的空闲空间映射（FSM）：每个数据块中有多少可用空间。有助于判断更新后空间能否被复用、是否出现空间浪费，是分析表膨胀的辅助手段。

```sql
CREATE EXTENSION IF NOT EXISTS pg_freespacemap;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_freespacemap;

-- 1. 查看某表各块的空闲空间分布
SELECT blkno, pg_freespace('public.orders', blkno) AS free_bytes
FROM generate_series(0, 9) AS blkno;

-- 2. 统计整表空闲空间分布（按空闲量分桶）
SELECT pg_freespace('public.orders') AS free_bytes, count(*)
FROM generate_series(0, (SELECT pg_relation_size('public.orders') / 8192 - 1)) blkno
GROUP BY 1
ORDER BY 1;

-- 3. 粗略估算可复用空间占比
SELECT round(avg(pg_freespace('public.orders')) / 8192.0 * 100, 2) AS avg_free_percent
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pgrowlocks"></div>

#### pgrowlocks

pgrowlocks 用于查看指定表中当前的行锁情况：哪些行被锁、由哪个事务持有、锁模式是什么、是否有多事务（multi）标记。排查「谁锁住了这行」「为什么更新被阻塞」时非常有用。

```sql
CREATE EXTENSION IF NOT EXISTS pgrowlocks;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgrowlocks;

-- 1. 会话 A：开启事务并更新一行（不提交）
-- BEGIN;
-- UPDATE public.accounts SET balance = balance - 100 WHERE id = 1;

-- 2. 会话 B：查看该表的行锁情况
SELECT * FROM pgrowlocks('public.accounts');

-- 3. 结合 pg_stat_activity 找到持锁会话
SELECT l.locked_row,
       l.modes,
       a.pid,
       a.usename,
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_prewarm"></div>

#### pg_prewarm

pg_prewarm 可以把表或索引的数据主动加载到操作系统缓存或 PostgreSQL 共享缓冲区，用于实例重启后、大查询前预热热点数据，避免冷启动时的大量随机 IO。

> 使用前需将 `pg_prewarm` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_prewarm;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_prewarm;

-- 1. 预热整表到操作系统缓存（默认模式）
SELECT pg_prewarm('public.orders');

-- 2. 只预热表的前 1000 个块
SELECT pg_prewarm('public.orders', 'read', 'main', 0, 999);

-- 3. 预热到 PostgreSQL 共享缓冲区
SELECT pg_prewarm('public.orders', 'buffer');

-- 4. 预热索引
SELECT pg_prewarm('public.idx_orders_created_at');

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_repack"></div>

#### pg_repack

pg_repack 用于在线消除表与索引膨胀：以重建表的方式回收空闲空间，相比 CLUSTER 或 VACUUM FULL 更轻量，执行过程中不需要长时间持有排它锁。支持整表 repack 与索引 repack。

```sql
CREATE EXTENSION IF NOT EXISTS pg_repack;
```

**使用示例**

```sql
-- 6. 整理前后对比表与索引大小
SELECT pg_size_pretty(pg_total_relation_size('public.orders')) AS total,
       pg_size_pretty(pg_relation_size('public.orders'))       AS table_only,
       pg_size_pretty(pg_indexes_size('public.orders'))        AS indexes;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_squeeze"></div>

#### pg_squeeze

pg_squeeze 用于在线重整膨胀的表与索引：通过建立表的压缩副本再切换，回收死元组与空闲空间，过程中不阻塞读写，也无需像 VACUUM FULL 那样长时间持有排它锁。

> 使用前需将 `pg_squeeze` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_squeeze;
```

**使用示例**

```sql
-- 0. 先看膨胀率（需要 pgstattuple 配合）
CREATE EXTENSION IF NOT EXISTS pgstattuple;
SELECT *, 1.0 - tuple_len::numeric / table_len AS bloat
FROM pgstattuple('public.bar');

-- 1. 创建插件（每个要整理的数据库都要创建）
CREATE EXTENSION IF NOT EXISTS pg_squeeze;

-- 2. 方式一：立即整理某张表
SELECT squeeze.squeeze_table('public', 'bar');

-- 3. 方式二：注册自动整理，仅在指定时间窗内执行
INSERT INTO squeeze.tables (nspname, relname, schedule, free_space_extra, vacuum_max_age, max_retry)
VALUES ('public', 'bar', '* * * * *', 30, '1 hour', 3);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="hypopg"></div>

#### hypopg

hypopg 提供虚拟索引能力：在不真正创建索引的前提下，让优化器按「假设存在的索引」生成执行计划，快速验证加索引是否有效。虚拟索引不占磁盘、不耗 CPU，且只在当前会话有效。

```sql
CREATE EXTENSION IF NOT EXISTS hypopg;
```

**使用示例**

```sql
-- 1. 准备测试表
CREATE EXTENSION IF NOT EXISTS hypopg;
CREATE TABLE hypo (id integer, val text);
INSERT INTO hypo SELECT i, 'line ' || i FROM generate_series(1, 100000) i;
VACUUM ANALYZE hypo;

-- 2. 查看当前执行计划（全表扫描）
EXPLAIN SELECT val FROM hypo WHERE id = 1;

-- 3. 创建虚拟索引
SELECT * FROM hypopg_create_index('CREATE INDEX ON hypo (id)');

-- 4. 再看计划：已走 Index Scan，说明该索引有效
EXPLAIN SELECT val FROM hypo WHERE id = 1;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_hint_plan"></div>

#### pg_hint_plan

pg_hint_plan 允许在 SQL 中以特殊的注释形式给出执行计划提示（扫描方式、连接方式、连接顺序、并行度等），用于修正优化器选择不够理想的执行计划，而不必改写业务 SQL。

> 使用前需将 `pg_hint_plan` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_hint_plan;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_hint_plan;

-- 1. 强制走索引扫描
/*+ IndexScan(t) */
EXPLAIN SELECT * FROM public.orders t WHERE t.id = 100;

-- 2. 指定连接方式与连接顺序
/*+ Leading(o c) HashJoin(o c) */
EXPLAIN
SELECT o.id, c.name
FROM public.orders o
JOIN public.customers c ON c.id = o.customer_id
WHERE o.created_at > now() - interval '7 days';

```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pageinspect"></div>

#### pageinspect

底层页面检查扩展（内置 contrib）：直接读取数据页与索引页内容（页面头、行指针、元组数据），用于深度排障与页面级分析。

```sql
CREATE EXTENSION IF NOT EXISTS pageinspect;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pageinspect;

-- 1. 表页头部信息
SELECT * FROM page_header(get_raw_page('public.orders', 0));

-- 2. 页内元组（前 5 行）
SELECT lp, lp_off, lp_flags, t_xmin, t_xmax, t_data
FROM heap_page_items(get_raw_page('public.orders', 0))
LIMIT 5;

-- 3. B-tree 索引页信息
SELECT * FROM bt_page_items('public.idx_orders_created_at', 1) LIMIT 10;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_visibility"></div>

#### pg_visibility

可见性映射检查扩展（内置 contrib）：查看表的可见性映射（VM）与页级可见性信息，用于分析 VACUUM 效果与仅索引扫描效率。

```sql
CREATE EXTENSION IF NOT EXISTS pg_visibility;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_visibility;

-- 1. 表级可见性映射汇总
SELECT * FROM pg_visibility_map_summary('public.orders');

-- 2. 页级信息（前 10 页）
SELECT blkno, all_visible, all_frozen
FROM pg_visibility_map('public.orders')
LIMIT 10;

-- 3. 未冻结元组数量
SELECT count(*) FROM pg_check_frozen('public.orders');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_walinspect"></div>

#### pg_walinspect

WAL 检查扩展（内置 contrib）：直接读取 WAL 记录内容（插入、更新、检查点、事务提交等），用于复制/恢复问题排查与逻辑解码调试。

```sql
CREATE EXTENSION IF NOT EXISTS pg_walinspect;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_walinspect;

-- 1. 取当前 LSN 作为起点
SELECT pg_current_wal_lsn() AS start_lsn;

-- 2. 产生一些变更
CREATE TABLE IF NOT EXISTS wal_demo (id int);
INSERT INTO wal_demo VALUES (1), (2);

-- 3. 查看区间内的 WAL 记录分布（把 <start_lsn> 替换为上一步结果）
SELECT resource_manager, record_type, count(*)
FROM pg_get_wal_records_info('<start_lsn>', pg_current_wal_lsn())
GROUP BY 1, 2 ORDER BY 3 DESC;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_logicalinspect"></div>

#### pg_logicalinspect

pg_logicalinspect 是 PostgreSQL 18 新增的内置模块，用于查看**逻辑解码快照文件**的元信息与内部状态，排查逻辑复制 / 逻辑解码（尤其是两阶段提交与全量快照构建阶段）的问题。

```sql
CREATE EXTENSION IF NOT EXISTS pg_logicalinspect;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_logicalinspect;

-- 1. 列出逻辑解码快照文件（核心函数，需要相应权限）
SELECT * FROM pg_ls_logicalsnapdir();

-- 2. 查看某个快照文件的元信息
SELECT * FROM pg_get_logical_snapshot_meta('000000010000000000000002.snap');

-- 3. 查看快照内部状态：是否正在构建全量快照、已提交事务数、两阶段提交位置等
SELECT state, xmin, xmax, building_full_snapshot, in_slot_creation,
       committed_count, next_phase_at
FROM pg_get_logical_snapshot_info('000000010000000000000002.snap');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_surgery"></div>

#### pg_surgery

数据修复工具扩展（内置 contrib）：在表数据损坏导致无法访问时，直接修改元组可见性等底层标记，用于应急抢救数据。

```sql
CREATE EXTENSION IF NOT EXISTS pg_surgery;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="adminpack"></div>

#### adminpack

PostgreSQL 管理辅助函数包（内置 contrib）：提供日志文件读写、数据库/表大小等管理函数，属早期维护工具。

```sql
CREATE EXTENSION IF NOT EXISTS adminpack;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pgagent"></div>

#### pgagent

pgAgent 作业调度器：把作业定义保存在数据库中，由 pgAgent 守护进程按计划执行 SQL/脚本任务，是 pgAdmin 配套的调度方案。

```sql
CREATE EXTENSION IF NOT EXISTS pgagent;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pg_cron"></div>

#### pg_cron

pg_cron 是基于 cron 的作业调度插件，语法与 Linux crontab 一致。插件在数据库内以后台进程运行，通过 SQL 函数注册、修改、删除定时任务，可用来做定时清理、定时统计、定时 VACUUM 等运维自动化。

> 使用前需将 `pg_cron` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_cron;
```

**使用示例**

```sql
-- 1. 每分钟执行一次，用于快速验证插件是否生效
SELECT cron.schedule('* * * * *', 'SELECT 1;');

-- 2. 每天 02:00 清理 7 天前的日志
SELECT cron.schedule(
  'clean-old-logs',
  '0 2 * * *',
  $$DELETE FROM public.app_log WHERE created_at < now() - interval '7 days'$$
);

-- 3. 每周日 04:00 对某张表做 VACUUM
SELECT cron.schedule('weekly-vacuum', '0 4 * * 0', 'VACUUM public.orders');

-- 4. 在指定数据库中创建任务（任务名必填）
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pgtap"></div>

#### pgtap

数据库单元测试框架：用 SQL 编写断言（`ok()`、`is()` 等）测试表结构、函数与数据结果，并可输出 TAP 格式供 CI 解析。

```sql
CREATE EXTENSION IF NOT EXISTS pgtap;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgtap;

-- 示例用例：检查表存在、函数返回值正确
SELECT plan(2);
SELECT has_table('public', 'orders', 'orders 表应存在');
SELECT is((SELECT 1 + 1), 2, '简单加法');
SELECT * FROM finish();
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="plpgsql_check"></div>

#### plpgsql_check

PL/pgSQL 静态检查扩展：在编译期检查函数中的语法、对象引用与类型问题（比默认的「调用时才报错」更早发现问题）。

```sql
CREATE EXTENSION IF NOT EXISTS plpgsql_check;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS plpgsql_check;

-- 检查指定函数（输出问题清单）
SELECT * FROM plpgsql_check_function_tb('public.my_function(int)');

-- 检查整个 schema 中所有函数
SELECT p.oid::regprocedure AS func, c.message
FROM pg_proc p, plpgsql_check_function_tb(p.oid) c
WHERE p.pronamespace = 'public'::regnamespace;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

<div id="pldbgapi"></div>

#### pldbgapi

PL/pgSQL 调试服务端支持：与 pldebugger 客户端配合，为存储过程/函数提供断点、单步与变量查看能力。

> 使用前需将 `pldbgapi` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pldbgapi;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回运维与监控插件一览](#ops-monitor-index)

### 安全与合规

口令与数据加密、审计日志与数据脱敏。 本类共 7 个插件。

<div id="security-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [pgcrypto](#pgcrypto) | pgcrypto 为 PostgreSQL 提供密码学函数，摘要（MD5、SHA 系列）、HMAC、密码哈希（cry… | 否 |
| [chkpass](#chkpass) | chkpass 提供一个用于存储加密口令的数据类型，写入明文时按 Unix crypt 方式加密存储，比较时自动加密… | 否 |
| [pgaudit](#pgaudit) | pgaudit（pgAudit）为 PostgreSQL 提供细粒度的审计日志能力，可按会话、对象、语句类型记录读、… | 是 |
| [sslinfo](#sslinfo) | sslinfo 提供查询当前连接 SSL 信息的能力，连接是否使用 SSL、TLS 版本与加密套件、客户端证书的主题… | 否 |
| [anon](#anon) | PostgreSQL Anonymizer 数据脱敏扩展，通过脱敏规则（屏蔽、伪造、随机化、泛化等）对敏感列做静态或… | 否 |
| [pgsodium](#pgsodium) | libsodium 加密函数扩展，提供现代密码学原语（密钥管理、AEAD 加密、HMAC、密钥派生等）与透明列加密能… | 是 |
| [supabase_vault](#supabase_vault) | Supabase Vault 密钥管理扩展，基于 pgsodium 提供加密的密钥/密文存储（Vault），用于在库… | 否 |

<div id="pgcrypto"></div>

#### pgcrypto

pgcrypto 为 PostgreSQL 提供密码学函数：摘要（MD5、SHA 系列）、HMAC、密码哈希（crypt / gen_salt，支持 bcrypt）、随机字节生成，以及 PGP 对称与非对称加解密，常用于口令存储、字段加密与数据脱敏。

```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```

**使用示例**

```sql
-- 1. 创建插件
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- 2. 摘要与 HMAC
SELECT encode(digest('hello', 'sha256'), 'hex')            AS sha256_hex;
SELECT encode(hmac('hello', 'secret-key', 'sha256'), 'hex') AS hmac_hex;

-- 3. 口令存储与校验（bcrypt，推荐做法）
CREATE TABLE app_user (username text PRIMARY KEY, pwd_hash text);

INSERT INTO app_user VALUES ('alice', crypt('S3cret!', gen_salt('bf', 10)));

SELECT username,
       (pwd_hash = crypt('S3cret!', pwd_hash)) AS password_ok
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回安全与合规插件一览](#security-index)

<div id="chkpass"></div>

#### chkpass

chkpass 提供一个用于存储加密口令的数据类型：写入明文时按 Unix crypt 方式加密存储，比较时自动加密后比对。该类型属于历史遗留能力，仅在低版本提供。

```sql
CREATE EXTENSION IF NOT EXISTS chkpass;
```

**使用示例**

```sql
-- 仅用于了解历史用法，新业务不建议使用
CREATE EXTENSION IF NOT EXISTS chkpass;

CREATE TABLE accounts (
  username text PRIMARY KEY,
  password chkpass
);

-- 写入明文，存储时自动加密
INSERT INTO accounts VALUES ('alice', 'S3cret!');

-- 比较时右侧明文会被加密后比对，返回布尔值
SELECT username, password = 'S3cret!' AS matched FROM accounts WHERE username = 'alice';
SELECT username, password = 'wrong'   AS matched FROM accounts WHERE username = 'alice';
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回安全与合规插件一览](#security-index)

<div id="pgaudit"></div>

#### pgaudit

pgaudit（pgAudit）为 PostgreSQL 提供细粒度的审计日志能力，可按会话、对象、语句类型记录读、写、DDL、角色变更等操作，满足等保、金融、ISO 等合规审计要求，也便于事后故障与行为分析。

> 使用前需将 `pgaudit` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pgaudit;
```

**使用示例**

```sql
-- 1. 创建插件
CREATE EXTENSION IF NOT EXISTS pgaudit;

-- 2. 开启需要的审计类别（会话级验证，正式环境建议在参数管理中配置）
SET pgaudit.log = 'write, ddl';
SET pgaudit.log_catalog = off;

-- 3. 触发一条被审计的语句
CREATE TABLE audit_demo (id int, info text);
INSERT INTO audit_demo VALUES (1, 'hello');
UPDATE audit_demo SET info = 'world' WHERE id = 1;

-- 4. 参数级审计：记录语句参数值
SET pgaudit.log_parameter = on;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回安全与合规插件一览](#security-index)

<div id="sslinfo"></div>

#### sslinfo

sslinfo 提供查询当前连接 SSL 信息的能力：连接是否使用 SSL、TLS 版本与加密套件、客户端证书的主题与序列号等。常用于验证「业务是否真的走了加密链路」以及基于客户端证书做访问控制。

```sql
CREATE EXTENSION IF NOT EXISTS sslinfo;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS sslinfo;

-- 1. 当前连接是否加密
SELECT ssl_is_used() AS ssl_used, ssl_version() AS tls_version, ssl_cipher() AS cipher;

-- 2. 统计当前实例上使用 SSL 的会话占比
SELECT count(*) FILTER (WHERE ssl)     AS ssl_sessions,
       count(*) FILTER (WHERE NOT ssl) AS plaintext_sessions,
       count(*)                        AS total
FROM pg_stat_ssl s
JOIN pg_stat_activity a ON a.pid = s.pid;

-- 3. 客户端证书信息（启用双向认证时有效）
SELECT ssl_client_cert_present()  AS cert_present,
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回安全与合规插件一览](#security-index)

<div id="anon"></div>

#### anon

PostgreSQL Anonymizer 数据脱敏扩展：通过脱敏规则（屏蔽、伪造、随机化、泛化等）对敏感列做静态或动态脱敏，用于测试数据与受限访问。

```sql
CREATE EXTENSION IF NOT EXISTS anon;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS anon;

-- 1. 声明某列使用假名伪造
SECURITY LABEL FOR anon ON COLUMN customers.name
  IS 'MASKED WITH FUNCTION anon.fake_last_name()';

-- 2. 静态脱敏（建议在测试库执行）
SELECT anon.anonymize_table('public.customers');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回安全与合规插件一览](#security-index)

<div id="pgsodium"></div>

#### pgsodium

libsodium 加密函数扩展：提供现代密码学原语（密钥管理、AEAD 加密、HMAC、密钥派生等）与透明列加密能力。

> 使用前需将 `pgsodium` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pgsodium;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pgsodium;

-- 1. 创建密钥
SELECT id, name, status FROM pgsodium.create_key(name := 'app_key');

-- 2. 查看密钥
SELECT id, name, status FROM pgsodium.key;

-- 3. 对称加密 / 解密（AEAD）
SELECT pgsodium.crypto_aead_det_encrypt('敏感内容'::bytea, '\x00'::bytea, (SELECT id FROM pgsodium.valid_key('app_key'))) AS cipher;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回安全与合规插件一览](#security-index)

<div id="supabase_vault"></div>

#### supabase_vault

Supabase Vault 密钥管理扩展：基于 pgsodium 提供加密的密钥/密文存储（Vault），用于在库内安全保存外部服务凭据。

```sql
CREATE EXTENSION IF NOT EXISTS supabase_vault;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS supabase_vault;

-- 1. 写入一个密钥
SELECT vault.create_secret('my-api-key-value', 'third_party_api', '调用外部接口用的密钥');

-- 2. 读取（解密后可见，注意权限）
SELECT id, name, description, decrypted_secret FROM vault.decrypted_secrets;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回安全与合规插件一览](#security-index)

### 过程语言

用 Perl / Python / Tcl / JavaScript 等语言编写数据库函数，以及各语言与内置类型的转换模块。 本类共 26 个插件。

<div id="pl-languages-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [plpgsql](#plpgsql) | PL/pgSQL 是 PostgreSQL 的过程语言扩展，用于编写存储过程、函数、触发器，支持变量、条件分支、循环… | 否 |
| [plperl](#plperl) | PL/Perl 过程语言，使用 Perl 编写数据库函数与触发器（受信任模式，不能访问宿主文件系统与网络）。 | 否 |
| [plperlu](#plperlu) | PL/PerlU 过程语言（不受信任），使用 Perl 编写函数，可访问宿主文件系统与网络，属于特权语言。 | 否 |
| [plpython2u](#plpython2u) | PL/Python2U 过程语言（不受信任），使用 Python 2 编写数据库函数，可调用 Python 生态库（… | 否 |
| [plpython3u](#plpython3u) | PL/Python3U 过程语言，使用 Python 3 编写数据库函数与触发器，可直接调用 Python 库（如 … | 否 |
| [plpythonu](#plpythonu) | PL/PythonU 过程语言（不受信任），使用 Python 2 编写数据库函数（仅在提供 Python2 的版本… | 否 |
| [pltcl](#pltcl) | PL/Tcl 过程语言，使用 Tcl 编写数据库函数与触发器（受信任模式）。 | 否 |
| [pltclu](#pltclu) | PL/TclU 过程语言（不受信任），使用 Tcl 编写函数，可执行系统命令与文件操作。 | 否 |
| [plv8](#plv8) | PL/V8 过程语言，使用 JavaScript（V8 引擎）编写数据库函数与触发器，适合字符串/JSON 处理与算… | 否 |
| [plcoffee](#plcoffee) | PL/CoffeeScript 过程语言，用 CoffeeScript（编译到 JavaScript）编写数据库函数… | 否 |
| [plls](#plls) | PL/LiveScript 过程语言，用 LiveScript（编译到 JavaScript）编写数据库函数与触发器… | 否 |
| [bool_plperl](#bool_plperl) | 类型转换扩展，为 PL/Perl 提供 bool 类型与 Perl 布尔值之间的自动转换。 | 否 |
| [bool_plperlu](#bool_plperlu) | 类型转换扩展，为不受信任的 PL/PerlU 提供 bool 类型与 Perl 布尔值之间的自动转换。 | 否 |
| [hstore_plperl](#hstore_plperl) | 类型转换扩展，为 PL/Perl 提供 hstore 与 Perl 哈希之间的自动转换。 | 否 |
| [hstore_plperlu](#hstore_plperlu) | 类型转换扩展，为不受信任的 PL/PerlU 提供 hstore 与 Perl 哈希之间的自动转换。 | 否 |
| [hstore_plpython2u](#hstore_plpython2u) | 类型转换扩展，为 PL/Python2U 提供 hstore 与 Python 字典之间的自动转换（仅在提供 Pyt… | 否 |
| [hstore_plpython3u](#hstore_plpython3u) | 类型转换扩展，为 PL/Python3U 提供 hstore 与 Python 字典之间的自动转换。 | 否 |
| [hstore_plpythonu](#hstore_plpythonu) | 类型转换扩展，为 PL/PythonU 提供 hstore 与 Python 字典之间的自动转换（仅在提供 Pyth… | 否 |
| [jsonb_plperl](#jsonb_plperl) | 类型转换扩展，为 PL/Perl 提供 jsonb 与 Perl 数据结构之间的自动转换。 | 否 |
| [jsonb_plperlu](#jsonb_plperlu) | 类型转换扩展，为不受信任的 PL/PerlU 提供 jsonb 与 Perl 数据结构之间的自动转换。 | 否 |
| [jsonb_plpython2u](#jsonb_plpython2u) | 类型转换扩展，为 PL/Python2U 提供 jsonb 与 Python 对象之间的自动转换（仅在提供 Pyth… | 否 |
| [jsonb_plpython3u](#jsonb_plpython3u) | 类型转换扩展，为 PL/Python3U 提供 jsonb 与 Python 对象之间的自动转换。 | 否 |
| [jsonb_plpythonu](#jsonb_plpythonu) | 类型转换扩展，为 PL/PythonU 提供 jsonb 与 Python 对象之间的自动转换（仅在提供 Pytho… | 否 |
| [ltree_plpython2u](#ltree_plpython2u) | 类型转换扩展，为 PL/Python2U 提供 ltree 与 Python 对象之间的自动转换（仅在提供 Pyth… | 否 |
| [ltree_plpython3u](#ltree_plpython3u) | 类型转换扩展，为 PL/Python3U 提供 ltree 与 Python 对象之间的自动转换。 | 否 |
| [ltree_plpythonu](#ltree_plpythonu) | 类型转换扩展，为 PL/PythonU 提供 ltree 与 Python 对象之间的自动转换（仅在提供 Pytho… | 否 |

<div id="plpgsql"></div>

#### plpgsql

PL/pgSQL 是 PostgreSQL 的过程语言扩展，用于编写存储过程、函数、触发器，支持变量、条件分支、循环、异常处理与动态 SQL。PostgreSQL 默认自带该语言，此处列出以便统一管理。

```sql
CREATE EXTENSION IF NOT EXISTS plpgsql;
```

**使用示例**

```sql
-- 1. 创建一个带异常处理的函数
CREATE OR REPLACE FUNCTION public.transfer(
  p_from  bigint,
  p_to    bigint,
  p_amount numeric
) RETURNS text
LANGUAGE plpgsql
AS $$
DECLARE
  v_balance numeric;
BEGIN
  SELECT balance INTO v_balance FROM public.accounts WHERE id = p_from FOR UPDATE;
  IF v_balance IS NULL THEN
    RAISE EXCEPTION '账号 % 不存在', p_from;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plperl"></div>

#### plperl

PL/Perl 过程语言：使用 Perl 编写数据库函数与触发器（受信任模式，不能访问宿主文件系统与网络）。

```sql
CREATE EXTENSION IF NOT EXISTS plperl;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS plperl;

CREATE OR REPLACE FUNCTION perl_upper(s text) RETURNS text AS $$
  return uc($s);
$$ LANGUAGE plperl;

SELECT perl_upper('hello');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plperlu"></div>

#### plperlu

PL/PerlU 过程语言（不受信任）：使用 Perl 编写函数，可访问宿主文件系统与网络，属于特权语言。

```sql
CREATE EXTENSION IF NOT EXISTS plperlu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plpython2u"></div>

#### plpython2u

PL/Python2U 过程语言（不受信任）：使用 Python 2 编写数据库函数，可调用 Python 生态库（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS plpython2u;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plpython3u"></div>

#### plpython3u

PL/Python3U 过程语言：使用 Python 3 编写数据库函数与触发器，可直接调用 Python 库（如 pandas、numpy）。

```sql
CREATE EXTENSION IF NOT EXISTS plpython3u;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS plpython3u;

CREATE OR REPLACE FUNCTION py_word_count(s text) RETURNS int AS $$
  return len(s.split())
$$ LANGUAGE plpython3u;

SELECT py_word_count('postgres is great');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plpythonu"></div>

#### plpythonu

PL/PythonU 过程语言（不受信任）：使用 Python 2 编写数据库函数（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS plpythonu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="pltcl"></div>

#### pltcl

PL/Tcl 过程语言：使用 Tcl 编写数据库函数与触发器（受信任模式）。

```sql
CREATE EXTENSION IF NOT EXISTS pltcl;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pltcl;

CREATE OR REPLACE FUNCTION tcl_upper(s text) RETURNS text AS $$
  return [string toupper $s]
$$ LANGUAGE pltcl;

SELECT tcl_upper('hello');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="pltclu"></div>

#### pltclu

PL/TclU 过程语言（不受信任）：使用 Tcl 编写函数，可执行系统命令与文件操作。

```sql
CREATE EXTENSION IF NOT EXISTS pltclu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plv8"></div>

#### plv8

PL/V8 过程语言：使用 JavaScript（V8 引擎）编写数据库函数与触发器，适合字符串/JSON 处理与算法实现。

```sql
CREATE EXTENSION IF NOT EXISTS plv8;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS plv8;

CREATE OR REPLACE FUNCTION js_upper(s text) RETURNS text AS $$
  return s.toUpperCase();
$$ LANGUAGE plv8;

SELECT js_upper('hello');
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plcoffee"></div>

#### plcoffee

PL/CoffeeScript 过程语言：用 CoffeeScript（编译到 JavaScript）编写数据库函数与触发器（基于 V8）。

```sql
CREATE EXTENSION IF NOT EXISTS plcoffee;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="plls"></div>

#### plls

PL/LiveScript 过程语言：用 LiveScript（编译到 JavaScript）编写数据库函数与触发器（基于 V8）。

```sql
CREATE EXTENSION IF NOT EXISTS plls;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="bool_plperl"></div>

#### bool_plperl

类型转换扩展：为 PL/Perl 提供 bool 类型与 Perl 布尔值之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS bool_plperl;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="bool_plperlu"></div>

#### bool_plperlu

类型转换扩展：为不受信任的 PL/PerlU 提供 bool 类型与 Perl 布尔值之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS bool_plperlu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="hstore_plperl"></div>

#### hstore_plperl

类型转换扩展：为 PL/Perl 提供 hstore 与 Perl 哈希之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS hstore_plperl;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="hstore_plperlu"></div>

#### hstore_plperlu

类型转换扩展：为不受信任的 PL/PerlU 提供 hstore 与 Perl 哈希之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS hstore_plperlu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="hstore_plpython2u"></div>

#### hstore_plpython2u

类型转换扩展：为 PL/Python2U 提供 hstore 与 Python 字典之间的自动转换（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS hstore_plpython2u;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="hstore_plpython3u"></div>

#### hstore_plpython3u

类型转换扩展：为 PL/Python3U 提供 hstore 与 Python 字典之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS hstore_plpython3u;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="hstore_plpythonu"></div>

#### hstore_plpythonu

类型转换扩展：为 PL/PythonU 提供 hstore 与 Python 字典之间的自动转换（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS hstore_plpythonu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="jsonb_plperl"></div>

#### jsonb_plperl

类型转换扩展：为 PL/Perl 提供 jsonb 与 Perl 数据结构之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS jsonb_plperl;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="jsonb_plperlu"></div>

#### jsonb_plperlu

类型转换扩展：为不受信任的 PL/PerlU 提供 jsonb 与 Perl 数据结构之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS jsonb_plperlu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="jsonb_plpython2u"></div>

#### jsonb_plpython2u

类型转换扩展：为 PL/Python2U 提供 jsonb 与 Python 对象之间的自动转换（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS jsonb_plpython2u;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="jsonb_plpython3u"></div>

#### jsonb_plpython3u

类型转换扩展：为 PL/Python3U 提供 jsonb 与 Python 对象之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS jsonb_plpython3u;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="jsonb_plpythonu"></div>

#### jsonb_plpythonu

类型转换扩展：为 PL/PythonU 提供 jsonb 与 Python 对象之间的自动转换（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS jsonb_plpythonu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="ltree_plpython2u"></div>

#### ltree_plpython2u

类型转换扩展：为 PL/Python2U 提供 ltree 与 Python 对象之间的自动转换（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS ltree_plpython2u;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="ltree_plpython3u"></div>

#### ltree_plpython3u

类型转换扩展：为 PL/Python3U 提供 ltree 与 Python 对象之间的自动转换。

```sql
CREATE EXTENSION IF NOT EXISTS ltree_plpython3u;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

<div id="ltree_plpythonu"></div>

#### ltree_plpythonu

类型转换扩展：为 PL/PythonU 提供 ltree 与 Python 对象之间的自动转换（仅在提供 Python2 的版本可用）。

```sql
CREATE EXTENSION IF NOT EXISTS ltree_plpythonu;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回过程语言插件一览](#pl-languages-index)

### 其他扩展

图数据库、Oracle 兼容函数、增量物化视图、报表函数与采样方法等。 本类共 6 个插件。

<div id="other-index"></div>

| 插件 | 用途 | 是否需要预加载 |
| --- | --- | --- |
| [age](#age) | Apache AGE，在 PostgreSQL 上提供图数据库能力（openCypher 查询语言），可把关系表与图… | 是 |
| [orafce](#orafce) | Oracle 兼容函数库，提供 Oracle 风格的函数、包与类型（如 `nvl`、`decode`、`dbms_o… | 是 |
| [pg_ivm](#pg_ivm) | 增量视图维护（IVM）扩展，为物化视图建立增量刷新能力，基表数据变更时只更新受影响部分，避免全量重算。 | 是 |
| [tablefunc](#tablefunc) | tablefunc 提供若干返回表（set-returning）的函数，最常用的是 crosstab（行列转置 / … | 否 |
| [tsm_system_rows](#tsm_system_rows) | tsm_system_rows 提供 TABLESAMPLE 采样方法 SYSTEM_ROWS，按「行数」对表采样，… | 否 |
| [tsm_system_time](#tsm_system_time) | tsm_system_time 提供 TABLESAMPLE 采样方法 SYSTEM_TIME，按「时间预算」对表采… | 否 |

<div id="age"></div>

#### age

Apache AGE：在 PostgreSQL 上提供图数据库能力（openCypher 查询语言），可把关系表与图模型放在同一实例中做图遍历与路径分析。

> 使用前需将 `age` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS age;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS age;
LOAD 'age';
SET search_path = ag_catalog, "$user", public;

SELECT create_graph('demo_graph');

SELECT * FROM cypher('demo_graph', $$
  CREATE (a:Person {name: 'alice'})-[:KNOWS]->(b:Person {name: 'bob'})
  RETURN a, b
$$) AS (a agtype, b agtype);

SELECT * FROM cypher('demo_graph', $$
  MATCH (a:Person)-[:KNOWS]->(b) RETURN a.name, b.name
$$) AS (from_name agtype, to_name agtype);
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回其他扩展插件一览](#other-index)

<div id="orafce"></div>

#### orafce

Oracle 兼容函数库：提供 Oracle 风格的函数、包与类型（如 `nvl`、`decode`、`dbms_output`、`dual` 表等），用于 Oracle 迁移过渡。

> 使用前需将 `orafce` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS orafce;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS orafce;

SELECT nvl(null, 'default') AS nvl_result;
SELECT decode(1, 1, 'one', 2, 'two', 'other') AS decode_result;
SELECT * FROM dual;

DO $$
BEGIN
  dbms_output.put_line('hello from orafce');
END $$;
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回其他扩展插件一览](#other-index)

<div id="pg_ivm"></div>

#### pg_ivm

增量视图维护（IVM）扩展：为物化视图建立增量刷新能力，基表数据变更时只更新受影响部分，避免全量重算。

> 使用前需将 `pg_ivm` 加入参数 `shared_preload_libraries`（修改后需重启实例）。

```sql
CREATE EXTENSION IF NOT EXISTS pg_ivm;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS pg_ivm;

CREATE TABLE orders (id bigserial PRIMARY KEY, user_id int, amount numeric);

-- 创建增量物化视图
SELECT create_immv('order_summary', 'SELECT user_id, sum(amount) AS total, count(*) AS cnt FROM orders GROUP BY user_id');

INSERT INTO orders (user_id, amount) VALUES (1, 100), (1, 50), (2, 30);

SELECT * FROM order_summary ORDER BY user_id;   -- 已自动增量更新
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回其他扩展插件一览](#other-index)

<div id="tablefunc"></div>

#### tablefunc

tablefunc 提供若干返回表（set-returning）的函数，最常用的是 crosstab（行列转置 / 透视表），此外还有 connectby（层级遍历）与 normal_rand（随机正态分布数据生成）。

```sql
CREATE EXTENSION IF NOT EXISTS tablefunc;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS tablefunc;

CREATE TABLE sales (year int, quarter text, amount numeric);
INSERT INTO sales VALUES
  (2025, 'Q1', 100), (2025, 'Q2', 150), (2025, 'Q3', 130),
  (2026, 'Q1', 120), (2026, 'Q2', 170), (2026, 'Q3', 160);

-- 1. 透视表：年份为行，季度为列
SELECT * FROM crosstab(
  'SELECT year, quarter, amount FROM sales ORDER BY 1, 2',
  'SELECT DISTINCT quarter FROM sales ORDER BY 1'
) AS t(year int, q1 numeric, q2 numeric, q3 numeric);

-- 2. 造正态分布测试数据（便于压测）
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回其他扩展插件一览](#other-index)

<div id="tsm_system_rows"></div>

#### tsm_system_rows

tsm_system_rows 提供 TABLESAMPLE 采样方法 SYSTEM_ROWS：按「行数」对表采样，例如随机取 1000 行。适合大表上的快速探查与抽样分析（结果条数可控，但代价随采样行数线性增长）。

```sql
CREATE EXTENSION IF NOT EXISTS tsm_system_rows;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS tsm_system_rows;

-- 1. 随机采样 1000 行（行数可控）
SELECT count(*) FROM big_table TABLESAMPLE SYSTEM_ROWS(1000);

-- 2. 基于样本做统计探查
SELECT avg(amount), max(amount)
FROM big_table TABLESAMPLE SYSTEM_ROWS(10000);

-- 3. 可重复抽样（配合 REPEATABLE，同一 seed 结果一致）
SELECT count(*) FROM big_table TABLESAMPLE SYSTEM_ROWS(1000) REPEATABLE (42);

-- 4. 与抽样汇总结合（生成样本表）
CREATE TABLE big_table_sample AS
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回其他扩展插件一览](#other-index)

<div id="tsm_system_time"></div>

#### tsm_system_time

tsm_system_time 提供 TABLESAMPLE 采样方法 SYSTEM_TIME：按「时间预算」对表采样，例如最多扫描 100 毫秒。适合希望控制单次查询响应时间的大表抽样场景。

```sql
CREATE EXTENSION IF NOT EXISTS tsm_system_time;
```

**使用示例**

```sql
CREATE EXTENSION IF NOT EXISTS tsm_system_time;

-- 1. 最多扫描 100 毫秒，返回该时间内读到的行
SELECT count(*) FROM big_table TABLESAMPLE SYSTEM_TIME(100);

-- 2. 做快速统计探查（响应时间可控）
SELECT avg(amount), max(amount)
FROM big_table TABLESAMPLE SYSTEM_TIME(200);

-- 3. 可重复采样
SELECT count(*) FROM big_table TABLESAMPLE SYSTEM_TIME(100) REPEATABLE (7);

-- 4. 生成样本表
CREATE TABLE big_table_sample AS
```

完整说明（支持版本、参数与注意事项）见本节。

[↑ 返回其他扩展插件一览](#other-index)
