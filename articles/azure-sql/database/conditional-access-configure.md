---
title: Acesso Condicional
description: Saiba como configurar o Acesso Condicional para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: c18d235977f1256a10e813fa8e02aa3590366fe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936418"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Acesso Condicional com Base de Dados Azure SQL e Azure Synapse Analytics

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suportam o Acesso Condicional da Microsoft.

Os passos a seguir mostram como configurar a Base de Dados Azure SQL, SQL Managed Instance ou Azure Synapse para impor uma política de acesso condicional.  

## <a name="prerequisites"></a>Pré-requisitos

- Você deve configurar Azure SQL Database, Azure SQL Managed Instance, ou piscina DE SQL dedicada em Azure Synapse para suportar a autenticação do Azure Ative Directory (Azure AD). Para etapas específicas, consulte [Configure e gerencie a autenticação do Azure Ative Directory com base de dados SQL ou Azure Synapse](authentication-aad-configure.md).  
- Quando a autenticação multi-factor estiver ativada, deve ligar-se a uma ferramenta suportada, como o mais recente SqL Server Management Studio (SSMS). Para obter mais informações, consulte a [autenticação multi-factor Configure Azure SQL Database para o SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Configurar o acesso condicional

> [!NOTE]
> O exemplo abaixo utiliza a Base de Dados Azure SQL, mas deve selecionar o produto apropriado que pretende configurar o acesso condicional.

1. Inscreva-se no portal Azure, selecione **Azure Ative Directory** e, em seguida, selecione **Acesso Condicional**. Para mais informações, consulte a referência técnica de Acesso Condicional do [Azure Ative Directory.](../../active-directory/conditional-access/concept-conditional-access-conditions.md)  
   ![Lâmina de acesso condicional](./media/conditional-access-configure/conditional-access-blade.png)

2. Na lâmina **De Acesso Condicional,** clique em **Nova política,** forneça um nome e, em seguida, clique nas **regras de Configuração**.  
3. Em **Atribuições**, selecione **Utilizadores e grupos,** verifique **selecione utilizadores e grupos** e, em seguida, selecione o utilizador ou grupo para acesso condicional. Clique **em Selecionar** e, em seguida, clique em **Fazer** para aceitar a sua seleção.  
   ![selecione utilizadores e grupos](./media/conditional-access-configure/select-users-and-groups.png)  

4. Selecione **aplicativos Cloud**, clique **em Selecionar apps**. Vê todas as aplicações disponíveis para Acesso Condicional. Selecione **Azure SQL Database**, no clique inferior **Selecione** e, em seguida, clique em **Fazer**.  
   ![selecionar Base de Dados SQL](./media/conditional-access-configure/select-sql-database.png)  
   Se não conseguir encontrar **a Base de Dados Azure SQL** listada na terceira imagem seguinte, complete os seguintes passos:
   - Conecte-se à sua base de dados na Base de Dados Azure SQL utilizando SSMS com uma conta de administração AD Azure.  
   - Executar `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Inscreva-se no Azure AD e verifique se a Base de Dados Azure SQL, a SQL Managed Instance ou a Azure Synapse estão listadas nas aplicações no seu exemplo AD Azure.  

5. Selecione **controlos de acesso,** selecione **Grant** e, em seguida, verifique a política que pretende aplicar. Para este exemplo, selecionamos **Requerer a autenticação multi-factor**.  
   ![selecionar acesso de concessão](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Resumo

A aplicação selecionada (Azure SQL Database) utilizando o Azure AD Premium, agora aplica a política de acesso condicional selecionado, **a autenticação de vários fatores necessária.**

Para questões sobre a Base de Dados Azure SQL e Azure Synapse relativas à autenticação de vários fatores, contacte <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Passos seguintes  

Para obter um tutorial, consulte [Secure your database in SQL Database](secure-database-tutorial.md).