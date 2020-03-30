---
title: Problemas que ligam bases de dados de fontes
titleSuffix: Azure Database Migration Service
description: Saiba como resolver problemas conhecidos de problemas/erros associados à ligação do Serviço de Migração de Bases de Dados Azure às bases de dados de origem.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297089"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Resolver problemas de erros do DMS ao ligar às bases de dados de origem

O seguinte artigo fornece detalhes sobre como lidar com potenciais problemas que poderá encontrar ao ligar o Serviço de Migração de Bases de Dados Azure (DMS) à sua base de dados fonte. Cada secção abaixo diz respeito a um tipo específico de base de dados de origem, enumerando o erro que pode encontrar juntamente com detalhes e links para informações sobre como resolver a conectividade.

## <a name="sql-server"></a>SQL Server

Problemas potenciais associados à ligação a uma base de dados do SQL Server de origem e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| A ligação SQL falhou. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e que o Servidor SQL está configurado para permitir ligações remotas.<br> | Este erro ocorre se o serviço não conseguir localizar o servidor de origem. Para resolver o problema, consulte o artigo [Erro ligado à fonte SQL Server ao utilizar porta dinâmica ou instância nomeada](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Erro 53** - Ligação SQL falhou. (Também, para os códigos de erro 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Este erro ocorre se o serviço não conseguir ligar-se ao servidor de origem. Para abordar a questão, consulte os seguintes recursos e, em seguida, tente novamente. <br><br>  [Guia interativo do utilizador para resolver problemas com problemas](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Pré-requisitos para migrar o Servidor SQL para a Base de Dados SQL Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Pré-requisitos para migrar o Servidor SQL para uma base de dados Azure SQL gerida](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Erro 18456** - O login falhou.<br> | Este erro ocorre se o serviço não conseguir ligar-se à base de dados de origem utilizando as credenciais T-SQL fornecidas. Para resolver o problema, verifique as credenciais inseridas. Pode também consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou aos documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tentar novamente. |
| Valor de Nome{0}de Conta Malformado ' fornecido. O formato esperado para o Nome da Conta é DomínioNome\UserName<br> | Este erro ocorre se o utilizador selecionar a autenticação do Windows, mas fornecer o nome de utilizador num formato inválido. Para resolver o problema, forneça o nome de utilizador no formato correto para autenticação do Windows ou selecione **a autenticação SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Problemas potenciais associados à ligação a uma base de dados AWS RDS MySQL de origem e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| **Erro [2003]**[HY000] - a ligação falhou. ERRO [HY000] [MySQL][ODBC x.x(w) driver] Não pode ligar-se ao servidor MySQL em '{server}' (10060) | Este erro ocorre se o controlador MySQL ODBC não conseguir ligar-se ao servidor de origem. Para abordar a questão, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [2005]**[HY000] - a ligação falhou. ERROR [HY000] [MySQL][ODBC x.x(w) driver] Unknown MySQL server host '{server}' | Este erro ocorre se o serviço não encontrar o hospedeiro de origem no RDS. A questão pode ou ser porque a fonte listada não existe ou há um problema com as infraestruturas RDS. Para abordar a questão, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [1045]**[HY000] - a ligação falhou. ERRO [HY000] [MySQL][ODBC x.x(w) driver] Acesso negado para utilizador '{user}'{server}' (usando palavra-passe: SIM) | Este erro ocorre se o controlador MySQL ODBC não conseguir ligar-se ao servidor de origem devido a credenciais inválidas. Verifique as credenciais que inseriu. Se o problema continuar, verifique se o computador de origem tem as credenciais corretas. Poderá ter de redefinir a palavra-passe na consola. Se ainda encontrar o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro [9002]**[HY000] - a ligação falhou. ERRO [HY000] [MySQL][ODBC x.x(w) driver] A corda de ligação pode não estar certa. Visite o portal para referências.| Este erro ocorre se a ligação estiver falhada devido a um problema com a cadeia de ligação. Verifique se a cadeia de ligação fornecida é válida. Para abordar a questão, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro na exploração madeireira binária. Variável binlog_format tem valor '{value}'. Por favor, mude para "row".** | Este erro ocorre se houver um erro na exploração madeireira binária; a variável binlog_format tem o valor errado. Para resolver o problema, altere a binlog_format do grupo de parâmetros para 'ROW' e, em seguida, reinicie a instância. Para mais informações, consulte [as opções e variáveis de registo binários](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) ou a documentação dos ficheiros de registo mysql de registo de dados de dados de dados de dados da [AWS RDS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Para obter mais informações sobre problemas relacionados com a ligação a uma base de dados AWS RDS MySQL, consulte os seguintes recursos:
> * [Resolução de problemas para problemas de conectividade RDS da Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como posso resolver problemas de ligação à minha base de dados amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Problemas potenciais associados à ligação a uma base de dados AWS RDS PostgreSQL de origem e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| **Erro [101]**[08001] - a ligação falhou. Error [08001] tempo de tempo expirou. | Este erro ocorre se o controlador Postgres não conseguir ligar-se ao servidor de origem. Para abordar a questão, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro: O parâmetro wal_level tem valor '{value}'. Por favor, mude-o para "lógico" para permitir a replicação.** | Este erro ocorre se o parâmetro wal_level tiver o valor errado. Para resolver o problema, mude o grupo rds.logical_replication em grupo de parâmetros para 1 e, em seguida, reiniciar a instância. Para mais informações, consulte os [pré-requisitos para a migração para O Azure PostgreSQL utilizando DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) ou [PostgreSQL no Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Para obter mais informações sobre problemas relacionados com a ligação a uma base de dados AWS RDS PostgreSQL, consulte os seguintes recursos:
> * [Resolução de problemas para problemas de conectividade RDS da Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como posso resolver problemas de ligação à minha base de dados amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>Servidor AWS RDS SQL

Problemas potenciais associados à ligação a uma base de dados de servidor AWS RDS SQL e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| **Erro 53** - Ligação SQL falhou. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da instância está correto e que o Servidor SQL está configurado para permitir ligações remotas. (fornecedor: Fornecedor de tubos nomeado, erro: 40 - Não conseguiu abrir uma ligação ao Servidor SQL | Este erro ocorre se o serviço não conseguir ligar-se ao servidor de origem. Para abordar a questão, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro 18456** - O login falhou. O login falhou para o utilizador '{user}' | Este erro ocorre se o serviço não conseguir ligar-se à base de dados de origem com as credenciais T-SQL fornecidas. Para resolver o problema, verifique as credenciais inseridas. Pode também consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou aos documentos de resolução de problemas listados na nota abaixo desta tabela, e tentar novamente. |
| **Erro 87** - A cadeia de ligação não é válida. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e que o Servidor SQL está configurado para permitir ligações remotas. (fornecedor: Interfaces de rede SQL, erro: 25 - A cadeia de ligação não é válida) | Este erro ocorre se o serviço não conseguir ligar-se ao servidor de origem devido a uma cadeia de ligação inválida. Para resolver o problema, verifique a cadeia de ligação fornecida. Se a questão persistir, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro - Certificado de servidor não confiável.** Uma ligação foi estabelecida com sucesso com o servidor, mas depois ocorreu um erro durante o processo de login. (fornecedor: SSL Provider, erro: 0 - A cadeia de certificados foi emitida por uma autoridade que não é de confiança.) | Este erro ocorre se o certificado utilizado não for de confiança. Para resolver o problema, é necessário encontrar um certificado de confiança e, em seguida, ativar no servidor. Em alternativa, pode selecionar a opção Trust Certificate durante a ligação. Tome esta ação apenas se estiver familiarizado com o certificado usado e confiar nele. <br> As ligações TLS que são encriptadas usando um certificado auto-assinado não fornecem segurança forte. Não confie no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet. <br> Para mais informações, consulte a [Utilização do SSL com um Microsoft SQL Server DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) ou [Tutorial: Migrar o Servidor RDS SQL para O Azure utilizando dMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Erro 300** - O utilizador não necessita de permissões. Ver server STATE permission foi negado em objeto '{server}', base de dados '{database}' | Este erro ocorre se o utilizador não tiver permissão para realizar a migração. Para resolver o problema, consulte [as permissões do servidor GRANT - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) ou [Tutorial: Migrar rds SQL Server para Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) para mais detalhes. |

> [!NOTE]
> Para obter mais informações sobre problemas relacionados com a ligação a uma fonte AWS RDS SQL Server, consulte os seguintes recursos:
>
> * [Resolver erros de Conectividade ao SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Como posso resolver problemas de ligação à minha base de dados amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemas conhecidos

* [Questões conhecidas/limitações de migração com migrações online para base de dados Azure SQL](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Questões conhecidas/limitações de migração com migrações online para Base de Dados Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Questões conhecidas/limitações de migração com migrações online para Base de Dados Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passos seguintes

* Ver o artigo Serviço de Migração de Bases de [Dados Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Ver o artigo Como configurar parâmetros de servidor na Base de [Dados Azure para MySQL utilizando o portal Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Ver o artigo [Visão geral dos pré-requisitos para a utilização](https://docs.microsoft.com/azure/dms/pre-reqs)do Serviço de Migração de Bases de Dados Azure .
* Consulte as [FAQ sobre a utilização](https://docs.microsoft.com/azure/dms/faq)do Serviço de Migração de Bases de Dados Azure .
