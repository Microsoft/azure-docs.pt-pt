---
title: Capacidades de vários modelos
description: O Microsoft Azure SQL permite-lhe trabalhar com vários modelos de dados na mesma base de dados.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: a8793b4e9bba0adb2efdc3a4831c08c9edfd14b5
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046804"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Capacidades multi-modelo da Base de Dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

As bases de dados multi-modelo permitem-lhe armazenar e trabalhar com dados representados em vários formatos de dados, tais como dados relacionais, gráficos, documentos JSON/XML, pares de valor-chave, etc.

## <a name="when-to-use-multi-model-capabilities"></a>Quando utilizar capacidades multi-modelo

A [família de produtos Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md) foi concebida para trabalhar com o modelo relacional que proporciona o melhor desempenho na maior parte dos casos para uma variedade de aplicações de propósito geral. No entanto, a família de produtos Azure SQL não se limita apenas a dados relacionais. A família de produtos Azure SQL permite-lhe utilizar uma variedade de formatos não relacionais que estão fortemente integrados no modelo relacional.
Deve considerar a utilização de capacidades multi-modelo da família de produtos Azure SQL nos seguintes casos:

- Tem algumas informações ou estruturas que são mais adequadas para os modelos NoSQL e não pretende utilizar uma base de dados noSQL separada.
- A maioria dos seus dados é adequada para um modelo relacional, e você precisa modelar algumas partes dos seus dados no estilo NoSQL.
- Você quer aproveitar a linguagem transact-SQL rica para consultar e analisar dados relacionais e noSQL, e integrá-lo com uma variedade de ferramentas e aplicações que podem usar a linguagem SQL.
- Pretende aplicar funcionalidades de base de dados como [tecnologias de memória](in-memory-oltp-overview.md) para melhorar o desempenho das suas estruturas de dados analíticas ou processadas das suas estruturas de dados NoSQL, utilizar [replicações transacionais](managed-instance/replication-transactional-overview.md) ou [réplicas legíveis](database/read-scale-out.md) para criar cópias dos seus dados no outro local e descarregar algumas cargas de trabalho analíticas da base de dados primária.

## <a name="overview"></a>Descrição geral

A família de produtos Azure SQL fornece as seguintes características multimodelo:

