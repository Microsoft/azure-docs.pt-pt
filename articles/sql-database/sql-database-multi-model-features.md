---
title: Capacidades de modelo multi do banco de dados SQL do Azure | Documentos da Microsoft
description: Base de dados SQL do Azure permite-lhe trabalhar com vários modelos de dados no mesmo banco de dados.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 69ca51776a61b43768ce7cb1565451c4f118de6e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316528"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Capacidades de vários modelos de base de dados do Azure SQL

Bases de dados com múltiplos modelos permitem-lhe armazenar e trabalhar com dados representados em vários formatos de dados como dados relacionais, gráficos, documentos JSON/XML, pares chave-valor, etc.

Base de dados SQL do Azure foi concebido para trabalhar com o modelo relacional que fornece o melhor desempenho na maioria dos casos para uma variedade de aplicativos para fins gerais. No entanto, a base de dados do Azure SQL não está limitado a relacional-apenas dados. Base de dados SQL do Azure permite-lhe utilizar uma variedade de formatos não relacionais que estão completamente integrados no modelo relacional. SQL do Azure fornece as seguintes funcionalidades com vários modelos:
- [Funcionalidades da Graph](#graph-features) permitem-lhe representar os seus dados como conjunto de nós e limites e utilizar consultas padrão do Transact-SQL melhoradas com gráfico `MATCH` operador para consultar os dados de gráfico.
- [Funcionalidades JSON](#json-features) permitem que coloque documentos JSON nas tabelas, transformar dados relacionais para documentos JSON e vice-versa. Pode utilizar o idioma padrão do Transact-SQL melhorado com funções JSON para analisar documentos e utilize índices não agrupados, os índices columnstore ou tabelas com otimização de memória, para otimizar as suas consultas.
- [Recursos espaciais](#spatial-features) permite-lhe armazenar dados geométricos e geográficos, índice-las com os índices espaciais e recuperar os dados através de consultas espaciais.
- [Recursos XML](#xml-features) permitem-lhe armazenar e indexar dados XML na sua base de dados e a utilizar operações de XQuery/XPath nativas para trabalhar com dados XML. Base de dados SQL do Azure tem especializadas motor de consulta XML incorporado que processam dados XML.
- [Pares chave-valor](#key-value-pairs) não são explicitamente suportados como recursos especiais, uma vez que paris de chave-valor podem ser modelados nativamente como tabelas de duas colunas.

  > [!Note]
  > Pode utilizar a expressão de caminho JSON, XQuery/XPath expressões, geográficos funções e expressões de consulta de gráfico da mesma consulta de Transact-SQL para aceder a todos os dados armazenados na base de dados. Além disso, qualquer ferramenta ou linguagem de programação que pode executar consultas de Transact-SQL, também pode utilizar essa interface de consulta para aceder a dados com vários modelos. Esta é a principal diferença em comparação comparada as bases de dados com múltiplos modelos, como [do Azure Cosmos DB](/azure/cosmos-db/) que fornece a API especializada para modelos de dados diferentes.

Nas seções a seguir, pode aprender sobre os recursos mais importantes com vários modelos de base de dados de SQL de Azures.

## <a name="graph-features"></a>Funcionalidades de gráficos

Base de dados SQL do Azure oferece capacidades de base de dados de gráficos para modelar relações de muitos-para-muitos no banco de dados. Um gráfico é uma coleção de nós (ou vértices) e margens (ou relações). Um nó representa uma entidade (por exemplo, uma pessoa ou uma organização) e uma periferia representa uma relação entre os dois nós que ele se conecta (por exemplo, gostos ou amigos). Aqui estão alguns recursos que tornam a uma base de dados exclusivo:
- Margens ou as relações são entidades de primeira classe num banco de dados do gráfico e podem ter atributos ou as propriedades associadas a eles.
- Um único limite pode conexão flexível de vários nós num banco de dados do gráfico.
- Pode expressar correspondência de padrões e as consultas de navegação multi-HOP facilmente.
- Pode expressar fechamento transitivo e consultas polimórficas facilmente.

As relações de gráfico e capacidades de consulta do gráfico estão integradas no Transact-SQL e receberem os benefícios do uso do SQL Server como o sistema de gestão de base de dados fundamentais.
[Processamento de gráficos](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) é a funcionalidade do motor de base de dados do SQL Server core, para que possa encontrar mais informações sobre o gráfico aqui de processamento.

### <a name="when-to-use-a-graph-capability"></a>Quando utilizar uma funcionalidade de gráfico

Não há nada que pode obter uma base de dados, que não pode ser obtido usando um banco de dados relacional. No entanto, uma base de dados pode tornar mais fácil expressar determinadas consultas. Sua decisão para escolher cada uma delas pode basear-se nos seguintes fatores:

- Modelar dados hierárquicos, em que um nó pode ter vários elementos principais, para que HierarchyId não pode ser utilizado
- Modelo tem o seu aplicativo tiver relações complexas de muitos-para-muitos; à medida que o aplicativo evolui, são adicionadas novas relações.
- Precisa analisar dados interligados e relações.

## <a name="json-features"></a>Funcionalidades JSON

Azure base de dados SQL permite-lhe analisa e consulta dados representados no JavaScript Object Notation [(JSON)](http://www.json.org/) formate e exportar os seus dados relacionais como texto JSON.

JSON é um formato de dados populares usado na troca de dados em aplicações web modernos e móveis. JSON também é utilizado para armazenar dados semi-estruturados nos ficheiros de registo ou em bases de dados NoSQL, como [do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Muitos serviços da web REST devolve resultados formatados como texto JSON ou aceitam dados formatados como JSON. A maioria dos Azure dos serviços, como [Azure Search](https://azure.microsoft.com/services/search/), [armazenamento do Azure](https://azure.microsoft.com/services/storage/), e [do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ter REST pontos de extremidade que devolverem ou consumam JSON.

Base de dados SQL do Azure permite-lhe trabalhar facilmente com dados JSON e integrar a sua base de dados com serviços modernos. Base de dados SQL do Azure fornece as seguintes funções para trabalhar com dados JSON:

![Funções JSON](./media/sql-database-json-features/image_1.png)

Se tiver texto JSON, pode extrair dados de JSON ou certifique-se de que JSON está formatado corretamente usando as funções incorporadas [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), e [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). O [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) função permite-lhe atualizar valor dentro de texto JSON. Para mais avançadas de consulta e análise, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) função pode transformar uma matriz de objetos JSON num conjunto de linhas. Qualquer consulta SQL pode ser executada no conjunto de resultados devolvidos. Por fim, há uma [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) cláusula que lhe permite formatar os dados armazenados em tabelas relacionais como texto JSON.

Para obter mais informações, consulte [como trabalhar com dados JSON na base de dados SQL do azure](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) é a funcionalidade do motor de base de dados do SQL Server core, para que possa encontrar mais informações sobre a funcionalidade JSON lá.

### <a name="when-to-use-a-json-capability"></a>Quando utilizar um recurso JSON

Modelos de documento podem ser usados em vez dos modelos relacionais em alguns cenários específicos:
- Alto-normalização de esquema não leva vantagens significativas, uma vez que aceder os todos os campos de objetos de uma só vez ou nunca atualizar partes normalizados de objetos. No entanto, o modelo normalizado aumenta a complexidade das suas consultas devido ao grande número de tabelas que precisa de associar a obter os dados.
- Está trabalhando com os aplicativos que nativamente a utilização de documentos JSON são comunicação ou modelos de dados e não pretende introduzir camadas adicionais que transforma os dados relacionais para JSON e vice-versa.
- Precisa simplificar o seu modelo de dados por anular a normalização tabelas-filho ou padrões de valor de objeto de entidade.
- Terá de carregar ou exportar dados armazenados no formato JSON, sem algumas ferramentas adicionais que analisa os dados.

## <a name="spatial-features"></a>Recursos espaciais

Dados espaciais representam informações sobre a localização física e a forma dos objetos geométricos. Estes objetos podem ser localizações de pontos ou objetos mais complexos, como países, estradas ou lakes.

Base de dados SQL do Azure suporta dois tipos de dados geográficos - escreva o tipo de dados de geometria e os dados de geografia.
- O tipo de geometria representa os dados num sistema de coordenadas Euclidiana (simples).
- O tipo de geografia representa os dados num sistema de coordenadas de arredondamento da terra.

Existe um número de objetos espaciais que podem ser utilizados na base de dados SQL do Azure, tal como [ponto](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [polígono](https://docs.microsoft.com/sql/relational-databases/spatial/polygon), etc.

Base de dados SQL do Azure também fornece especializada [índices espaciais](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) que podem ser utilizados para melhorar o desempenho das suas consultas espaciais.

[Suporte geográfico](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) é a funcionalidade do motor de base de dados do SQL Server core, para que possa encontrar mais informações sobre a funcionalidade geográfica lá.

## <a name="xml-features"></a>Recursos XML

SQL Server fornece uma plataforma poderosa para o desenvolvimento de aplicativos avançados para gerenciamento de dados semi-estruturados. Suporte para XML é integrado ao todos os componentes no SQL Server e inclui o seguinte:

- O tipo de dados xml. Valores XML podem ser armazenados nativamente numa coluna de tipo de dados xml que pode ser escrita, de acordo com uma coleção de esquemas XML, ou à esquerda sem tipos. Permite-lhe indexar a coluna XML.
- A capacidade de especificar uma consulta de XQuery contra dados armazenados em colunas e variáveis do tipo xml do XML. Funcionalidades de XQuery podem ser utilizadas em qualquer consulta de Transact-SQL que aceder a qualquer modelo de dados que utilizar na sua base de dados.
- Indexar automaticamente todos os elementos em documentos XML usando [índice XML principal](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) ou especifique os caminhos exatos que devem ser indexados usando [índice XML secundário](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET que permite o carregamento em massa de dados XML.
- Transforme dados relacionais para o formato XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) é a funcionalidade do motor de base de dados do SQL Server core, para que possa encontrar mais informações sobre a funcionalidade XML aqui.

### <a name="when-to-use-an-xml-capability"></a>Quando utilizar uma capacidade XML

Modelos de documento podem ser usados em vez dos modelos relacionais em alguns cenários específicos:
- Alto-normalização de esquema não leva vantagens significativas, uma vez que aceder os todos os campos de objetos de uma só vez ou nunca atualizar partes normalizados de objetos. No entanto, o modelo normalizado aumenta a complexidade das suas consultas devido ao grande número de tabelas que precisa de associar a obter os dados.
- Está trabalhando com os aplicativos que nativamente documentos XML de utilização são comunicação ou modelos de dados e não pretende introduzir camadas adicionais que transforma os dados relacionais em XML e vice-versa.
- Precisa simplificar o seu modelo de dados por anular a normalização tabelas-filho ou padrões de valor de objeto de entidade.
- Terá de carregar ou exportar dados armazenados no formato XML sem algumas ferramentas adicionais que analisa os dados.

## <a name="key-value-pairs"></a>Pares chave-valor

Base de dados SQL do Azure não tem tipos especializados ou estruturas que suportam pares chave-valor, uma vez que as estruturas de chave-valor podem ser representadas nativamente como tabelas relacionais padrão:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Pode personalizar esta estrutura de chave-valor de acordo com as suas necessidades, sem limitações. Por exemplo, o valor pode ser o documento XML em vez de `nvarchar(max)` tipo, se o valor é um documento JSON, pode colocar `CHECK` restrição que verifica a validade do conteúdo JSON. Pode colocar qualquer número de valores relacionados com uma chave nas colunas adicionais, adicionar colunas calculadas e índices para simplificar e otimizar o acesso a dados, definir a tabela como uma tabela com otimização de memória/de só de esquema para obter um melhor desempenho, etc.

Ver [como BWin está a utilizar o OLTP dentro da memória para alcançar o desempenho sem precedentes e o dimensionamento](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) para o seu cache de ASP.NET solução que alcançado 1.200.000 dos lotes por segundos, como um exemplo de como relacional modelo pode ser usado com eficiência como solução de par chave-valor na prática.

## <a name="next-steps"></a>Passos Seguintes
Recursos com vários modelos em bases de dados do Azure SQL também são os principais de recursos de motor de base de dados do SQL Server que são partilhados entre a base de dados do Azure SQL e SQL Server. Para obter mais detalhes sobre estas funcionalidades, visite as páginas de documentação da base de dados relacional do SQL:

* [Processamento de gráficos](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Suporte espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Dados XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
