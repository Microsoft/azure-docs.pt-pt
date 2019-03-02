---
title: Códigos de erro do SQL - erro de ligação de base de dados | Documentos da Microsoft
description: 'Saiba mais sobre códigos de erro SQL para aplicações de cliente da base de dados SQL, como erros de ligação de base de dados comuns, problemas de cópia do banco de dados e erros gerais. '
keywords: código de erro de SQL, sql de acesso, erro de ligação de base de dados, códigos de erro de sql
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/01/2019
ms.openlocfilehash: 3e31842cf4b6afa2117ea71c644b0376e8434bd0
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247312"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Códigos de erro SQL para aplicações de cliente da base de dados SQL: Erros de ligação de base de dados e outros problemas

Este artigo apresenta uma lista de códigos de erro SQL para aplicações de cliente de base de dados SQL, incluindo erros de ligação de base de dados, os erros transitórios (também chamados de falhas transitórias), erros de governação de recursos, problemas de cópia do banco de dados, conjunto elástico e outros erros. A maioria das categorias são específicas para a base de dados do Azure SQL e não se aplicam ao Microsoft SQL Server. Consulte também [mensagens de erro do sistema](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erros de ligação de base de dados, de erros transitórios e de outros erros temporários

A tabela seguinte abrange os códigos de erro SQL para erros de perda de ligação e outros erros transitórios que poderão surgir quando a aplicação tenta aceder a base de dados SQL. Para obter tutoriais de introdução sobre como ligar à base de dados do Azure SQL, consulte [ligar à base de dados do Azure SQL](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erros de ligação de base de dados mais comuns e erros de falhas transitórias

A infraestrutura do Azure tem a capacidade de reconfigurar os servidores dinamicamente quando surgem de cargas de trabalho pesadas no serviço de base de dados SQL.  Este comportamento dinâmico pode fazer com que o seu programa cliente perca a ligação à base de dados SQL. Esse tipo de condição de erro é chamado um *falhas transitórias*.

É altamente recomendável que o seu programa cliente tem a lógica de repetição para que ele foi possível restabelecer uma ligação depois de fornecer o tempo de falhas transitórias para corrigir-se.  Recomendamos que atraso por 5 segundos antes de sua primeira repetição. Repetir após um atraso menor do que os riscos de 5 segundos sobrecarregar o serviço em nuvem. Para cada repetição posterior, o atraso deve aumentar exponencialmente, até um máximo de 60 segundos.

Erros de falhas transitórias normalmente manifestam como uma das seguintes mensagens de erro de seus programas do cliente:

* Base de dados &lt;db_name&gt; no servidor &lt;Azure_instance&gt; não está atualmente disponível. Tente novamente a ligação mais tarde. Se o problema persistir, contacte o suporte técnico e forneça o ID de rastreio de sessão de &lt;session_id&gt;
* Base de dados &lt;db_name&gt; no servidor &lt;Azure_instance&gt; não está atualmente disponível. Tente novamente a ligação mais tarde. Se o problema persistir, contacte o suporte técnico e forneça o ID de rastreio de sessão de &lt;session_id&gt;. (Microsoft SQL Server, Error: 40613)
* Uma ligação existente foi forçado a fechar pelo anfitrião remoto.
* System.Data.Entity.Core.EntityCommandExecutionException: Ocorreu um erro ao executar a definição de comando. Veja a exceção interna para obter detalhes. ---> System.Data.SqlClient.SqlException: Ocorreu um erro de nível de transporte ao receber os resultados do servidor. (fornecedor: Fornecedor de sessão, erro: 19 - ligação física não é utilizável)
* Uma tentativa de ligação para uma base de dados secundário falhou porque a base de dados está no processo de reconfiguração, é ocupado aplicar novas páginas no meio de uma transação ativa na base de dados primária. 

Para obter exemplos de código da lógica de repetição, consulte:

* [Bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md) 
* [Ações para corrigir erros de ligação e erros transitórios na base de dados SQL](sql-database-connectivity-issues.md)

Uma discussão sobre o *período de bloqueio* para clientes que usam o ADO.NET está disponível no [SQL Server ligação agrupamento (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de erro de falhas transitórias

Os seguintes erros estão transitórios e devem ser repetidos na lógica do aplicativo: 

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir a base de dados "%.&#x2a;ls" pedida pelo início de sessão. O início de sessão falhou. |
| 40197 |17 |O serviço encontrou um erro ao processar o pedido. Tente novamente. Código de erro %d.<br/><br/>Recebe este erro quando o serviço está desativado devido a software ou atualizações de hardware, falhas de hardware ou outros problemas de ativação pós-falha. O código de erro (%d) incorporado na mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou ativação pós-falha que ocorreu. Alguns exemplos do erro códigos são incorporados na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Restabelecer ligação ao seu servidor de base de dados SQL automaticamente liga-o para uma cópia de bom estado de funcionamento da base de dados. A aplicação tem de capturar o registo de erros 40197, o código de erro embedded (%d) na mensagem de para resolução de problemas e tente voltar a ligar à base de dados SQL até que os recursos estão disponíveis e a ligação é estabelecida novamente. |
| 40501 |20 |O serviço está ocupado neste momento. Repita o pedido após 10 segundos. ID do incidente: %ls. Código: %d.<br/><br/>Para obter mais informações, consulte:<br/>• [Limites de recursos do azure SQL Database](sql-database-service-tiers-dtu.md). |
| 40613 |17 |Base de dados '%.&#x2a;ls' no servidor '%.&#x2a;ls' não está atualmente disponível. Tente novamente a ligação mais tarde. Se o problema persistir, contacte o suporte ao cliente e forneça o ID de rastreio de sessão de '%.&#x2a;ls'.<br/><br/> Este erro pode ocorrer se já existir uma existente ligação de administrador dedicada (DAC) estabelecida para a base de dados. |
| 49918 |16 |Não é possível processar o pedido. Sem recursos suficientes para processar o pedido.<br/><br/>O serviço está ocupado neste momento. Repita o pedido mais tarde. |
| 49919 |16 |Não é possível o processo de criar ou atualizar o pedido. Demasiado muitos criar ou atualizar operações em curso para a subscrição "% ld".<br/><br/>O serviço está ocupado a processar vários criar ou atualizar pedidos para a sua subscrição ou o servidor. Pedidos atualmente estão bloqueados para otimização de recursos. Consulta [DM operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Aguarde até que pendente criar ou atualizar pedidos forem concluídos ou elimine um dos seus pedidos pendentes e repita o pedido mais tarde. |
| 49920 |16 |Não é possível processar o pedido. Demasiadas operações em curso para a subscrição "% ld".<br/><br/>O serviço está ocupado a processar vários pedidos para esta subscrição. Pedidos atualmente estão bloqueados para otimização de recursos. Consulta [DM operation_status](https://msdn.microsoft.com/library/dn270022.aspx) estado da operação. Aguarde até que os pedidos pendentes estiverem concluída ou elimine um dos seus pedidos pendentes e repita o pedido mais tarde. |
| 4221 |16 |Início de sessão para a leitura secundária falhou devido a longa espera em "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". A réplica não está disponível para início de sessão porque as versões de linha estão em falta para transações que estavam a decorrer quando a réplica foi reciclada. O problema pode ser resolvido pelo reverta ou consolide as transações ativas na réplica primária. Ocorrências desta condição podem ser minimizadas, evitando transações de escrita prolongada na primária. |

## <a name="database-copy-errors"></a>Erros de cópia da base de dados

Podem ser encontrados os seguintes erros ao copiar uma base de dados na base de dados do Azure SQL. Para mais informações, consulte [Copiar uma Base de Dados SQL do Azure](sql-database-copy.md).

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |Cliente com o endereço IP '%.&#x2a;ls' está temporariamente desativada. |
| 40637 |16 |Criar cópia de base de dados está atualmente desativada. |
| 40561 |16 |Falha na cópia da base de dados. Base de dados de origem ou de destino não existe. |
| 40562 |16 |Falha na cópia da base de dados. A base de dados de origem foi removida. |
| 40563 |16 |Falha na cópia da base de dados. A base de dados de destino foi removida. |
| 40564 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova a base de dados de destino e tente novamente. |
| 40565 |16 |Falha na cópia da base de dados. Mais do que 1 cópia de base de dados em simultâneo da mesma origem é permitida. Remova a base de dados de destino e tente novamente mais tarde. |
| 40566 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova a base de dados de destino e tente novamente. |
| 40567 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova a base de dados de destino e tente novamente. |
| 40568 |16 |Falha na cópia da base de dados. Base de dados de origem ficou indisponível. Remova a base de dados de destino e tente novamente. |
| 40569 |16 |Falha na cópia da base de dados. Base de dados de destino ficou indisponível. Remova a base de dados de destino e tente novamente. |
| 40570 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova a base de dados de destino e tente novamente mais tarde. |
| 40571 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova a base de dados de destino e tente novamente mais tarde. |

## <a name="resource-governance-errors"></a>Erros de governação de recursos

Os seguintes erros são causados por uso excessivo de recursos durante o trabalho com a base de dados do Azure SQL. Por exemplo:

* Uma transação foi aberta durante demasiado tempo.
* Uma transação está mantendo bloqueios demasiados.
* Uma aplicação está a consumir demasiada memória.
* Uma aplicação está a consumir muito `TempDb` espaço.

Tópicos relacionados:

* Informações mais detalhadas estão disponíveis aqui: [Limites de recursos de base de dados SQL do Azure](sql-database-service-tiers-dtu.md).

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |ID do recurso: %d. O limite de %s para a base de dados é %d e foi atingido. Para obter mais informações, consulte [limites de recursos de base de dados SQL para bases de dados únicos e em pool](sql-database-resource-limits-database-server.md).<br/><br/>O ID de recurso indica o recurso que atingiu o limite. Para threads de trabalho, o ID de recurso = 1. Para as sessões, o ID de recurso = 2.<br/><br/>Para obter mais informações sobre este erro e como resolvê-lo, consulte:<br/>• [Limites de recursos do azure SQL Database](sql-database-service-tiers-dtu.md). |
| 10929 |20 |ID do recurso: %d. A garantia mínima %s é %d, limite máximo é %d e a utilização atual da base de dados é %d. No entanto, o servidor está atualmente demasiado ocupado para suportar solicitações que ultrapassarem %d para esta base de dados. Para obter mais informações, consulte [limites de recursos de base de dados SQL para bases de dados únicos e em pool](sql-database-resource-limits-database-server.md). Caso contrário, volte a tentar novamente mais tarde.<br/><br/>O ID de recurso indica o recurso que atingiu o limite. Para threads de trabalho, o ID de recurso = 1. Para as sessões, o ID de recurso = 2.<br/><br/>Para obter mais informações sobre este erro e como resolvê-lo, consulte:<br/>• [Limites de recursos do azure SQL Database](sql-database-service-tiers-dtu.md). |
| 40544 |20 |A base de dados atingiu a quota de tamanho. Particione ou elimine dados, remova índices ou consulte a documentação para encontrar possíveis resoluções. |
| 40549 |16 |A sessão foi terminada porque tem uma transação de longa execução. Tente encurtar a transação. |
| 40550 |16 |A sessão foi terminada porque obteve muito muitos bloqueios. Tente ler ou modificar algumas linhas numa única transação. |
| 40551 |16 |A sessão foi terminada devido a excesso `TEMPDB` utilização. Tente modificar a consulta para reduzir a utilização de espaço da tabela temporária.<br/><br/>Se estiver a utilizar objetos temporários, poupar espaço no `TEMPDB` soltando objetos temporários após eles já não são necessários para a sessão da base de dados. |
| 40552 |16 |A sessão foi terminada devido a utilização do espaço de log de transação excessiva. Tente modificar algumas linhas numa única transação.<br/><br/>Se executar em massa insere a utilizar o `bcp.exe` utilitário ou o `System.Data.SqlClient.SqlBulkCopy` de classe, tente utilizar o `-b batchsize` ou `BatchSize` opções para limitar o número de linhas copiadas para o servidor em cada transação. Se é recompilar um índice com o `ALTER INDEX` instrução, experimente utilizar o `REBUILD WITH ONLINE = ON` opção. |
| 40553 |16 |A sessão foi terminada devido a utilização de memória excessiva. Tente modificar a consulta para processar menos linhas.<br/><br/>Reduzindo o número de `ORDER BY` e `GROUP BY` operações no seu código Transact-SQL reduz os requisitos de memória da sua consulta. |

## <a name="elastic-pool-errors"></a>Erros de conjunto elástico

Para criar e utilizar os conjuntos elásticos relacionados com os seguintes erros:

| Código de erro | Gravidade | Descrição | Ação corretiva |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |O conjunto elástico atingiu o limite de armazenamento. A utilização do armazenamento para o conjunto elástico não pode exceder (%d) MBs. A tentar gravar dados num banco de dados quando for atingido o limite de armazenamento do conjunto elástico. |Considere aumentar as DTUs de e/ou adicionar armazenamento para o conjunto elástico se possível para aumentar o limite de armazenamento, reduzir o armazenamento utilizado pelas bases de dados individuais dentro do conjunto elástico ou remover bases de dados do conjunto elástico. |
| 10929 | 16 |A garantia mínima %s é %d, limite máximo é %d e a utilização atual da base de dados é %d. No entanto, o servidor está atualmente demasiado ocupado para suportar solicitações que ultrapassarem %d para esta base de dados. Ver [limites de recursos de base de dados SQL para bases de dados únicos e em pool](sql-database-resource-limits-database-server.md) para obter assistência. Caso contrário, volte a tentar novamente mais tarde. DTU / mínimo de vCore por base de dados; DTU / máximo de vCore por base de dados. O número total de trabalhadores simultâneos (pedidos) em todas as bases de dados do conjunto elástico tentou exceder o limite do conjunto. |Considere aumentar o DTUs ou vCores do conjunto elástico se possível, para aumentar o limite de trabalho ou remover bases de dados do conjunto elástico. |
| 40844 | 16 |Base de dados "%ls" no servidor "%ls" é uma base de dados de edição de "%ls" num conjunto elástico e não pode ter uma relação de cópia contínua.  |N/A |
| 40857 | 16 |Conjunto elástico não encontrado para o servidor: '%ls', nome do conjunto elástico: "%ls". Conjunto elástico especificado não existe no servidor especificado. | Forneça um nome de conjunto elástico válido. |
| 40858 | 16 |Conjunto elástico "%ls" já existe no servidor: "%ls". Conjunto elástico especificado já existe no servidor de base de dados SQL especificado. | Forneça o nome do novo conjunto elástico. |
| 40859 | 16 |Conjunto elástico não suporta a camada de serviço "%ls". Escalão de serviço especificado não é suportado para o aprovisionamento do conjunto elástico. |Forneça a edição correta ou deixe o escalão de serviço em branco para usar a camada de serviço predefinida. |
| 40860 | 16 |Combinação de conjunto elástico "%ls" e o serviço de objetivo "%ls" é inválida. Elástico escalão do conjunto e o serviço pode ser especificado em conjunto, apenas se o tipo de recurso é especificado como 'ElasticPool'. |Especifica a correta combinação de conjunto elástico e o escalão de serviço. |
| 40861 | 16 |A edição de base de dados ' %. *ls não podem ser diferentes do que o escalão de serviço do conjunto elástico que é ' %.* dos ls. A edição de base de dados é diferente do que o escalão de serviço do conjunto elástico. |Não especifica uma edição de base de dados que é diferente do que o escalão de serviço do conjunto elástico.  Tenha em atenção que a edição de base de dados não precisa de ser especificado. |
| 40862 | 16 |Nome do conjunto elástico têm de ser especificado se o objetivo de serviço do conjunto elástico é especificado. Objetivo de serviço do conjunto elástico não identifica exclusivamente um conjunto elástico. |Especifique o nome do conjunto elástico se utilizar o objetivo de serviço do conjunto elástico. |
| 40864 | 16 |As DTUs do conjunto elástico tem de ser, pelo menos, (%d) DTUs no escalão de serviço "%. * ls'. A tentar definir as DTUs do conjunto elástico abaixo o limite mínimo. |Repita as DTUs a definição para o elastic pool, pelo menos, o limite mínimo. |
| 40865 | 16 |As DTUs do conjunto elástico não podem exceder (%d) DTUs no escalão de serviço "%. * ls'. A tentar definir as DTUs do conjunto elástico acima do limite máximo. |Repita a definir as DTUs do conjunto elástico para não mais do que o limite máximo. |
| 40867 | 16 |O máximo de DTUS por base de dados tem de ser, pelo menos, (%d) no escalão de serviço "%. * ls'. A tentar definir o máximo de DTUS por base de dados abaixo o limite suportado. | Considere utilizar o escalão de serviço do conjunto elástico que suporta a definição pretendida. |
| 40868 | 16 |O máximo de DTUS por base de dados não pode exceder (%d) no escalão de serviço "%. * ls'. A tentar definir o máximo de DTUS por base de dados além do limite suportado. | Considere utilizar o escalão de serviço do conjunto elástico que suporta a definição pretendida. |
| 40870 | 16 |O mínimo de DTUS por base de dados não pode exceder (%d) no escalão de serviço "%. * ls'. A tentar definir o mínimo de DTUS por base de dados além do limite suportado. | Considere utilizar o escalão de serviço do conjunto elástico que suporta a definição pretendida. |
| 40873 | 16 |O número de bases de dados (%d) e o mínimo de DTUS por base de dados (%d) não pode exceder as DTUs do conjunto elástico (%d). A tentar especificar o mínimo de DTUS para bases de dados do conjunto elástico que exceda as DTUs do conjunto elástico. | Considere as DTUs do conjunto elástico, a aumentar ou diminuir o mínimo de DTUS por base de dados ou diminuir o número de bases de dados do conjunto elástico. |
| 40877 | 16 |Não é possível eliminar um conjunto elástico, a menos que ele não contém quaisquer bases de dados. O conjunto elástico contém um ou mais bases de dados e, portanto, não pode ser eliminado. |Remova bases de dados do conjunto elástico para eliminá-lo. |
| 40881 | 16 |O conjunto elástico "%. * ls atingiu o limite de contagem de base de dados.  O limite de contagem de base de dados para o conjunto elástico não pode exceder (%d) de um conjunto elástico com (%d) DTUs. A tentar criar ou adicionar base de dados para o conjunto elástico quando for atingido o limite de contagem de base de dados do conjunto elástico. | Considere aumentar as DTUs do conjunto elástico se possível para aumentar o limite de base de dados ou remover bases de dados do conjunto elástico. |
| 40889 | 16 |As DTUs ou o limite de armazenamento para o conjunto elástico "%. * ls não podem ser diminuídos, uma vez que o que isso não forneceria espaço de armazenamento suficiente para seus bancos de dados. A tentar diminuir o limite de armazenamento do conjunto elástico abaixo a utilização de armazenamento. | Considere reduzir a utilização do armazenamento de bases de dados individuais do conjunto elástico ou remover bases de dados do conjunto para reduzir seu DTUs ou o limite de armazenamento. |
| 40891 | 16 |O mínimo de DTUS por base de dados (%d) não pode exceder o máximo de DTUS por base de dados (%d). A tentar definir o mínimo de DTUS por base de dados maior do que o máximo de DTUS por base de dados. |Certifique-se de que o mínimo de DTUS por bancos de dados não excede o máximo de DTUS por base de dados. |
| TBD | 16 |O tamanho de armazenamento para uma base de dados individual num conjunto elástico não pode exceder o tamanho máximo permitido por ' %. * ls conjunto elástico do escalão de serviço. O tamanho máximo da base de dados excede o tamanho máximo permitido pela camada de serviço do conjunto elástico. |Defina o tamanho máximo da base de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do conjunto elástico. |

Tópicos relacionados:

* [Criar um conjunto elástico (c#)](sql-database-elastic-pool-manage-csharp.md)
* [Gerir um conjunto elástico (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Criar um conjunto elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Monitorizar e gerir um conjunto elástico (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Erros gerais

Os seguintes erros se encontra em qualquer categorias anteriores.

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) não é um nome válido porque contém carateres inválidos. |
| 18452 |14 |O início de sessão falhou. O início de sessão é proveniente de um domínio não fidedigno e não pode ser utilizado com o Windows authentication.%.&#x2a;ls (inícios de sessão do Windows não são suportados nesta versão do SQL Server.) |
| 18456 |14 |Falha de início de sessão do utilizador "%. &#x2a;ls'.%. &#x2a;ls %. &#x2a;ls (o início de sessão falhou para o utilizador "%.&#x2a; ls".) |
| 18470 |14 |Falha de início de sessão para o utilizador '%.&#x2a;ls'. Razão: A conta é disabled.%. &#x2a;ls |
| 40014 |16 |Não não possível utilizar várias bases de dados na mesma transação. |
| 40054 |16 |Tabelas sem um índice em cluster não são suportadas nesta versão do SQL Server. Criar um índice em cluster e tente novamente. |
| 40133 |15 |Esta operação não é suportada nesta versão do SQL Server. |
| 40506 |16 |SID especificado é inválido para esta versão do SQL Server. |
| 40507 |16 |%.&#x2a;ls não não possível invocar com parâmetros nesta versão do SQL Server. |
| 40508 |16 |A instrução USE não é suportada para alternar entre bases de dados. Utilize uma nova ligação para ligar a uma base de dados diferente. |
| 40510 |16 |Instrução '%.&#x2a;ls' não é suportada nesta versão do SQL Server |
| 40511 |16 |A função incorporada '%.&#x2a;ls' não é suportada nesta versão do SQL Server. |
| 40512 |16 |Funcionalidade despromovida "%ls" não é suportada nesta versão do SQL Server. |
| 40513 |16 |Servidor variável '%.&#x2a;ls' não é suportada nesta versão do SQL Server. |
| 40514 |16 |"%ls" não é suportada nesta versão do SQL Server. |
| 40515 |16 |Referência ao nome de base de dados e/ou servidor em '%.&#x2a;ls' não é suportada nesta versão do SQL Server. |
| 40516 |16 |Os objetos temp globais não são suportados nesta versão do SQL Server. |
| 40517 |16 |Opção de palavra-chave ou instrução '%.&#x2a;ls' não é suportada nesta versão do SQL Server. |
| 40518 |16 |O comando DBCC '%.&#x2a;ls' não é suportada nesta versão do SQL Server. |
| 40520 |16 |A classe com capacidade de segurança "% S_MSG" não é suportada nesta versão do SQL Server. |
| 40521 |16 |A classe com capacidade de segurança "% S_MSG" não é suportada no âmbito do servidor nesta versão do SQL Server. |
| 40522 |16 |Tipo de base de dados principal '%.&#x2a;ls' não é suportado nesta versão do SQL Server. |
| 40523 |16 |Criação de utilizador implícito '%.&#x2a;ls' não é suportada nesta versão do SQL Server. Crie explicitamente o utilizador antes de o utilizar. |
| 40524 |16 |Tipo de dados '%.&#x2a;ls' não é suportada nesta versão do SQL Server. |
| 40525 |16 |COM '%.ls' não é suportada nesta versão do SQL Server. |
| 40526 |16 |'%.&#x2a;ls o fornecedor de conjunto de linhas dos ls não é suportado nesta versão do SQL Server. |
| 40527 |16 |Servidores ligados não são suportados nesta versão do SQL Server. |
| 40528 |16 |Os utilizadores não podem ser mapeados para certificados, chaves assimétricas ou inícios de sessão do Windows nesta versão do SQL Server. |
| 40529 |16 |A função incorporada '%.&#x2a;ls' em representação contexto não é suportado nesta versão do SQL Server. |
| 40532 |11 |Não é possível abrir o servidor "%.&#x2a;ls" pedida pelo início de sessão. O início de sessão falhou. |
| 40553 |16 |A sessão foi terminada devido a utilização de memória excessiva. Tente modificar a consulta para processar menos linhas.<br/><br/> Reduzindo o número de `ORDER BY` e `GROUP BY` operações no seu código Transact-SQL ajuda a reduzir os requisitos de memória da sua consulta. |
| 40604 |16 |Pode não CREATE/ALTER DATABASE porque excede a quota do servidor. |
| 40606 |16 |Anexar bases de dados não é suportada nesta versão do SQL Server. |
| 40607 |16 |Inícios de sessão do Windows não são suportados nesta versão do SQL Server. |
| 40611 |16 |Os servidores podem ter até 128 regras de firewall definidas. |
| 40614 |16 |O endereço IP inicial da regra de firewall não pode exceder o endereço IP final. |
| 40615 |16 |Não é possível abrir o servidor '{0}"pedida pelo início de sessão. Cliente com o endereço IP{1}' não tem permissão para aceder ao servidor.<br /><br />Para ativar o acesso, utilize o Portal de base de dados SQL ou execute sp\_definir\_firewall\_regra na base de dados mestra para criar uma regra de firewall para este endereço IP ou intervalo de endereços. Pode demorar até cinco minutos para que esta alteração tenha efeito. |
| 40617 |16 |O firewall nome da regra que começa com (nome da regra) é demasiado longo. Comprimento máximo é 128. |
| 40618 |16 |O nome da regra de firewall não pode estar vazio. |
| 40620 |16 |Falha de início de sessão do utilizador "%.&#x2a;ls". A alteração de palavra-passe falhou. Alteração de palavra-passe durante o início de sessão não é suportada nesta versão do SQL Server. |
| 40627 |20 |Operação no servidor de '{0}"e da base de dados"{1}' está em curso. Aguarde alguns minutos antes de tentar novamente. |
| 40630 |16 |Falha na validação da palavra-passe. A palavra-passe não cumpre os requisitos da política porque é demasiado curta. |
| 40631 |16 |A palavra-passe que especificou é demasiado longa. A palavra-passe deve ter mais do que 128 carateres. |
| 40632 |16 |Falha na validação da palavra-passe. A palavra-passe não cumpre os requisitos da política devido a não ser suficientemente complexa. |
| 40636 |16 |Não é possível utilizar um nome de base de dados reservado '%.&#x2a;ls' nesta operação. |
| 40638 |16 |Id de subscrição inválido (id de subscrição). Subscrição não existe. |
| 40639 |16 |Pedido não obedece ao esquema: (erro do esquema). |
| 40640 |20 |O servidor encontrou uma exceção inesperada. |
| 40641 |16 |A localização especificada é inválida. |
| 40642 |17 |O servidor está atualmente demasiado ocupado. Tente novamente mais tarde. |
| 40643 |16 |O valor de cabeçalho x-ms-version especificado é inválido. |
| 40644 |14 |Falha ao autorizar o acesso à subscrição especificada. |
| 40645 |16 |ServerName (servername) não pode estar vazio ou é nulo. Ele pode apenas ser constituído por letras minúsculas "a"-"z", os números 0-9 e o hífen. O hífen pode não início ou no nome. |
| 40646 |16 |ID de subscrição não pode estar vazio. |
| 40647 |16 |Subscrição (id de subscrição) não tem o servidor (servername). |
| 40648 |17 |Demasiados pedidos foram realizados. Tente novamente mais tarde. |
| 40649 |16 |Foi especificado o tipo de conteúdo inválido. Aplicação/xml só é suportada. |
| 40650 |16 |Subscrição (id de subscrição) não existe ou não está pronta para a operação. |
| 40651 |16 |Falha ao criar o servidor porque a subscrição (id de subscrição) está desativada. |
| 40652 |16 |Não é possível mover ou criar o servidor. Subscrição (id de subscrição) irá exceder a quota do servidor. |
| 40671 |17 |Falha na comunicação entre o gateway e o serviço de gestão. Tente novamente mais tarde. |
| 40852 |16 |Não é possível abrir a base de dados ' %. \*dos ls no servidor "%. \*ls solicitada pelo início de sessão. Acesso à base de dados só é permitido através de uma cadeia de ligação com segurança ativada. Para aceder a esta base de dados, modifique as cadeias de ligação para conter "seguro" no servidor FQDN - "name server".database.windows .net deve ser modificado para .database "name server". `secure`. windows.net. |
| 40914 | 16 | Não é possível abrir o servidor de '*[nome do servidor]*"pedida pelo início de sessão. Cliente não tem permissão para aceder ao servidor.<br /><br />Para corrigir o problema, considere adicionar uma [regra de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |O sistema do SQL Azure está sob carga e é colocar um limite superior em operações CRUD de DB simultâneas para um único servidor de base de dados SQL (por exemplo, criar base de dados). O servidor especificado na mensagem de erro foi excedido o número máximo de ligações simultâneas. Tente novamente mais tarde. |
| 45169 |16 |O sistema SQL do azure está sob carga e é colocar um limite superior no número de operações de CRUD do servidor em simultâneo para uma única subscrição (por exemplo, criar servidor). A subscrição especificada na mensagem de erro foi excedido o número máximo de ligações simultâneas e o pedido foi negado. Tente novamente mais tarde. |

## <a name="next-steps"></a>Passos Seguintes

* Leia sobre [funcionalidades de base de dados do Azure SQL](sql-database-features.md).
* Leia sobre [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
* Leia sobre [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

