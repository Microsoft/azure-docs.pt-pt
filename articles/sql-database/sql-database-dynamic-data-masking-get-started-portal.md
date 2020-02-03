---
title: 'Portal Azure: Mascaramento de dados dinâmico'
description: Como começar com a Base de Dados SQL mascarar dados dinâmicos no portal Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722123"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Começar com a Base de Dados SQL mascarar dados dinâmicos com o portal Azure

Este artigo mostra-lhe como implementar a [máscara dinâmica](sql-database-dynamic-data-masking-get-started.md) de dados com o portal Azure. Também pode implementar máscaras dinâmicas de dados utilizando cmdlets de base de [dados Azure SQL](https://docs.microsoft.com/powershell/module/az.sql/) ou a [API REST](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurar máscaras de dados dinâmicos para a sua base de dados utilizando o portal Azure

1. Lance o portal Azure em [https://portal.azure.com. ](https://portal.azure.com)
2. Navegue para a página de definições da base de dados que inclui os dados sensíveis que pretende mascarar.
3. Clique no azulejo **Dynamic Data Masking** que lança a página de configuração **de mascaramento** de dados dinâmicos.

   * Em alternativa, pode deslocar-se até à secção **Operações** e clicar em Máscaras de **Dados Dinâmicos**.

     ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Na página de configuração **Dynamic Data Masking,** pode ver algumas colunas de base de dados que o motor de recomendações sinalizou para mascarar. Para aceitar as recomendações, basta clicar em **Adicionar Máscara** para uma ou mais colunas e é criada uma máscara com base no tipo padrão para esta coluna. Pode alterar a função de mascarar clicando na regra de mascarar e editando o formato de campo de máscarapara um formato diferente da sua escolha. Certifique-se de clicar em **Guardar** para guardar as definições.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Para adicionar uma máscara para qualquer coluna na sua base de dados, no topo da página de configuração de máscara de **dados dinâmicos,** clique em **Adicionar Máscara** para abrir a página de configuração da Regra **de Mascaramento de Adicionar.**

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Selecione o **Schema,** **a Tabela** e a **Coluna** para definir o campo designado para mascarar.
7. Escolha um **formato** de campo de máscara sabotado da lista de categorias sensíveis de máscara de dados.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Clique em **Guardar** na página de regras de máscara de dados para atualizar o conjunto de regras de mascaramento na política dinâmica de máscara de dados.
9. Digite os utilizadores SQL ou identidades AAD que devem ser excluídas da máscara e tenha acesso aos dados sensíveis desmascarados. Esta deve ser uma lista de utilizadores separados pelo ponto e vírgula. Os utilizadores com privilégios de administrador têm sempre acesso aos dados originais desmascarados.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Para que a camada de aplicação possa apresentar dados sensíveis para utilizadores privilegiados da aplicação, adicione o utilizador SQL ou a identidade AAD que a aplicação utiliza para consultar a base de dados. É altamente recomendado que esta lista contenha um número mínimo de utilizadores privilegiados para minimizar a exposição dos dados sensíveis.

10. Clique em **Guardar** na página de configuração de máscara de dados para guardar a nova ou atualizada política de mascaramento.

## <a name="next-steps"></a>Passos Seguintes

* Para uma visão geral da máscara dinâmica de dados, consulte a máscara de [dados dinâmicos](sql-database-dynamic-data-masking-get-started.md).
* Também pode implementar máscaras dinâmicas de dados utilizando cmdlets de base de [dados Azure SQL](https://docs.microsoft.com/powershell/module/az.sql/) ou a [API REST](https://docs.microsoft.com/rest/api/sql/).
