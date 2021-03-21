---
title: Descrição geral das consultas elásticas
description: A consulta elástica permite-lhe executar uma consulta Transact-SQL que abrange várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: cac17bbac96d44d8d9bfce2e168de4ea6d4c5c08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364958"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Visão geral da consulta elástica Azure SQL Database (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A funcionalidade de consulta elástica (em pré-visualização) permite-lhe executar uma consulta Transact-SQL que abrange várias bases de dados na Base de Dados SQL do Azure. Permite-lhe realizar consultas de base de dados cruzadas para aceder a tabelas remotas e ligar ferramentas da Microsoft e de terceiros (Excel, Power BI, Tableau, etc.) a consultas através de níveis de dados com várias bases de dados. Com esta funcionalidade, pode aumentar horizontalmente as consultas para grandes escalões de dados e visualizar os resultados em relatórios BI (business intelligence).

## <a name="why-use-elastic-queries"></a>Por que usar consultas elásticas

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Consulta através de bases de dados na Base de Dados Azure SQL completamente em T-SQL. Isto permite a consulta apenas de leitura de bases de dados remotas e fornece uma opção para os clientes atuais do SQL Server migrarem aplicações usando nomes de três e quatro partes ou servidor ligado à Base de Dados SQL.

### <a name="available-on-standard-tier"></a>Disponível no nível padrão

A consulta elástica é suportada tanto nos níveis de serviço Standard como Premium. Consulte a secção sobre limitações de pré-visualização abaixo sobre limitações de desempenho para níveis de serviço mais baixos.

### <a name="push-parameters-to-remote-databases"></a>Empurre os parâmetros para bases de dados remotas

As consultas elásticas podem agora empurrar os parâmetros SQL para as bases de dados remotas para execução.

### <a name="stored-procedure-execution"></a>Execução de procedimentos armazenados

Executar chamadas de procedimentos de armazenas remotas ou funções remotas utilizando [ \_ o controlo \_ remoto do sp](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database).

### <a name="flexibility"></a>Flexibilidade

Tabelas externas com consulta elástica podem se referir a tabelas remotas com um esquema ou nome de mesa diferente.

## <a name="elastic-query-scenarios"></a>Cenários de consulta elástica

O objetivo é facilitar cenários de consulta onde várias bases de dados contribuem com linhas para um único resultado global. A consulta pode ser composta pelo utilizador ou pela aplicação diretamente, ou indiretamente através de ferramentas que estão ligadas à base de dados. Isto é especialmente útil na criação de relatórios, utilizando ferramentas comerciais de BI ou integração de dados, ou qualquer aplicação que não possa ser alterada. Com uma consulta elástica, pode consultar várias bases de dados utilizando a experiência familiar de conectividade SQL Server em ferramentas como Excel, Power BI, Tableau ou Cognos.
Uma consulta elástica permite um fácil acesso a toda uma coleção de bases de dados através de consultas emitidas pelo SQL Server Management Studio ou visual Studio, e facilita a consulta cruzada de bases de dados a partir do Quadro de Entidades ou outros ambientes ORM. A figura 1 mostra um cenário em que uma aplicação em nuvem existente (que utiliza a [biblioteca de clientes de base de dados elásticas)](elastic-database-client-library.md)se baseia num nível de dados dimensionado, e uma consulta elástica é usada para reportar bases de dados cruzadas.

**Figura 1** Consulta elástica utilizada no nível de dados escalonado

![Consulta elástica utilizada no nível de dados escalonado][1]

Os cenários do cliente para consulta elástica caracterizam-se pelas seguintes topologias:

