---
title: Autenticação
description: Saiba como autenticar no Azure SQL Data Warehouse usando Azure Active Directory (AAD) ou SQL Server autenticação.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fda29e432fbd952261893f3c32a4df7b9990ae66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692929"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Autenticar no Azure SQL Data Warehouse
Saiba como autenticar no Azure SQL Data Warehouse usando Azure Active Directory (AAD) ou SQL Server autenticação.

Para se conectar ao SQL Data Warehouse, você deve passar as credenciais de segurança para fins de autenticação. Ao estabelecer uma conexão, determinadas configurações de conexão são configuradas como parte do estabelecimento da sessão de consulta.  

Para obter mais informações sobre segurança e como habilitar conexões com seu data warehouse, consulte [proteger um banco de dados no SQL data warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Autenticação do SQL
Para se conectar ao SQL Data Warehouse, você deve fornecer as seguintes informações:

* Nome do Server totalmente qualificado
* Especificar a autenticação do SQL
* Nome de utilizador
* Palavra-passe
* Banco de dados padrão (opcional)

Por padrão, a conexão se conecta ao banco de dados *mestre* e não ao seu banco de dados de usuário. Para se conectar ao seu banco de dados de usuário, você pode optar por fazer uma das duas coisas:

* Especifique o banco de dados padrão ao registrar seu servidor com o Pesquisador de Objetos do SQL Server em SSDT, SSMS ou na cadeia de conexão do aplicativo. Por exemplo, inclua o parâmetro InitialCatalog para uma conexão ODBC.
* Realce o banco de dados do usuário antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução Transact-SQL **use MyDatabase;** não tem suporte para alterar o banco de dados para uma conexão. Para obter orientações sobre como se conectar a SQL Data Warehouse com o SSDT, consulte o artigo [consulta com o Visual Studio][Query with Visual Studio] .
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticação Azure Active Directory (AAD)
[Azure Active Directory][What is Azure Active Directory] autenticação é um mecanismo de conexão com o SQL data warehouse do Microsoft Azure usando identidades no Azure Active Directory (AD do Azure). Com a autenticação Azure Active Directory, você pode gerenciar centralmente as identidades dos usuários de banco de dados e outros serviços da Microsoft em um local central. O gerenciamento de identificação central fornece um único local para gerenciar SQL Data Warehouse usuários e simplifica o gerenciamento de permissões. 

### <a name="benefits"></a>Vantagens
Os benefícios do Azure Active Directory incluem:

* Fornece uma alternativa para SQL Server autenticação.
* Ajuda a interromper a proliferação de identidades de usuário entre servidores de banco de dados.
* Permite a rotação de palavras-passe num único local
* Gerenciar permissões de banco de dados usando grupos externos (AAD).
* Elimina o armazenamento de senhas habilitando a autenticação integrada do Windows e outras formas de autenticação com suporte pelo Azure Active Directory.
* Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
* Dá suporte à autenticação baseada em token para aplicativos que se conectam ao SQL Data Warehouse.
* Dá suporte à autenticação multifator por meio do Active Directory autenticação universal para várias ferramentas, incluindo [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory ainda é relativamente novo e tem algumas limitações. Para garantir que Azure Active Directory seja uma boa opção para seu ambiente, consulte [limitações e recursos do Azure ad][Azure AD features and limitations], especificamente as considerações adicionais.
> 
> 

### <a name="configuration-steps"></a>Passos de configuração
Siga estas etapas para configurar a autenticação Azure Active Directory.

1. Criar e popular um Azure Active Directory
2. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua assinatura do Azure
3. Crie um administrador de Azure Active Directory para o SQL Data Warehouse do Azure.
4. Configurar seus computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados mapeado para identidades do Azure AD
6. Conectar-se ao seu data warehouse usando identidades do Azure AD

Atualmente Azure Active Directory usuários não são mostrados no Pesquisador de objetos do SSDT. Como alternativa, exiba os usuários em [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Localizar os detalhes
* As etapas para configurar e usar a autenticação Azure Active Directory são praticamente idênticas para o banco de dados SQL do Azure e o Azure SQL Data Warehouse. Siga as etapas detalhadas no tópico [conectando-se ao banco de dados SQL ou SQL data warehouse usando a autenticação Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares às funções. Para obter mais informações, consulte [introdução com permissões de mecanismo de banco de dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Passos seguintes
Para começar a consultar o armazém de dados com o Visual Studio e outras aplicações, veja [Query with Visual Studio (Consulta com o Visual Studio)][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
