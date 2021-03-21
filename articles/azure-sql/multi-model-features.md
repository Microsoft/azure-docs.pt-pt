---
title: Capacidades de vários modelos
description: O Microsoft Azure SQL permite-lhe trabalhar com vários modelos de dados na mesma base de dados.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: b16a2fc9f107a8420fb7d05667807a869fa3e00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172762"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Capacidades multi-modelo da Base de Dados Azure SQL & SQL Gestd Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Bases de dados multi-modelos permitem armazenar e trabalhar com dados representados em vários formatos de dados, tais como dados relacionais, gráficos, documentos JSON/XML, pares de valores-chave, e assim por diante.

## <a name="when-to-use-multi-model-capabilities"></a>Quando utilizar capacidades multi-modelo

A [família de produtos Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md) é projetada para trabalhar com o modelo relacional que proporciona o melhor desempenho na maioria dos casos para uma variedade de aplicações de fins gerais. No entanto, a família de produtos Azure SQL não se limita apenas aos dados relacionais. A família de produtos Azure SQL permite-lhe utilizar uma variedade de formatos não relacionais que estão fortemente integrados no modelo relacional.
Deve considerar a utilização de capacidades multi-modelo da família de produtos Azure SQL nos seguintes casos:

- Tem algumas informações ou estruturas que se adequam melhor aos modelos NoSQL e não quer utilizar a base de dados NoSQL separada.
- A maioria dos seus dados é adequada para um modelo relacional, e precisa de modelar algumas partes dos seus dados no estilo NoSQL.
- Pretende aproveitar a rica linguagem Transact-SQL para consultar e analisar os dados relacionais e noSQL, e integrá-los com uma variedade de ferramentas e aplicações que podem usar a linguagem SQL.
- Pretende aplicar funcionalidades de base de dados, tais como [tecnologias de memória,](in-memory-oltp-overview.md) para melhorar o desempenho da sua analítica ou processamento das suas estruturas de dados NoSQL, utilizar [replicações transacionais](managed-instance/replication-transactional-overview.md) ou [réplicas legíveis](database/read-scale-out.md) para criar cópia dos seus dados no outro local e descarregar algumas cargas de trabalho analíticas da base de dados primária.

## <a name="overview"></a>Descrição geral

A família de produtos Azure SQL fornece as seguintes características multi-modelo:

- [As funcionalidades de gráfico](#graph-features) permitem-lhe representar os seus dados como conjunto de nós e bordas, e utilizar consultas padrão Transact-SQL melhoradas com o operador de `MATCH` gráficos para consultar os dados do gráfico.
- [As funcionalidades JSON](#json-features) permitem-lhe colocar documentos JSON em tabelas, transformar dados relacionais em documentos JSON e vice-versa. Pode utilizar a linguagem padrão Transact-SQL melhorada com funções JSON para analisar documentos e utilizar índices não agrupados, índices de loja de colunas ou tabelas otimizadas pela memória, para otimizar as suas consultas.
- [As características espaciais](#spatial-features) permitem armazenar dados geográficos e geométricos, indexá-los utilizando os índices espaciais e recuperar os dados usando consultas espaciais.
- [As funcionalidades XML](#xml-features) permitem armazenar e indexar dados XML na sua base de dados e utilizar operações nativas de XQuery/XPath para trabalhar com dados XML. A família de produtos Azure SQL tem um motor de consulta XML incorporado especializado que processa dados XML.
- [Os pares de valor-chave](#key-value-pairs) não são explicitamente suportados como características especiais, uma vez que os pares de valor-chave podem ser modelados de forma nativa como tabelas de duas colunas.

  > [!Note]
  > Pode utilizar a expressão JSON Path, expressões XQuery/XPath, funções espaciais e expressões de consulta de gráficos na mesma consulta Transact-SQL para aceder a quaisquer dados que tenha armazenado na base de dados. Além disso, qualquer ferramenta ou linguagem de programação que possa executar consultas Transact-SQL, também pode usar essa interface de consulta para aceder a dados multi-modelo. Esta é a diferença chave em comparação com as bases de dados multi-modelos, como [a Azure Cosmos DB,](../cosmos-db/index.yml) que fornece API especializada para diferentes modelos de dados.

Nas secções seguintes, pode aprender sobre as mais importantes capacidades multi-modelo da família de produtos Azure SQL.

## <a name="graph-features"></a>Funcionalidades de gráficos

A família de produtos Azure SQL oferece capacidades de base de dados de gráficos para modelar muitas para muitas relações na base de dados. Um gráfico é uma coleção de nós (ou vértices) e bordas (ou relacionamentos). Um nó representa uma entidade (por exemplo, uma pessoa ou uma organização) e uma borda representa uma relação entre os dois nós que liga (por exemplo, gostos ou amigos). Aqui estão algumas funcionalidades que tornam uma base de dados de gráficos única:

- Bordas ou relacionamentos são entidades de primeira classe numa Base de Dados de Gráficos e podem ter atributos ou propriedades associadas a eles.
- Uma única borda pode ligar flexívelmente vários nós numa base de dados de gráficos.
- Você pode expressar facilmente consultas de correspondência de padrões e navegação multi-hop.
- Pode expressar facilmente o encerramento transitório e as consultas polimórficas.

As [relações de gráfico e as capacidades de consulta de gráficos](/sql/relational-databases/graphs/sql-graph-overview) estão integradas na Transact-SQL e recebem os benefícios de utilizar o motor de base de dados sql Server como o sistema de gestão de bases de dados fundacional.

### <a name="when-to-use-a-graph-capability"></a>Quando usar uma capacidade de gráfico

Não há nada que uma base de dados de gráficos possa alcançar, o que não pode ser alcançado através de uma base de dados relacional. No entanto, uma base de dados de gráficos pode facilitar a expressão de determinadas consultas. A sua decisão de escolher uma em vez da outra pode basear-se em seguintes fatores:

- Dados hierárquicos modelo onde um nó pode ter múltiplos pais, por isso a Hierarquia Não pode ser usada
- Modelo tem A sua aplicação tem complexos relacionamentos de muitos a muitos; à medida que a aplicação evolui, novas relações são adicionadas.
- É preciso analisar dados e relacionamentos interligados.

## <a name="json-features"></a>JSON apresenta

A família de produtos Azure SQL permite analisar e consultar dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exportar os seus dados relacionais como texto JSON.

JSON é um formato de dados popular usado para o intercâmbio de dados em aplicações web e móveis modernas. O JSON também é utilizado para armazenar dados semi-estruturados em ficheiros de registo ou em bases de dados NoSQL como [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Muitos serviços web REST devolvem resultados formatados como texto JSON ou aceitam dados formatados como JSON. A maioria dos serviços Azure, como [Azure Cognitive Search,](https://azure.microsoft.com/services/search/) [Azure Storage,](https://azure.microsoft.com/services/storage/)e [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) têm pontos finais REST que devolvem ou consomem JSON.


A família de produtos Azure SQL permite-lhe trabalhar facilmente com os dados da JSON e integrar a sua base de dados com serviços modernos, e fornece as seguintes funções para trabalhar com dados JSON:

![Funções JSON](./media/multi-model-features/image_1.png)

Se tiver texto JSON, pode extrair dados da JSON ou verificar se o JSON está devidamente formatado utilizando as funções incorporadas [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)e [ISJSON](/sql/t-sql/functions/isjson-transact-sql). A [função JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) permite-lhe atualizar o valor dentro do texto JSON. Para consultas e análises mais avançadas, a função [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) pode transformar uma matriz de objetos JSON num conjunto de linhas. Qualquer consulta SQL pode ser executada no conjunto de resultados devolvidos. Finalmente, existe uma cláusula [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) que permite-lhe formatar dados armazenados nas suas tabelas relacionais como texto JSON.

Para mais informações, consulte [Como trabalhar com os dados da JSON.](database/json-features.md)
[JSON](/sql/relational-databases/json/json-data-sql-server) é uma função de motor de base de dados do SQL Server.

### <a name="when-to-use-a-json-capability"></a>Quando usar uma capacidade JSON

Os modelos documentais podem ser utilizados em vez dos modelos relacionais em alguns cenários específicos:

- A alta normalização do esquema não traz benefícios significativos porque acede a todos os campos dos objetos ao mesmo tempo, ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade das suas consultas devido ao grande número de tabelas que precisa de aderir para obter os dados.
- Está a trabalhar com as aplicações que utilizam documentos JSON de forma nativa são modelos de comunicação ou de dados, e não quer introduzir camadas adicionais que transformem dados relacionais para JSON e vice-versa.
- É necessário simplificar o seu modelo de dados des normalizando as tabelas de crianças ou os padrões de Valor-Objecto de Entidade.
- É necessário carregar ou exportar dados armazenados em formato JSON sem uma ferramenta adicional que analise os dados.

## <a name="spatial-features"></a>Características espaciais

Os dados espaciais representam informações sobre a localização física e a forma dos objetos geométricos. Estes objetos podem ser locais de ponto ou objetos mais complexos, tais como países/regiões, estradas ou lagos.

 Os dois tipos de dados espaciais suportados: 

- O tipo de geometria representa dados num sistema de coordenadas euclidídeos (plano).
- O tipo de geografia representa dados num sistema de coordenadas de terra redonda.

Existem vários objetos espaciais que podem ser usados na família de produtos Azure SQL, permitindo-lhe analisar e consultar dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exportar os seus dados relacionais como texto JSON.
tais como [Point](/sql/relational-databases/spatial/point), [LineString,](/sql/relational-databases/spatial/linestring) [Polygon,](/sql/relational-databases/spatial/polygon)e assim por diante.

A família de produtos Azure SQL também fornece [índices espaciais especializados](/sql/relational-databases/spatial/spatial-indexes-overview) que podem ser usados para melhorar o desempenho das suas consultas espaciais.

[Suporte espacial](/sql/relational-databases/spatial/spatial-data-sql-server) é uma função de motor de base de dados do sql server.

## <a name="xml-features"></a>Recursos XML

O motor de base de dados SQL Server fornece uma plataforma poderosa para o desenvolvimento de aplicações ricas para gestão de dados semi-estruturadas. O suporte para o XML está integrado em todos os componentes do motor da base de dados e inclui o seguinte:

- O tipo de dados xml. Os valores XML podem ser armazenados de forma nativa numa coluna do tipo de dados xml que pode ser digitada de acordo com uma coleção de esquemas XML, ou deixadas sem tipologia. Pode indexar a coluna XML.
- A capacidade de especificar uma consulta XQuery contra dados XML armazenados em colunas e variáveis do tipo xml. As funcionalidades XQuery podem ser utilizadas em qualquer consulta Transact-SQL que aceda a qualquer modelo de dados que utilize na sua base de dados.
- Indexar automaticamente todos os elementos em documentos XML utilizando o [índice XML primário](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) ou especificar os caminhos exatos que devem ser indexados usando [o índice secundário de XML](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET que permite o carregamento em massa de dados XML.
- Transforme dados relacionais para o formato XML.

[XML](/sql/relational-databases/xml/xml-data-sql-server) é uma função de motor de base de dados do sql server.

### <a name="when-to-use-an-xml-capability"></a>Quando usar uma capacidade XML

Os modelos documentais podem ser utilizados em vez dos modelos relacionais em alguns cenários específicos:

- A alta normalização do esquema não traz benefícios significativos porque acede a todos os campos dos objetos ao mesmo tempo, ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade das suas consultas devido ao grande número de tabelas que precisa de aderir para obter os dados.
- Está a trabalhar com as aplicações que utilizam documentos XML de forma nativa são modelos de comunicação ou de dados, e não quer introduzir camadas adicionais que transformem dados relacionais para XML e vice-versa.
- É necessário simplificar o seu modelo de dados des normalizando as tabelas de crianças ou os padrões de Valor-Objecto de Entidade.
- É necessário carregar ou exportar dados armazenados em formato XML sem uma ferramenta adicional que analise os dados.

## <a name="key-value-pairs"></a>Pares chave-valor

A família de produtos Azure SQL não tem tipos ou estruturas especializados que suportem pares de valor-chave, uma vez que as estruturas de valor-chave podem ser representadas de forma nativa como tabelas relacionais padrão:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Pode personalizar esta estrutura de valor-chave para se adaptar às suas necessidades sem quaisquer constrangimentos. Como exemplo, o valor pode ser documento XML em vez de `nvarchar(max)` tipo, se o valor for documento JSON, pode colocar `CHECK` restrições que verifiquem a validade do conteúdo JSON. Pode colocar qualquer número de valores relacionados com uma chave nas colunas adicionais, adicionar colunas e índices computados para simplificar e otimizar o acesso aos dados, definir a tabela como tabela apenas de memória/otimizada para obter um melhor desempenho, etc.

Veja [como a BWin está a usar In-Memory OLTP para alcançar um desempenho e escala sem precedentes](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale) para a sua solução de caching ASP.NET que alcançou 1.200.000 lotes por segundo, como um exemplo de como o modelo relacional pode ser efetivamente usado como solução de par de valor-chave na prática.

## <a name="next-steps"></a>Passos seguintes

As capacidades multi-modelo na família de produtos Azure SQL são também as principais funcionalidades do motor de base de dados SQL Server que são partilhadas entre a família de produtos Azure SQL. Para obter mais detalhes sobre estas funcionalidades, visite as páginas de documentação da base de dados relacional SQL:

- [Processamento de gráficos](/sql/relational-databases/graphs/sql-graph-overview)
- [Dados JSON](/sql/relational-databases/json/json-data-sql-server)
- [Apoio espacial](/sql/relational-databases/spatial/spatial-data-sql-server)
- [Dados XML](/sql/relational-databases/xml/xml-data-sql-server)