* **Partição vertical - Consultas de base de dados cruzadas** (Topologia 1): Os dados são divididos verticalmente entre uma série de bases de dados num nível de dados. Tipicamente, diferentes conjuntos de tabelas residem em diferentes bases de dados. Isto significa que o esquema é diferente em diferentes bases de dados. Por exemplo, todas as tabelas de inventário estão numa base de dados, enquanto todas as tabelas relacionadas com a contabilidade estão numa segunda base de dados. Casos de uso comum com esta topologia requerem uma consulta através ou para compilar relatórios em tabelas em várias bases de dados.
* **Partição Horizontal - Caco** (Topologia 2): Os dados são divididos horizontalmente para distribuir linhas através de um nível de dados escalonado. Com esta abordagem, o esquema é idêntico em todas as bases de dados participantes. Esta abordagem também é chamada de "fragmento". O fragmento pode ser realizado e gerido utilizando (1) as bibliotecas de ferramentas de base de dados elásticas ou (2) auto-fragmentos. Uma consulta elástica é usada para consultar ou compilar relatórios em muitos fragmentos. Os fragmentos são tipicamente bases de dados dentro de uma piscina elástica. Pode pensar na consulta elástica como uma forma eficiente de consultar todas as bases de dados de piscina elástica de uma só vez, desde que as bases de dados partilhem o esquema comum.

