---
title: Visão geral da consulta elástica
description: A consulta elástica permite executar uma consulta Transact-SQL que abrange vários bancos de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 07/01/2019
ms.openlocfilehash: 9566ac7169144d984f9200734c99eb10368b3142
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823745"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Visão geral da consulta elástica do banco de dados SQL do Azure (versão prévia)

O recurso de consulta elástica (em versão prévia) permite que você execute uma consulta Transact-SQL que abrange vários bancos de dados no Azure SQL Database. Ele permite que você execute consultas entre bancos de dados para acessar tabelas remotas e conecte as ferramentas da Microsoft e de terceiros (Excel, Power BI, tableau, etc.) para consultar as camadas de dados com vários bancos. Usando esse recurso, você pode escalar horizontalmente as consultas para as camadas de dados grandes no SQL Database e visualizar os resultados em relatórios de business intelligence (BI).

## <a name="why-use-elastic-queries"></a>Por que usar consultas elásticas

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Consulta em bancos de dados SQL do Azure completamente no T-SQL. Isso permite a consulta somente leitura de bancos de dados remotos e fornece uma opção para os clientes locais atuais do SQL Server para migrar aplicativos usando nomes de três e quatro partes ou servidor vinculado para o banco de dados SQL.

### <a name="available-on-standard-tier"></a>Disponível na camada Standard

A consulta elástica tem suporte nas camadas de serviço Standard e Premium. Consulte a seção sobre limitações de visualização abaixo sobre limitações de desempenho para níveis de serviço inferiores.

### <a name="push-parameters-to-remote-databases"></a>Parâmetros de push para bancos de dados remotos

As consultas elásticas agora podem enviar por push parâmetros SQL para os bancos de dados remotos para execução.

### <a name="stored-procedure-execution"></a>Execução de procedimento armazenado

