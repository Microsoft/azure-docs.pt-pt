---
title: Artigo sobre conhecidos problemas/erros associados ao Azure Database Migration Service bases de dados de origem de resolução de problemas | Documentos da Microsoft
description: Saiba mais sobre como resolver problemas/erros conhecidos associados com a ligação de serviço de migração de base de dados do Azure para bases de dados de origem.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462853"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Resolver erros DMS ao ligar a bases de dados de origem

O artigo seguinte fornece detalhes sobre como resolver problemas potenciais que poderá encontrar ao ligar o Azure Database Migration Service (DMS) para a base de dados de origem. Cada secção abaixo está relacionado com um tipo específico de banco de dados de origem, listando o erro poderá encontrar juntamente com detalhes e ligações para informações sobre como resolver problemas de conectividade.

## <a name="sql-server"></a>SQL Server

Problemas potenciais associados a ligar a uma base de dados do SQL Server de origem e como resolvê-los são fornecidos na tabela seguinte.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| Falha na ligação de SQL. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Certifique-se de que o nome da instância está correto e que o SQL Server está configurado para permitir ligações remotas.<br> | Este erro ocorre se o serviço não é possível localizar o servidor de origem. Para resolver o problema, consulte o artigo [erro ao ligar à origem do SQL Server, ao utilizar a porta dinâmica ou a instância nomeada](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Erro 53** -ligação de SQL falhou. (Além disso, para erro de códigos de 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Este erro ocorre se o serviço não é possível ligar ao servidor de origem. Para resolver o problema, consulte os seguintes recursos e, em seguida, tente novamente. <br><br>  [Guia do utilizador interativo para resolver o problema de conectividade](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Pré-requisitos para migração do SQL Server para a base de dados do Azure SQL](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Pré-requisitos para migrar o servidor SQL para uma instância gerida da base de dados do Azure SQL](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Erro 18456** -início de sessão falhou.<br> | Este erro ocorre se o serviço não é possível ligar à base de dados de origem com as credenciais fornecidas do T-SQL. Para resolver o problema, verifique se as credenciais inseridas. Também pode consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou para os documentos de resolução de problemas listados na nota abaixo deste de tabela e, em seguida, tente novamente. |
| Valor de AccountName tem um formato incorreto "{0}' fornecido. Formato esperado para AccountName é NomeDomínio \ NomeUtilizador<br> | Este erro ocorre se o utilizador seleciona a autenticação do Windows, mas fornece o nome de utilizador num formato inválido. Para resolver o problema, fornecer um nome de utilizador no formato correto para a autenticação do Windows ou selecione **autenticação de SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Problemas potenciais associados a ligar a uma base de dados do AWS RDS MySQL de origem e como resolvê-los são fornecidos na tabela seguinte.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| **Erro [2003]** [HY000] - Falha na ligação. Erro [HY000] [MySQL] [controlador ODBC do x.x(w)] não consegue ligar ao servidor MySQL "{server}" (10060) | Este erro ocorre se o controlador ODBC do MySQL não é possível ligar ao servidor de origem. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [2005]** [HY000] - Falha na ligação. Erro [HY000] anfitrião do servidor MySQL de desconhecido [MySQL] [controlador ODBC do x.x(w)] "{server}" | Este erro ocorre se o serviço não é possível localizar o anfitrião de origem no RDS. O problema poderia ser porque a origem listado não existe ou não existe um problema com a infraestrutura RDS. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [1045]** [HY000] - Falha na ligação. Erro [HY000] acesso negado para o utilizador "{user}'@'{server}" ao [MySQL] [controlador ODBC do x.x(w)] (usando a palavra-passe: SIM) | Este erro ocorre se o controlador ODBC do MySQL não é possível ligar ao servidor de origem devido a credenciais inválidas. Verifique se as credenciais que introduziu. Se o problema continuar, certifique-se de que esse computador de origem tiver as credenciais corretas. Poderá ter de repor a palavra-passe na consola do. Se ainda encontrar o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [9002]** [HY000] - Falha na ligação. Erro [HY000] [MySQL] [controlador ODBC do x.x(w)] a cadeia de ligação pode não ser adequada. Visite o portal para referências.| Este erro ocorre se a ligação está a falhar devido a um problema com a cadeia de ligação. Certifique-se de que a cadeia de ligação fornecida é válida. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro no registo binário. Variável binlog_format tem um valor "{value}". . Altere-o para "linha".** | Este erro ocorre se houver um erro no registo binário; a variável binlog_format tem o valor incorreto. Para resolver o problema, altere o binlog_format no grupo de parâmetro para "Linha" e, em seguida, reiniciar a instância. Para obter mais informações, consulte a [variáveis e as opções de registo binário](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) ou [documentação de ficheiros de registo de base de dados do AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Para obter mais informações sobre como resolver problemas relacionados à conexão com uma base de dados do AWS RDS MySQL de origem, consulte os seguintes recursos:
> * [Resolução de problemas para problemas de conectividade de RDS do Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como posso resolver problemas de ligação à minha instância de base de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Problemas potenciais associados a ligar a uma base de dados do PostgreSQL do AWS RDS de origem e como resolvê-los são fornecidos na tabela seguinte.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| **Erro [101]** [08001] - Falha na ligação. Tempo limite de erro [08001] expirou. | Este erro ocorre se o controlador de Postgres não é possível ligar ao servidor de origem. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro: Parâmetro wal_level tem o valor "{value}". Altere-a para 'logical' para permitir a replicação.** | Este erro ocorre se o parâmetro wal_level tem o valor incorreto. Para resolver o problema, altere o rds.logical_replication no grupo de parâmetro para 1 e, em seguida, reiniciar a instância. Para obter mais informações, consulte a [pré-requisitos para migrar para o PostgreSQL do Azure utilizando DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) ou [PostgreSQL no Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Para obter mais informações sobre como resolver problemas relacionados à conexão com uma base de dados do PostgreSQL do AWS RDS de origem, consulte os seguintes recursos:
> * [Resolução de problemas para problemas de conectividade de RDS do Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como posso resolver problemas de ligação à minha instância de base de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Problemas potenciais associados a ligar a uma base de dados do AWS RDS SQL Server de origem e como resolvê-los são fornecidos na tabela seguinte.

| Erro         | Causa e solução de problemas de detalhe |
| ------------- | ------------- |
| **Erro 53** -ligação de SQL falhou. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não estava acessível. Certifique-se de que o nome da instância está correto e que o SQL Server está configurado para permitir ligações remotas. (fornecedor: Fornecedor Pipes nomeados, erro: 40 - não é foi possível abrir uma ligação ao SQL Server | Este erro ocorre se o serviço não é possível ligar ao servidor de origem. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro 18456** -início de sessão falhou. Falha de início de sessão do utilizador "{user}" | Este erro ocorre se o serviço não é possível ligar à base de dados de origem com as credenciais de T-SQL fornecidas. Para resolver o problema, verifique se as credenciais inseridas. Também pode consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou para os documentos de resolução de problemas listados na nota abaixo deste de tabela e tente novamente. |
| **Erro 87** -cadeia de ligação não é válida. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Certifique-se de que o nome da instância está correto e que o SQL Server está configurado para permitir ligações remotas. (fornecedor: Interfaces de rede do SQL, erro: 25 - a cadeia de ligação não é válida) | Este erro ocorre se o serviço não é possível ligar ao servidor de origem devido a uma cadeia de ligação inválido. Para resolver o problema, verifique se a cadeia de ligação fornecida. Se o problema persistir, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro - certificado de servidor não fidedigno.** Uma ligação foi estabelecida com êxito com o servidor, mas, em seguida, Ocorreu um erro durante o processo de início de sessão. (fornecedor: Fornecedor de SSL, erro: 0 - a cadeia de certificados foi emitida por uma autoridade de que não é fidedigna.) | Este erro ocorre se o certificado utilizado não for fidedigno. Para resolver o problema, precisa localizar um certificado que pode ser confiáveis e, em seguida, ativá-la no servidor. Em alternativa, pode selecionar a opção de certificado de confiança ao ligar. Executar esta ação apenas se estiver familiarizado com o certificado utilizado e confiar nela. <br> Ligações de SSL, que são encriptadas com um certificado autoassinado não fornecem uma segurança forte--são suscetíveis a ataques man-in-the-middle. Não confie em SSL usando certificados auto-assinados num ambiente de produção ou em servidores que estão ligados à internet. <br> Para obter mais informações, consulte a [através de SSL com uma instância de BD do Microsoft SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) ou [Tutorial: Migrar o RDS SQL Server para o Azure utilizando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Erro de 300** -o utilizador não tem as permissões necessárias. Permissão VIEW SERVER STATE foi negada no objeto "{server}", '{database}' da base de dados | Este erro ocorre se o utilizador não tem permissão para executar a migração. Para resolver o problema, consulte [conceder permissões de servidor - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) ou [Tutorial: Migrar o RDS SQL Server para o Azure utilizando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) para obter mais detalhes. |

> [!NOTE]
> Para obter mais informações sobre como resolver problemas relacionados ao ligar a uma origem AWS RDS SQL Server, consulte os seguintes recursos:
>
> * [Resolver erros de Conectividade ao SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Como posso resolver problemas de ligação à minha instância de base de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemas conhecidos

* [Limitações de migração/problemas conhecidos com as migrações de online para a base de dados do Azure SQL](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Limitações de migração/problemas conhecidos com as migrações de online para a base de dados do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Limitações de migração/problemas conhecidos com as migrações de online para a base de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passos Seguintes

* Visualizar o artigo [migração de base de dados do Azure Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Visualizar o artigo [como configurar os parâmetros do servidor na base de dados do Azure para MySQL com o portal do Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Visualizar o artigo [descrição geral da pré-requisitos para utilizar o Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte a [perguntas frequentes sobre como utilizar o Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
