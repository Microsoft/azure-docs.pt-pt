---
title: Problemas ao conectar bancos de dados de origem
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre como solucionar problemas/erros conhecidos associados à conexão do serviço de migração de banco de dados do Azure a bancos de dados de origem.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: b697faeded4177381f70ebb9d1f93d928b25d7b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437799"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Solucionar erros DMS ao conectar-se a bancos de dados de origem

O artigo a seguir fornece detalhes sobre como resolver possíveis problemas que você pode encontrar ao conectar o serviço de migração de banco de dados do Azure (DMS) ao seu banco de dados de origem. Cada seção abaixo está relacionada a um tipo específico de banco de dados de origem, que lista o erro que você pode encontrar juntamente com detalhes e links para informações sobre como solucionar problemas de conectividade.

## <a name="sql-server"></a>SQL Server

Os possíveis problemas associados à conexão a um banco de dados de SQL Server de origem e como solucioná-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| Falha na conexão SQL. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e se SQL Server está configurado para permitir conexões remotas.<br> | Esse erro ocorrerá se o serviço não puder localizar o servidor de origem. Para resolver o problema, consulte o artigo [erro ao conectar-se ao SQL Server de origem ao usar a porta dinâmica ou a instância nomeada](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Erro 53** -falha na conexão do SQL. (Também, para os códigos de erro 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Esse erro ocorrerá se o serviço não puder se conectar ao servidor de origem. Para resolver o problema, consulte os recursos a seguir e tente novamente. <br><br>  [Guia do usuário interativo para solucionar o problema de conectividade](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Pré-requisitos para migrar SQL Server para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Pré-requisitos para migrar SQL Server para uma instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Erro 18456** -falha no logon.<br> | Esse erro ocorrerá se o serviço não puder se conectar ao banco de dados de origem usando as credenciais do T-SQL fornecidas. Para resolver o problema, verifique as credenciais inseridas. Você também pode consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou os documentos de solução de problemas listados na observação abaixo desta tabela e, em seguida, tentar novamente. |
| Valor '{0}' de AccountName malformado fornecido. O formato esperado para AccountName é DomainName\UserName<br> | Esse erro ocorrerá se o usuário selecionar a autenticação do Windows, mas fornecer o nome de usuário em um formato inválido. Para resolver o problema, forneça o nome de usuário no formato correto para a autenticação do Windows ou selecione **autenticação SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Os possíveis problemas associados à conexão a um banco de dados do AWS RDS MySQL de origem e como solucioná-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| **Erro [2003]** [HY000]-falha na conexão. ERRO [HY000] [MySQL] [ODBC x. x (w) Driver] não é possível conectar ao servidor MySQL em ' {Server} ' (10060) | Esse erro ocorrerá se o driver ODBC do MySQL não puder se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e tente novamente.<br> |
| **Erro [2005]** [HY000]-falha na conexão. ERRO [HY000] [MySQL] [ODBC x. x (w) Driver] host do MySQL Server desconhecido ' {Server} ' | Esse erro ocorrerá se o serviço não conseguir localizar o host de origem no RDS. O problema pode ser porque a fonte listada não existe ou há um problema com a infraestrutura do RDS. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e tente novamente.<br> |
| **Erro [1045]** [HY000]-falha na conexão. ERRO [HY000] [MySQL] [ODBC x. x (w) Driver] acesso negado para o usuário ' {user} ' @ ' {Server} ' (usando a senha: Sim) | Esse erro ocorrerá se o driver ODBC do MySQL não puder se conectar ao servidor de origem devido a credenciais inválidas. Verifique as credenciais que você inseriu. Se o problema continuar, verifique se o computador de origem tem as credenciais corretas. Talvez seja necessário redefinir a senha no console do. Se você ainda encontrar o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e tente novamente.<br> |
| **Erro [9002]** [HY000]-falha na conexão. ERRO [HY000] [MySQL] [ODBC x. x (w) Driver] a cadeia de conexão pode não estar correta. Visite o portal para obter referências.| Esse erro ocorrerá se a conexão estiver falhando devido a um problema com a cadeia de conexão. Verifique se a cadeia de conexão fornecida é válida. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e tente novamente.<br> |
| **Erro no log binário. A variável binlog_format tem o valor ' {value} '. Altere-o para ' Row '.** | Esse erro ocorrerá se houver um erro no registro em log binário; a variável binlog_format tem o valor incorreto. Para resolver o problema, altere o binlog_format no grupo de parâmetros para ' ROW ' e reinicialize a instância. Para obter mais informações, consulte a documentação [Opções de log binário e variáveis](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) ou [arquivos de log do banco de dados AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Para obter mais informações sobre como solucionar problemas relacionados à conexão com um banco de dados do AWS RDS MySQL de origem, consulte os seguintes recursos:
> * [Solução de problemas de conectividade do Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como fazer resolver problemas de conexão com minha instância de banco de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Os possíveis problemas associados à conexão a um banco de dados do AWS RDS de origem e como solucioná-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| **Erro [101]** [08001]-falha na conexão. ERRO [08001] tempo limite expirado. | Esse erro ocorrerá se o driver postgres não puder se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e tente novamente. |
| **Erro: o parâmetro wal_level tem o valor ' {value} '. Altere-o para ' lógico ' para permitir a replicação.** | Esse erro ocorrerá se o parâmetro wal_level tiver o valor incorreto. Para resolver o problema, altere o RDS. logical_replication no grupo de parâmetros para 1 e, em seguida, reinicialize a instância. Para obter mais informações, consulte [pré-requisitos para migrar para o PostgreSQL do Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) ou [POSTGRESQL no Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Para obter mais informações sobre como solucionar problemas relacionados à conexão a um banco de dados do AWS do RDS de origem, consulte os seguintes recursos:
> * [Solução de problemas de conectividade do Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como fazer resolver problemas de conexão com minha instância de banco de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Os possíveis problemas associados à conexão a um banco de dados AWS SQL Server RDS de origem e como solucioná-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| **Erro 53** -falha na conexão do SQL. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da instância está correto e se SQL Server está configurado para permitir conexões remotas. (provedor: provedor de pipes nomeados, erro: 40-não foi possível abrir uma conexão com SQL Server | Esse erro ocorrerá se o serviço não puder se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e tente novamente. |
| **Erro 18456** -falha no logon. Falha de logon do usuário ' {user} ' | Esse erro ocorrerá se o serviço não puder se conectar ao banco de dados de origem com as credenciais de T-SQL fornecidas. Para resolver o problema, verifique as credenciais inseridas. Você também pode se referir a [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou aos documentos de solução de problemas listados na observação abaixo desta tabela e tentar novamente. |
| **Erro 87** -a cadeia de conexão não é válida. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e se SQL Server está configurado para permitir conexões remotas. (provedor: interfaces de rede do SQL, erro: 25-a cadeia de conexão não é válida) | Esse erro ocorrerá se o serviço não puder se conectar ao servidor de origem devido a uma cadeia de conexão inválida. Para resolver o problema, verifique a cadeia de conexão fornecida. Se o problema persistir, consulte os documentos de solução de problemas listados na observação abaixo desta tabela e tente novamente. |
| **Erro-certificado de servidor não confiável.** Uma conexão foi estabelecida com êxito com o servidor, mas ocorreu um erro durante o processo de logon. (provedor: provedor SSL, erro: 0-a cadeia de certificados foi emitida por uma autoridade que não é confiável.) | Esse erro ocorrerá se o certificado usado não for confiável. Para resolver o problema, você precisa encontrar um certificado que possa ser confiável e, em seguida, habilitá-lo no servidor. Como alternativa, você pode selecionar a opção de certificado de confiança durante a conexão. Execute esta ação somente se você estiver familiarizado com o certificado usado e confiar nele. <br> As conexões SSL que são criptografadas usando um certificado autoassinado não fornecem segurança forte – elas são suscetíveis a ataques man-in-the-Middle. Não confie no SSL usando certificados autoassinados em um ambiente de produção ou em servidores conectados à Internet. <br> Para obter mais informações, consulte como [usar SSL com uma instância Microsoft SQL Server DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) ou [tutorial: migrar RDS SQL Server para o Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Erro 300** -o usuário não tem as permissões necessárias. A permissão VIEW SERVER STATE foi negada no objeto ' {Server} ', banco de dados ' {database} ' | Esse erro ocorrerá se o usuário não tiver permissão para executar a migração. Para resolver o problema, consulte [permissões de servidor Grant-Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) ou [tutorial: migrar RDS SQL Server para o Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) para obter mais detalhes. |

> [!NOTE]
> Para obter mais informações sobre como solucionar problemas relacionados à conexão com um SQL Server RDS de origem AWS, consulte os seguintes recursos:
>
> * [Resolver erros de Conectividade ao SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Como fazer resolver problemas de conexão com minha instância de banco de dados do Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemas conhecidos

* [Problemas conhecidos/limitações de migração com migrações online para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Problemas conhecidos/limitações de migração com migrações online para o banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Problemas conhecidos/limitações de migração com migrações online para o banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passos seguintes

* Exiba o artigo [PowerShell do serviço de migração de banco de dados do Azure](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Exiba o artigo [como configurar parâmetros de servidor no banco de dados do Azure para MySQL usando o portal do Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Veja o artigo [visão geral dos pré-requisitos para usar o serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte as [perguntas frequentes sobre como usar o serviço migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/faq).
