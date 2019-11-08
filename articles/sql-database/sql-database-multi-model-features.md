---
title: Capacidades de vários modelos
description: O banco de dados SQL do Azure permite que você trabalhe com vários modelos de data no mesmo banco de dado.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802818"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Funcionalidades de vários modelos do banco de dados SQL do Azure

Os bancos de dados multimodelo permitem armazenar e trabalhar com dados representados em vários formatos, como dados relacionais, grafos, documentos JSON/XML, pares chave-valor, etc.

## <a name="when-to-use-multi-model-capabilities"></a>Quando usar os recursos de vários modelos

O banco de dados SQL do Azure foi projetado para trabalhar com o modelo relacional que fornece o melhor desempenho na maioria dos casos para uma variedade de aplicativos de uso geral. No entanto, o banco de dados SQL do Azure não está limitado somente a data relacional. O banco de dados SQL do Azure permite que você use uma variedade de formatos não relacionais que são totalmente integrados ao modelo relacional.
Você deve considerar o uso de recursos de vários modelos do banco de dados SQL do Azure nos seguintes casos:
- Você tem algumas informações ou estruturas que são mais adequadas para modelos NoSQL e não deseja usar um banco de dados NoSQL separado.
- A maioria dos seus dados é adequada para o modelo relacional e você precisa modelar algumas partes de seus dados no estilo NoSQL.
- Você deseja aproveitar a rica linguagem Transact-SQL para consultar e analisar dados relacionais e NoSQL e integrá-los a uma variedade de ferramentas e aplicativos que podem usar a linguagem SQL.
- Você deseja aplicar recursos de banco de dados, como [tecnologias na memória](sql-database-in-memory.md) , para melhorar o desempenho de sua análise ou processamento de suas estruturas de dado NoSQL, usar a [replicação transacional](sql-database-managed-instance-transactional-replication.md) ou [réplicas legíveis](sql-database-read-scale-out.md) para criar uma cópia dos dados em o outro local e descarregar algumas cargas de trabalho analíticas do banco de dados primário.

## <a name="overview"></a>Descrição geral

