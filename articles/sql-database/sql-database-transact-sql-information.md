---
title: Resolução de base de dados do T-SQL diferenças-migração o Azure SQL | Documentos da Microsoft
description: Instruções de Transact-SQL que são menos do que totalmente suportadas na Base de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 4d3f27d48819a4bd997cbb62177f5aae4afc85eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993160"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolver diferenças do Transact-SQL durante a migração para a base de dados SQL

Quando [migrar a sua base de dados](sql-database-single-database-migrate.md) do SQL Server para o Azure SQL Server, pode descobrir que a sua base de dados requer alguma reengenharia antes do SQL Server podem ser migrado. Este artigo fornece orientações para ajudá-lo a realizar este reengenharia tanto Noções básicas sobre os motivos subjacentes por que motivo é necessário o reengenharia. Para detetar incompatibilidades, utilize o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Descrição geral

A maioria das funcionalidades de Transact-SQL que utilizam aplicações são totalmente suportadas no Microsoft SQL Server e base de dados do Azure SQL. Por exemplo, os componentes principais do SQL, como tipos de dados, operadores, cadeia de caracteres, aritmética, lógicas e as funções de cursor, funcionam do mesmo modo no SQL Server e base de dados SQL. Existem, no entanto, algumas diferenças do T-SQL no DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que são suportadas apenas parcialmente (o que discutimos neste artigo).

Além disso, existem algumas funcionalidades e sintaxe que não é suportada em todos os porque a base de dados SQL do Azure foi concebida para isolar as funcionalidades de dependências na base de dados mestra e o sistema operativo. Como tal, a maioria das atividades ao nível do servidor são inadequadas para a base de dados SQL. Instruções T-SQL e as opções não estão disponíveis caso configure opções ao nível do servidor, componentes do sistema operacional, ou especificar a configuração do sistema de ficheiros. Quando esses recursos são necessários, uma alternativa apropriada, muitas vezes, está disponível de outro modo da base de dados SQL ou do Azure outra funcionalidade ou serviço.

