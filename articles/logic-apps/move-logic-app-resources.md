---
title: Mover aplicativos lógicos através de subscrições, grupos de recursos ou regiões
description: Migrar aplicativos lógicos ou contas de integração para outras subscrições do Azure, grupos de recursos ou localizações (regiões)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87826216"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Mover recursos de aplicativos lógicos para outros grupos de recursos Azure, regiões ou subscrições

Para migrar a sua aplicação lógica ou recursos relacionados para outro grupo de recursos Azure, região ou subscrição, tem várias formas de completar estas tarefas, como o portal Azure PowerShell, Azure CLI e REST API. Antes de mover recursos, reveja estas considerações: 

* Só pode mover [tipos específicos de recursos de aplicações lógicas](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) entre grupos de recursos Azure ou subscrições.

* Verifique os [limites](../logic-apps/logic-apps-limits-and-config.md) do número de recursos de aplicações lógicas que pode ter na sua subscrição Azure e em cada região do Azure. Estes limites afetam se pode mover tipos específicos de recursos quando a região permanece a mesma entre subscrições ou grupos de recursos. Por exemplo, pode ter apenas uma conta de integração de nível Livre para cada região do Azure em cada subscrição do Azure.

* Quando se movimentam recursos, o Azure cria novas identidades de recursos. Por isso, certifique-se de que utiliza os novos IDs e atualize quaisquer scripts ou ferramentas que estejam associados aos recursos movidos.

* Depois de migrar aplicações lógicas entre subscrições, grupos de recursos ou regiões, tem de recriar ou reautorizar quaisquer ligações que exijam autenticação aberta (OAuth).

