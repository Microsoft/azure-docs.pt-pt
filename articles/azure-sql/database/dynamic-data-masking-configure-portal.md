---
title: 'Portal Azure: Mascaramento dinâmico de dados'
description: Como começar com a Azure SQL Database máscara dinâmica de dados no portal Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 1d4c29cdb9cf24f87df4acc3556b318a1079810a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443624"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Começar com a sql Database dinâmico mascarando dados com o portal Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra-lhe como implementar [uma máscara dinâmica de dados](dynamic-data-masking-overview.md) com o portal Azure. Também pode implementar máscaras de dados dinâmicas utilizando [cmdlets Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) ou a [API REST](https://docs.microsoft.com/rest/api/sql/).

> [!NOTE]
> Esta função não pode ser definida utilizando o portal Azure Synapse (use PowerShell ou REST API) ou SQL Managed Instance. Para obter mais informações, consulte [a Máscara de Dados Dinâmicos.](/sql/relational-databases/security/dynamic-data-masking)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Confiem de uma máscara de dados dinâmica para a sua base de dados utilizando o portal Azure

1. Lançar o portal Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue para a página de definições da base de dados que inclui os dados sensíveis que pretende mascarar.
3. Clique na lâmina **de mascaramento de dados dinâmicos** na secção **de Segurança** da sua base de dados.

   ![Screenshot que mostra a secção de Segurança com máscara de dados dinâmicos em destaque.](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. Na página **de configuração de máscara de dados dinâmicos,** pode ver algumas colunas de base de dados que o motor de recomendações sinalizou para mascarar. Para aceitar as recomendações, basta clicar em **Adicionar Máscara** para uma ou mais colunas e uma máscara é criada com base no tipo predefinido para esta coluna. Pode alterar a função de máscara clicando na regra de mascarar e editando o formato de campo de máscara para um formato diferente à sua escolha. Certifique-se de clicar **em Guardar** para guardar as suas definições.

    ![Screenshot que mostra a página de configuração de máscara de dados dinâmicos.](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Para adicionar uma máscara para qualquer coluna na sua base de dados, no topo da página de configuração **de máscara de dados dinâmicos,** clique em Adicionar **Máscara** para abrir a página de configuração **'Adicionar Regra de Máscara'.**

    ![Screenshot que mostra a página de configuração da Regra de Máscara de Adicionar.](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Selecione o **Esquema,** **Tabela** e **Coluna** para definir o campo designado para mascarar.
7. **Selecione como mascarar** a lista de categorias sensíveis de mascaramento de dados.

    ![Screenshot que mostra as categorias sensíveis de mascaramento de dados na secção Select how to mask.](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Clique **Add** na página de regra de mascarar dados para atualizar o conjunto de regras de mascaramento na política dinâmica de mascaramento de dados.
9. Digite os utilizadores SQL ou as identidades do Azure Ative Directory (Azure AD) que devem ser excluídas da máscara e tenham acesso aos dados sensíveis desmascarados. Esta deve ser uma lista separada de utilizadores. Os utilizadores com privilégios de administrador têm sempre acesso aos dados originais desmascarados.

    ![Painel de navegação](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Para que a camada de aplicação possa apresentar dados sensíveis para utilizadores privilegiados da aplicação, adicione o utilizador SQL ou a identidade AD AZure que a aplicação utiliza para consultar a base de dados. Recomenda-se vivamente que esta lista contenha um número mínimo de utilizadores privilegiados para minimizar a exposição dos dados sensíveis.

10. Clique em **Guardar** na página de configuração de mascarar dados para guardar a nova ou atualizada política de máscaras.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral da máscara dinâmica de dados, consulte [a máscara de dados dinâmica](dynamic-data-masking-overview.md).
- Também pode implementar máscaras de dados dinâmicas utilizando [cmdlets Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) ou a [API REST](https://docs.microsoft.com/rest/api/sql/).