Por exemplo, elevada disponibilidade é criada na base de dados do Azure SQL com a tecnologia semelhante [grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Instruções T-SQL relacionadas com grupos de disponibilidade não são suportadas pela base de dados SQL e as vistas de gestão dinâmica relacionadas com grupos de Disponibilidade AlwaysOn não também são suportadas.

Para obter uma lista das funcionalidades que são suportados e não suportados pela base de dados SQL, consulte [comparação de funcionalidades de base de dados do Azure SQL](sql-database-features.md). A lista nesta página complementa esse artigo diretrizes e funcionalidades e se concentra em instruções Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instruções de sintaxe do Transact-SQL com diferenças parciais

As instruções DDL (linguagem de definição de dados) de núcleo estão disponíveis, mas algumas instruções DDL têm extensões de relacionadas com a colocação de disco e não suportados funcionalidades.

- Instruções de criar e alterar a base de dados têm mais dúzia de três opções. As instruções incluem o posicionamento de arquivos, FILESTREAM e opções de Mediador de serviço aplicam-se apenas ao SQL Server. Isso pode não importa se criar bases de dados antes de migrar, mas se estiver a migrar o código T-SQL que cria bases de dados deve comparar [CREATE DATABASE (base de dados do Azure SQL)](https://msdn.microsoft.com/library/dn268335.aspx) com a sintaxe do SQL Server em [CREATE Base de dados (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) para se certificar de que todas as opções que usar são suportadas. Criar base de dados para a base de dados do Azure SQL também tem o objetivo de serviço e as opções de escala elástica que se aplicam apenas a base de dados SQL.
- As instruções CREATE e ALTER TABLE tem opções FileTable que não podem ser utilizadas na base de dados SQL, pois FILESTREAM não é suportado.
- CRIAR e alterar as declarações de início de sessão são suportadas mas a base de dados SQL não oferece todas as opções. Para tornar a sua base de dados mais portátil, base de dados SQL estimula o uso de utilizadores de base de dados contida em vez de inícios de sessão sempre que possível. Para obter mais informações, consulte [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) e [controlando e conceder acesso de base de dados](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxe do Transact-SQL não suportada na base de dados do Azure SQL

Além de instruções de Transact-SQL relacionadas com as funcionalidades não suportadas descritas [comparação de funcionalidades de base de dados do Azure SQL](sql-database-features.md), as seguintes instruções e grupos de instruções não são suportadas. Assim, se a sua base de dados a serem migradas utilizar qualquer uma das seguintes funcionalidades, fazer a reengenharia seu T-SQL para eliminar estas funcionalidades de T-SQL e instruções.

- Agrupamento de objetos de sistema
- Ligação relacionada: Instruções de ponto final. A Base de Dados SQL não suporta a autenticação do Windows, mas suporta a autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação precisam da versão mais recente do SSMS. Para mais informações, consulte [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory)](sql-database-aad-authentication.md).
- Consultas entre bases de dados com três ou quatro nomes de partes. (As consultas entre bases de dados só de leitura são suportadas através da [consulta de base de dados elástica](sql-database-elastic-query-overview.md).)
- Encadeamento de propriedade entre bases de dados, `TRUSTWORTHY` definição
- `EXECUTE AS LOGIN`Em vez disso, utilize "EXECUTAR COMO UTILIZADOR".
- A encriptação é suportada, exceto para a gestão de chaves extensível
- Eventos: Eventos, notificações de eventos, notificações de consulta
- Colocação de ficheiros: Sintaxe relacionada com a colocação de ficheiros de base de dados, tamanho e ficheiros de base de dados que são geridas automaticamente pelo Microsoft Azure.
- Elevada disponibilidade: Sintaxe relacionada com disponibilidade elevada, que é gerida através da sua conta do Microsoft Azure. Isto inclui a sintaxe da cópia de segurança, restauro, Always On, espelhamento da base de dados, envio de registos, modos de recuperação.
- Leitor do registo: Sintaxe que se baseia no leitor de log, que não está disponível na base de dados SQL: Enviar por push a replicação, captura de dados de alteração. A Base de Dados SQL pode ser um subscritor de um artigo de replicação push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Sintaxe relacionada às definições do servidor relacionadas a hardware:, como memória, threads de trabalho, afinidade de CPU, sinalizadores de rastreio. Utilizar os escalões de serviço e tamanhos de computação em vez disso.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`e os nomes de quatro partes
- .NET Framework: Integração do CLR com o SQL Server
- Pesquisa semântica
- Credenciais do servidor: Uso [da base de dados no âmbito de credenciais](https://msdn.microsoft.com/library/mt270260.aspx) em vez disso.
- Itens ao nível do servidor: Funções de servidor, `sys.login_token`. `GRANT`, `REVOKE`, e `DENY` das permissões ao nível do servidor não estão disponíveis, apesar de algumas serem substituídas por permissões ao nível da base de dados. Algumas DMVs ao nível do servidor útil têm DMVs equivalentes ao nível da base de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` opções e `RECONFIGURE`. Algumas opções estão disponíveis com [ALTERAR BASE DE DADOS NO ÂMBITO DA CONFIGURAÇÃO](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Sintaxe que se baseia no Agente do SQL Server ou na base de dados do MSDB: alertas, operadores, servidores de gestão central. Ao invés, utilize o scripting, como o Azure PowerShell.
- Auditoria de SQL Server: Ao invés, utilize a auditoria da Base de Dados SQL.
- Rastreio do SQL Server
- Sinalizadores de rastreio: Alguns itens do sinalizador de rastreio foram movidos para modos de compatibilidade.
- Depuração do Transact-SQL
- Acionadores: No âmbito do servidor ou acionadores de início de sessão
- `USE` Instrução: Para alterar o contexto de base de dados para outra base de dados, tem de efetuar uma nova ligação para a nova base de dados.

## <a name="full-transact-sql-reference"></a>Referência do Transact-SQL completa

Para obter mais informações sobre a gramática de Transact-SQL, utilização e exemplos, consulte [referência de Transact-SQL (motor de base de dados)](https://msdn.microsoft.com/library/bb510741.aspx) nos manuais Online do SQL Server.

### <a name="about-the-applies-to-tags"></a>Sobre as etiquetas "Aplica-se a"

A referência de Transact-SQL inclui artigos relacionados com versões do SQL Server 2008 ao presente. Abaixo do título do artigo aqui está um ícone de barra, listando as quatro plataformas do SQL Server e indica a aplicabilidade. Por exemplo, os grupos de disponibilidade foram introduzidos no SQL Server 2012. O [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) artigo indica que a instrução se aplica ao **do SQL Server (começando com o 2012)**. A instrução não é aplicável ao SQL Server 2008, ao SQL Server 2008 R2, à Base de Dados SQL do Azure, ao Azure SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o assunto geral de um artigo pode ser usado num produto, mas existem pequenas diferenças entre produtos. As diferenças são indicadas em pontos intermédios no artigo conforme apropriado. Em alguns casos, o assunto geral de um artigo pode ser usado num produto, mas existem pequenas diferenças entre produtos. As diferenças são indicadas em pontos intermédios no artigo conforme apropriado. Por exemplo o artigo de CREATE TRIGGER está disponível na base de dados SQL. Mas o **todos os SERVER** opção para acionadores ao nível do servidor, indica que os acionadores ao nível do servidor não podem ser utilizados na base de dados SQL. Em alternativa, utilize os acionadores ao nível da base de dados.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista das funcionalidades que são suportados e não suportados pela base de dados SQL, consulte [comparação de funcionalidades de base de dados do Azure SQL](sql-database-features.md). A lista nesta página complementa esse artigo diretrizes e funcionalidades e se concentra em instruções Transact-SQL.