* Pode mover um ambiente de serviço de [integração (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) apenas para outro grupo de recursos que exista na mesma região de Azure ou assinatura Azure. Não é possível mover um ISE para um grupo de recursos que exista numa região de Azure ou Azure diferente. Além disso, após tal mudança, deve atualizar todas as referências ao ISE nos fluxos de trabalho de aplicações lógicas, contas de integração, ligações, e assim por diante.

## <a name="prerequisites"></a>Pré-requisitos

* A mesma subscrição do Azure que foi usada para criar a app lógica ou conta de integração que você quer mover

* Permissões do proprietário de recursos para mover e configurar os recursos que deseja. Saiba mais sobre [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Mover recursos entre subscrições

Para mover um recurso, como uma aplicação lógica ou conta de integração, para outra subscrição do Azure, pode utilizar o portal Azure PowerShell, Azure CLI ou REST API. Estes passos cobrem o portal Azure, que pode utilizar quando a região do recurso permanece a mesma. Para outras etapas e preparação geral, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso de aplicação lógica que pretende mover.

1. Na **página** geral do recurso, ao lado **da Subscrição,** selecione o link **de alteração.**

1. Na página **de recursos Move,** selecione o recurso de aplicação lógica e quaisquer recursos relacionados que pretenda mover.

1. Na lista **de Assinaturas,** selecione a subscrição do destino.

1. Na lista de **grupos de Recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, **selecione Criar um novo grupo**.

1. Para confirmar a sua compreensão de que quaisquer scripts ou ferramentas que estejam associados aos recursos movidos não funcionarão até que os atualize com os novos IDs de recursos, selecione a caixa de confirmação e, em seguida, selecione **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Mover recursos entre grupos de recursos

Para mover um recurso, como uma aplicação lógica, conta de integração ou [ambiente de serviço de integração (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), para outro grupo de recursos Azure, pode utilizar o portal Azure PowerShell, Azure CLI ou REST API. Estes passos cobrem o portal Azure, que pode utilizar quando a região do recurso permanece a mesma. Para outras etapas e preparação geral, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de realmente mover recursos entre grupos, você pode testar se você pode mover o seu recurso com sucesso para outro grupo. Para mais informações, consulte [Validar o seu movimento.](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o recurso de aplicação lógica que pretende mover.

1. Na página **'Visão Geral'** do recurso, ao lado **do grupo Resource**, selecione o link de **alteração.**

1. Na página **de recursos Move,** selecione o recurso de aplicação lógica e quaisquer recursos relacionados que pretenda mover.

1. Na lista de **grupos de Recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, **selecione Criar um novo grupo**.

1. Para confirmar a sua compreensão de que quaisquer scripts ou ferramentas que estejam associados aos recursos movidos não funcionarão até que os atualize com os novos IDs de recursos, selecione a caixa de confirmação e, em seguida, selecione **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Mover recursos entre regiões

Quando quer mover uma aplicação lógica para uma região diferente, as suas opções dependem da forma como criou a sua aplicação lógica. Com base na opção que escolher, tem de recriar ou reautorizar as ligações na sua aplicação lógica.

* No portal Azure, recrie a aplicação lógica na nova região e reconfigure as definições do fluxo de trabalho. Para economizar tempo, pode copiar a definição de fluxo de trabalho subjacente e as ligações da aplicação de origem para a aplicação de destino. Para ver o "código" por trás de uma aplicação lógica, na barra de ferramentas Logic App Designer, selecione **Code view**.

* Ao utilizar o Visual Studio e o Azure Logic Apps Tools for Visual Studio, pode [abrir e descarregar a sua aplicação lógica](../logic-apps/manage-logic-apps-with-visual-studio.md) a partir do portal Azure como modelo de Gestor de Recursos [Azure.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Este modelo está maioritariamente pronto para ser implantado e inclui as definições de recursos para a sua aplicação lógica, incluindo o próprio fluxo de trabalho, e ligações. O modelo também declara parâmetros para os valores a utilizar na implementação. Desta forma, pode mudar mais facilmente onde e como implementa a aplicação lógica, com base nas suas necessidades. Para especificar a localização e outras informações necessárias para a implementação, pode utilizar um ficheiro de parâmetros separados.

* Se criou e implementou a sua aplicação lógica utilizando ferramentas de integração contínua (CI) e de entrega contínua (CD), como a Azure Pipelines em Azure DevOps, pode implementar a sua app para outra região utilizando essas ferramentas.

Para obter mais informações sobre modelos de implementação para aplicações lógicas, consulte estes tópicos:

* [Visão geral: Automatizar a implementação para apps Azure Logic utilizando modelos de Gestor de Recursos Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Encontre, abra e baixe a sua app lógica do portal Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Criar modelos de gestor de recursos Azure para apps Azure Logic](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implementar modelos do Azure Resource Manager para o Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Recursos relacionados

Alguns recursos da Azure, como os recursos de gateway de dados no local em Azure, podem existir numa região que difere das aplicações lógicas que utilizam esses recursos. No entanto, outros recursos Azure, como contas de integração ligadas, devem existir na mesma região que as suas aplicações lógicas. Com base no seu cenário, certifique-se de que as suas aplicações lógicas podem aceder aos recursos que as suas apps esperam existir na mesma região.

Por exemplo, para ligar uma aplicação lógica a uma conta de integração, ambos os recursos devem existir na mesma região. Em cenários como a recuperação de desastres, normalmente quer contas de integração que tenham a mesma configuração e artefactos. Noutros cenários, poderá necessitar de contas de integração com diferentes configurações e artefactos.

Conectores personalizados em Azure Logic Apps são visíveis para os autores e utilizadores dos conectores que têm a mesma subscrição Azure e o mesmo inquilino do Azure Ative Directory. Estes conectores estão disponíveis na mesma região onde as aplicações lógicas são implementadas. Para obter mais informações, veja [Partilhar conectores personalizados na sua organização](/connectors/custom-connectors/share).

O modelo que obtém do Visual Studio inclui apenas as definições de recursos para a sua aplicação lógica e suas ligações. Assim, se a sua aplicação lógica utilizar outros recursos, por exemplo, uma conta de integração e artefactos B2B, como parceiros, acordos e esquemas, deve exportar o modelo dessa conta de integração utilizando o portal Azure. Este modelo inclui as definições de recursos tanto para a conta de integração como para os artefactos. No entanto, o modelo não é totalmente parametrizado. Por isso, deve parametrizar manualmente os valores que pretende utilizar para a implantação.

### <a name="export-templates-for-integration-accounts"></a>Modelos de exportação para contas de integração

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração.

1. No menu da sua conta de integração, em **Definições,** selecione **Modelo de Exportação**.

1. Na barra de ferramentas, selecione **Download**e guarde o modelo.

1. Abra e edite o modelo para parametrizar os valores necessários para a implantação.

## <a name="next-steps"></a>Passos seguintes

[Mover recursos da Azure para novos grupos de recursos ou subscrições](../azure-resource-manager/management/move-resource-group-and-subscription.md)
