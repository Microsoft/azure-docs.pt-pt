---
title: Migrar aplicativos lógicos através de subscrições, grupos de recursos ou regiões
description: Migrar aplicações lógicas ou contas de integração para outras subscrições, grupos de recursos ou localizações do Azure (regiões)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878736"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Mover recursos de aplicações lógicas para outros grupos de recursos Do Azure, regiões ou subscrições

Para migrar a sua aplicação lógica ou recursos relacionados para outro grupo de recursos Azure, região ou subscrição, tem várias formas de completar estas tarefas, tais como o portal Azure, Azure PowerShell, Azure CLI e REST API. Antes de mover recursos, reveja estas considerações: 

* Só pode mover [tipos específicos de recursos de aplicações lógicas](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) entre grupos de recursos Do Azure ou subscrições.

* Verifique os [limites](../logic-apps/logic-apps-limits-and-config.md) do número de recursos de aplicações lógicas que pode ter na sua subscrição Azure e em cada região do Azure. Estes limites afetam se pode mover tipos de recursos específicos quando a região permanece a mesma entre subscrições ou grupos de recursos. Por exemplo, você pode ter apenas uma conta de integração de nível livre para cada região Azure em cada subscrição Azure.

* Quando se movimenta recursos, o Azure cria novas identidades de recursos. Por isso, certifique-se de que utiliza os novos IDs e atualize quaisquer scripts ou ferramentas que estejam associadas aos recursos movidos.

* Depois de migrar aplicações lógicas entre subscrições, grupos de recursos ou regiões, deve recriar ou reautorizar quaisquer ligações que exijam autenticação aberta (OAuth).

