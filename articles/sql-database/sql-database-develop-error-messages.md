---
title: Códigos de erro do SQL-erro de conexão do banco de dados | Microsoft Docs
description: 'Saiba mais sobre códigos de erro do SQL para aplicativos cliente do banco de dados SQL, como erros comuns de conexão de banco de dados, problemas de cópia de banco de dados e erros gerais. '
keywords: código de erro do SQL, acesso SQL, erro de conexão do banco de dados, códigos de erro do SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/02/2019
ms.openlocfilehash: 8d4e7fa314ce3a5f8534e7742880114ccc7f7144
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598133"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Códigos de erro do SQL para aplicativos cliente do banco de dados SQL: erros de conexão do banco de dados e outros problemas

Este artigo lista os códigos de erro do SQL para aplicativos cliente do banco de dados SQL, incluindo erros de conexão de banco de dados, erros transitórios (também chamados de falhas transitórias), erros de governança de recursos, problemas de cópia de banco de dados, pool elástico e outros erros. A maioria das categorias é específica para o banco de dados SQL do Azure e não se aplica a Microsoft SQL Server. Consulte também [mensagens de erro do sistema](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erros de conexão de banco de dados, erros transitórios e outros erros temporários

A tabela a seguir aborda os códigos de erro do SQL para erros de perda de conexão e outros erros transitórios que você pode encontrar quando seu aplicativo tenta acessar o banco de dados SQL. Para obter tutoriais de introdução sobre como se conectar ao banco de dados SQL do Azure, consulte [conectando-se ao banco de dados SQL do Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erros de conexão de banco de dados mais comuns e erros transitórios de falha

A infraestrutura do Azure tem a capacidade de reconfigurar dinamicamente os serviços quando surgem cargas de trabalho pesadas no serviço Base de Dados SQL.  Este comportamento dinâmico poderá fazer com que o seu programa cliente perca a ligação à Base de Dados SQL. Esse tipo de condição de erro é chamado de *falha transitória*.

É altamente recomendável que o programa cliente tenha a lógica de repetição para que possa restabelecer uma conexão depois de dar o tempo de falha transitória para corrigir a si mesmo.  É recomendável que você adie por 5 segundos antes da primeira tentativa. Repetir após um atraso menor que 5 segundos riscos sobrecarregam o serviço de nuvem. Para cada repetição subsequente, o atraso deve aumentar exponencialmente, até um máximo de 60 segundos.

Erros transitórios normalmente são manifestados como uma das seguintes mensagens de erro de seus programas cliente:

* O banco de dados &lt;db_name &gt; no servidor &lt;Azure_instance &gt; não está disponível no momento. Repita a conexão mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão de &lt;session_id &gt;
* O banco de dados &lt;db_name &gt; no servidor &lt;Azure_instance &gt; não está disponível no momento. Repita a conexão mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão do &lt;session_id &gt;. (Microsoft SQL Server, erro: 40613)
* Uma conexão existente foi fechada forçosamente pelo host remoto.
* System. Data. Entity. Core. EntityCommandExecutionException: ocorreu um erro ao executar a definição de comando. Consulte a exceção interna para obter detalhes. ---> System. Data. SqlClient. SqlException: ocorreu um erro no nível de transporte ao receber os resultados do servidor. (provedor: provedor de sessão, erro: 19-a conexão física não é utilizável)
* Uma tentativa de conexão a um banco de dados secundário falhou porque o banco de dados está no processo de reconfiguração e está ocupado aplicando novas páginas enquanto estiver no meio de uma transação ativa no banco de dados primário. 

Para obter exemplos de código de lógica de repetição, consulte:

* [Bibliotecas de conexão para o banco de dados SQL e SQL Server](sql-database-libraries.md) 
* [Ações para corrigir erros de conexão e erros transitórios no banco de dados SQL](sql-database-connectivity-issues.md)

Uma discussão sobre o *período de bloqueio* para clientes que usam o ADO.net está disponível no [ADO.net (pooling de conexão SQL Server)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de erro transitórios

Os seguintes erros são transitórios e devem ser repetidos na lógica do aplicativo: 

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir o banco de dados "%. &#x2a;ls "solicitado pelo logon. Falha no logon. Para obter mais informações, consulte [erros 4000 a 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro% d.<br/><br/>Você recebe esse erro quando o serviço está inativo devido a atualizações de software ou hardware, falhas de hardware ou quaisquer outros problemas de failover. O código de erro (% d) [incorporado na mensagem de erro 40197](sql-database-develop-error-messages.md#embedded-error-codes) fornece informações adicionais sobre o tipo de falha ou failover ocorrido. Alguns exemplos dos códigos de erro são inseridos dentro da mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Reconectar-se ao servidor do banco de dados SQL automaticamente o conecta a uma cópia íntegra do seu banco de dados. Seu aplicativo deve capturar o erro 40197, registrar o código de erro inserido (% d) na mensagem para solução de problemas e tentar reconectar-se ao banco de dados SQL até que os recursos estejam disponíveis e sua conexão seja estabelecida novamente. Para obter mais informações, consulte [erros transitórios](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |O serviço está ocupado no momento. Repita a solicitação após 10 segundos. ID do incidente:% ls. Código:% d. Para obter mais informações, veja: <br/>limites de[recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Banco de dados '%. &#x2a;ls "no servidor"%. &#x2a;ls ' não está disponível no momento. Repita a conexão mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento de sessão de '%. &#x2a;ls '.<br/><br/> Esse erro pode ocorrer se já houver uma conexão de administrador dedicada (DAC) existente estabelecida com o banco de dados. Para obter mais informações, consulte [erros transitórios](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Não é possível processar o pedido. Não há recursos suficientes para processar a solicitação.<br/><br/>O serviço está ocupado no momento. Repita a solicitação mais tarde. Para obter mais informações, veja: <br/>limites de[recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Não é possível processar a solicitação de criação ou atualização. Muitas operações de criação ou atualização em andamento para a assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações de criação ou atualização para sua assinatura ou servidor. Atualmente, as solicitações estão bloqueadas para otimização de recursos. Consulte [Sys. dm _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Aguarde até que as solicitações de criação ou atualização pendentes sejam concluídas ou exclua uma de suas solicitações pendentes e repita a solicitação mais tarde. Para obter mais informações, veja: <br/>limites de[recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Não é possível processar o pedido. Muitas operações em andamento para a assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações para esta assinatura. Atualmente, as solicitações estão bloqueadas para otimização de recursos. Consulte [Sys. dm _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para obter o status da operação. Aguarde até que as solicitações pendentes sejam concluídas ou exclua uma de suas solicitações pendentes e repita a solicitação mais tarde. Para obter mais informações, veja: <br/>limites de[recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Falha de logon para leitura-secundária devido a longa espera em ' HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING '. A réplica não está disponível para logon porque as versões de linha estão ausentes para transações que estavam em andamento quando a réplica foi reciclada. O problema pode ser resolvido recuperando ou confirmando as transações ativas na réplica primária. As ocorrências dessa condição podem ser minimizadas, evitando transações de gravação longas no primário. |

## <a name="embedded-error-codes"></a>Códigos de erro inseridos

Os seguintes erros são inseridos no código de erro mais geral 40197:

```
The service has encountered an error processing your request. Please try again. Error code %d.
```

| Código de erro | Gravidade | Descrição | 
| ---:| ---:|:---|
|  1104 |17 |O TEMPDB ficou sem espaço durante o despejo. Crie espaço descartando objetos e/ou reescreva a consulta para consumir menos linhas. Se o problema ainda persistir, considere atualizar para um objetivo de nível de serviço mais alto.|
| 40020 |16 |O banco de dados está em transição e as transações estão sendo encerradas.|
| 40143 |16 |A réplica que o nó de dados hospeda para a partição solicitada não é primária.|
| 40166 |16 |Uma reconfiguração de CloudDB está em andamento e todas as novas transações de usuário são anuladas.|
| 40540 |16 |A transação foi anulada porque o banco de dados foi movido para o modo somente leitura. Esta é uma situação temporária e, em seguida, repita a operação.|

Os detalhes sobre outros erros inseridos podem ser encontrados consultando `sys.messages`:

```sql
SELECT * FROM sys.[messages] WHERE [message_id] = <error_code>
```

## <a name="database-copy-errors"></a>Erros de cópia do banco de dados

Os erros a seguir podem ser encontrados ao copiar um banco de dados no banco de dados SQL do Azure. Para mais informações, consulte [Copiar uma Base de Dados SQL do Azure](sql-database-copy.md).

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |Cliente com endereço IP '%. &#x2a;ls ' está temporariamente desabilitado. |
| 40637 |16 |A cópia do banco de dados de criação está desabilitada no momento. |
| 40561 |16 |Falha na cópia do banco de dados. O banco de dados de origem ou de destino não existe. |
| 40562 |16 |Falha na cópia do banco de dados. O banco de dados de origem foi Descartado. |
| 40563 |16 |Falha na cópia do banco de dados. O banco de dados de destino foi Descartado. |
| 40564 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40565 |16 |Falha na cópia do banco de dados. Não é permitida mais de uma cópia de banco de dados simultânea da mesma fonte. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40566 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40567 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40568 |16 |Falha na cópia do banco de dados. O banco de dados de origem tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40569 |16 |Falha na cópia do banco de dados. O banco de dados de destino tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40570 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40571 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |

## <a name="resource-governance-errors"></a>Erros de governança de recursos

Os erros a seguir são causados pelo uso excessivo de recursos ao trabalhar com o banco de dados SQL do Azure. Por exemplo:

* Uma transação foi aberta por muito tempo.
* Uma transação está mantendo um número excessivo de bloqueios.
* Um aplicativo está consumindo muita memória.
* Um aplicativo está consumindo muito espaço `TempDb`.

Tópicos relacionados:

* Para obter mais informações, veja:
  * [Limites de recursos do servidor de banco de dados](sql-database-resource-limits-database-server.md)
  * [Limites baseados em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)
  * [Limites baseados em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)
  * [limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)
  * [limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). 

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |ID do recurso:% d. O limite de% s para o banco de dados é% d e foi atingido. Para obter mais informações, consulte [limites de recursos do banco de dados SQL para bancos de dados individuais e em pool](sql-database-resource-limits-database-server.md).<br/><br/>A ID do recurso indica o recurso que atingiu o limite. Para threads de trabalho, a ID de recurso = 1. Para sessões, a ID do recurso = 2.<br/><br/>Para obter mais informações sobre esse erro e como resolvê-lo, consulte: <br/>limites de[recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |ID do recurso:% d. A garantia mínima de% s é% d, o limite máximo é% d, e o uso atual do banco de dados é% d. No entanto, o servidor está muito ocupado no momento para dar suporte a solicitações maiores que% d para este banco de dados. A ID do recurso indica o recurso que atingiu o limite. Para threads de trabalho, a ID de recurso = 1. Para sessões, a ID do recurso = 2. Para obter mais informações, veja: <br/>limites de[recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). <br/>Caso contrário, tente novamente mais tarde. |
| 40544 |20 |O banco de dados atingiu sua cota de tamanho. Particione ou exclua dados, remova índices ou consulte a documentação para obter possíveis resoluções. Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).|
| 40549 |16 |A sessão foi encerrada porque você tem uma transação de longa execução. Tente encurtar sua transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |A sessão foi encerrada porque adquiriu muitos bloqueios. Tente ler ou modificar menos linhas em uma única transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |A sessão foi encerrada devido ao excesso de uso de `TEMPDB`. Tente modificar sua consulta para reduzir o uso de espaço de tabela temporário.<br/><br/>Se você estiver usando objetos temporários, conservar espaço no banco de dados `TEMPDB` removendo objetos temporários depois que eles não forem mais necessários para a sessão. Para obter mais informações sobre o uso de tempdb no banco de dados SQL, consulte [banco de dados tempdb no banco de dados SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |A sessão foi encerrada devido ao uso excessivo de espaço no log de transações. Tente modificar menos linhas em uma única transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](sql-database-use-batching-to-improve-performance.md).<br/><br/>Se você executar inserções em massa usando o utilitário `bcp.exe` ou a classe `System.Data.SqlClient.SqlBulkCopy`, tente usar as opções `-b batchsize` ou `BatchSize` para limitar o número de linhas copiadas para o servidor em cada transação. Se você estiver recriando um índice com a instrução `ALTER INDEX`, tente usar a opção `REBUILD WITH ONLINE = ON`. Para obter informações sobre tamanhos de log de transações para o modelo de compra vCore, consulte: <br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/>A redução do número de operações de `ORDER BY` e `GROUP BY` em seu código Transact-SQL reduz os requisitos de memória de sua consulta. Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Erros de pool elástico

Os seguintes erros estão relacionados à criação e ao uso de pools elásticos:

| Código de erro | Gravidade | Descrição | Ação corretiva |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |O pool elástico atingiu seu limite de armazenamento. O uso de armazenamento para o pool elástico não pode exceder (% d) MBs. Tentativa de gravar dados em um banco de dado quando o limite de armazenamento do pool elástico foi atingido. Para obter informações sobre limites de recursos, consulte: <br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Considere aumentar as DTUs de e/ou adicionar armazenamento ao pool elástico, se possível, para aumentar seu limite de armazenamento, reduzir o armazenamento usado por bancos de dados individuais dentro do pool elástico ou Remover bancos de dados do pool elástico. Para o dimensionamento do pool elástico, consulte [dimensionar recursos do pool elástico](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |A garantia mínima de% s é% d, o limite máximo é% d, e o uso atual do banco de dados é% d. No entanto, o servidor está muito ocupado no momento para dar suporte a solicitações maiores que% d para este banco de dados. Para obter informações sobre limites de recursos, consulte: <br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). <br/> Caso contrário, tente novamente mais tarde. DTU/mínimo de vCore por banco de dados; DTU/vCore máximo por banco de dados. O número total de trabalhos simultâneos (solicitações) em todos os bancos de dados no pool elástico tentou exceder o limite do pool. |Considere aumentar as DTUs ou vCores do pool elástico, se possível, para aumentar seu limite de trabalho ou Remover bancos de dados do pool elástico. |
| 40844 | 16 |O banco de dados '% ls ' no servidor '% ls ' é um banco de dados de edição '% ls ' em um pool elástico e não pode ter uma relação de cópia contínua.  |N/A |
| 40857 | 16 |Pool elástico não encontrado para o servidor: '% ls ', nome do pool elástico: '% ls '. O pool elástico especificado não existe no servidor especificado. | Forneça um nome de pool elástico válido. |
| 40858 | 16 |O pool elástico '% ls ' já existe no servidor: '% ls '. O pool elástico especificado já existe no servidor de banco de dados SQL especificado. | Forneça o novo nome do pool elástico. |
| 40859 | 16 |O pool elástico não dá suporte à camada de serviço '% ls '. A camada de serviço especificada não tem suporte para provisionamento de pool elástico. |Forneça a edição correta ou deixe a camada de serviço em branco para usar a camada de serviço padrão. |
| 40860 | 16 |A combinação do pool elástico '% ls ' e do objetivo de serviço '% ls ' é inválida. O pool elástico e a camada de serviço podem ser especificados juntos somente se o tipo de recurso for especificado como ' ElasticPool '. |Especifique a combinação correta de pool elástico e camada de serviço. |
| 40861 | 16 |A edição do banco de dados '%. *ls ' não pode ser diferente da camada de serviço do pool elástico, que é '%.* ls '. A edição do banco de dados é diferente da camada de serviço do pool elástico. |Não especifique uma edição de banco de dados que seja diferente da camada de serviço do pool elástico.  Observe que a edição do banco de dados não precisa ser especificada. |
| 40862 | 16 |O nome do pool elástico deve ser especificado se o objetivo do serviço do pool elástico for especificado. O objetivo do serviço de pool elástico não identifica exclusivamente um pool elástico. |Especifique o nome do pool elástico se estiver usando o objetivo de serviço do pool elástico. |
| 40864 | 16 |As DTUs para o pool elástico devem ser de pelo menos (% d) DTUs para a camada de serviço '%. * ls '. Tentando definir as DTUs para o pool elástico abaixo do limite mínimo. |Repita a configuração das DTUs para o pool elástico para pelo menos o limite mínimo. |
| 40865 | 16 |As DTUs para o pool elástico não podem exceder (% d) DTUs para a camada de serviço '%. * ls '. Tentando definir DTUs para o pool elástico acima do limite máximo. |Repita a configuração das DTUs para o pool elástico para não maior que o limite máximo. |
| 40867 | 16 |O máximo de DTU por banco de dados deve ser pelo menos (% d) para a camada de serviço '%. * ls '. Tentando definir o máximo de DTU por banco de dados abaixo do limite com suporte. | Considere usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40868 | 16 |O máximo de DTU por banco de dados não pode exceder (% d) para a camada de serviço '%. * ls '. Tentando definir o máximo de DTU por banco de dados além do limite com suporte. | Considere usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40870 | 16 |O mínimo de DTU por banco de dados não pode exceder (% d) para a camada de serviço '%. * ls '. Tentando definir o mínimo de DTU por banco de dados além do limite com suporte. | Considere usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40873 | 16 |O número de bancos de dados (% d) e o mínimo de DTU por banco (% d) não podem exceder as DTUs do pool elástico (% d). Tentativa de especificar o mínimo de DTU para bancos de dados no pool elástico que excede as DTUs do pool elástico. | Considere aumentar as DTUs do pool elástico, ou diminuir o mínimo de DTU por banco de dados, ou diminuir o número de banco de dados no pool elástico. |
| 40877 | 16 |Um pool elástico não pode ser excluído, a menos que não contenha nenhum banco de dados. O pool elástico contém um ou mais bancos de dados e, portanto, não pode ser excluído. |Remova os bancos de dados do pool elástico para excluí-los. |
| 40881 | 16 |O pool elástico '%. * ls ' atingiu seu limite de contagem de banco de dados.  O limite de contagem de banco de dados para o pool elástico não pode exceder (% d) para um pool elástico com DTUs (% d). Tentativa de criar ou adicionar banco de dados ao pool elástico quando o limite de contagem de banco de dados do pool elástico foi atingido. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar seu limite de banco de dados ou Remover bancos de dados do pool elástico. |
| 40889 | 16 |O limite de DTUs ou de armazenamento para o pool elástico '%. * ls ' não pode ser diminuído, pois isso não forneceria espaço de armazenamento suficiente para seus bancos de dados. Tentando diminuir o limite de armazenamento do pool elástico abaixo de seu uso de armazenamento. | Considere reduzir o uso de armazenamento de bancos de dados individuais no pool elástico ou Remover bancos de dados do pool para reduzir seu limite de DTUs ou de armazenamento. |
| 40891 | 16 |O mínimo de DTU por banco de dados (% d) não pode exceder o máximo de DTU por banco de dados (% d). Tentativa de definir o mínimo de DTU por banco de dados acima do máximo de DTU por banco de dados. |Certifique-se de que o mínimo de DTU por bancos de dados não exceda o máximo de DTU por Database. |
| TBD | 16 |O tamanho do armazenamento de um banco de dados individual em um pool elástico não pode exceder o tamanho máximo permitido pelo pool elástico da camada de serviço '%. * ls '. O tamanho máximo do banco de dados excede o tamanho máximo permitido pela camada de serviço do pool elástico. |Defina o tamanho máximo do banco de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do pool elástico. |

Tópicos relacionados:

* [Criar um pool elástico (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Gerenciar um pool elástico (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Criar um pool elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Monitorar e gerenciar um pool elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Erros gerais

Os seguintes erros não se enquadram em nenhuma categoria anterior.

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(AdministratorLogin) não é um nome válido porque contém caracteres inválidos.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |O início de sessão falhou. O logon é de um domínio não confiável e não pode ser usado com a autenticação do Windows .%. &#x2a;ls (logons do Windows não têm suporte nesta versão do SQL Server.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Falha de logon do usuário '%. &#x2a;ls ' .%. &#x2a;% ls. &#x2a;ls (o logon falhou para o usuário "%&#x2a; . ls ".) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Falha de logon do usuário '%. &#x2a;ls '. Motivo: a conta está desabilitada .%. &#x2a;ls |
| 40014 |16 |Vários bancos de dados não podem ser usados na mesma transação. |
| 40054 |16 |Não há suporte para tabelas sem um índice clusterizado nesta versão do SQL Server. Crie um índice clusterizado e tente novamente. |
| 40133 |15 |Esta operação não tem suporte nesta versão do SQL Server. |
| 40506 |16 |O SID especificado é inválido para esta versão do SQL Server. |
| 40507 |16 |'%. &#x2a;ls ' não pode ser invocado com parâmetros nesta versão do SQL Server. |
| 40508 |16 |Não há suporte para a instrução USE para alternar entre bancos de dados. Use uma nova conexão para se conectar a um banco de dados diferente. |
| 40510 |16 |Instrução '%. &#x2a;ls ' não tem suporte nesta versão do SQL Server |
| 40511 |16 |Função interna '%. &#x2a;ls ' não tem suporte nesta versão do SQL Server. |
| 40512 |16 |Não há suporte para o recurso preterido '% ls ' nesta versão do SQL Server. |
| 40513 |16 |Variável de servidor '%. &#x2a;ls ' não tem suporte nesta versão do SQL Server. |
| 40514 |16 |'% ls ' não tem suporte nesta versão do SQL Server. |
| 40515 |16 |Referência ao nome do banco de dados e/ou servidor em '%. &#x2a;ls ' não tem suporte nesta versão do SQL Server. |
| 40516 |16 |Não há suporte para objetos temporários globais nesta versão do SQL Server. |
| 40517 |16 |Opção de palavra-chave ou instrução '%. &#x2a;ls ' não tem suporte nesta versão do SQL Server. |
| 40518 |16 |Comando DBCC '%. &#x2a;ls ' não tem suporte nesta versão do SQL Server. |
| 40520 |16 |A classe protegível '% S_MSG ' não tem suporte nesta versão do SQL Server. |
| 40521 |16 |A classe protegível '% S_MSG ' não tem suporte no escopo do servidor nesta versão do SQL Server. |
| 40522 |16 |Entidade de segurança do banco de dados '%. &#x2a;não há suporte para o tipo nesta versão do SQL Server. |
| 40523 |16 |Usuário implícito '%. &#x2a;não há suporte para a criação nesta versão do SQL Server. Crie explicitamente o usuário antes de usá-lo. |
| 40524 |16 |Tipo de dados '%. &#x2a;ls ' não tem suporte nesta versão do SQL Server. |
| 40525 |16 |COM '%. ls ' não tem suporte nesta versão do SQL Server. |
| 40526 |16 |'%. &#x2a;o provedor de conjunto de linhas não tem suporte nesta versão do SQL Server. |
| 40527 |16 |Não há suporte para servidores vinculados nesta versão do SQL Server. |
| 40528 |16 |Os usuários não podem ser mapeados para certificados, chaves assimétricas ou logons do Windows nesta versão do SQL Server. |
| 40529 |16 |Função interna '%. &#x2a;não há suporte para ls no contexto de representação nesta versão do SQL Server. |
| 40532 |11 |Não é possível abrir o servidor "%. &#x2a;ls "solicitado pelo logon. Falha no logon. |
| 40553 |16 |A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/> Reduzir o número de operações de `ORDER BY` e `GROUP BY` em seu código Transact-SQL ajuda a reduzir os requisitos de memória de sua consulta. |
| 40604 |16 |Não foi possível criar/alterar o banco de dados porque ele excederia a cota do servidor. |
| 40606 |16 |Não há suporte para a anexação de bancos de dados nesta versão do SQL Server. |
| 40607 |16 |Não há suporte para logons do Windows nesta versão do SQL Server. |
| 40611 |16 |Os servidores podem ter no máximo 128 regras de firewall definidas. |
| 40614 |16 |O endereço IP inicial da regra de firewall não pode exceder o endereço IP final. |
| 40615 |16 |Não é possível abrir o servidor ' {0} ' solicitado pelo logon. O cliente com o endereço IP ' {1} ' não tem permissão para acessar o servidor.<br /><br />Para habilitar o acesso, use o portal do banco de dados SQL ou execute o SP \_set \_firewall \_rule no banco de dados mestre para criar uma regra de firewall para esse endereço IP ou intervalo de endereços. Pode levar até cinco minutos para que essa alteração entre em vigor. |
| 40617 |16 |O nome da regra de firewall que começa com (nome da regra) é muito longo. O comprimento máximo é 128. |
| 40618 |16 |O nome da regra de firewall não pode ficar vazio. |
| 40620 |16 |Falha no logon do usuário "%. &#x2a;ls ". Falha na alteração da senha. Não há suporte para a alteração de senha durante o logon nesta versão do SQL Server. |
| 40627 |20 |A operação no servidor ' {0} ' e no banco de dados ' {1} ' está em andamento. Aguarde alguns minutos antes de tentar novamente. |
| 40630 |16 |Falha na validação da senha. A senha não atende aos requisitos da política porque ela é muito curta. |
| 40631 |16 |A senha que você especificou é muito longa. A senha não deve ter mais de 128 caracteres. |
| 40632 |16 |Falha na validação da senha. A senha não atende aos requisitos da política porque ela não é complexa o suficiente. |
| 40636 |16 |Não é possível usar um nome de banco de dados reservado '%. &#x2a;ls ' nesta operação. |
| 40638 |16 |ID de assinatura inválida (Subscription-ID). A assinatura não existe. |
| 40639 |16 |A solicitação não está em conformidade com o esquema: (erro de esquema). |
| 40640 |20 |O servidor encontrou uma exceção inesperada. |
| 40641 |16 |O local especificado é inválido. |
| 40642 |17 |O servidor está muito ocupado no momento. Tente novamente mais tarde. |
| 40643 |16 |O valor do cabeçalho x-MS-Version especificado é inválido. |
| 40644 |14 |Falha ao autorizar o acesso à assinatura especificada. |
| 40645 |16 |ServerName (ServerName) não pode ser vazio ou nulo. Ele só pode ser composto de letras minúsculas ' a-z ', os números 0-9 e hífen. O hífen pode não ser o início ou o fim do nome. |
| 40646 |16 |A ID da assinatura não pode estar vazia. |
| 40647 |16 |A assinatura (Subscription-ID) não tem servidor (ServerName). |
| 40648 |17 |Muitas solicitações foram executadas. Tente novamente mais tarde. |
| 40649 |16 |Tipo de conteúdo inválido especificado. Somente o aplicativo/XML tem suporte. |
| 40650 |16 |A assinatura (Subscription-ID) não existe ou não está pronta para a operação. |
| 40651 |16 |Falha ao criar o servidor porque a assinatura (Subscription-ID) está desabilitada. |
| 40652 |16 |Não é possível mover ou criar o servidor. A assinatura (Subscription-ID) excederá a cota do servidor. |
| 40671 |17 |Falha de comunicação entre o gateway e o serviço de gerenciamento. Tente novamente mais tarde. |
| 40852 |16 |Não é possível abrir o banco de dados '%. \*ls ' no servidor '%. \*ls ' solicitado pelo logon. O acesso ao banco de dados só é permitido usando uma cadeia de conexão habilitada para segurança. Para acessar esse banco de dados, modifique suas cadeias de conexão para conter ' seguro ' no FQDN do servidor-' nome do servidor '. Database. Windows. NET deve ser modificado para ' Server Name '. Database. `secure`. windows.net. |
| 40914 | 16 | Não é possível abrir o servidor ' *[Server-Name]* ' solicitado pelo logon. O cliente não tem permissão para acessar o servidor.<br /><br />Para corrigir, considere adicionar uma [regra de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |O sistema de SQL Azure está sob carga e está colocando um limite superior em operações CRUD de BD simultâneas para um único servidor de banco de dados SQL (por exemplo, criar banco de dados). O servidor especificado na mensagem de erro excedeu o número máximo de conexões simultâneas. Tente novamente mais tarde. |
| 45169 |16 |O sistema do SQL Azure está sob carga e está colocando um limite superior no número de operações CRUD de servidor simultâneas para uma única assinatura (por exemplo, criar servidor). A assinatura especificada na mensagem de erro excedeu o número máximo de conexões simultâneas e a solicitação foi negada. Tente novamente mais tarde. |

## <a name="next-steps"></a>Passos seguintes

* Leia sobre os [recursos do banco de dados SQL do Azure](sql-database-features.md).
* Leia sobre o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
* Leia sobre o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

