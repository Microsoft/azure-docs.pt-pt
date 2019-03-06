---
title: Acesso condicional - base de dados SQL do Azure e o armazém de dados | Microsoft Docs
description: Saiba como configurar o acesso condicional para o Azure SQL Database e o armazém de dados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 8e7e72236abdaeb77a6bfe3dd85c9ab0c8fbe40e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442703"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Acesso condicional (MFA) com a base de dados SQL do Azure e o armazém de dados  

Do Azure [base de dados SQL](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suportam o acesso condicional do Microsoft. 

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

Os passos seguintes mostram como configurar a base de dados SQL para impor uma política de acesso condicional.  

## <a name="prerequisites"></a>Pré-requisitos  
- Tem de configurar a base de dados SQL ou o SQL Data Warehouse para suportar a autenticação do Azure Active Directory. Para obter passos específicos, consulte [configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Quando a autenticação multifator estiver ativada, tem de ligar com a ferramenta de suporte, como o SSMS mais recente. Para obter mais informações, consulte [configurar a base de dados de SQL do Azure multi-factor authentication para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurar a AC para o Azure SQL DB/armazém de dados  
1.  Inicie sessão no Portal, selecione **do Azure Active Directory**e, em seguida, selecione **acesso condicional**. Para obter mais informações, consulte [referência técnica do Azure Active Directory condicional acesso](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![Painel de acesso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  Na **políticas de acesso condicional** painel, clique em **nova política**, forneça um nome e, em seguida, clique em **configurar regras de**.  
3.  Sob **atribuições**, selecione **utilizadores e grupos**, verifique **selecionar utilizadores e grupos**e, em seguida, selecione o utilizador ou grupo para o acesso condicional. Clique em **selecionar**e, em seguida, clique em **feito** para aceitar a sua seleção.  
  ![Selecionar utilizadores e grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Selecione **aplicações na Cloud**, clique em **selecionar aplicações**. Ver todas as aplicações disponíveis para o acesso condicional. Selecione **Azure SQL Database**, na parte inferior, clique em **selecione**e, em seguida, clique em **feito**.  
  ![Selecione a base de dados SQL](./media/sql-database-conditional-access/select-sql-database.png)  
  Se não conseguir encontrar **base de dados do Azure SQL** listados na seguinte captura de ecrã terceiro, conclua os seguintes passos:   
  - Inicie sessão na sua instância do Azure SQL DB/armazém de dados com o SSMS com uma conta de administrador do AAD.  
  - Executar `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Inicie sessão no AAD e certifique-se de que a base de dados do Azure SQL e o armazém de dados estão listados nos aplicativos no seu AAD.  

5.  Selecione **controlos de acesso**, selecione **concessão**e, em seguida, verifique a política que pretende aplicar. Neste exemplo, selecionamos **exigir autenticação multifator**.  
  ![Selecione conceder acesso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumo  
A aplicação selecionada (base de dados do Azure SQL) que permite estabelecer ligação ao Azure SQL DB/armazém de dados com o Azure AD Premium, agora impõe a política de acesso condicional selecionada, **necessária a autenticação multifator.**  
Para perguntas sobre o Azure SQL Database e o armazém de dados relativamente à autenticação multifator, entre em contato com MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Passos Seguintes  

Para obter um tutorial, veja [proteger a sua base de dados do SQL Azure](sql-database-security-tutorial.md).
