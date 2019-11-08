---
title: Acesso Condicional
description: Saiba como configurar o acesso condicional para o banco de dados SQL do Azure e data warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 9b8c0dbe03e47d32d8194408663973f07a07b1b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827168"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Acesso condicional (MFA) com o banco de dados SQL do Azure e data warehouse  

O [banco de dados SQL](sql-database-technical-overview.md)do Azure, [instância gerenciada](sql-database-managed-instance.md)e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dão suporte ao acesso condicional da Microsoft. 

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

As etapas a seguir mostram como configurar o banco de dados SQL para impor uma política de acesso condicional.  

## <a name="prerequisites"></a>Pré-requisitos  
- Você deve configurar seu banco de dados SQL ou SQL Data Warehouse para dar suporte à autenticação Azure Active Directory. Para obter etapas específicas, consulte [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL ou SQL data warehouse](sql-database-aad-authentication-configure.md).  
- Quando a autenticação multifator está habilitada, você deve se conectar com a ferramenta com suporte, como o SSMS mais recente. Para obter mais informações, consulte [Configurar a autenticação multifator do banco de dados SQL do Azure para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurar a AC para o banco de BD SQL do Azure/DW  
1. Entre no portal, selecione **Azure Active Directory**e, em seguida, selecione **acesso condicional**. Para obter mais informações, consulte [Azure Active Directory referência técnica de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![folha de acesso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Na folha **acesso condicional-políticas** , clique em **nova política**, forneça um nome e clique em **configurar regras**.  
3. Em **atribuições**, selecione **usuários e grupos**, marque **Selecionar usuários e grupos**e, em seguida, selecione o usuário ou grupo para acesso condicional. Clique em **selecionar**e, em seguida, clique em **concluído** para aceitar sua seleção.  
   ![Selecionar usuários e grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selecione **aplicativos de nuvem**, clique em **selecionar aplicativos**. Você verá todos os aplicativos disponíveis para acesso condicional. Selecione **banco de dados SQL do Azure**, na parte inferior, clique em **selecionar**e, em seguida, clique em **concluído**.  
   ![selecionar banco de dados SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se você não encontrar o **banco de dados SQL do Azure** listado na terceira captura de tela a seguir, conclua as seguintes etapas:   
   - Entre em sua instância do BD/DW do SQL do Azure usando o SSMS com uma conta de administrador do AAD.  
   - Execute `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Entre no AAD e verifique se o banco de dados SQL do Azure e os data warehouse estão listados nos aplicativos em seu AAD.  

5. Selecione **controles de acesso**, selecione **conceder**e, em seguida, verifique a política que você deseja aplicar. Para este exemplo, selecionamos **exigir autenticação multifator**.  
   ![selecione conceder acesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumo  
O aplicativo selecionado (banco de dados SQL do Azure) que permite conectar-se ao BD/DW do SQL do Azure usando o Azure AD Premium, agora impõe a política de acesso condicional selecionada, a **autenticação multifator necessária.**  
Para dúvidas sobre o banco de dados SQL do Azure e data warehouse sobre a autenticação multifator, entre em contato com MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Passos seguintes  

Para obter um tutorial, consulte [proteger seu banco de dados SQL do Azure](sql-database-security-tutorial.md).