> [!NOTE]
> A consulta elástica funciona melhor para os cenários de reporte onde a maior parte do processamento (filtragem, agregação) pode ser realizado no lado externo da fonte. Não é adequado para operações ETL onde uma grande quantidade de dados está a ser transferida da ou das bases de dados remotas. Para trabalhos de trabalho de relato pesado ou cenários de armazenamento de dados com consultas mais complexas, considere também a utilização de [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Partição vertical - consultas de base de dados cruzadas

Para começar a codificar, consulte [Começar com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).

Uma consulta elástica pode ser usada para disponibilizar dados localizados numa base de dados na Base de Dados SQL para outras bases de dados na Base de Dados SQL. Isto permite que as consultas de uma base de dados se refiram a tabelas em qualquer outra base de dados remota na Base de Dados SQL. O primeiro passo é definir uma fonte de dados externa para cada base de dados remota. A fonte de dados externa é definida na base de dados local a partir da qual pretende ter acesso a tabelas localizadas na base de dados remota. Não são necessárias alterações na base de dados remota. Para cenários típicos de partição vertical onde diferentes bases de dados têm esquemas diferentes, podem ser usadas consultas elásticas para implementar casos de uso comum, tais como acesso a dados de referência e consulta de bases de dados cruzadas.

> [!IMPORTANT]
> Tem de possuir qualquer permissão DE FONTE DE DADOS EXTERNA. Esta permissão está incluída na permissão ALTER DATABASE. ALTERAR QUAISquer permissões EXTERNAS DE FONTE DE DADOS são necessárias para consultar a fonte de dados subjacente.
>

**Dados de referência**: A topologia é utilizada para a gestão de dados de referência. Na figura abaixo, duas tabelas (T1 e T2) com dados de referência são mantidas numa base de dados dedicada. Utilizando uma consulta elástica, pode agora aceder às tabelas T1 e T2 remotamente a partir de outras bases de dados, como mostra a figura. Utilize topologia 1 se as tabelas de referência forem pequenas ou remotas em tabela de referência têm predicados seletivos.

**Figura 2** Partição vertical - Utilização de consulta elástica para consultar dados de referência

![Partição vertical - Utilização de consulta elástica para consultar dados de referência][3]

**Consulta cruzada:** Consultas elásticas permitem a utilização de casos que requerem consulta em várias bases de dados na Base de Dados SQL. A figura 3 mostra quatro bases de dados diferentes: CRM, Inventário, RH e Produtos. As consultas realizadas numa das bases de dados também precisam de acesso a uma ou a todas as outras bases de dados. Utilizando uma consulta elástica, pode configurar a sua base de dados para este caso, executando algumas simples declarações DDL em cada uma das quatro bases de dados. Após esta configuração única, o acesso a uma mesa remota é tão simples como se referir a uma mesa local a partir das suas consultas T-SQL ou das suas ferramentas BI. Esta abordagem é recomendada se as consultas remotas não devolverem grandes resultados.

**Figura 3** Partição vertical - Utilização de consulta elástica para consulta em várias bases de dados

![Partição vertical - Utilização de consulta elástica para consulta em várias bases de dados][4]

Os seguintes passos configuram consultas elásticas de base de dados para cenários de partição vertical que requerem acesso a uma tabela localizada em bases de dados remotas na Base de Dados SQL com o mesmo esquema:

* [CRIAR MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CRIAR BASE DE DADOS SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* [CREATE/DROP DADOS EXTERNOS FONTE](/sql/t-sql/statements/create-external-data-source-transact-sql) DE DADOS mydatasource do tipo **RDBMS**
* [LÓGICO/DROP TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) mytable

Depois de executar as declarações do DDL, pode aceder à mesa remota "mytable" como se fosse uma mesa local. O Azure SQL Database abre automaticamente uma ligação à base de dados remota, processa o seu pedido na base de dados remota e devolve os resultados.

## <a name="horizontal-partitioning---sharding"></a>Divisória horizontal - fragmento

A utilização de consultas elásticas para executar tarefas de reporte sobre um fragmento, isto é, horizontalmente dividido, o nível de dados requer um [mapa de fragmentos de base de dados elástico](elastic-scale-shard-map-management.md) para representar as bases de dados do nível de dados. Normalmente, apenas um único mapa de fragmentos é usado neste cenário e uma base de dados dedicada com capacidades de consulta elástica (nó de cabeça) serve como ponto de entrada para consultas de reporte. Só esta base de dados dedicada precisa de acesso ao mapa de fragmentos. A Figura 4 ilustra esta topologia e a sua configuração com a base de dados de consulta elástica e o mapa de fragmentos. Para obter mais informações sobre a biblioteca de clientes de base de dados elástica e criar mapas de fragmentos, consulte a gestão do [mapa Shard.](elastic-scale-shard-map-management.md)

**Figura 4** Partição horizontal - Utilização de consulta elástica para reportar sobre níveis de dados fragmentos

![Partição horizontal - Utilização de consulta elástica para reportar sobre níveis de dados fragmentos][5]

> [!NOTE]
> A Base de Dados de Consulta Elástica (nó de cabeça) pode ser uma base de dados separada, ou pode ser a mesma base de dados que hospeda o mapa de fragmentos.
> Qualquer que seja a configuração que escolher, certifique-se de que o nível de serviço e o tamanho do cálculo dessa base de dados são altos o suficiente para lidar com a quantidade esperada de pedidos de login/consulta.

Os seguintes passos configuram consultas elásticas de base de dados para cenários horizontais de partição que exigem o acesso a um conjunto de tabelas localizadas em (tipicamente) várias bases de dados remotas na Base de Dados SQL:

* [CRIAR MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CRIAR BASE DE DADOS SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Crie um [mapa de fragmentos](elastic-scale-shard-map-management.md) que represente o seu nível de dados utilizando a biblioteca de clientes de base de dados elástica.
* [CREATE/DROP DADOS EXTERNOS FONTE](/sql/t-sql/statements/create-external-data-source-transact-sql) DE DADOS mydatasource do tipo **SHARD_MAP_MANAGER**
* [LÓGICO/DROP TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) mytable

Uma vez realizados estes passos, pode aceder à mesa "miotável" horizontalmente dividida como se fosse uma mesa local. O Azure SQL Database abre automaticamente múltiplas ligações paralelas às bases de dados remotas onde as tabelas estão fisicamente armazenadas, processa os pedidos nas bases de dados remotas e devolve os resultados.
Podem ser encontradas mais informações sobre os passos necessários para o cenário de partição horizontal [em consulta elástica para a partição horizontal](elastic-query-horizontal-partitioning.md).

Para começar a codificar, consulte [Começar com consulta elástica para partição horizontal (fragmento)](elastic-query-getting-started.md).

> [!IMPORTANT]
> A execução bem sucedida de consultas elásticas sobre um grande conjunto de bases de dados depende fortemente da disponibilidade de cada uma das bases de dados durante a execução da consulta. Se uma das bases de dados não estiver disponível, toda a consulta falhará. Se planeia consultar centenas ou milhares de bases de dados ao mesmo tempo, certifique-se de que a sua aplicação de clientes tem novamente a lógica incorporada, ou considere alavancar [a Elastic Database Jobs](./job-automation-overview.md) (pré-visualização) e consultar subconjuntos menores de bases de dados, consolidando os resultados de cada consulta num único destino.

## <a name="t-sql-querying"></a>Consulta T-SQL

Uma vez definidas as suas fontes de dados externas e as suas tabelas externas, pode utilizar as cadeias regulares de ligação do SQL Server para ligar às bases de dados onde definiu as suas tabelas externas. Em seguida, pode executar declarações T-SQL sobre as suas tabelas externas nessa ligação com as limitações descritas abaixo. Pode encontrar mais informações e exemplos de consultas T-SQL nos tópicos de documentação para [divisórias horizontais](elastic-query-horizontal-partitioning.md) e [divisórias verticais.](elastic-query-vertical-partitioning.md)

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Pode utilizar as cadeias regulares de ligação do SQL Server para ligar as suas aplicações e ferramentas de integração de dados ou BI ou de integração de dados a bases de dados que tenham tabelas externas. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Uma vez ligado, consulte a base de dados de consulta elástica e as tabelas externas nessa base de dados, tal como faria com qualquer outra base de dados do SQL Server a que se conecte com a sua ferramenta.

> [!IMPORTANT]
> A autenticação utilizando o Azure Ative Directory com consultas elásticas não é suportada atualmente.

## <a name="cost"></a>Custo

A consulta elástica está incluída no custo da Base de Dados Azure SQL. Note que as topologias onde as suas bases de dados remotas estão num centro de dados diferente do ponto final de consulta elástica são suportadas, mas a saída de dados de bases de dados remotas é cobrada regularmente [taxas de Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Executar a sua primeira consulta elástica pode demorar até alguns minutos no nível de serviço Standard. Este tempo é necessário para carregar a funcionalidade de consulta elástica; o desempenho de carregamento melhora com níveis de serviço mais elevados e tamanhos de cálculo.
* A scripting de fontes de dados externas ou tabelas externas de SSMS ou SSDT ainda não está suportada.
* A Importação/Exportação para a Base de Dados SQL ainda não suporta fontes de dados externas e tabelas externas. Se precisar de utilizar a Importação/Exportação, largue estes objetos antes de exportar e, em seguida, reu crie-os após a importação.
* A consulta elástica suporta atualmente apenas o acesso de leitura a tabelas externas. No entanto, pode utilizar a funcionalidade T-SQL completa na base de dados onde a tabela externa é definida. Isto pode ser útil para, por exemplo, persistir em resultados temporários utilizando, por exemplo, SELECT <column_list> INTO <local_table>, ou para definir procedimentos armazenados na base de dados de consulta elástica que se referem a tabelas externas.
* Com exceção do nvarchar(máx), os tipos de LOB (incluindo tipos espaciais) não são suportados em definições de tabela externa. Como uma solução alternativa, pode criar uma visão na base de dados remota que lança o tipo LOB em nvarchar(máx), definir a sua tabela externa sobre a vista em vez da tabela base e, em seguida, lanhá-la de volta para o tipo LOB original nas suas consultas.
* Colunas de nvarchar(max) tipo de dados no conjunto de resultados desativam os técnicos avançados de loteamento utilizados na implementação da consulta elástica e podem afetar o desempenho da consulta por ordem de magnitude, ou mesmo duas ordens de magnitude em casos de utilização não canónica em que uma grande quantidade de dados não agregados é transferida em resultado de consulta.
* Atualmente, as estatísticas das colunas sobre as tabelas externas não são suportadas. As estatísticas dos quadros são apoiadas, mas precisam de ser criadas manualmente.
* A consulta elástica funciona apenas com base de dados Azure SQL. Não é possível usá-lo para consultar uma instância do SQL Server.

## <a name="share-your-feedback"></a>Partilhe o seu feedback

Partilhe feedback sobre a sua experiência com consultas elásticas connosco abaixo, nos fóruns da MSDN ou no Stack Overflow. Estamos interessados em todo o tipo de feedback sobre o serviço (defeitos, arestas ásperas, lacunas de recursos).

## <a name="next-steps"></a>Passos seguintes

* Para obter um tutorial de partição vertical, consulte [Começar com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e amostra para dados verticalmente divididos, consulte [consulta de dados partidos verticalmente)](elastic-query-vertical-partitioning.md)
* Para obter um tutorial de partição horizontal (fragmento), consulte [Começar com consulta elástica para partição horizontal (fragmento)](elastic-query-getting-started.md).
* Para consultas de sintaxe e amostra para dados com divisórias horizontais, consulte [consulta de dados com partição horizontal)](elastic-query-horizontal-partitioning.md)
* Consulte [o sp executar o controlo \_ \_ remoto](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem de fragmentos num esquema de partição horizontal.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->