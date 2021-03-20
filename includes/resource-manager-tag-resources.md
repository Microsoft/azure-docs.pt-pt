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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80132238"
---
Se um utilizador não tiver o acesso necessário para aplicar tags, pode atribuir a função **De Colaborador tag** ao utilizador. Para obter mais informações, consulte [Tutorial: Conceder a um utilizador acesso aos recursos do Azure utilizando o RBAC e o portal Azure.](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)

1. Para visualizar as etiquetas para um recurso ou um grupo de recursos, procure etiquetas existentes na visão geral. Se não tiver aplicado etiquetas anteriormente, a lista estará vazia.

   ![Ver tags para recursos ou grupo de recursos](./media/resource-manager-tag-resources/view-tags.png)

1. Para adicionar uma etiqueta, **selecione Clique aqui para adicionar tags**.

1. Forneça um nome e valor.

   ![Adicionar etiqueta](./media/resource-manager-tag-resources/add-tag.png)

1. Continue a adicionar etiquetas conforme necessário. Quando terminar, selecione **Guardar**.

   ![Guardar tags](./media/resource-manager-tag-resources/save-tags.png)

1. As etiquetas são agora apresentadas no panorama geral.

   ![Mostrar tags](./media/resource-manager-tag-resources/view-new-tags.png)

1. Para adicionar ou eliminar uma etiqueta, selecione **alterar**.

1. Para eliminar uma etiqueta, selecione o ícone do lixo. Em seguida, **selecione Save**.

   ![Eliminar tag](./media/resource-manager-tag-resources/delete-tag.png)

Para atribuir etiquetas a granel a múltiplos recursos:

1. A partir de qualquer lista de recursos, selecione a caixa de verificação para os recursos que pretende atribuir à etiqueta. Em seguida, **selecione Atribuir tags**.

   ![Selecione vários recursos](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Adicione nomes e valores. Quando terminar, selecione **Guardar**.

   ![Selecione atribuir](./media/resource-manager-tag-resources/select-assign.png)

Para ver todos os recursos com uma etiqueta:

1. No menu do portal Azure, procure **etiquetas.** Selecione-o nas opções disponíveis.

   ![Encontrar por etiqueta](./media/resource-manager-tag-resources/find-tags-general.png)

1. Selecione a etiqueta para visualizar recursos.

   ![Selecione tag](./media/resource-manager-tag-resources/select-tag.png)

1. Todos os recursos com esta etiqueta são exibidos.

   ![Ver recursos por etiqueta](./media/resource-manager-tag-resources/view-resources-by-tag.png)
