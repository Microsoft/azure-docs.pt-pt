---
title: Gerir recursos do Azure com o portal do Azure | Documentos da Microsoft
description: Utilize o portal do Azure e Azure Resource Manager para gerir os recursos.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825242"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gerir recursos do Azure com o portal do Azure

Saiba como utilizar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para gerir os recursos do Azure. Para gerir grupos de recursos, veja [grupos de recursos de gerir o Azure com o portal do Azure](./manage-resource-groups-portal.md).

Outros artigos sobre a gestão de recursos:

- [Gerir recursos do Azure com a CLI do Azure](./manage-resources-cli.md)
- [Gerir recursos do Azure com o Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Implementar recursos para um grupo de recursos

Depois de criar um modelo do Resource Manager, pode utilizar o portal do Azure para implementar os recursos do Azure. Para criar um modelo, consulte [início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Para implementar um modelo com o portal, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Recursos abertos

Recursos do Azure são organizados por serviços do Azure e grupos de recursos. Os procedimentos a seguir mostra como abrir uma conta de armazenamento denominada **mystorage0207**. A máquina virtual reside num grupo de recursos chamado **mystorage0207rg**.

Para abrir um recurso pelo tipo de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione o serviço do Azure. Neste caso, **contas de armazenamento**.  Se não vir o serviço listado, selecione **todos os serviços**e, em seguida, selecione o tipo de serviço.

    ![Abra o recurso do azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecione o recurso que pretende abrir.

    ![Abra o recurso do azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Uma conta de armazenamento é semelhante a:

    ![Abra o recurso do azure no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Para abrir um grupo de recursos por recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione **grupos de recursos** para listar o recurso dentro do grupo.
3. Selecione o recurso que pretende abrir. 

## <a name="manage-resources"></a>Gerir recursos

Ao visualizar um recurso no portal, verá as opções para gerir desse recurso.

![gerir recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A captura de ecrã mostra as opções de gestão para uma máquina virtual do Azure. Pode efetuar operações como iniciar, reiniciar e parar uma máquina virtual.

## <a name="delete-resources"></a>Eliminar recursos

1. Abra o recurso no portal. Para obter os passos, consulte [abrir recursos](#open-resources).
2. Selecione **Eliminar**. Captura de ecrã seguinte mostra as opções de gestão para uma máquina virtual.

    ![eliminar recurso do azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Escreva o nome do recurso para confirmar a eliminação e, em seguida, selecione **eliminar**.

Para obter mais informações sobre como o Azure Resource Manager ordena a eliminação de recursos, consulte [eliminação do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Mover recursos

1. Abra o recurso no portal. Para obter os passos, consulte [abrir recursos](#open-resources).
2. Selecione **mover**. Captura de ecrã seguinte mostra as opções de gestão para uma conta de armazenamento.

    ![mover recursos do azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecione **mover para outro grupo de recursos** ou **Moeve para outra subscrição** consoante as suas necessidades.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloquear recursos

Bloqueio evita que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos, como o recurso, grupo de recursos ou subscrição do Azure. 

1. Abra o recurso no portal. Para obter os passos, consulte [abrir recursos](#open-resources).
2. Selecione **bloqueia**. Captura de ecrã seguinte mostra as opções de gestão para uma conta de armazenamento.

    ![recurso de bloqueio do azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecione **adicionar**e, em seguida, especifique as propriedades de bloqueio.

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Etiquetar recursos

Ajuda a organizar logicamente o seu grupo de recursos e recursos de marcação. 

1. Abra o recurso no portal. Para obter os passos, consulte [abrir recursos](#open-resources).
2. Selecione **etiquetas**. Captura de ecrã seguinte mostra as opções de gestão para uma conta de armazenamento.

    ![resource etiqueta do azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Especificar as propriedades de etiqueta e, em seguida, selecione **guardar**.

Para obter informações, consulte [utilizar etiquetas para organizar os recursos do Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Monitorizar recursos

Quando abre um recurso, o portal apresenta gráficos predefinidos e tabelas para esse tipo de recurso de monitorização. Captura de ecrã seguinte mostra os gráficos para uma máquina virtual:

![recursos de monitor do azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Pode selecionar o ícone de pin no canto superior direito de gráficos para afixar o gráfico ao dashboard. Para saber mais sobre como trabalhar com os dashboards, veja [criar e partilhar dashboards no portal do Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para ver os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).