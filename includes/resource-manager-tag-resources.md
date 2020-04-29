---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132238"
---
Se um utilizador não tiver o acesso necessário para a aplicação de etiquetas, pode atribuir a função **'Colaborador** de Etiquetas' ao utilizador. Para mais informações, consulte [Tutorial: Conceda ao utilizador o acesso aos recursos Azure utilizando o RBAC e o portal Azure](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Para visualizar as etiquetas para um recurso ou um grupo de recursos, procure etiquetas existentes na visão geral. Se não tiver aplicado etiquetas anteriormente, a lista estará vazia.

   ![Ver etiquetas para recursos ou grupo de recursos](./media/resource-manager-tag-resources/view-tags.png)

1. Para adicionar uma etiqueta, selecione **Clique aqui para adicionar tags**.

1. Forneça um nome e valor.

   ![Adicionar etiqueta](./media/resource-manager-tag-resources/add-tag.png)

1. Continue a adicionar etiquetas conforme necessário. Quando terminar, selecione **Guardar**.

   ![Guardar etiquetas](./media/resource-manager-tag-resources/save-tags.png)

1. As etiquetas são agora exibidas na visão geral.

   ![Mostrar etiquetas](./media/resource-manager-tag-resources/view-new-tags.png)

1. Para adicionar ou apagar uma etiqueta, selecione **alterar**.

1. Para apagar uma etiqueta, selecione o ícone do lixo. Em seguida, selecione **Guardar**.

   ![Eliminar etiqueta](./media/resource-manager-tag-resources/delete-tag.png)

Para atribuir etiquetas a granel a vários recursos:

1. De qualquer lista de recursos, selecione a caixa de verificação para os recursos que pretende atribuir à etiqueta. Em seguida, selecione **etiquetas de atribuição**.

   ![Selecione vários recursos](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Adicione nomes e valores. Quando terminar, selecione **Guardar**.

   ![Selecione aatribuição](./media/resource-manager-tag-resources/select-assign.png)

Para ver todos os recursos com uma etiqueta:

1. No menu do portal Azure, procure **etiquetas.** Selecione-o a partir das opções disponíveis.

   ![Encontre por tag](./media/resource-manager-tag-resources/find-tags-general.png)

1. Selecione a etiqueta para visualizar recursos.

   ![Selecione etiqueta](./media/resource-manager-tag-resources/select-tag.png)

1. Todos os recursos com esta etiqueta são exibidos.

   ![Ver recursos por tag](./media/resource-manager-tag-resources/view-resources-by-tag.png)