- [As funcionalidades](#graph-features) do gráfico permitem-lhe representar os seus dados como conjunto de nós e bordas, e utilizar consultas padrão transact-SQL melhoradas com o operador de `MATCH` gráficos para consultar os dados do gráfico.
- As [funcionalidades jSON](#json-features) permitem-lhe colocar documentos JSON nas tabelas, transformar dados relacionais em documentos JSON e vice-versa. Pode utilizar a linguagem transact-SQL padrão melhorada com funções JSON para analisar documentos, e utilizar índices não agrupados, índices de lojas de colunas ou tabelas otimizadas pela memória, para otimizar as suas consultas.
- [As características espaciais](#spatial-features) permitem armazenar dados geográficos e geométricos, indexá-los usando os índices espaciais e recuperar os dados usando consultas espaciais.
- [As funcionalidades XML](#xml-features) permitem-lhe armazenar e indexar dados XML na sua base de dados e utilizar operações nativas de XQuery/XPath para trabalhar com dados XML. A família de produtos Azure SQL tem um motor de consulta XML integrado especializado que processa dados XML.
- [Os pares de valor-chave](#key-value-pairs) não são explicitamente suportados como características especiais, uma vez que os pares de valor-chave podem ser modelados de forma nativa como tabelas de duas colunas.

  > [!Note]
  > Pode utilizar expressão JSON Path, expressões XQuery/XPath, funções espaciais e expressões de consulta de gráficos na mesma consulta Transact-SQL para aceder a quaisquer dados que tenha armazenado na base de dados. Além disso, qualquer ferramenta ou linguagem de programação que possa executar consultas Transact-SQL, também pode usar essa interface de consulta para aceder a dados multi-modelos. Esta é a diferença fundamental em comparação com as bases de dados multi-modelos, como o [Azure Cosmos DB,](/azure/cosmos-db/) que fornece API especializado para diferentes modelos de dados.

Nas seguintes secções, pode aprender sobre as mais importantes capacidades multi-modelo da família de produtos Azure SQL.

## <a name="graph-features"></a>Funcionalidades de gráficos

A família de produtos Azure SQL oferece capacidades de base de dados de gráficos para modelar muitas a muitas relações na base de dados. Um gráfico é uma coleção de nós (ou vértices) e bordas (ou relacionamentos). Um nó representa uma entidade (por exemplo, uma pessoa ou uma organização) e uma borda representa uma relação entre os dois nós que liga (por exemplo, gostos ou amigos). Aqui estão algumas funcionalidades que tornam uma base de dados de gráficos única:

- As bordas ou relacionamentos são entidades de primeira classe numa Base de Dados de Gráficos e podem ter atributos ou propriedades associadas a eles.
- Uma única borda pode ligar flexionavelmente vários nódosos numa base de dados de gráficos.
- Você pode expressar padrão combinando e consultas de navegação multi-hop facilmente.
- Pode expressar facilmente o fecho transitório e consultas polimórficas.

As [relações de gráficoe as capacidades](/sql/relational-databases/graphs/sql-graph-overview) de consulta de gráficos estão integradas na Transact-SQL e recebem os benefícios de usar o motor de base de dados SQL Server como o sistema de gestão de bases de dados fundacional.

### <a name="when-to-use-a-graph-capability"></a>Quando usar uma capacidade de gráfico

Não há nada que uma base de dados de gráficos possa alcançar, que não possa ser alcançada através de uma base de dados relacional. No entanto, uma base de dados de gráficos pode facilitar a expressão de determinadas consultas. A sua decisão de escolher um em vez do outro pode basear-se nos seguintes fatores:

- Dados hierárquicos modelo onde um nó pode ter múltiplos pais, por isso a Hierarquia não pode ser usada
- Modelo tem A sua aplicação tem relações complexas de muitas a muitas; à medida que a aplicação evolui, novas relações são adicionadas.
- Precisa analisar dados e relacionamentos interligados.

## <a name="json-features"></a>Características jSON

A família de produtos Azure SQL permite-lhe analisar e consultar dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exportar os seus dados relacionais como texto JSON.

JSON é um formato de dados popular usado para a troca de dados em aplicações modernas web e móveis. JSON também é usado para armazenar dados semi-estruturados em ficheiros de registo ou em bases de dados NoSQL como [O Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Muitos serviços web rest devolvem resultados formatados como texto JSON ou aceitam dados formatados como JSON. A maioria dos serviços Azure, como [Azure Cognitive Search,](https://azure.microsoft.com/services/search/) [Azure Storage,](https://azure.microsoft.com/services/storage/)e [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) têm pontos finais REST que devolvem ou consomem JSON.


A família de produtos Azure SQL permite-lhe trabalhar facilmente com os dados da JSON e integrar a sua base de dados com serviços modernos, e fornece as seguintes funções para trabalhar com dados da JSON:

![Funções JSON](./media/multi-model-features/image_1.png)

Se tiver texto JSON, pode extrair dados da JSON ou verificar se a JSON está devidamente formatada utilizando as funções incorporadas [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)e [ISJSON](/sql/t-sql/functions/isjson-transact-sql). A função [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) permite atualizar o valor dentro do texto JSON. Para consultas e análises mais avançadas, a função [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) pode transformar uma série de objetos JSON num conjunto de linhas. Qualquer consulta SQL pode ser executada no conjunto de resultados devolvidos. Finalmente, existe uma cláusula [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) que permite formato de dados armazenados nas suas tabelas relacionais como texto JSON.

Para mais informações, consulte [Como trabalhar com os dados da JSON](database/json-features.md).
[JSON](/sql/relational-databases/json/json-data-sql-server) é uma função de motor de base de dados SQL Server.

### <a name="when-to-use-a-json-capability"></a>Quando usar uma capacidade JSON

Os modelos documentais podem ser utilizados em vez dos modelos relacionais em alguns cenários específicos:

- A alta normalização do esquema não traz benefícios significativos porque acede a todos os campos de objetos ao mesmo tempo, ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade das suas consultas devido ao grande número de tabelas que precisa de aderir para obter os dados.
- Está a trabalhar com as aplicações que utilizam os documentos JSON de forma nativa são modelos de comunicação ou dados, e não pretende introduzir camadas adicionais que transformem dados relacionais para a JSON e vice-versa.
- É necessário simplificar o seu modelo de dados desnormalizando tabelas infantis ou padrões de valor de entidade.objeto.
- É necessário carregar ou exportar dados armazenados em formato JSON sem alguma ferramenta adicional que analise os dados.

## <a name="spatial-features"></a>Características espaciais

Os dados espaciais representam informações sobre a localização física e forma de objetos geométricos. Estes objetos podem ser locais de ponto ou objetos mais complexos, tais como países/regiões, estradas ou lagos.

A família de produtos Azure SQL permite-lhe analisar e consultar dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exportar os seus dados relacionais como texto JSON.


 Os dois suportavam dois tipos de dados espaciais: 

- O tipo de geometria representa dados num sistema de coordenadas Euclideanos (plano).
- O tipo de geografia representa dados num sistema de coordenadas terra redonda.

Existem vários objetos Espaciais que podem ser usados na família de produtos Azure SQL permitem-lhe analisar e consultar dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exportar os seus dados relacionais como texto JSON.
como [Point,](/sql/relational-databases/spatial/point) [LineString,](/sql/relational-databases/spatial/linestring) [Polygon,](/sql/relational-databases/spatial/polygon)e assim por diante.

A família de produtos Azure SQL também fornece [índices espaciais especializados](/sql/relational-databases/spatial/spatial-indexes-overview) que podem ser usados para melhorar o desempenho das suas consultas espaciais.

[O suporte espacial](/sql/relational-databases/spatial/spatial-data-sql-server) é uma função de motor de base de dados SQL Server.

## <a name="xml-features"></a>Características xml

O motor de base de dados SQL Server fornece uma plataforma poderosa para o desenvolvimento de aplicações ricas para gestão de dados semi-estruturados. O suporte para XML está integrado em todos os componentes do motor de base de dados e inclui o seguinte:

- O tipo de dados xml. Os valores xml podem ser armazenados de forma nativa numa coluna do tipo xml que pode ser dactilografada de acordo com uma coleção de esquemas XML, ou deixados sem tipo. Pode indexar a coluna XML.
- A capacidade de especificar uma consulta XQuery contra os dados XML armazenados em colunas e variáveis do tipo xml. As funcionalidades XQuery podem ser utilizadas em qualquer consulta Transact-SQL que aceda a qualquer modelo de dados que utilize na sua base de dados.
- Indexe automaticamente todos os elementos em documentos XML utilizando [o índice XML primário](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) ou especifique os caminhos exatos que devem ser indexados utilizando o índice Secundário De [XML](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET que permite o carregamento a granel de dados XML.
- Transforme dados relacionais para o formato XML.

[XML](/sql/relational-databases/xml/xml-data-sql-server) é uma função de motor de base de dados SQL Server.

### <a name="when-to-use-an-xml-capability"></a>Quando usar uma capacidade XML

Os modelos documentais podem ser utilizados em vez dos modelos relacionais em alguns cenários específicos:

- A alta normalização do esquema não traz benefícios significativos porque acede a todos os campos de objetos ao mesmo tempo, ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade das suas consultas devido ao grande número de tabelas que precisa de aderir para obter os dados.
- Está a trabalhar com as aplicações que utilizam os documentos XML de forma nativa são modelos de comunicação ou dados, e não pretende introduzir camadas adicionais que transformem dados relacionais em XML e vice-versa.
- É necessário simplificar o seu modelo de dados desnormalizando tabelas infantis ou padrões de valor de entidade.objeto.
- É necessário carregar ou exportar dados armazenados em formato XML sem alguma ferramenta adicional que analise os dados.

## <a name="key-value-pairs"></a>Pares valor-chave

A família de produtos Azure SQL não tem tipos ou estruturas especializadas que suportem pares de valor-chave, uma vez que as estruturas de valor-chave podem ser representadas de forma nativa como tabelas relacionais padrão:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Pode personalizar esta estrutura de valor-chave para se adaptar às suas necessidades sem quaisquer restrições. Como exemplo, o valor pode ser o documento XML em vez de `nvarchar(max)` tipo, se o valor for documento JSON, pode colocar `CHECK` constrangimentos que verificam a validade do conteúdo da JSON. Pode colocar qualquer número de valores relacionados com uma chave nas colunas adicionais, adicionar colunas e índices computacionados para simplificar e otimizar o acesso de dados, definir a tabela como uma tabela apenas de memória/schema otimizada para obter um melhor desempenho, etc.

Veja como a BWin está a [usar o OLTP in-Memory para alcançar um desempenho e escala sem precedentes](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) para a sua ASP.NET solução de cache que alcançou 1.200.000 lotes por segundo, como um exemplo de como o modelo relacional pode ser eficazmente usado como solução de par de valor-chave na prática.

## <a name="next-steps"></a>Próximos passos

As capacidades multi-modelo na família de produtos Azure SQL são também as principais funcionalidades do motor de base de dados SQL Server que são partilhadas entre a família de produtos Azure SQL. Para saber mais detalhes sobre estas funcionalidades, visite as páginas de documentação da base de dados Relacional SQL:

- [Processamento de gráficos](/sql/relational-databases/graphs/sql-graph-overview)
- [Dados JSON](/sql/relational-databases/json/json-data-sql-server)
- [Apoio espacial](/sql/relational-databases/spatial/spatial-data-sql-server)
- [Dados XML](/sql/relational-databases/xml/xml-data-sql-server)