Execute chamadas de procedimento armazenado remoto ou funções remotas usando o [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilidade

Tabelas externas com consulta elástica podem se referir a tabelas remotas com um esquema ou nome de tabela diferente.

## <a name="elastic-query-scenarios"></a>Cenários de consulta elástica

O objetivo é facilitar os cenários de consulta em que vários bancos de dados contribuem linhas em um único resultado geral. A consulta pode ser composta pelo usuário ou aplicativo diretamente ou indiretamente por meio de ferramentas que estão conectadas ao banco de dados. Isso é especialmente útil ao criar relatórios, usando o BI comercial ou ferramentas de integração de dados ou qualquer aplicativo que não possa ser alterado. Com uma consulta elástica, você pode consultar vários bancos de dados usando a experiência de conectividade SQL Server familiar em ferramentas como Excel, Power BI, tableau ou Cognos.
Uma consulta elástica permite acesso fácil a uma coleção inteira de bancos de dados por meio de consultas emitidas pelo SQL Server Management Studio ou pelo Visual Studio e facilita a consulta entre bancos de dados Entity Framework ou outros ambientes de ORM. A Figura 1 mostra um cenário em que um aplicativo de nuvem existente (que usa a [biblioteca de cliente do banco](sql-database-elastic-database-client-library.md)de dados elástico) é criado em uma camada de dados expandida e uma consulta elástica é usada para relatórios entre bancos de dados.

**Figura 1** Consulta elástica usada na camada de dados expandida

![Consulta elástica usada na camada de dados expandida][1]

Os cenários de cliente para consulta elástica são caracterizados pelas seguintes topologias:

* **Particionamento vertical-consultas entre bancos** de dados (topologia 1): os dados são particionados verticalmente entre um número de databases em uma camada de dado. Normalmente, diferentes conjuntos de tabelas residem em bancos de dados diferentes. Isso significa que o esquema é diferente em bancos de dados diferentes. Por exemplo, todas as tabelas para o inventário estão em um banco de dados enquanto todas as tabelas relacionadas à contabilidade estão em um segundo banco de dados. Casos de uso comuns com essa topologia exigem que uma consulta entre ou compile relatórios entre tabelas em vários bancos de dados.
* **Particionamento horizontal-fragmentação** (topologia 2): os dados são particionados horizontalmente para distribuir linhas em uma camada de dados expandida. Com essa abordagem, o esquema é idêntico em todos os bancos de dados participantes. Essa abordagem também é chamada de "fragmentação". A fragmentação pode ser realizada e gerenciada usando (1) as bibliotecas de ferramentas de banco de dados elástico ou (2) autofragmentação. Uma consulta elástica é usada para consultar ou compilar relatórios em vários fragmentos.

> [!NOTE]
> A consulta elástica funciona melhor para cenários de relatório em que a maior parte do processamento (filtragem, agregação) pode ser executada no lado da fonte externa. Ele não é adequado para operações de ETL em que uma grande quantidade de dados está sendo transferida de banco (es) remoto (s). Para obter cargas de trabalho de relatório pesadas ou cenários de data warehouse com consultas mais complexas, considere também o uso [do Azure SQL data warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Particionamento vertical-consultas entre bancos de dados

Para começar a codificar, consulte [introdução à consulta entre bancos de dados (particionamento vertical)](sql-database-elastic-query-getting-started-vertical.md).

Uma consulta elástica pode ser usada para tornar os dados localizados em um banco de dados SQL disponíveis para outros bancos de dado SQL. Isso permite que as consultas de um banco de dados façam referência a tabelas em qualquer outro banco de dados SQL remoto. A primeira etapa é definir uma fonte de dados externa para cada banco de dado remoto. A fonte de dados externa é definida no banco de dado local do qual você deseja obter acesso a tabelas localizadas no banco de dados remoto. Nenhuma alteração é necessária no banco de dados remoto. Para cenários de particionamento vertical típicos em que bancos de dados diferentes têm esquemas diferentes, as consultas elásticas podem ser usadas para implementar casos de uso comuns, como o acesso a dados de referência e consulta entre bancos.

> [!IMPORTANT]
> Você deve ter a permissão alterar qualquer fonte de dados externa. Essa permissão está incluída com a permissão ALTER DATABASE. As permissões alterar qualquer fonte de dados externa são necessárias para fazer referência à fonte de dados subjacente.
>

**Dados de referência**: a topologia é usada para o gerenciamento de dados de referência. Na figura abaixo, duas tabelas (T1 e T2) com dados de referência são mantidas em um banco de dado dedicado. Usando uma consulta elástica, agora você pode acessar as tabelas T1 e T2 remotamente de outros bancos de dados, conforme mostrado na figura. Use a topologia 1 se as tabelas de referência forem consultas pequenas ou remotas na tabela de referência têm predicados seletivos.

**Figura 2** Particionamento vertical-usando a consulta elástica para consultar dados de referência

![Particionamento vertical-usando a consulta elástica para consultar dados de referência][3]

**Consulta entre bancos de dados**: consultas elásticas permitem casos de uso que exigem consultas em vários bancos de dados SQL. A Figura 3 mostra quatro bancos de dados diferentes: CRM, inventário, RH e produtos. As consultas executadas em um dos bancos de dados também precisam de acesso a um ou todos os outros bancos de dados. Usando uma consulta elástica, você pode configurar seu banco de dados para esse caso executando algumas instruções DDL simples em cada um dos quatro bancos de dados. Após essa configuração única, o acesso a uma tabela remota é tão simples quanto fazer referência a uma tabela local de suas consultas T-SQL ou de suas ferramentas de BI. Essa abordagem é recomendada se as consultas remotas não retornarem resultados grandes.

**Figura 3** Particionamento vertical-usando a consulta elástica para consultar em vários bancos de dados

![Particionamento vertical-usando a consulta elástica para consultar em vários bancos de dados][4]

As etapas a seguir configuram as consultas de banco de dados elástico para cenários de particionamento vertical que exigem acesso a uma tabela localizada em banco de dados SQL remoto com o mesmo esquema:

* [Criar chave mestra](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [Criar credencial do escopo do banco de dados](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [Criar/remover fonte de dados externa](https://msdn.microsoft.com/library/dn935022.aspx) mydataname do tipo **RDBMS**
* [Criar/remover tabela externa](https://msdn.microsoft.com/library/dn935021.aspx) MyTable

Depois de executar as instruções DDL, você pode acessar a tabela remota "mytable" como se fosse uma tabela local. O banco de dados SQL do Azure abre automaticamente uma conexão com o banco de dados remoto, processa sua solicitação no banco de dados remoto e retorna os resultados.

## <a name="horizontal-partitioning---sharding"></a>Particionamento horizontal-fragmentação

Usar a consulta elástica para executar tarefas de relatório em uma camada de dados fragmentada, ou seja, particionada horizontalmente, requer um [mapa de fragmentos de banco](sql-database-elastic-scale-shard-map-management.md) de dados elástico para representar os bancos de dados da camada de dado. Normalmente, apenas um único mapa de fragmentos é usado neste cenário e um banco de dados dedicado com recursos de consulta elástica (nó de cabeçalho) serve como o ponto de entrada para as consultas de relatório. Somente esse banco de dados dedicado precisa acessar o mapa de fragmentos. A Figura 4 ilustra essa topologia e sua configuração com o banco de dados de consulta elástica e o mapa de fragmentos. Os bancos de dados na camada de dados podem ser de qualquer edição ou versão de banco de dado SQL do Azure. Para obter mais informações sobre a biblioteca de cliente do banco de dados elástico e criar mapas de fragmento, consulte [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** Particionamento horizontal-usando a consulta elástica para relatórios sobre camadas de dados fragmentados

![Particionamento horizontal-usando a consulta elástica para relatórios sobre camadas de dados fragmentados][5]

> [!NOTE]
> O banco de dados de consulta elástica (nó de cabeçalho) pode ser um banco de dados separado ou pode ser o mesmo banco de dados que hospeda o mapa de fragmentos.
> Qualquer que seja a configuração que você escolher, verifique se a camada de serviço e o tamanho de computação desse banco de dados são altos o suficiente para lidar com a quantidade esperada de solicitações de logon/consulta.

As etapas a seguir configuram consultas de banco de dados elástico para cenários de particionamento horizontal que exigem acesso a um conjunto de tabelas localizadas em (normalmente) vários bancos de dados SQL remotos:

* [Criar chave mestra](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Criar credencial do escopo do banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Crie um [mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) que represente sua camada de dados usando a biblioteca de cliente do Database elástico.
* [Criar/remover fonte de dados externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydataname do tipo **SHARD_MAP_MANAGER**
* [Criar/remover tabela externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) MyTable

Depois de executar essas etapas, você poderá acessar a tabela particionada horizontalmente "mytable" como se fosse uma tabela local. O banco de dados SQL do Azure abre automaticamente várias conexões paralelas aos bancos de dados remotos em que as tabelas são armazenadas fisicamente, processa as solicitações nos bancos de dados remotos e retorna os resultados.
Mais informações sobre as etapas necessárias para o cenário de particionamento horizontal podem ser encontradas em [consulta elástica para particionamento horizontal](sql-database-elastic-query-horizontal-partitioning.md).

Para começar a codificar, consulte [introdução à consulta elástica para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Consultas T-SQL

Depois de definir suas fontes de dados externas e suas tabelas externas, você pode usar as cadeias de conexão SQL Server regulares para se conectar aos bancos de dado em que você definiu as tabelas externas. Em seguida, você pode executar instruções T-SQL em suas tabelas externas nessa conexão com as limitações descritas abaixo. Você pode encontrar mais informações e exemplos de consultas T-SQL nos tópicos de documentação para [particionamento horizontal](sql-database-elastic-query-horizontal-partitioning.md) e [particionamento vertical](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Você pode usar as cadeias de conexão SQL Server regulares para conectar seus aplicativos e as ferramentas de integração de dados e BI a bancos que têm tabelas externas. Verifique se SQL Server tem suporte como uma fonte de dados para sua ferramenta. Uma vez conectado, consulte o banco de dados de consulta elástica e as tabelas externas nesse banco de dados, assim como faria com qualquer outro banco de dados SQL Server ao qual você se conecta com sua ferramenta.

> [!IMPORTANT]
> Atualmente, não há suporte para a autenticação usando Azure Active Directory com consultas elásticas.

## <a name="cost"></a>Custo

A consulta elástica está incluída no custo dos bancos de dados do banco de dados SQL do Azure. Observe que as topologias em que os bancos de dados remotos estão em um data center diferente do ponto de extremidade de consulta elástica têm suporte, mas a saída de dados de bancos de dados remotos é cobrada regularmente pelas [tarifas do Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitações de visualização

* A execução da sua primeira consulta elástica pode levar alguns minutos na camada de serviço Standard. Esse tempo é necessário para carregar a funcionalidade de consulta elástica; o carregamento de desempenho melhora com as camadas de serviço e os tamanhos de computação mais altos.
* Ainda não há suporte para scripts de fontes de dados externas ou tabelas externas do SSMS ou SSDT.
* A importação/exportação para o BD SQL ainda não dá suporte a fontes de dados externas e tabelas externas. Se você precisar usar a importação/exportação, Remova esses objetos antes de exportá-los e recriá-los após a importação.
* Atualmente, a consulta elástica só dá suporte ao acesso somente leitura a tabelas externas. No entanto, você pode usar a funcionalidade completa do T-SQL no banco de dados em que a tabela externa está definida. Isso pode ser útil para, por exemplo, manter os resultados temporários usando, como SELECT < column_list > em < local_table > ou para definir procedimentos armazenados no banco de dados de consulta elástica que se referem a tabelas externas.
* Exceto nvarchar (max), os tipos LOB (incluindo tipos espaciais) não têm suporte em definições de tabela externa. Como alternativa, você pode criar uma exibição no banco de dados remoto que converte o tipo LOB em nvarchar (max), definir sua tabela externa na exibição em vez da tabela base e, em seguida, convertê-la novamente no tipo LOB original em suas consultas.
* Colunas de tipo de dados nvarchar (max) no conjunto de resultados desabilite o envio em lote avançado técnicas usado na implementação de consulta elástica e pode afetar o desempenho da consulta para uma ordem de magnitude, ou até duas ordens de magnitude em casos de uso não canônicos em que grande quantidade de os dados não agregados estão sendo transferidos como resultado da consulta.
* Atualmente, não há suporte para estatísticas de coluna em tabelas externas. As estatísticas de tabela têm suporte, mas precisam ser criadas manualmente.
* A consulta elástica funciona somente com o banco de dados SQL do Azure. Você não pode usá-lo para consultar SQL Server locais ou SQL Server em uma VM.

## <a name="feedback"></a>Comentários

Compartilhe comentários sobre sua experiência com consultas elásticas com os EUA abaixo, nos fóruns do MSDN ou em Stack Overflow. Estamos interessados em todos os tipos de comentários sobre o serviço (defeitos, bordas aproximadas, lacunas de recursos).

## <a name="next-steps"></a>Passos seguintes

* Para obter um tutorial de particionamento vertical, consulte [introdução à consulta entre bancos de dados (particionamento vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para obter sintaxe e exemplos de consultas para dados particionados verticalmente, consulte [consultando dados particionados verticalmente)](sql-database-elastic-query-vertical-partitioning.md)
* Para obter um tutorial horizontal de particionamento (fragmentação), consulte [introdução à consulta elástica para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).
* Para obter sintaxe e exemplos de consultas para dados particionados horizontalmente, consulte [consultando dados particionados horizontalmente)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) para obter um procedimento armazenado que executa uma instrução TRANSACT-SQL em um único banco de dados SQL do Azure remoto ou conjunto de bancos de dados que serve como fragmentos em um esquema de particionamento horizontal.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
