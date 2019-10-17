---
title: Gerenciar recursos do Azure usando o portal do Azure | Microsoft Docs
description: Use o portal do Azure e Azure Resource Manager para gerenciar seus recursos. Mostra como implantar e excluir recursos.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c52d54ca02436f7224346a65048244a5ec8d104c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390399"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gerenciar recursos do Azure usando o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [Azure Resource Manager](resource-group-overview.md) para gerenciar seus recursos do Azure. Para gerenciar grupos de recursos, consulte [gerenciar grupos de recursos do Azure usando o portal do Azure](./manage-resource-groups-portal.md).

Outros artigos sobre o gerenciamento de recursos:

- [Gerenciar recursos do Azure usando o CLI do Azure](./manage-resources-cli.md)
- [Gerenciar recursos do Azure usando o Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo do Resource Manager, você pode usar o portal do Azure para implantar os recursos do Azure. Para criar um modelo, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [implantar recursos com modelos do Resource Manager e portal do Azure](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Abrir recursos

Os recursos do Azure são organizados por serviços do Azure e por grupos de recursos. Os procedimentos a seguir mostram como abrir uma conta de armazenamento chamada **mystorage0207**. A máquina virtual reside em um grupo de recursos chamado **mystorage0207rg**.

Para abrir um recurso pelo tipo de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione o serviço do Azure. Nesse caso, **contas de armazenamento**.  Se você não vir o serviço listado, selecione **todos os serviços**e, em seguida, selecione o tipo de serviço.

    ![abrir recurso do Azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecione o recurso que você deseja abrir.

    ![abrir recurso do Azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Uma conta de armazenamento é semelhante A:

    ![abrir recurso do Azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Para abrir um recurso por grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione **grupos de recursos** para listar o recurso dentro do grupo.
3. Selecione o recurso que você deseja abrir. 

## <a name="manage-resources"></a>Gerir recursos

Ao exibir um recurso no portal, você verá as opções para gerenciar esse recurso específico.

![gerenciar recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A captura de tela mostra as opções de gerenciamento para uma máquina virtual do Azure. Você pode executar operações como iniciar, reiniciar e parar uma máquina virtual.

## <a name="delete-resources"></a>Eliminar recursos

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecione **Eliminar**. A captura de tela a seguir mostra as opções de gerenciamento para uma máquina virtual.

    ![excluir recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digite o nome do recurso para confirmar a exclusão e, em seguida, selecione **excluir**.

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](./resource-group-delete.md).

## <a name="move-resources"></a>Mover recursos

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecione **mover**. A captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![mover recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecione **mover para outro grupo de recursos** ou **mover para outra assinatura** , dependendo de suas necessidades.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecione **bloqueios**. A captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![bloquear recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecione **Adicionar**e, em seguida, especifique as propriedades do bloqueio.

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Etiquetar recursos

A marcação ajuda a organizar o grupo de recursos e os recursos logicamente. 

1. Abra o recurso no Portal. Para as etapas, consulte [recursos abertos](#open-resources).
2. Selecione **Etiquetas**. A captura de tela a seguir mostra as opções de gerenciamento para uma conta de armazenamento.

    ![marcar recurso do Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Especifique as propriedades da marca e, em seguida, selecione **salvar**.

Para obter informações, consulte [usando marcas para organizar os recursos do Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Monitorizar recursos

Quando você abre um recurso, o portal apresenta gráficos e tabelas padrão para monitorar esse tipo de recurso. A captura de tela a seguir mostra os grafos de uma máquina virtual:

![monitorar recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Você pode selecionar o ícone de pino no canto superior direito dos grafos para fixar o grafo no painel. Para saber mais sobre como trabalhar com painéis, consulte [criando e compartilhando painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](./resource-group-overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).
