---
title: Gerir recursos - Portal Azure
description: Utilize o portal Azure e o Azure Resource Manager para gerir os seus recursos. Mostra como implantar e eliminar recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 266ad613a4966b26fc4a6ddffbb7b4196aa937b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372107"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gerir os recursos do Azure utilizando o portal Azure

Saiba como utilizar o [portal Azure](https://portal.azure.com) com [o Azure Resource Manager](overview.md) para gerir os seus recursos Azure. Para gerir grupos de recursos, consulte [os grupos de recursos Manage Azure utilizando o portal Azure](manage-resource-groups-portal.md).

Outros artigos sobre gestão de recursos:

- [Gerir os recursos do Azure utilizando o Azure CLI](manage-resources-cli.md)
- [Gerir os recursos da Azure utilizando a Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Mobilizar recursos para um grupo de recursos

Depois de ter criado um modelo de Gestor de Recursos, pode utilizar o portal Azure para implementar os seus recursos Azure. Para criar um modelo, consulte [Quickstart: Crie e implemente os modelos do Gestor de Recursos Azure utilizando o portal Azure](../templates/quickstart-create-templates-use-the-portal.md). Para implementar um modelo utilizando o portal, consulte [implementar recursos com modelos de Gestor de Recursos e portal Azure](../templates/deploy-portal.md).

## <a name="open-resources"></a>Recursos abertos

Os recursos Azure são organizados pelos serviços Azure e por grupos de recursos. Os seguintes procedimentos mostram como abrir uma conta de armazenamento chamada **mystorage0207**. A máquina virtual reside num grupo de recursos chamado **mystorage0207rg.**

Para abrir um recurso pelo tipo de serviço:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione o serviço Azure. Neste caso, **contas de armazenamento.**  Se não vir o serviço listado, selecione **Todos os serviços**e, em seguida, selecione o tipo de serviço.

    ![recurso azul aberto no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecione o recurso que pretende abrir.

    ![recurso azul aberto no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Uma conta de armazenamento parece:

    ![recurso azul aberto no portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Para abrir um recurso por grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel esquerdo, selecione **grupos de recursos** para listar o recurso dentro do grupo.
3. Selecione o recurso que pretende abrir. 

## <a name="manage-resources"></a>Gerir recursos

Ao visualizar um recurso no portal, vê as opções para gerir esse recurso em particular.

![gerir os recursos do Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

A imagem mostra as opções de gestão de uma máquina virtual Azure. Pode executar operações como iniciar, reiniciar e parar uma máquina virtual.

## <a name="delete-resources"></a>Eliminar recursos

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Eliminar**. A imagem que se segue mostra as opções de gestão de uma máquina virtual.

    ![eliminar recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Digite o nome do recurso para confirmar a eliminação e, em seguida, **selecione Delete**.

Para obter mais informações sobre como o Azure Resource Manager ordena a supressão de recursos, consulte a eliminação do [grupo de recursos Azure Resource Manager](delete-resource-group.md).

## <a name="move-resources"></a>Mover recursos

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Move**. A imagem que se segue mostra as opções de gestão de uma conta de armazenamento.

    ![mover recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecione **Mover-se para outro grupo de recursos** ou mude para outra **subscrição** dependendo das suas necessidades.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos, tais como subscrição do Azure, grupo de recursos ou recursos. 

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Fechaduras**. A imagem que se segue mostra as opções de gestão de uma conta de armazenamento.

    ![bloquear recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. **Selecione Adicionar**e, em seguida, especifique as propriedades do bloqueio.

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Etiquetar recursos

A marcação ajuda a organizar logicamente o seu grupo de recursos e recursos. 

1. Abra o recurso no portal. Para os passos, consulte [os recursos abertos.](#open-resources)
2. Selecione **Etiquetas**. A imagem que se segue mostra as opções de gestão de uma conta de armazenamento.

    ![recurso de marca azul](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Especifique as propriedades da etiqueta e, em seguida, **selecione Guardar**.

Para obter informações, consulte [a utilização de tags para organizar os seus recursos Azure.](tag-resources.md#portal)

## <a name="monitor-resources"></a>Monitorizar recursos

Quando abre um recurso, o portal apresenta gráficos e tabelas predefinidos para monitorizar esse tipo de recurso. A imagem que se segue mostra os gráficos de uma máquina virtual:

![monitorar recurso azul](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Pode selecionar o ícone do pino no canto superior direito dos gráficos para fixar o gráfico ao painel de instrumentos. Para aprender a trabalhar com dashboards, consulte [criar e partilhar dashboards no portal Azure.](../../azure-portal/azure-portal-dashboards.md)

## <a name="manage-access-to-resources"></a>Gerir o acesso aos recursos

[O controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para obter mais informações, veja [Utilizar o portal do Azure para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber o Gestor de Recursos Azure, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../templates/template-syntax.md).
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](../index.yml)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte [a referência do modelo](/azure/templates/).
