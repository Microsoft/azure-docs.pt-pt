---
title: Autenticação para piscina SQL dedicada (anteriormente SQL DW)
description: Aprenda a autenticar para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics utilizando a autenticação do Azure Ative Directory (Azure AD) ou sql Server.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 80bc9f6fc6af94ba2a5ade77cc1d53b3fc29f1ea
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685349"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Autenticar para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Aprenda a autenticar para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse utilizando a autenticação do Azure Ative Directory (Azure AD) ou sql Server.

Para se ligar a uma piscina SQL dedicada (anteriormente SQL DW), tem de passar em credenciais de segurança para fins de autenticação. Ao estabelecer uma ligação, determinadas definições de ligação são configuradas como parte do estabelecimento da sua sessão de consulta.  

Para obter mais informações sobre segurança e como permitir ligações à sua piscina SQL dedicada (anteriormente SQL DW), consulte [a obtenção de uma documentação de base de dados.](sql-data-warehouse-overview-manage-security.md)

## <a name="sql-authentication"></a>Autenticação do SQL

Para se conectar à piscina SQL dedicada (anteriormente SQL DW), deve fornecer as seguintes informações:

* Nome de servidor totalmente qualificado
* Especificar a autenticação SQL
* Nome de utilizador
* Palavra-passe
* Base de dados predefinido (opcional)

Por predefinição, a sua ligação liga-se à base de dados *principal* e não à base de dados do utilizador. Para se ligar à base de dados do utilizador, pode optar por fazer uma de duas coisas:

* Especifique a base de dados predefinitiva ao registar o seu servidor com o SQL Server Object Explorer em SSDT, SSMS ou na sua cadeia de ligação de aplicação. Por exemplo, inclua o parâmetro InitialCatalog para uma ligação ODBC.
* Realce a base de dados do utilizador antes de criar uma sessão em SSDT.

> [!NOTE]
> A declaração Transact-SQL **USE MyDatabase;** não é suportada para alterar a base de dados para uma ligação. Para obter orientações que liguem a uma piscina SQL com SSDT, consulte o artigo da Consulta com o [Visual Studio.](sql-data-warehouse-query-visual-studio.md)

## <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

A autenticação [do Azure Ative Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) é um mecanismo de ligação à piscina SQL utilizando identidades no Azure Ative Directory (Azure AD). Com a autenticação do Azure Ative Directory, pode gerir centralmente as identidades dos utilizadores da base de dados e de outros serviços da Microsoft numa localização central. A gestão central de ID fornece um único lugar para gerir utilizadores de piscinas SQL dedicadas (anteriormente SQL DW) e simplifica a gestão de permissões.

### <a name="benefits"></a>Benefícios

Os benefícios do Azure Ative Directory incluem:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a parar a proliferação de identidades de utilizadores através dos servidores.
* Permite a rotação de palavras-passe num único local
* Gerir permissões de base de dados utilizando grupos externos (Azure AD).
* Elimina o armazenamento de palavras-passe, permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Azure Ative Directory.
* Utiliza utilizadores de bases de dados contidos para autenticar identidades ao nível da base de dados.
* Suporta a autenticação baseada em fichas para aplicações que se ligam à piscina SQL.
* Suporta a autenticação multi-factor através da autenticação universal do Diretório Ativo para várias ferramentas, incluindo [o SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e as [Ferramentas de Dados do Servidor SQL.](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

> [!NOTE]
> O Azure Ative Directory ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Ative Directory é um bom ajuste para o seu ambiente, consulte [as funcionalidades e limitações AD do Azure,](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)especificamente as considerações adicionais.

### <a name="configuration-steps"></a>Passos de configuração

Siga estes passos para configurar a autenticação do Azure Ative Directory.

1. Criar e povoar um Diretório Ativo Azure
2. Opcional: Associar ou alterar o diretório ativo que está atualmente associado à sua Subscrição Azure
3. Criar um administrador de diretório ativo Azure para a Azure Synapse
4. Configure os computadores do seu cliente
5. Crie utilizadores de bases de dados contidos na sua base de dados mapeadas para identidades Azure AD
6. Conecte-se à sua piscina SQL utilizando identidades AD AZure

Atualmente, os utilizadores do Azure Ative Directory não são apresentados no SSDT Object Explorer. Como solução alternativa, veja os utilizadores em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="find-the-details"></a>Encontre os detalhes

* Os passos para configurar e utilizar a autenticação do Azure Ative Directory são quase idênticos para a Base de Dados Azure SQL e sinapse SQL em Azure Synapse. Siga os passos detalhados no tópico [Ligando à Base de Dados SQL ou piscina SQL Utilizando a autenticação do Diretório Ativo Azure](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Crie funções de base de dados personalizadas e adicione utilizadores às funções. Em seguida, conceda permissões granulares para os papéis. Para obter mais informações, consulte ["Começar com permissões de motores de base de dados".](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Passos seguintes

Para começar a consultar o Visual Studio e outras aplicações, consulte [Consulta com o Visual Studio](sql-data-warehouse-query-visual-studio.md).
