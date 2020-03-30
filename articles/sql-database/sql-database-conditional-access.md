---
title: Acesso Condicional
description: Saiba configurar o Acesso Condicional para a Base de Dados Azure SQL e para o Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124896"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Acesso Condicional (MFA) com Base de Dados Azure SQL e Azure Synapse Analytics

Base de [Dados Azure SQL,](sql-database-technical-overview.md) [Instância Gerida](sql-database-managed-instance.md)e [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suportam o Microsoft Conditional Access. 

> [!NOTE]
> Este tópico aplica-se ao servidor Azure SQL, e tanto à Base de Dados SQL como ao Synapse Azure que são criados no servidor Azure SQL. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure.

Os seguintes passos mostram como configurar a Base de Dados SQL para impor uma política de acesso condicional.  

## <a name="prerequisites"></a>Pré-requisitos  
- Deve configurar a sua base de dados SQL ou piscina SQL no Azure Synapse para apoiar a autenticação do Diretório Ativo Azure. Para passos específicos, consulte [Configure e gerea autenticação de Diretório Ativo Azure com Base de Dados SQL ou Azure Synapse](sql-database-aad-authentication-configure.md).  
- Quando a autenticação de vários fatores estiver ativada, deve ligar-se a uma ferramenta apoiada, como o mais recente SSMS. Para mais informações, consulte a [autenticação multifactor da Configure Azure SQL Database para o Estúdio de Gestão de Servidores SQL](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configure CA para Azure SQL DB/DW  
1. Inscreva-se no Portal, selecione **Diretório Ativo Azure**e, em seguida, selecione **Acesso Condicional**. Para mais informações, consulte a referência técnica de acesso condicional do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)  
   ![Lâmina de acesso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Na lâmina **De Acesso Condicional,** clique em **Nova política,** forneça um nome e clique em **configurar as regras**.  
3. Em **Atribuições**, **selecione Utilizadores e grupos,** verifique **Selecione utilizadores e grupos,** e, em seguida, selecione o utilizador ou grupo para acesso condicional. Clique em **Selecionar**e, em seguida, clique em **Done** para aceitar a sua seleção.  
   ![selecionar utilizadores e grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selecione **aplicações Cloud,** clique em **Selecionar aplicações**. Você vê todas as aplicações disponíveis para Acesso Condicional. Selecione Base de **Dados SQL Azure,** no clique inferior **Selecione,** e, em seguida, clique **em Done**.  
   ![selecionar Base de Dados SQL](./media/sql-database-conditional-access/select-sql-database.png)  
   Se não encontrar a Base de **Dados Azure SQL** listada na seguinte terceira imagem, complete os seguintes passos:   
   - Inscreva-se na sua instância Azure SQL DB/DW utilizando SSMS com uma conta de administradora AAD.  
   - Executar `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Inscreva-se na AAD e verifique se a Base de Dados Azure SQL e a Azure Synapse estão listadas nas aplicações do seu AAD.  

5. Selecione **controlos de acesso,** selecione **Grant**e, em seguida, verifique a política que pretende aplicar. Para este exemplo, selecionamos **Exigir autenticação de vários fatores.**  
   ![selecionar acesso de concessão](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumo  
A aplicação selecionada (Base de Dados Azure SQL) que permite ligar-se ao Azure SQL DB/DW utilizando o Azure AD Premium, aplica agora a política de acesso condicional selecionada, **autenticação de vários fatores necessária.**  
Para perguntas sobre a Base de Dados Azure SQL e o Azure Synapse sobre a autenticação de vários fatores, contacte. MFAforSQLDB@microsoft.com  

## <a name="next-steps"></a>Passos seguintes  

Para um tutorial, consulte [Secure your Azure SQL Database](sql-database-security-tutorial.md).
