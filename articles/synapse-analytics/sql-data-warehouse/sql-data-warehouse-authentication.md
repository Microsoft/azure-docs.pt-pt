---
title: Autenticação
description: Saiba como autenticar a Azure Synapse Analytics utilizando o Diretório Ativo Azure (Azure AD) ou a autenticação do Servidor SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 868130b23bc29acf0f1271f68a45f44cee98fe98
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84015629"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Autenticar a Azure Synapse Analytics

Aprenda a autenticar a Synapse SQL em Azure Synapse utilizando o Diretório Ativo Azure (AAD) ou a autenticação do Servidor SQL.

Para se ligar a uma piscina SQL, deve passar em credenciais de segurança para fins de autenticação. Ao estabelecer uma ligação, determinadas definições de ligação são configuradas como parte do estabelecimento da sua sessão de consulta.  

Para obter mais informações sobre segurança e como permitir ligações ao seu armazém de dados, consulte a obtenção de [uma documentação](sql-data-warehouse-overview-manage-security.md)de base de dados .

## <a name="sql-authentication"></a>Autenticação do SQL

Para se ligar ao pool SQL, deve fornecer as seguintes informações:

* Nome de servidor totalmente qualificado
* Especificar a autenticação SQL
* Nome de utilizador
* Palavra-passe
* Base de dados por defeito (opcional)

Por predefinição, a sua ligação liga-se à base de dados *principal* e não à base de dados do utilizador. Para se ligar à sua base de dados de utilizadores, pode optar por fazer uma de duas coisas:

* Especifique a base de dados predefinida ao registar o seu servidor com o Explorador de Objetos do Servidor SQL em SSDT, SSMS ou na sua cadeia de ligação de aplicação. Por exemplo, inclua o parâmetro InitialCatalog para uma ligação ODBC.
* Realce a base de dados do utilizador antes de criar uma sessão em SSDT.

> [!NOTE]
> A declaração Transact-SQL **USE MyDatabase;** não é suportada para alterar a base de dados para uma ligação. Para obter orientação conectando-se a uma piscina SQL com SSDT, consulte o artigo [da Consulta com o Estúdio Visual.](sql-data-warehouse-query-visual-studio.md)

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do Diretório Ativo Azure (AAD)

A autenticação [do Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) é um mecanismo de ligação à piscina SQL utilizando identidades no Azure Ative Directory (Azure AD). Com a autenticação do Diretório Ativo Azure, pode gerir centralmente as identidades dos utilizadores de bases de dados e outros serviços da Microsoft numa localização central. A gestão central de ID fornece um único lugar para gerir os utilizadores do Azure Synapse e simplifica a gestão da permissão.

### <a name="benefits"></a>Benefícios

Os benefícios do Diretório Ativo Azure incluem:

* Fornece uma alternativa à autenticação do Servidor SQL.
* Ajuda a parar a proliferação de identidades de utilizadores através dos servidores.
* Permite a rotação de palavras-passe num único local
* Gerir permissões de base de dados utilizando grupos externos (Azure AD).
* Elimina o armazenamento de palavras-passe permitindo a autenticação integrada do Windows e as formas de autenticação suportadas pelo Diretório Ativo do Azure.
* Os utilizadores da base de dados continham utilizadores de bases de dados para autenticar identidades ao nível da base de dados.
* Suporta a autenticação baseada em token para aplicações que liguem ao pool SQL.
* Suporta a autenticação multi-factor através da autenticação universal do Diretório Ativo para várias ferramentas, incluindo o Estúdio de [Gestão de Servidores SQL](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e as Ferramentas de Dados do [Servidor SQL](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> O Diretório Ativo Azure ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Ative Directory é um bom ajuste para o seu ambiente, consulte [as funcionalidades e limitações da Azure AD,](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)especificamente as considerações adicionais.

### <a name="configuration-steps"></a>Passos de configuração

Siga estes passos para configurar a autenticação do Diretório Ativo Azure.

1. Criar e povoar um Diretório Ativo Azure
2. Opcional: Associar ou alterar o diretório ativo que está atualmente associado à sua Subscrição Azure
3. Criar um administrador de Diretório Ativo Azure para o Azure Synapse
4. Configure os computadores dos seus clientes
5. Crie utilizadores de bases de dados contidos na sua base de dados mapeada para identidades da AD Azure
6. Conecte-se à sua piscina SQL utilizando identidades Azure AD

Atualmente, os utilizadores do Diretório Ativo Azure não são mostrados no SSDT Object Explorer. Como uma suposição, veja os utilizadores em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Encontre os detalhes

* Os passos para configurar e utilizar a autenticação do Diretório Ativo Azure são quase idênticos para a Base de Dados Azure SQL e Synapse SQL em Azure Synapse. Siga os passos detalhados no tópico De Ligação à Base de [Dados SQL ou ao SQL Pool utilizando a autenticação de diretório ativo Azure](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Crie funções de base de dados personalizadas e adicione os utilizadores às funções. Em seguida, conceda permissões granulares para os papéis. Para mais informações, consulte [Iniciar-se com permissões](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)de motor de base de dados .

## <a name="next-steps"></a>Próximos passos

Para começar a consultar o Visual Studio e outras aplicações, consulte [A Consulta com O Estúdio Visual.](sql-data-warehouse-query-visual-studio.md)
