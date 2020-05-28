---
title: Descrição geral das consultas elásticas
description: A consulta elástica permite-lhe executar uma consulta Transact-SQL que abrange várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: ab104463ad0bd3db88af47176d541dd4074037d7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048442"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Visão geral de consulta elástica azure SQL Database (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A função de consulta elástica (na pré-visualização) permite-lhe executar uma consulta Transact-SQL que abrange várias bases de dados na Base de Dados Azure SQL. Permite-lhe realizar consultas de base de dados cruzadas para aceder a tabelas remotas e ligar ferramentas da Microsoft e de terceiros (Excel, Power BI, Tableau, etc.) para consultar os níveis de dados com várias bases de dados. Utilizando esta funcionalidade, pode aumentar as consultas para grandes níveis de dados e visualizar os resultados em relatórios de inteligência empresarial (BI).

## <a name="why-use-elastic-queries"></a>Por que usar consultas elásticas

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Consulta através de bases de dados em Azure SQL Database completamente em T-SQL. Isto permite a consulta apenas de bases de dados remotas e fornece uma opção para os clientes atuais no local SQL Server migrarem aplicações usando nomes de três e quatro partes ou servidor ligado à Base de Dados SQL.

### <a name="available-on-standard-tier"></a>Disponível em nível padrão

A consulta elástica é suportada tanto nos níveis de serviço Standard como Premium. Consulte a secção sobre limitações de pré-visualização abaixo sobre as limitações de desempenho para níveis de serviço mais baixos.

### <a name="push-parameters-to-remote-databases"></a>Empurre parâmetros para bases de dados remotas

Consultas elásticas podem agora empurrar os parâmetros SQL para as bases de dados remotas para execução.

### <a name="stored-procedure-execution"></a>Execução de procedimento armazenado

