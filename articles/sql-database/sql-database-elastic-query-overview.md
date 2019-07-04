---
title: Descrição geral de consulta elástica de base de dados SQL do Azure | Documentos da Microsoft
description: Consulta elástica permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5188862c50895c8e3f1bdecb4e08d39409bb5f9e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491654"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Descrição geral de consulta elástica do Azure SQL Database (pré-visualização)

A funcionalidade de consulta elástica (em pré-visualização) permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados na base de dados do Azure SQL. Permite-lhe executar consultas entre bases de dados para aceder a tabelas remotas e para ligar a Microsoft e ferramentas de terceiros (Excel, Power BI, Tableau, etc.) para consultar em camadas de dados com várias bases de dados. Utilizar esta funcionalidade, pode aumentar horizontalmente consultas para camadas de dados de grande dimensão na base de dados SQL e visualizar os resultados em relatórios de business intelligence (BI).

## <a name="why-use-elastic-queries"></a>Por que usar consultas elásticas

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Consultar entre bases de dados SQL do Azure completamente em T-SQL. Isso permite que só de leitura consulta dos bancos de dados remotos e fornece uma opção para clientes atuais do SQL Server no local migrar aplicativos usando os nomes de três e quatro partes ou o servidor ligado para o SQL DB.

### <a name="available-on-standard-tier"></a>Disponíveis no escalão standard

Consulta elástica é suportada nos escalões de serviço Standard e Premium. Consulte a secção sobre limitações de pré-visualização abaixo sobre limitações de desempenho para os escalões de serviço mais baixos.

### <a name="push-parameters-to-remote-databases"></a>Parâmetros de push às bases de dados remotos

Consultas elásticas agora podem enviar parâmetros SQL para as bases de dados remotos para execução.

### <a name="stored-procedure-execution"></a>Execução do procedimento armazenado