O SQL do Azure fornece os seguintes recursos de vários modelos:
- Os [recursos do Graph](#graph-features) permitem que você represente seus dados como conjunto de nós e bordas e use consultas TRANSACT-SQL padrão aprimoradas com o operador de `MATCH` do grafo para consultar os dados do grafo.
- Os [recursos JSON](#json-features) permitem que você coloque documentos JSON em tabelas, transforme dados relacionais em documentos JSON e vice-versa. Você pode usar a linguagem Transact-SQL padrão aprimorada com funções JSON para analisar documentos e usar índices não clusterizados, índices columnstore ou tabelas com otimização de memória, para otimizar suas consultas.
- Os [recursos espaciais](#spatial-features) permitem armazenar dados geométricos e geométricas, indexá-los usando os índices espaciais e recuperar os dados usando consultas espaciais.
- Os [recursos XML](#xml-features) permitem que você armazene e indexe dados XML em seu banco de dado e use operações XQuery/XPath nativas para trabalhar com dados XML. O banco de dados SQL do Azure tem um mecanismo de consulta XML interno especializado que processa os dados XML.
- Os [pares chave-valor](#key-value-pairs) não têm suporte explícito como recursos especiais, uma vez que os pares chave-valor podem ser modelados nativamente como tabelas de duas colunas.

  > [!Note]
  > Você pode usar a expressão de caminho JSON, expressões XQuery/XPath, funções espaciais e expressões de consulta de grafo na mesma consulta Transact-SQL para acessar os dados que você armazenou no banco de dado. Além disso, qualquer ferramenta ou linguagem de programação que possa executar consultas Transact-SQL também pode usar essa interface de consulta para acessar dados de vários modelos. Essa é a principal diferença em comparação com os bancos de dados de vários modelos, como [Azure Cosmos DB](/azure/cosmos-db/) que fornece uma API especializada para modelos de data diferentes.

Nas seções a seguir, você pode aprender sobre os recursos de vários modelos mais importantes do banco de dados SQL do Azure.

## <a name="graph-features"></a>Funcionalidades de gráficos

O banco de dados SQL do Azure oferece recursos de banco de dados de grafo para modelar relações muitos para muitos no banco de dados. Um grafo é uma coleção de nós (ou vértices) e bordas (ou relações). Um nó representa uma entidade (por exemplo, uma pessoa ou organização) e uma borda representa uma relação entre os dois nós que ele conecta (por exemplo, gosta ou amigos). Aqui estão alguns recursos que tornam um banco de dados de grafo exclusivo:
- As bordas ou relações são entidades de primeira classe em um banco de dados de grafo e podem ter atributos ou propriedades associadas a elas.
- Uma única borda pode conectar vários nós de maneira flexível em um banco de dados de grafo.
- Você pode expressar facilmente a correspondência de padrões e as consultas de navegação de vários saltos.
- Você pode expressar o fechamento transitivo e as consultas polimórficas facilmente.

As relações de grafo e os recursos de consulta de grafo são integrados ao Transact-SQL e recebem os benefícios de usar SQL Server como o sistema de gerenciamento de banco de dados básico.
O [processamento de grafo](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) é o principal recurso de Mecanismo de Banco de Dados de SQL Server, para que você possa encontrar mais informações sobre o processamento de grafo lá.

### <a name="when-to-use-a-graph-capability"></a>Quando usar um recurso de grafo

Não há nada que um banco de dados de grafo possa obter, o que não pode ser obtido usando um banco de dados relacional. No entanto, um banco de dados de grafo pode tornar mais fácil expressar determinadas consultas. Sua decisão de escolher um sobre o outro pode ser baseada nos seguintes fatores:

- Modelar dados hierárquicos em que um nó pode ter vários pais, portanto, hierarchyid não pode ser usado
- O modelo tem seu aplicativo tem relações complexas de muitos para muitos; à medida que o aplicativo evolui, novas relações são adicionadas.
- Você precisa analisar os dados interconectados e as relações.

## <a name="json-features"></a>Recursos JSON

O banco de dados SQL do Azure permite que você analise e consulte as consultas representadas no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exporte os dados relacionais como texto JSON.

JSON é um formato de dados popular usado para trocar dados em aplicativos Web e móveis modernos. O JSON também é usado para armazenar dados semiestruturados em arquivos de log ou em dados NoSQL, como [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Muitos serviços Web REST retornam resultados formatados como texto JSON ou aceitam dados formatados como JSON. A maioria dos serviços do Azure, como o [azure pesquisa cognitiva](https://azure.microsoft.com/services/search/), o [armazenamento do Azure](https://azure.microsoft.com/services/storage/)e [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) têm pontos de extremidade REST que retornam ou consomem JSON.

O banco de dados SQL do Azure permite que você trabalhe facilmente com o JSON e integre seu banco de dado aos serviços modernos. O banco de dados SQL do Azure fornece as seguintes funções para trabalhar com o JSON Data:

![Funções JSON](./media/sql-database-json-features/image_1.png)

Se você tiver texto JSON, poderá extrair dados do JSON ou verificar se o JSON está formatado corretamente usando as funções internas [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)e [isjson](https://msdn.microsoft.com/library/dn921896.aspx). A função [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) permite que você atualize o valor dentro do texto JSON. Para consulta e análise mais avançadas, a função [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) pode transformar uma matriz de objetos JSON em um conjunto de linhas. Qualquer consulta SQL pode ser executada no conjunto de resultados retornado. Por fim, há uma cláusula [for JSON](https://msdn.microsoft.com/library/dn921882.aspx) que permite que você formate os dados armazenados em suas tabelas relacionais como texto JSON.

Para obter mais informações, confira [como trabalhar com dados JSON no Azure SQL Database](sql-database-json-features.md).
O [JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) é o recurso de Mecanismo de Banco de Dados de SQL Server principal, para que você possa encontrar mais informações sobre o recurso JSON lá.

### <a name="when-to-use-a-json-capability"></a>Quando usar um recurso JSON

Os modelos de documento podem ser usados em vez dos modelos relacionais em alguns cenários específicos:
- A normalização alta do esquema não traz benefícios significativos porque você acessa todos os campos de objetos de uma vez ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade de suas consultas devido ao grande número de tabelas que você precisa unir para obter os dados.
- Você está trabalhando com os aplicativos que usam nativamente documentos JSON são modelos de dados ou de comunicação, e você não quer introduzir camadas adicionais que transformam dados relacionais para JSON e vice-versa.
- Você precisa simplificar seu modelo de dados desnormalizando tabelas filho ou padrões de valor de objeto de entidade.
- Você precisa carregar ou exportar dados armazenados no formato JSON sem alguma ferramenta adicional que analise os dados.

## <a name="spatial-features"></a>Recursos espaciais

Dados espaciais representam informações sobre o local físico e a forma de objetos geométricos. Esses objetos podem ser locais de ponto ou objetos mais complexos, como países/regiões, estradas ou lagos.

O banco de dados SQL do Azure dá suporte a dois tipos de dado espacial: o tipo de dados geometry e o tipo de dados geography.
- O tipo geometry representa dados em um sistema de coordenadas euclidiana (simples).
- O tipo de geografia representa dados em um sistema de coordenadas de terra redonda.

Há uma série de objetos espaciais que podem ser usados no banco de dados SQL do Azure, como [ponto](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [polígono](https://docs.microsoft.com/sql/relational-databases/spatial/polygon), etc.

O banco de dados SQL do Azure também fornece [índices espaciais](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) especializados que podem ser usados para melhorar o desempenho de suas consultas espaciais.

O [suporte espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) é core SQL Server mecanismo de banco de dados recurso, para que você possa encontrar mais informações sobre o recurso espacial lá.

## <a name="xml-features"></a>Recursos XML

O SQL Server fornece uma plataforma poderosa para o desenvolvimento de aplicativos avançados para o gerenciamento de dados semiestruturado. O suporte para XML é integrado a todos os componentes no SQL Server e inclui o seguinte:

- O tipo de dados XML. Os valores XML podem ser armazenados nativamente em uma coluna de tipo de dados XML que pode ser digitada de acordo com uma coleção de esquemas XML ou deixada sem tipo. Você pode indexar a coluna XML.
- A capacidade de especificar uma consulta XQuery em relação aos dados XML armazenados em colunas e variáveis do tipo XML. As funcionalidades do XQuery podem ser usadas em qualquer consulta Transact-SQL que acesse qualquer modelo de dados que você usa em seu banco de dado.
- Indexe automaticamente todos os elementos em documentos XML usando o [índice XML primário](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) ou especifique os caminhos exatos que devem ser indexados usando o [índice XML secundário](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET que permite o carregamento em massa de dados XML.
- Transformar dados relacionais em formato XML.

O [XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) é o recurso de Mecanismo de Banco de Dados de SQL Server principal, para que você possa encontrar mais informações sobre o recurso XML lá.

### <a name="when-to-use-an-xml-capability"></a>Quando usar um recurso XML

Os modelos de documento podem ser usados em vez dos modelos relacionais em alguns cenários específicos:
- A normalização alta do esquema não traz benefícios significativos porque você acessa todos os campos de objetos de uma vez ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade de suas consultas devido ao grande número de tabelas que você precisa unir para obter os dados.
- Você está trabalhando com os aplicativos que usam nativamente documentos XML são modelos de dados ou de comunicação, e você não quer introduzir camadas adicionais que transformam dados relacionais em XML e vice-versa.
- Você precisa simplificar seu modelo de dados desnormalizando tabelas filho ou padrões de valor de objeto de entidade.
- Você precisa carregar ou exportar dados armazenados em formato XML sem alguma ferramenta adicional que analise os dados.

## <a name="key-value-pairs"></a>Pares valor-chave

O banco de dados SQL do Azure não tem tipos especializados ou estruturas que dão suporte a pares chave-valor, pois estruturas de valor-chave podem ser representadas nativamente como tabelas relacionais padrão:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Você pode personalizar essa estrutura de chave-valor para atender às suas necessidades sem nenhuma restrição. Por exemplo, o valor pode ser documento XML em vez de `nvarchar(max)` tipo, se o valor for documento JSON, você poderá colocar `CHECK` restrição que verifica a validade do conteúdo JSON. Você pode colocar qualquer número de valores relacionados a uma chave nas colunas adicionais, adicionar colunas computadas e índices para simplificar e otimizar o acesso a dados, definir a tabela como tabela de somente esquema de memória/otimizada para obter melhor desempenho, etc.

Veja [como o bwin está usando o OLTP na memória para obter desempenho e escala sem precedentes](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) para sua solução de cache ASP.NET que alcançou lotes de 1.200.000 por segundo, como um exemplo de como o modelo relacional pode ser efetivamente usado como solução de par chave-valor no Bom.

## <a name="next-steps"></a>Passos seguintes
Os recursos multimodelo em bancos de dados SQL do Azure também são os recursos principais de Mecanismo de Banco de Dados de SQL Server que são compartilhados entre o banco de dados SQL do Azure e o SQL Server. Para saber mais detalhes sobre esses recursos, visite as páginas de documentação do banco de dados relacional do SQL:

* [Processamento de grafo](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Suporte espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Dados XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
