---
title: 'Portal do Azure: Máscara de dados dinâmicos da base de dados SQL | Documentos da Microsoft'
description: Como começar com a máscara de dados dinâmicos da base de dados SQL no portal do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2018
ms.openlocfilehash: 3d5ab203268ced1951d2ba9c852ece5bd5467c68
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341940"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Introdução à máscara com o portal do Azure de dados dinâmicos da base de dados SQL

Este artigo mostra-lhe como implementar [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md) com o portal do Azure. Também pode implementar através de máscara de dados dinâmicos [cmdlets do Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) ou o [REST API](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurar a máscara da base de dados com o portal do Azure de dados dinâmicos

1. Iniciar o portal do Azure no [ https://portal.azure.com ](https://portal.azure.com).
2. Navegue para a página de definições da base de dados que inclui os dados confidenciais que pretende mascarar.
3. Clique nas **máscara de dados dinâmicos** mosaico que inicia o **Dynamic Data Masking** página de configuração.

   * Em alternativa, pode rolar para baixo para o **Operations** secção e clique em **Dynamic Data Masking**.

     ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Na **Dynamic Data Masking** página de configuração, poderá ver algumas colunas de base de dados que o motor de recomendações tem sinalizados para máscara. Para aceitar as recomendações, basta clicar **adicionar máscara** para uma ou mais colunas e uma máscara, é criado com base no tipo de padrão para esta coluna. Pode alterar a função de máscara ao clicar na regra de máscara e a máscara de edição formato do campo num formato diferente da sua preferência. Certifique-se de que clique em **guardar** para guardar as definições.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Para adicionar uma máscara para qualquer coluna na base de dados, na parte superior a **máscara de dados dinâmicos** página de configuração, clique em **adicionar máscara** para abrir o **Adicionar regra de máscara** página de configuração .

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Selecione o **esquema**, **tabela** e **coluna** para definir o campo designado de máscara.
7. Escolher uma **formato de máscara de campo** na lista de categorias de máscara de dados confidenciais.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Clique em **guardar** nos página de regra para atualizar o conjunto de regras de política de máscara de dados dinâmicos de máscara de máscara de dados.
9. Escreva os utilizadores SQL ou identidades do AAD que devem ser excluídas da máscara e tem acesso aos dados confidenciais sem máscara. Isso deve ser uma lista delimitada por vírgulas de utilizadores. Os utilizadores com privilégios de administrador tenham sempre acesso aos dados sem máscara originais.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Com que a camada de aplicativo pode apresentar dados confidenciais para usuários do aplicativo com privilégios, adicione o utilizador SQL ou o aplicativo usa para consultar a base de dados de identidade do AAD. É altamente recomendável que esta lista contém um número mínimo de utilizadores com privilégios para minimizar a exposição dos dados confidenciais.

10. Clique em **guardar** nos página de configuração para guardar a nova ou atualizada de máscara de dados a máscara de política.

## <a name="next-steps"></a>Passos Seguintes

* Para uma descrição geral de máscara de dados dinâmica, consulte [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md).
* Também pode implementar através de máscara de dados dinâmicos [cmdlets do Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) ou o [REST API](https://docs.microsoft.com/rest/api/sql/).