* Você pode mover um ambiente de serviço de [integração (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) apenas para outro grupo de recursos que existe na mesma região Azure ou subscrição Azure. Não é possível mover um ISE para um grupo de recursos que existe numa região azure diferente ou subscrição azure. Além disso, depois de tal movimento, deve atualizar todas as referências ao ISE nos fluxos de trabalho de aplicações lógicas, contas de integração, conexões, e assim por diante.

## <a name="prerequisites"></a>Pré-requisitos

* A mesma subscrição azure que foi usada para criar a app lógica ou conta de integração que você quer mover

* O proprietário de recursos permissões para mover e configurar os recursos que você quer. Saiba mais sobre [o controlo de acesso baseado em papéis (RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Mover recursos entre subscrições

Para mover um recurso, como uma app lógica ou uma conta de integração, para outra subscrição do Azure, pode utilizar o portal Azure, Azure PowerShell, Azure CLI ou REST API. Estes passos cobrem o portal Azure, que pode utilizar quando a região do recurso permanece a mesma. Para outras etapas e preparação geral, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso de aplicações lógicas que pretende mover.

1. Na página **'Overview'** do recurso, junto à **Subscrição,** selecione o link **de alteração.**

1. Na página **de recursos Move,** selecione o recurso de aplicações lógicae quaisquer recursos relacionados que queira mover.

1. Na lista **de Subscrição,** selecione a subscrição do destino.

1. A partir da lista de **grupos de Recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo**.

1. Para confirmar a sua compreensão de que quaisquer scripts ou ferramentas associadas aos recursos movidos não funcionarão até que os atualize com os novos IDs de recurso, selecione a caixa de confirmação e, em seguida, selecione **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Mover recursos entre grupos de recursos

Para mover um recurso, como uma app lógica, conta de integração ou ambiente de serviço de [integração (ISE),](connect-virtual-network-vnet-isolated-environment-overview.md)para outro grupo de recursos Azure, você pode usar o portal Azure, Azure PowerShell, Azure CLI ou REST API. Estes passos cobrem o portal Azure, que pode utilizar quando a região do recurso permanece a mesma. Para outras etapas e preparação geral, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

Antes de realmente mover recursos entre grupos, você pode testar se você pode mover com sucesso o seu recurso para outro grupo. Para mais informações, consulte [Validar o seu movimento](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso de aplicações lógicas que pretende mover.

1. Na página **'Overview'** do recurso, junto ao **grupo Recurso,** selecione o link de **alteração.**

1. Na página **de recursos Move,** selecione o recurso de aplicações lógicae quaisquer recursos relacionados que queira mover.

1. A partir da lista de **grupos de Recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo**.

1. Para confirmar a sua compreensão de que quaisquer scripts ou ferramentas associadas aos recursos movidos não funcionarão até que os atualize com os novos IDs de recurso, selecione a caixa de confirmação e, em seguida, selecione **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Movimentar recursos entre regiões

Quando pretende mover uma aplicação lógica para uma região diferente, as suas opções dependem da forma como criou a sua aplicação lógica. Com base na opção que escolher, deve recriar ou reautorizar as ligações na sua aplicação lógica.

* No portal Azure, recrie a aplicação lógica na nova região e reconfigure as definições de fluxo de trabalho. Para poupar tempo, pode copiar a definição de fluxo de trabalho subjacente e as ligações da aplicação de origem para a aplicação de destino. Para ver o "código" por trás de uma aplicação lógica, na barra de ferramentas Logic App Designer, selecione **a visão**de Código .

* Ao utilizar o Visual Studio e as Ferramentas de Aplicações Lógicas Azure para Estúdio Visual, pode abrir e descarregar a [sua aplicação lógica](../logic-apps/manage-logic-apps-with-visual-studio.md) a partir do portal Azure como [modelo de Gestor de Recursos Azure.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Este modelo está maioritariamente pronto para ser implantado e inclui as definições de recursos para a sua aplicação lógica, incluindo o próprio fluxo de trabalho, e ligações. O modelo também declara parâmetros para os valores a utilizar na implantação. Dessa forma, pode mudar mais facilmente onde e como implementa a aplicação lógica, com base nas suas necessidades. Para especificar a localização e outras informações necessárias para a implementação, pode utilizar um ficheiro de parâmetros separados.

* Se criou e implementou a sua aplicação lógica utilizando ferramentas de integração contínua (CI) e entrega contínua (CD), como o Azure Pipelines em Azure DevOps, pode implementar a sua app para outra região utilizando essas ferramentas.

Para obter mais informações sobre modelos de implementação para aplicações lógicas, consulte estes tópicos:

* [Visão geral: Implementação automática para Aplicações Lógicas Azure utilizando modelos de Gestor de Recursos Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Encontre, abra e descarregue a sua aplicação lógica do portal Azure para o Estúdio Visual](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Crie modelos de Gestor de Recursos Azure para aplicações lógicas azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implementar modelos de Gestor de Recursos Azure para aplicações lógicas azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Recursos relacionados

Alguns recursos do Azure, como os recursos de gateway de dados no Local em Azure, podem existir numa região que difere das aplicações lógicas que utilizam esses recursos. No entanto, outros recursos do Azure, como contas de integração ligadas, devem existir na mesma região que as suas aplicações lógicas. Com base no seu cenário, certifique-se de que as suas aplicações lógicas podem aceder aos recursos que as suas apps esperam existir na mesma região.

Por exemplo, para ligar uma aplicação lógica a uma conta de integração, ambos os recursos devem existir na mesma região. Em cenários como a recuperação de desastres, normalmente quer contas de integração que tenham a mesma configuração e artefactos. Noutros cenários, poderá necessitar de contas de integração com diferentes configurações e artefactos.

Os conectores personalizados em Aplicações Lógicas Azure são visíveis para os autores e utilizadores dos conectores que têm a mesma subscrição Azure e o mesmo inquilino azure Ative Directory. Estes conectores estão disponíveis na mesma região onde são implementadas aplicações lógicas. Para obter mais informações, veja [Partilhar conectores personalizados na sua organização](https://docs.microsoft.com/connectors/custom-connectors/share).

O modelo que obtém do Visual Studio inclui apenas as definições de recursos para a sua aplicação lógica e as suas ligações. Assim, se a sua aplicação lógica utiliza outros recursos, por exemplo, uma conta de integração e artefactos B2B, como parceiros, acordos e esquemas, você deve exportar o modelo dessa conta de integração usando o portal Azure. Este modelo inclui as definições de recursos tanto para a conta de integração como para os artefactos. No entanto, o modelo não está totalmente parametrizado. Por isso, tem de parametrizar manualmente os valores que pretende utilizar para a sua implantação.

### <a name="export-templates-for-integration-accounts"></a>Modelos de exportação para contas de integração

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração.

1. No menu da sua conta de integração, em **Definições,** selecione modelo de **exportação**.

1. Na barra de ferramentas, selecione **Descarregar**e guardar o modelo.

1. Abra e edite o modelo para parametrizar os valores necessários para a implantação.

## <a name="next-steps"></a>Passos seguintes

[Mover recursos do Azure para novos grupos de recursos ou subscrições](../azure-resource-manager/management/move-resource-group-and-subscription.md)
