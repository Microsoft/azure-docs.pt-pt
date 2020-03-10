---
title: Gerir recursos - Portal Azure
description: Utilize o portal Azure e o Gestor de Recursos Azure para gerir os seus recursos. Mostra como implantar e apagar recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360025"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gerir os recursos do Azure utilizando o portal Azure

Aprenda a utilizar o [portal Azure](https://portal.azure.com) com o Gestor de [Recursos Azure](overview.md) para gerir os seus recursos Azure. Para gerir grupos de recursos, consulte manage os grupos de [recursos Do Manage Azure utilizando o portal Azure](manage-resource-groups-portal.md).

Outros artigos sobre gestão de recursos:

- [Gerir os recursos azure utilizando o Azure CLI](manage-resources-cli.md)
- [Gerir os recursos azure utilizando o Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Implementar recursos para um grupo de recursos

Depois de ter criado um modelo de Gestor de Recursos, pode utilizar o portal Azure para implementar os seus recursos Azure. Para criar um modelo, consulte [Quickstart: Crie e implemente modelos](../templates/quickstart-create-templates-use-the-portal.md)do Gestor de Recursos Azure utilizando o portal Azure . Para implementar um modelo utilizando o portal, consulte [recursos de implantação com modelos de Gestor](../templates/deploy-portal.md)de Recursos e portal Azure .

## <a name="open-resources"></a>Recursos abertos

Os recursos azure são organizados pelos serviços azure e por grupos de recursos. Os seguintes procedimentos mostram como abrir uma conta de armazenamento chamada **mystorage0207**. A máquina virtual reside num grupo de recursos chamado **mystorage0207rg**.

Para abrir um recurso pelo tipo de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione o serviço Azure. Neste caso, contas de **armazenamento.**  Se não vir o serviço listado, selecione **Todos os serviços**e, em seguida, selecione o tipo de serviço.

    ![recurso azure aberto no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecione o recurso que pretende abrir.

    ![recurso azure aberto no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Uma conta de armazenamento parece:

    ![recurso azure aberto no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Para abrir um recurso por grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione **Grupos de Recursos** para listar o recurso dentro do grupo.
3. Selecione o recurso que pretende abrir. 

## <a name="manage-resources"></a>Gerir recursos

Ao visualizar um recurso no portal, vê as opções para gerir esse recurso em particular.

![gerir os recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A imagem mostra as opções de gestão de uma máquina virtual Azure. Pode realizar operações como iniciar, reiniciar e parar uma máquina virtual.

## <a name="delete-resources"></a>Eliminar recursos

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Eliminar**. A imagem seguinte mostra as opções de gestão de uma máquina virtual.

    ![eliminar recurso azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digite o nome do recurso para confirmar a eliminação e, em seguida, **selecione Eliminar**.

Para obter mais informações sobre como o Gestor de Recursos azure ordena a eliminação de recursos, consulte a eliminação do grupo de recursos do Gestor de [Recursos do Azure.](delete-resource-group.md)

## <a name="move-resources"></a>Mover recursos

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Mover**. A imagem seguinte mostra as opções de gestão de uma conta de armazenamento.

    ![mover recurso azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecione **Mover para outro grupo** de recursos ou avançar para outra **subscrição** dependendo das suas necessidades.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros utilizadores da sua organização apagam ou modifiquem acidentalmente recursos críticos, tais como subscrição azure, grupo de recursos ou recursos. 

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Fechaduras**. A imagem seguinte mostra as opções de gestão de uma conta de armazenamento.

    ![lock recurso azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecione **Adicionar**e, em seguida, especificar as propriedades de bloqueio.

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Etiquetar recursos

A marcação ajuda a organizar o seu grupo de recursos e recursos logicamente. 

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Etiquetas**. A imagem seguinte mostra as opções de gestão de uma conta de armazenamento.

    ![recurso tag azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Especifique as propriedades da etiqueta e, em seguida, selecione **Guardar**.

Para obter informações, consulte [Utilizar tags para organizar os seus recursos Azure.](tag-resources.md#portal)

## <a name="monitor-resources"></a>Monitorizar recursos

Ao abrir um recurso, o portal apresenta gráficos e tabelas predefinidos para monitorizar esse tipo de recursos. A imagem que se segue mostra os gráficos para uma máquina virtual:

![monitorizar recurso azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Pode selecionar o ícone do pino no canto superior direito dos gráficos para fixar o gráfico no painel de instrumentos. Para aprender a trabalhar com dashboards, consulte [Criar e partilhar dashboards no portal Azure.](../../azure-portal/azure-portal-dashboards.md)

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes

- Para aprender O Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](overview.md)
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](../templates/template-syntax.md)de Recursos Azure .
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](/azure/azure-resource-manager/)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte a referência do [modelo](/azure/templates/).