Executar chamadas de procedimento armazenado remoto ou funções remotas usando [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilidade

Podem consultar tabelas externas com consulta elástica tabelas remotas com um esquema diferente ou o nome da tabela.

## <a name="elastic-query-scenarios"></a>Cenários de consulta elástica

O objetivo é facilitar a consultas cenários em que várias bases de dados contribuem linhas num único resultado geral. A consulta ou pode ser composta pelo usuário ou aplicativo diretamente ou indiretamente através de ferramentas que estão ligadas à base de dados. Isto é especialmente útil durante a criação de relatórios, com BI comercial ou ferramentas de integração de dados ou qualquer aplicativo que não pode ser alterado. Com uma consulta elástica, pode consultar em várias bases de dados com a experiência familiar de conectividade do SQL Server em ferramentas como o Excel, Power BI, Tableau ou Cognos.
Uma consulta elástica permite o fácil acesso a uma coleção inteira de bases de dados através de consultas emitidas pelo SQL Server Management Studio ou o Visual Studio e facilita a consultar entre bases de dados do Entity Framework ou outros ambientes de ORM. A figura 1 mostra um cenário onde um existente aplicação na cloud (que utiliza a [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md)) a camada de compilações em dados de escalamento horizontal e uma consulta elástica é utilizada para relatórios entre bases de dados.

**Figura 1** consulta elástica usada na camada de dados aumentadas horizontalmente

![Consulta elástica usada na camada de dados aumentadas horizontalmente][1]

Consulta elástica em cenários de clientes são caracterizados pelas seguintes topologias:

* **Criação de partições verticais - consultas entre bases de dados** (topologia 1): Os dados estão particionados verticalmente entre um número de bases de dados numa camada de dados. Normalmente, os diferentes conjuntos de tabelas residem em diferentes bases de dados. Isso significa que o esquema é diferente em diferentes bases de dados. Por exemplo, todas as tabelas para o inventário estão num banco de dados enquanto todas as tabelas relacionadas com a gestão de contas estão numa segunda base de dados. Casos de utilização comuns com esta topologia exigem um consultar em ou compilar relatórios em tabelas em vários bancos de dados.
* **Criação de partições horizontais - fragmentação** (topologia 2): Os dados são particionados horizontalmente para distribuir as linhas num dados aumentados horizontalmente camadas. Com esta abordagem, o esquema é idêntico em todas as bases de dados de participantes. Essa abordagem também é denominada "fragmentação". A fragmentação pode ser executada e gerenciado usar (1) a base de dados elástica ferramentas bibliotecas ou (2) self-fragmentação. Uma consulta elástica é utilizada para consultar ou compilar relatórios através de vários shards.

> [!NOTE]
> Consulta elástica funciona melhor para cenários em que a maior parte do processamento (filtragem, agregação) pode ser executadas no lado de origem externa de relatórios. Não é adequado para operações de ETL em que a grande quantidade de dados está a ser transferida de bases de dados remota. Para cargas de trabalho relatórios pesadas ou cenários com consultas mais complexas do armazém de dados, também considerar a utilização [do Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Criação de partições verticais - entre bases de dados consultas

Para começar a codificar, veja [guia de introdução consultas entre bases de dados (criação de partições verticais)](sql-database-elastic-query-getting-started-vertical.md).

Uma consulta elástica pode ser utilizada para tornar os dados localizados num banco de dados SQL disponível para outros bancos de dados SQL. Isto permite consultas a partir de uma base de dados para fazer referência a tabelas do qualquer outro remoto base de dados SQL. A primeira etapa é definir uma origem de dados externos para cada base de dados remota. A origem de dados externa é definida na base de dados local do qual pretende obter acesso às tabelas localizado na base de dados remota. Nenhuma alteração é necessária na base de dados remota. Para verticais particionamento cenários típicos onde diferentes bancos de dados possuem esquemas diferentes, consultas elásticas podem servir-se para implementar casos de utilização comuns, como o acesso a dados de referência e entre bases de dados consultar.

> [!IMPORTANT]
> Precisa de ter permissão de alterar qualquer origem de dados externa. Esta permissão está incluída com a permissão ALTER DATABASE. São necessárias permissões de alterar qualquer origem de dados externa para fazer referência à origem de dados subjacente.
>

**Dados de referência**: A topologia é utilizada para gestão de dados de referência. Na figura abaixo, as duas tabelas (T1 e T2) com dados de referência são mantidas numa base de dados dedicado. Utilizar uma consulta elástica, agora pode aceder a tabelas T1 e T2 remotamente a partir de outras bases de dados, conforme mostrado na figura. Topologia de utilização 1 se tabelas de referência são pequenas ou remotas consultas na tabela de referência ter predicados seletivos.

**Figura 2** criação de partições verticais - o utilizando dados de referência de consulta para consulta elástica

![Criação de partições verticais - o utilizando dados de referência de consulta para consulta elástica][3]

**Consultar entre bases de dados**: Consultas elásticas permitem casos de utilização que exigem a consultas entre várias bases de dados do SQL. Figura 3 mostra quatro bases de dados diferentes: CRM, inventário, RH e produtos. Consultas executadas em uma das bases de dados também precisam de acesso a uma ou todas as outras bases de dados. Utilizar uma consulta elástica, pode configurar a base de dados neste caso, através da execução de algumas instruções de DDL simples em cada um dos quatro bases de dados. Após esta configuração de uso individual, acesso a uma tabela remota é tão simples quanto fazer referência a uma tabela local de suas consultas de T-SQL ou de suas ferramentas de BI. Essa abordagem é recomendada se as consultas remotas devolver grandes resultados.

**Figura 3** criação de partições verticais - o usando elástico consulta para consulta em várias bases de dados

![Criação de partições verticais - o usando elástico consulta para consulta em várias bases de dados][4]

Consultas de bases de dados elásticas para cenários de criação de partições verticais que necessitam de acesso a uma tabela localizado nas bases de dados SQL remotos com o mesmo esquema de configurar os seguintes passos:

* [Criar chave MESTRA](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [ORIGEM de dados externa CREATE/DROP](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource do tipo **RDBMS**
* [TABELA externa de CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Depois de executar as instruções DDL, pode acessar a tabela remota "mytable" como se fosse uma tabela local. Base de dados SQL do Azure automaticamente abre uma conexão com a base de dados remoto, processa o pedido na base de dados remoto e retorna os resultados.

## <a name="horizontal-partitioning---sharding"></a>Criação de partições horizontais - fragmentação

Com uma consulta elástica para fazer tarefas de criação de relatórios através de um em partição horizontal, ou seja, particionados horizontalmente, camada de dados requer um [mapa de partições horizontais de bases de dados elásticas](sql-database-elastic-scale-shard-map-management.md) para representar as bases de dados de camada de dados. Normalmente, apenas um mapa com uma única partição horizontal é utilizado neste cenário e uma base de dados dedicado com capacidades de consulta elástica (nó principal) serve como ponto de entrada para consultas de relatórios. Apenas esta base de dados dedicado precisa de acesso para o mapa de partições horizontais. Figura 4 ilustra essa topologia e a respetiva configuração com o mapa de consulta elástica da base de dados e a partição horizontal. As bases de dados na camada de dados podem ser de qualquer versão da base de dados do Azure SQL ou edição. Para obter mais informações sobre a biblioteca de clientes de bases de dados elásticas e a criação de mapas de partições horizontais, veja [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** criação de partições horizontais - o com uma consulta elástica para relatórios sobre camadas de dados em partição horizontal

![Criação de partições horizontais - o com uma consulta elástica para relatórios sobre camadas de dados em partição horizontal][5]

> [!NOTE]
> Base de dados elástica para a consulta (nó principal) pode ser separado da base de dados, ou pode ser a mesma base de dados que aloja o mapa de partições horizontais.
> Qualquer configuração que escolha, certificar-se de que essa camada de serviço de computação e o tamanho dessa base de dados é suficientemente elevada para processar a quantidade esperada de pedidos de início de sessão/consulta.

Consultas de bases de dados elásticas para cenários de criação de partições horizontais que exigem acesso a um conjunto de tabelas localizadas em (normalmente) vários remotos bases de dados SQL de configurar os seguintes passos:

* [Criar chave MESTRA](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Criar uma [mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md) que representa sua camada de dados usando a biblioteca de cliente da base de dados elástica.
* [ORIGEM de dados externa CREATE/DROP](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource do tipo **SHARD_MAP_MANAGER**
* [TABELA externa de CREATE/DROP](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Depois de ter de efetuar estes passos, pode acessar a tabela particionada horizontalmente "mytable" como se fosse uma tabela local. Base de dados SQL do Azure automaticamente abre-se várias ligações paralelas para as bases de dados remotos onde as tabelas fisicamente são armazenadas, processa as solicitações nas bases de dados remotos e retorna os resultados.
Obter mais informações sobre os passos necessários para o cenário de criação de partições horizontal pode ser encontrado na [consulta elástica para a criação de partições horizontais](sql-database-elastic-query-horizontal-partitioning.md).

Para começar a codificar, veja [guia de introdução consulta elástica para a criação de partições horizontais (fragmentação)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>A consulta T-SQL

Depois de definir suas origens de dados externos e as tabelas externas, pode utilizar cadeias de ligação normais do SQL Server para ligar às bases de dados em que tiver definido as tabelas externas. Em seguida, pode executar instruções T-SQL sobre as tabelas externas nessa ligação com as limitações descritas abaixo. Pode encontrar mais informações e exemplos de consultas de T-SQL nos tópicos de documentação [criação de partições horizontais](sql-database-elastic-query-horizontal-partitioning.md) e [criação de partições verticais](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas

Pode utilizar cadeias de ligação normais do SQL Server para ligar as suas aplicações e ferramentas de integração de dados ou de BI para bases de dados com tabelas externas. Certifique-se de que o SQL Server é suportado como uma origem de dados para a sua ferramenta. Assim que estiver ligado, consulte a base de dados de consulta elástica e as tabelas externas nessa base de dados tal como faria com qualquer outro SQL Server da base de dados que se ligam a com a sua ferramenta.

> [!IMPORTANT]
> Autenticação com o Azure Active Directory com consultas elásticas não é atualmente suportada.

## <a name="cost"></a>Custo

Consulta elástica está incluída no custo de bases de dados de base de dados do Azure SQL. Tenha em atenção que são suportadas topologias de onde as suas bases de dados remotos estão num centro de dados diferente do que o ponto de extremidade de consulta elástica, mas a saída de dados dos bancos de dados remotos é cobrada regularmente [as taxas do Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Executar a sua primeira consulta elástica pode demorar alguns minutos na camada de serviço Standard. Desta vez, é necessária para carregar a funcionalidade de consulta elástica; a carregar o desempenho é aprimorado com escalões de serviço mais elevados e tamanhos de computação.
* Ainda, o script de origens de dados externas ou tabelas externas do SSMS ou o SSDT não é suportado.
* Importar/exportar para o SQL DB ainda não suporta origens de dados externas e tabelas externas. Se precisar de utilizar a importação/exportação, remova estes objetos antes de exportar e, em seguida, voltar a criá-los depois de importar.
* Atualmente a consulta elástica só suporta acesso só de leitura para tabelas externas. No entanto, pode usar todas as funcionalidades T-SQL na base de dados em que a tabela externa está definida. Isso pode ser útil para, por exemplo, manter os resultados temporários, usando, por exemplo, SELECIONE < column_list > em < local_table >, ou para definir os procedimentos armazenados na base de dados consulta elástica que se referem a tabelas externas.
* Exceto nvarchar (Max), os tipos LOB (incluindo tipos geográficos) não são suportados nas definições de tabela externa. Como solução, pode criar uma vista na base de dados remoto que converte o tipo LOB em nvarchar (Max), definir sua tabela externa na vista em vez da tabela base e, em seguida, convertê-lo novamente para o tipo LOB original em suas consultas.
* As colunas do tipo de dados nvarchar (Max) na desativação do conjunto de resultados advanced technics utilizado na implementação de consulta elástica de criação de batches e podem afetar o desempenho de consulta para uma ordem de magnitude ou até mesmo duas ordens de magnitude não canônico em casos de utilização em que grandes quantidade de estão a ser transferidos dados não agregados como resultado da consulta.
* Estatísticas de coluna em tabelas externas não são atualmente suportadas. Estatísticas de tabela são suportadas, mas tem de ser criadas manualmente.
* Consulta elástica funciona apenas com a base de dados do Azure SQL. Não é possível utilizá-lo para consultar o SQL Server no local ou o SQL Server numa VM.

## <a name="feedback"></a>Comentários

Partilhar comentários sobre a sua experiência com consultas elásticas connosco abaixo, nos fóruns do MSDN ou no Stack Overflow. Estamos interessados em todos os tipos de comentários sobre o serviço (defeitos, arestas, intervalos de funcionalidades).

## <a name="next-steps"></a>Passos Seguintes

* Para obter um tutorial de criação de partições vertical, consulte [guia de introdução consultas entre bases de dados (criação de partições verticais)](sql-database-elastic-query-getting-started-vertical.md).
* Para consultas de exemplo de sintaxe e para dados particionados verticalmente, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para obter um tutorial de criação de partições horizontais (fragmentação), consulte [guia de introdução consulta elástica para a criação de partições horizontais (fragmentação)](sql-database-elastic-query-getting-started.md).
* Para consultas de exemplo de sintaxe e para dados particionados horizontalmente, consulte [horizontalmente a consulta particionados dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Ver [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que serve como fragmentos num esquema de partições horizontal.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