Execute chamadas de procedimentos armazenadas à distância ou funções remotas utilizando [sp \_ executar \_ remotamente](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilidade

Tabelas externas com consulta elástica podem consultar mesas remotas com um esquema ou nome de mesa diferente.

## <a name="elastic-query-scenarios"></a>Cenários de consulta elástica

O objetivo é facilitar cenários de consulta onde várias bases de dados contribuem com linhas para um único resultado global. A consulta pode ser composta diretamente pelo utilizador ou aplicação, ou indiretamente através de ferramentas que estejam ligadas à base de dados. Isto é especialmente útil na criação de relatórios, utilizando ferramentas comerciais de BI ou integração de dados, ou qualquer aplicação que não possa ser alterada. Com uma consulta elástica, pode consultar várias bases de dados utilizando a experiência familiar de conectividade SQL Server em ferramentas como Excel, Power BI, Tableau ou Cognos.
Uma consulta elástica permite o fácil acesso a toda uma coleção de bases de dados através de consultas emitidas pelo SQL Server Management Studio ou pelo Visual Studio, e facilita a consulta de base de dados cruzada a partir do Quadro de Entidades ou de outros ambientes ORM. A figura 1 mostra um cenário em que uma aplicação em nuvem existente (que utiliza a biblioteca de clientes de base de [dados elástica)](elastic-database-client-library.md)se baseia num nível de dados escalado, e uma consulta elástica é usada para reportagens de bases de dados cruzadas.

**Figura 1** Consulta elástica utilizada no nível de dados escalonado

![Consulta elástica utilizada no nível de dados escalonado][1]

Os cenários do cliente para consulta elástica caracterizam-se pelas seguintes topologias:

* **Partição vertical - Consultas de base de dados cruzadas** (Topologia 1): Os dados são divididos verticalmente entre várias bases de dados num nível de dados. Tipicamente, diferentes conjuntos de tabelas residem em diferentes bases de dados. Isto significa que o esquema é diferente em diferentes bases de dados. Por exemplo, todas as tabelas para inventário estão numa base de dados, enquanto todas as tabelas relacionadas com contabilidade estão numa segunda base de dados. Casos de uso comum com esta topologia requerem que se questione ou compilar relatórios através das tabelas em várias bases de dados.
* **Partição Horizontal - Sharding** (Topologia 2): Os dados são divididos horizontalmente para distribuir linhas através de um nível de dados escalado. Com esta abordagem, o esquema é idêntico em todas as bases de dados participantes. Esta abordagem também é chamada de "sharding". A sharding pode ser realizada e gerida utilizando (1) as bibliotecas de ferramentas de base de dados elásticas ou (2) auto-sharding. Uma consulta elástica é usada para consultar ou compilar relatórios em muitos fragmentos. Os fragmentos são normalmente bases de dados dentro de uma piscina elástica. Você pode pensar em consulta elástica como uma maneira eficiente de consultar todas as bases de dados de piscina elástica ao mesmo tempo, desde que as bases de dados partilhem o esquema comum.

> [!NOTE]
> A consulta elástica funciona melhor para cenários de reporte onde a maior parte do processamento (filtragem, agregação) pode ser realizada no lado externo da fonte. Não é adequado para operações ETL em que grande quantidade de dados está a ser transferido de bases de dados remotas. Para trabalhos de trabalho de reporte pesados ou cenários de armazenamento de dados com consultas mais complexas, considere também a utilização de [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Partição vertical - consultas de base de dados cruzadas

Para começar a codificar, consulte [Começar com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).

Uma consulta elástica pode ser usada para disponibilizar dados localizados numa base de dados da Base de Dados SQL para outras bases de dados na Base de Dados SQL. Isto permite que as consultas de uma base de dados se refiram a tabelas em qualquer outra base de dados remota na Base de Dados SQL. O primeiro passo é definir uma fonte de dados externa para cada base de dados remota. A fonte de dados externa está definida na base de dados local a partir da qual pretende ter acesso a tabelas localizadas na base de dados remota. Não são necessárias alterações na base de dados remota. Para cenários típicos de partição vertical onde diferentes bases de dados têm diferentes esquemas, consultas elásticas podem ser usadas para implementar casos de uso comum, tais como acesso a dados de referência e consulta de bases de dados cruzadas.

> [!IMPORTANT]
> Deve possuir alterar qualquer permissão externa de FONTE DE DADOS. Esta permissão está incluída com a permissão alter DATABASE. ALTERE quaisquer permissões externas de FONTE DE DADOS são necessárias para se referir à fonte de dados subjacente.
>

**Dados de referência**: A topologia é utilizada para a gestão de dados de referência. Na figura abaixo, duas tabelas (T1 e T2) com dados de referência são mantidas numa base de dados dedicada. Utilizando uma consulta elástica, pode agora aceder às tabelas T1 e T2 remotamente a partir de outras bases de dados, como mostra a figura. Utilize topologia 1 se as tabelas de referência forem pequenas ou remotas consultas em tabela de referência têm predicados seletivos.

**Figura 2** Partição vertical - Utilização de consulta elástica para consultar dados de referência

![Partição vertical - Utilização de consulta elástica para consultar dados de referência][3]

**Consulta de base de dados cruzada:** Consultas elásticas permitem a utilização de casos que requerem consulta em várias bases de dados na Base de Dados SQL. A figura 3 mostra quatro bases de dados diferentes: CRM, Inventário, RH e Produtos. As consultas realizadas numa das bases de dados também precisam de acesso a uma ou todas as outras bases de dados. Utilizando uma consulta elástica, pode configurar a sua base de dados para este caso executando algumas simples declarações de DDL em cada uma das quatro bases de dados. Após esta configuração única, o acesso a uma mesa remota é tão simples como referir-se a uma tabela local a partir das suas consultas T-SQL ou das suas ferramentas BI. Esta abordagem é recomendada se as consultas remotas não devolverem grandes resultados.

**Figura 3** Partição vertical - Utilização de consulta elástica para consulta em várias bases de dados

![Partição vertical - Utilização de consulta elástica para consulta em várias bases de dados][4]

Os seguintes passos configuram consultas elásticas de bases de dados para cenários verticais de partição que requerem acesso a uma tabela localizada em bases de dados remotas na Base de Dados SQL com o mesmo esquema:

* [CRIAR CHAVE MASTER](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CRIAR BASE DE DADOS SCOPED CREdential](https://msdn.microsoft.com/library/mt270260.aspx) mycredential mycredential
* [CREATE/DROP DADOS EXTERNOS FONTE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource of type **RDBMS**
* [MESA EXTERNA CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx)

Depois de executar as declarações do DDL, você pode aceder à mesa remota "mytable" como se fosse uma mesa local. A Base de Dados Azure SQL abre automaticamente uma ligação à base de dados remota, processa o seu pedido na base de dados remota e devolve os resultados.

## <a name="horizontal-partitioning---sharding"></a>Partição horizontal - sharding

A utilização de consultas elásticas para executar tarefas de reporte sobre um sharded, isto é, horizontalmente dividido, nível de dados requer um mapa de bases de [dados elástico](elastic-scale-shard-map-management.md) para representar as bases de dados do nível de dados. Tipicamente, apenas um único mapa de fragmentos é usado neste cenário e uma base de dados dedicada com capacidades elásticas de consulta (nó de cabeça) serve como ponto de entrada para consultas de reporte. Só esta base de dados dedicada precisa de acesso ao mapa do fragmento. A figura 4 ilustra esta topologia e a sua configuração com a base de dados de consulta elástica e o mapa do fragmento. Para obter mais informações sobre a biblioteca de clientes de base de dados elástica e criar mapas de fragmentos, consulte a gestão do [mapa de Shard.](elastic-scale-shard-map-management.md)

**Figura 4** Partição horizontal - Utilização de consulta elástica para reportar sobre os níveis de dados fragmentos

![Partição horizontal - Utilização de consulta elástica para reportar sobre os níveis de dados fragmentos][5]

> [!NOTE]
> A Base de Dados elástica de Consulta (nó de cabeça) pode ser uma base de dados separada, ou pode ser a mesma base de dados que acolhe o mapa do fragmento.
> Qualquer que seja a configuração que escolher, certifique-se de que o nível de serviço e o tamanho da computação dessa base de dados são altos o suficiente para lidar com a quantidade esperada de pedidos de login/consulta.

Os seguintes passos configuram consultas elásticas de bases de dados para cenários horizontais de partição que requerem acesso a um conjunto de tabelas localizadas em (tipicamente) várias bases de dados remotas na Base de Dados SQL:

* [CRIAR CHAVE MASTER](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CRIAR BASE DE DADOS SCOPED CREdential](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential mycredential
* Crie um mapa de [fragmentos](elastic-scale-shard-map-management.md) que represente o seu nível de dados utilizando a biblioteca de clientes de base de dados elástica.
* [CREATE/DROP DADOS EXTERNOS FONTE](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) de dados do tipo **SHARD_MAP_MANAGER**
* [MESA EXTERNA CREATE/DROP](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)

Uma vez realizados estes passos, podes aceder à tabela horizontalmente dividida "mitable" como se fosse uma mesa local. A Base de Dados Azure SQL abre automaticamente múltiplas ligações paralelas às bases de dados remotas onde as tabelas são fisicamente armazenadas, processa os pedidos nas bases de dados remotas e devolve os resultados.
Mais informações sobre os passos necessários para o cenário horizontal de partição podem ser encontradas em [consulta elástica para a partilha horizontal](elastic-query-horizontal-partitioning.md).

Para começar a codificar, consulte [Começar com consulta elástica para divisóriahorizontal (sharding)](elastic-query-getting-started.md).

> [!IMPORTANT]
> A execução bem sucedida de consultas elásticas sobre um grande conjunto de bases de dados depende fortemente da disponibilidade de cada uma das bases de dados durante a execução da consulta. Se uma das bases de dados não estiver disponível, a consulta total falhará. Se planeia consultar centenas ou milhares de bases de dados ao mesmo tempo, certifique-se de que a sua aplicação de cliente tem uma lógica de retry incorporada, ou considere alavancar o [Elastic Database Jobs](https://docs.microsoft.com/azure/sql-database/sql-database-job-automation-overview#elastic-database-jobs-preview) (pré-visualização) e consultar subconjuntos menores de bases de dados, consolidando os resultados de cada consulta num único destino.

## <a name="t-sql-querying"></a>Consulta T-SQL

Depois de definir as suas fontes de dados externas e as suas tabelas externas, pode utilizar cordas regulares de ligação do Servidor SQL para se ligar às bases de dados onde definiu as suas tabelas externas. Em seguida, pode executar declarações T-SQL sobre as suas tabelas externas nessa ligação com as limitações descritas abaixo. Pode encontrar mais informações e exemplos de consultas T-SQL nos tópicos de documentação para [divisória horizontal](elastic-query-horizontal-partitioning.md) e [partição vertical](elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Pode utilizar cordas regulares de ligação sQL Server para ligar as suas aplicações e ferramentas de integração de dados a bases de dados que tenham tabelas externas. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Uma vez ligado, consulte a base de dados de consulta elástica e as tabelas externas dessa base de dados, tal como faria com qualquer outra base de dados do SQL Server a que se conecta com a sua ferramenta.

> [!IMPORTANT]
> A autenticação utilizando o Diretório Ativo Azure com consultas elásticas não é suportada atualmente.

## <a name="cost"></a>Custo

A consulta elástica está incluída no custo da Base de Dados Azure SQL. Note que as topoologias em que as suas bases de dados remotas estão num centro de dados diferente do ponto final de consulta elástica são suportadas, mas os dados que se afastam das bases de dados remotas são carregados regularmente [com taxas Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Executar a sua primeira consulta elástica pode demorar até alguns minutos no nível de serviço Standard. Este tempo é necessário para carregar a funcionalidade de consulta elástica; o desempenho de carregamento melhora com níveis de serviço mais elevados e tamanhos de computação.
* A inscrição de fontes de dados externas ou tabelas externas de SSMS ou SSDT ainda não é suportada.
* A importação/exportação da Base de Dados SQL ainda não suporta fontes de dados externas e quadros externos. Se precisar de utilizar a Importação/Exportação, deixe cair estes objetos antes de exportar e, em seguida, recrie-os após a importação.
* A tualmente, a consulta elástica apenas suporta o acesso apenas a leitura seleções a tabelas externas. Pode, no entanto, utilizar a funcionalidade T-SQL completa na base de dados onde a tabela externa está definida. Isto pode ser útil para, por exemplo, persistir resultados temporários usando, por exemplo, SELECT <column_list> INTO <local_table>, ou para definir procedimentos armazenados na base de dados de consulta elástica que se refere maquea a tabelas externas.
* Com exceção do nvarchar (máx), os tipos lob (incluindo tipos espaciais) não são suportados em definições externas de tabela. Como uma suposição, pode criar uma visão na base de dados remota que lança o tipo LOB em nvarchar (máx), definir a sua tabela externa sobre a vista em vez da tabela base e, em seguida, lançá-la novamente para o tipo lob original nas suas consultas.
* As colunas do tipo de dados nvarchar (max) em resultados desativam técnicos avançados de lotação utilizados na implementação de consultas elásticas e podem afetar o desempenho da consulta por uma ordem de grandeza, ou mesmo duas ordens de magnitude em casos de utilização não canónica em que uma grande quantidade de dados não agregados está a ser transferida em resultado de consulta.
* Atualmente, as estatísticas das colunas sobre as tabelas externas não são suportadas. As estatísticas de tabelas são apoiadas, mas precisam de ser criadas manualmente.
* A consulta elástica funciona apenas com base de dados Azure SQL. Não é possível usá-lo para consulta no local SQL Server, ou SQL Server num VM.

## <a name="feedback"></a>Comentários

Partilhe feedback sobre a sua experiência com consultas elásticas connosco abaixo, nos fóruns da MSDN ou no Stack Overflow. Estamos interessados em todo o tipo de feedback sobre o serviço (defeitos, arestas ásperas, lacunas de características).

## <a name="next-steps"></a>Próximos passos

* Para um tutorial de partição vertical, consulte [Iniciar-se com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e amostras para dados verticalmente divididos, consulte a [consulta de dados verticalmente divididos)](elastic-query-vertical-partitioning.md)
* Para um tutorial de partição horizontal (sharding), consulte [Iniciar-se com consulta elástica para partição horizontal (sharding)](elastic-query-getting-started.md).
* Para consultas de sintaxe e amostras para dados horizontalmente divididos, consulte dados [horizontalmente divididos)](elastic-query-horizontal-partitioning.md)
* Consulte o [sp \_ execute o controlo \_ remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem como fragmentos num esquema de partição horizontal.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->