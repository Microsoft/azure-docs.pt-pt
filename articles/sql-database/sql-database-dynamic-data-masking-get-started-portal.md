---
title: 'Portal do Azure: máscara de dados dinâmicos do banco de dados SQL '
description: Como começar a usar o mascaramento de dados dinâmicos do SQL Database no portal do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: d92f77c56221d7c79ed30a6ca67d31d1b3f7f703
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690581"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Introdução ao mascaramento de dados dinâmicos do SQL Database com o portal do Azure

Este artigo mostra como implementar a [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md) com o portal do Azure. Você também pode implementar a máscara de dados dinâmicos usando [cmdlets do banco de dados SQL do Azure](https://docs.microsoft.com/powershell/module/az.sql/) ou a [API REST](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurar o mascaramento de dados dinâmicos para seu banco usando o portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configurações do banco de dados do que inclui as informações confidenciais que você deseja mascarar.
3. Clique no bloco **máscara de dados dinâmicos** que inicia a página de configuração de **máscara de dados dinâmicos** .

   * Como alternativa, você pode rolar para baixo até a seção **operações** e clicar em **máscara de dados dinâmicos**.

     ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Na página configuração do **máscara de dados dinâmicos** , você pode ver algumas colunas do banco de dados que o mecanismo de recomendações sinalizou para mascaramento. Para aceitar as recomendações, basta clicar em **Adicionar máscara** para uma ou mais colunas e uma máscara será criada com base no tipo padrão para essa coluna. Você pode alterar a função de mascaramento clicando na regra de mascaramento e editando o formato do campo de mascaramento para um formato diferente de sua escolha. Certifique-se de clicar em **salvar** para salvar as configurações.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Para adicionar uma máscara para qualquer coluna no banco de dados, na parte superior da página de configuração do **máscara de dados dinâmicos** , clique em **Adicionar máscara** para abrir a página de configuração **Adicionar regra de mascaramento** .

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Selecione o **esquema**, **tabela** e **coluna** para definir o campo designado para mascaramento.
7. Escolha um **formato de campo de mascaramento** na lista de categorias de máscara de dados confidenciais.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Clique em **salvar** na página regra de mascaramento de dados para atualizar o conjunto de regras de mascaramento na política de mascaramento de dados dinâmicos.
9. Digite os usuários do SQL ou as identidades do AAD que devem ser excluídas do mascaramento e tenham acesso aos dados confidenciais sem máscara. Essa deve ser uma lista de usuários separados por ponto e vírgula. Os usuários com privilégios de administrador sempre têm acesso aos dados não mascarados originais.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Para fazer isso, a camada de aplicativo pode exibir dados confidenciais para usuários com privilégios de aplicativo, adicionar a identidade do usuário do SQL ou do AAD que o aplicativo usa para consultar o banco de dados. É altamente recomendável que essa lista contenha um número mínimo de usuários com privilégios para minimizar a exposição dos dados confidenciais.

10. Clique em **salvar** na página de configuração de mascaramento de dados para salvar a política de mascaramento nova ou atualizada.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral da máscara de dados dinâmicos, consulte [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md).
* Você também pode implementar a máscara de dados dinâmicos usando [cmdlets do banco de dados SQL do Azure](https://docs.microsoft.com/powershell/module/az.sql/) ou a [API REST](https://docs.microsoft.com/rest/api/sql/).
