---
title: Problemas que ligam bases de dados de origem
titleSuffix: Azure Database Migration Service
description: Saiba como resolver problemas/erros associados à ligação do Serviço de Migração da Base de Dados Azure às bases de dados de origem.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 93ac6ae3c8aed61557a239bb9c84d3587dce1daa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94962336"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Resolver problemas de erros do DMS ao ligar às bases de dados de origem

O seguinte artigo fornece detalhes sobre como lidar com potenciais problemas que poderá encontrar ao ligar o Serviço de Migração de Bases de Dados (DMS) da Azure Database Service (DMS) à sua base de dados fonte. Cada secção abaixo diz respeito a um tipo específico de base de dados de origem, enumerando o erro que poderá encontrar juntamente com detalhes e ligações a informações sobre como resolver problemas na conectividade.

## <a name="sql-server"></a>SQL Server

Os potenciais problemas associados à ligação a uma base de dados do SQL Server de origem e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| A ligação SQL falhou. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e que o SQL Server está configurado para permitir ligações remotas.<br> | Este erro ocorre se o serviço não conseguir localizar o servidor de origem. Para resolver o problema, consulte o artigo [Erro de ligação ao Servidor SQL de origem ao utilizar uma porta dinâmica ou uma instância nomeada](./known-issues-troubleshooting-dms.md#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Erro 53** - A ligação SQL falhou. (Também, para os códigos de erro 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Este erro ocorre se o serviço não conseguir ligar-se ao servidor de origem. Para resolver o problema, consulte os seguintes recursos e, em seguida, tente novamente. <br><br>  [Guia interativo do utilizador para resolver problemas na questão da conectividade](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Pré-requisitos para migrar o SQL Server para a Base de Dados Azure SQL](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) <br><br> [Pré-requisitos para migrar o SQL Server para uma instância gerida Azure SQL](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) |
| **Erro 18456** - O início de sessão falhou.<br> | Este erro ocorre se o serviço não conseguir ligar-se à base de dados de origem utilizando as credenciais T-SQL fornecidas. Para resolver o problema, verifique as credenciais inscritas. Também pode consultar [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tentar novamente. |
| Valor mal formado do nome de conta {0} ' fornecido. Formato esperado para Nome de Conta é Nome de Domínio\UserName<br> | Este erro ocorre se o utilizador selecionar a autenticação do Windows, mas fornecer o nome de utilizador num formato inválido. Para resolver o problema, forneça o nome de utilizador no formato correto para a autenticação do Windows ou selecione **A autenticação SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Os potenciais problemas associados à ligação a uma base de dados AWS RDS MySQL de origem e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| **Error [2003]**[HY000] - a ligação falhou. ERROR [HY000] [MySQL][ODBC x.x(w) driver] Não é possível ligar-se ao servidor MySQL em '{server}' (10060) | Este erro ocorre se o controlador MySQL ODBC não conseguir ligar-se ao servidor de origem. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Error [2005]**[HY000] - a ligação falhou. ERROR [HY000] [MySQL][ODBC x.x(w) driver] Unknown MySQL server host '{server}' | Este erro ocorre se o serviço não encontrar o hospedeiro de origem em RDS. A questão pode ser porque a fonte listada não existe ou há um problema com a infraestrutura RDS. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Error [1045]**[HY000] - a ligação falhou. ERROR [HY000] [MySQL][ODBC x.x(w) driver] Acesso negado para utilizador '{user}'@'{server}' (usando a palavra-passe: SIM) | Este erro ocorre se o controlador MySQL ODBC não conseguir ligar-se ao servidor de origem devido a credenciais inválidas. Verifique as credenciais que inseriu. Se o problema continuar, verifique se o computador de origem tem as credenciais corretas. Pode ser necessário redefinir a palavra-passe na consola. Se ainda encontrar o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e tente novamente.<br> |
| **Error [9002]**[HY000] - a ligação falhou. ERROR [HY000] [MySQL][ODBC x.x(w) driver] A cadeia de ligação pode não estar certa. Visite o portal para referências.| Este erro ocorre se a ligação estiver a falhar devido a um problema com a cadeia de ligação. Verifique se a cadeia de ligação fornecida é válida. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente.<br> |
| **Erro na exploração madeireira binária. Binlog_format variável tem valor '{value}'. Por favor, mude para "row".** | Este erro ocorre se houver um erro na exploração madeireira binária; a variável binlog_format tem o valor errado. Para resolver o problema, altere o binlog_format em grupo de parâmetros para 'ROW', e, em seguida, reinicie a instância. Para obter mais informações, consulte as [opções e variáveis de registo binário ou](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) a [documentação dos ficheiros de registo de base de dados do MySQL MSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Para obter mais informações sobre problemas relacionados com a ligação a uma base de dados MSQL AWS RDS DE origem, consulte os seguintes recursos:
> * [Resolução de problemas para problemas de conectividade RDS Da Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como resolvo problemas ligados à minha página da base de dados da Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Os potenciais problemas associados à ligação a uma base de dados postgresQL de fonte AWS RDSQL e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| **Error [101]**[08001] - a ligação falhou. ERROR [08001] timeout expirou. | Este erro ocorre se o controlador Postgres não conseguir ligar-se ao servidor de origem. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro: O wal_level de parâmetro tem valor '{value}'. Por favor, altere-o para "lógico" para permitir a replicação.** | Este erro ocorre se o parâmetro wal_level tiver o valor errado. Para resolver o problema, altere o rds.logical_replication no grupo de parâmetros para 1 e, em seguida, reinicie a instância. Para obter mais informações, consulte [os pré-requisitos para a migração para Azure PostgreSQL utilizando DMS](./tutorial-postgresql-azure-postgresql-online.md#prerequisites) ou [PostgreSQL na Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Para obter mais informações sobre problemas relacionados com a ligação a uma base de dados postgresQL de fonte AWS RDSQL, consulte os seguintes recursos:
> * [Resolução de problemas para problemas de conectividade RDS Da Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como resolvo problemas ligados à minha página da base de dados da Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>Servidor AWS RDS SQL

Os potenciais problemas associados à ligação a uma base de dados do Servidor AWS RDS SQL de origem e como endereçá-los são fornecidos na tabela seguinte.

| Erro         | Detalhes de causa e resolução de problemas |
| ------------- | ------------- |
| **Erro 53** - A ligação SQL falhou. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da instância está correto e que o SQL Server está configurado para permitir ligações remotas. (fornecedor: Fornecedor de tubos nomeado, erro: 40 - Não foi possível abrir uma ligação ao SQL Server | Este erro ocorre se o serviço não conseguir ligar-se ao servidor de origem. Para resolver o problema, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e, em seguida, tente novamente. |
| **Erro 18456** - O início de sessão falhou. O login falhou para o utilizador '{user}' | Este erro ocorre se o serviço não conseguir ligar à base de dados de origem com as credenciais T-SQL fornecidas. Para resolver o problema, verifique as credenciais inscritas. Também pode consultar [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou os documentos de resolução de problemas listados na nota abaixo desta tabela, e tentar novamente. |
| **Erro 87** - A cadeia de ligação não é válida. Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não está acessível. Verifique se o nome da instância está correto e que o SQL Server está configurado para permitir ligações remotas. (fornecedor: SQL Network Interfaces, erro: 25 - Cadeia de ligação não é válida) | Este erro ocorre se o serviço não conseguir ligar-se ao servidor de origem devido a uma cadeia de ligação inválida. Para resolver o problema, verifique a cadeia de ligação fornecida. Se a questão persistir, consulte os documentos de resolução de problemas listados na nota abaixo desta tabela e tente novamente. |
| **Erro - Certificado de servidor não confiável.** Uma ligação foi estabelecida com sucesso com o servidor, mas então ocorreu um erro durante o processo de login. (prestador: Fornecedor SSL, erro: 0 - A cadeia de certificados foi emitida por uma autoridade que não é de confiança.) | Este erro ocorre se o certificado utilizado não for de confiança. Para resolver o problema, precisa de encontrar um certificado de confiança e, em seguida, adiá-lo no servidor. Em alternativa, pode selecionar a opção Certificado Fiduciário durante a ligação. Tome esta ação apenas se estiver familiarizado com o certificado usado e confiar nele. <br> As ligações TLS que são encriptadas usando um certificado auto-assinado não fornecem uma segurança forte. Não confie no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet. <br> Para obter mais informações, consulte a [utilização do SSL com um Microsoft SQL Server DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) ou [Tutorial: Migrar o Servidor RDS SQL para Azure utilizando o DMS](./tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md#prerequisites). |
| **Erro 300** - O utilizador não tem permissões necessárias. VER SERVIDOR STATE permissão foi negada no objeto '{server}', base de dados '{database}' | Este erro ocorre se o utilizador não tiver permissão para realizar a migração. Para resolver o problema, consulte [permissões do servidor GRANT - Transact-SQL](/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) ou [Tutorial: Migrar o Servidor RDS SQL para Azure utilizando o DMS](./tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md#prerequisites) para obter mais detalhes. |

> [!NOTE]
> Para obter mais informações sobre problemas relacionados com a ligação a um servidor AWS RDS SQL de origem, consulte os seguintes recursos:
>
> * [Resolver erros de Conectividade ao SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Como resolvo problemas ligados à minha página da base de dados da Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemas conhecidos

* [Questões conhecidas/limitações de migração com migrações online para Azure SQL Database](./known-issues-azure-sql-online.md)
* [Questões conhecidas/limitações de migração com migrações on-line para Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Questões conhecidas/limitações de migração com migrações on-line para Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Passos seguintes

* Ver o artigo [Serviço de Migração de Bases de Dados Azure PowerShell](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Ver o artigo [Como configurar os parâmetros do servidor na Base de Dados Azure para o MySQL utilizando o portal Azure](../mysql/howto-server-parameters.md).
* Ver o artigo [Visão geral dos pré-requisitos para a utilização do Serviço de Migração da Base de Dados Azure](./pre-reqs.md).
* Consulte as FAQ sobre a [utilização do Serviço de Migração da Base de Dados Azure.](./faq.md)
