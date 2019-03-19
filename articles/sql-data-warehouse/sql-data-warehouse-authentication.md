---
title: Autenticar para o armazém de dados SQL do Azure | Documentos da Microsoft
description: Saiba como autenticar-se ao Azure SQL Data Warehouse, utilizando a autenticação do Azure Active Directory (AAD) ou o SQL Server.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/12/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: ab439566da38a537534fc10e70058ab57005ce22
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183803"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Autenticar para o armazém de dados SQL do Azure
Saiba como autenticar-se ao Azure SQL Data Warehouse, utilizando a autenticação do Azure Active Directory (AAD) ou o SQL Server.

Para ligar ao SQL Data Warehouse, tem de passar credenciais de segurança para fins de autenticação. Após estabelecer uma ligação, determinadas definições de ligação estão configuradas como parte do estabelecimento de sua sessão de consulta.  

Para obter mais informações sobre como ativar ligações ao seu armazém de dados e de segurança, consulte [proteger uma base de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Autenticação do SQL
Para ligar ao SQL Data Warehouse, tem de fornecer as seguintes informações:

* Servername completamente qualificado
* Especificar a autenticação de SQL
* Nome de utilizador
* Palavra-passe
* (Opcional) da base de dados predefinida

Por predefinição, a ligação liga para o *mestre* da base de dados e não a base de dados do utilizador. Para ligar à sua base de dados do utilizador, pode optar por fazer uma das duas coisas:

* Especifique a base de dados predefinida ao registar o seu servidor com o SQL Server Object Explorer no SSDT, SSMS, ou na sua cadeia de ligação do aplicativo. Por exemplo, inclua o parâmetro de InitialCatalog para uma ligação de ODBC.
* Realce a base de dados do utilizador antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução Transact-SQL **MyDatabase de utilização;** não é suportada para alterar a base de dados para uma ligação. Para obter orientações sobre como ligar ao SQL Data Warehouse com o SSDT, consulte a [consulta com o Visual Studio] [ Query with Visual Studio] artigo.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do Azure Active Directory (AAD)
[O Azure Active Directory] [ What is Azure Active Directory] autenticação é um mecanismo de ligar ao Microsoft Azure SQL Data Warehouse utilizando as identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure Active Directory, pode gerenciar centralmente as identidades dos utilizadores de base de dados e outros serviços da Microsoft num local central. Gerenciamento de ID central fornece um único local para gerir utilizadores do SQL Data Warehouse e simplifica a gestão de permissões. 

### <a name="benefits"></a>Benefícios
Benefícios do Azure Active Directory:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a parar a proliferação de identidades de utilizador em servidores de base de dados.
* Permite a rotação da palavra-passe num único local
* Gerir permissões de base de dados com grupos de externos (AAD).
* Elimina a armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportado pelo Azure Active Directory.
* Utilizadores de base de dados utiliza contido para autenticar identidades ao nível da base de dados.
* Suporta a autenticação baseada em tokens para aplicativos de ligar ao SQL Data Warehouse.
* Suporta o multi-factor authentication através do Active Directory Universal Authentication para o SQL Server Management Studio. Para obter uma descrição de multi-factor Authentication, consulte [suporte do SSMS para o Azure AD MFA com base de dados SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> O Azure Active Directory é ainda relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory é uma boa opção para o seu ambiente, veja [funcionalidades do Azure AD e limitações][Azure AD features and limitations], especificamente as considerações adicionais.
> 
> 

### <a name="configuration-steps"></a>Passos de configuração
Siga estes passos para configurar a autenticação do Azure Active Directory.

1. Criar e preencher um Azure Active Directory
2. Opcional: Associar ou alterar o active directory que está atualmente associado a sua subscrição do Azure
3. Crie um administrador do Azure Active Directory para o Azure SQL Data Warehouse.
4. Configurar os seus computadores de cliente
5. Criar utilizadores de base de dados contidos na base de dados mapeado para identidades do Azure AD
6. Ligar ao seu armazém de dados com identidades do Azure AD

Atualmente, os utilizadores do Azure Active Directory não são apresentados no SSDT Object Explorer. Como solução, ver os usuários [database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Encontrar os detalhes
* Os passos para configurar e utilizar a autenticação do Azure Active Directory são quase idênticos para a base de dados do Azure SQL e o Azure SQL Data Warehouse. Siga os passos detalhados no tópico [ligar à base de dados SQL ou SQL Data Warehouse por através do Azure autenticação do Active Directory](../sql-database/sql-database-aad-authentication.md).
* Criar funções de base de dados personalizada e adicionar utilizadores às funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte [introdução a permissões de motor de base de dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Passos Seguintes
Para começar a consultar o armazém de dados com o Visual Studio e outras aplicações, consulte [consulta com o Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
