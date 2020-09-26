---
title: Descrição geral do Azure Resource Manager
description: Descreve como utilizar o Azure Resource Manager para a implementação, a gestão e o controlo de acesso de recursos no Azure.
ms.topic: overview
ms.date: 09/01/2020
ms.custom: contperfq1
ms.openlocfilehash: f78b6015846253d79020752522c10af96839a854
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372277"
---
# <a name="what-is-azure-resource-manager"></a>O que é o Azure Resource Manager?

O Azure Resource Manager é o serviço de implementação e gestão do Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua conta Azure. Utiliza funcionalidades de gestão, como controlo de acessos, fechaduras e tags, para proteger e organizar os seus recursos após a implantação.

Para saber mais sobre os modelos do Gestor de Recursos Azure, consulte [a visão geral da implementação do modelo](../templates/overview.md).

## <a name="consistent-management-layer"></a>Camada de gestão consistente

Quando um utilizador envia um pedido de qualquer uma das ferramentas Azure, APIs ou SDKs, o Gestor de Recursos recebe o pedido. Autentica e autoriza o pedido. O Gestor de Recursos envia o pedido para o serviço Azure, que toma a ação solicitada. Como todos os pedidos são tratados através da mesma API, você vê resultados e capacidades consistentes em todas as diferentes ferramentas.

A imagem a seguir mostra o papel que o Azure Resource Manager desempenha no tratamento dos pedidos do Azure.

![Modelo de pedido do Resource Manager](./media/overview/consistent-management-layer.png)

Todas as capacidades disponíveis no portal também estão disponíveis através de PowerShell, Azure CLI, REST APIs e Cliente SDKs. A funcionalidade inicialmente lançada através de APIs será representada no portal dentro de 180 dias do lançamento inicial.

## <a name="terminology"></a>Terminologia

Se é a primeira vez que utiliza o Azure Resource Manager, existem alguns termos com os quais poderá não estar familiarizado.

* **recurso** - Um item manejável que está disponível através do Azure. Máquinas virtuais, contas de armazenamento, aplicações web, bases de dados e redes virtuais são exemplos de recursos. Grupos de recursos, subscrições, grupos de gestão e tags são também exemplos de recursos.
* **grupo de recursos** - um contentor que retém recursos relacionados para uma solução do Azure. O grupo de recursos inclui os recursos que pretende gerir como um grupo. Decide que recursos pertencem a um grupo de recursos com base no que faz mais sentido para a sua organização. Consulte [grupos de recursos](#resource-groups).
* **fornecedor de recursos** - Um serviço que fornece recursos Azure. Por exemplo, um fornecedor comum de recursos é o Microsoft.Compute, que fornece o recurso de máquina virtual. Microsoft.Storage é outro fornecedor comum de recursos. Consulte [os fornecedores e tipos de recursos.](resource-providers-and-types.md)
* **Modelo de Gestor de Recursos** - Um ficheiro JavaScript Object Notation (JSON) que define um ou mais recursos para implantar num grupo de recursos, subscrição, grupo de gestão ou inquilino. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida. Consulte [a visão geral da implementação do modelo](../templates/overview.md).
* **sintaxe declarativa** - sintaxe que permite afirmar "Aqui está o que pretendo criar" sem ter de escrever a sequência de comandos de programação para criá-la. O modelo do Resource Manager é um exemplo de sintaxe declarativa. No ficheiro, é possível definir as propriedades da infraestrutura de implementação para o Azure.  Consulte [a visão geral da implementação do modelo](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Vantagens da utilização do Resource Manager

Com o Gestor de Recursos, pode:

* Gerencie a sua infraestrutura através de modelos declarativos em vez de scripts.

* Implemente, gerencie e monitorize todos os recursos para a sua solução como um grupo, em vez de lidar com estes recursos individualmente.

* Reimplantar a sua solução ao longo do ciclo de vida do desenvolvimento e ter confiança que os seus recursos são implantados num estado consistente.

* Defina as dependências entre recursos para que sejam implantados na ordem correta.

* Aplicar o controlo de acesso a todos os serviços porque o controlo de acesso baseado em funções Azure (Azure RBAC) está nativamente integrado na plataforma de gestão.

* Aplique etiquetas em recursos para organizar logicamente todos os recursos na sua subscrição.

* Esclareça a faturação da sua organização visualizando os custos de um grupo de recursos que partilham a mesma etiqueta.

## <a name="understand-scope"></a>Compreender o âmbito

O Azure fornece quatro níveis de âmbito: grupos de [gestão,](../../governance/management-groups/overview.md)assinaturas, [grupos de recursos](#resource-groups)e recursos. A imagem seguinte mostra um exemplo destas camadas.

![Níveis de gestão](./media/overview/scope-levels.png)

Pode aplicar as definições de gestão em qualquer um destes níveis de âmbito. O nível que selecionar determina o quanto a definição é aplicada. Os níveis inferiores herdam as definições de níveis mais altos. Por exemplo, quando aplica uma [política](../../governance/policy/overview.md) à subscrição, a política é aplicada a todos os grupos de recursos e recursos na sua subscrição. Quando se aplica uma política ao grupo de recursos, essa política é aplicada ao grupo de recursos e a todos os seus recursos. No entanto, outro grupo de recursos não tem essa atribuição política.

Você pode implementar modelos para inquilinos, grupos de gestão, subscrições ou grupos de recursos.

## <a name="resource-groups"></a>Grupos de recursos

Existem alguns fatores importantes a considerar ao definir o grupo de recursos:

* Todos os recursos do seu grupo de recursos devem partilhar o mesmo ciclo de vida. Implemente-os, atualize-os e elimine-os em conjunto. Se um recurso, como um servidor, precisar de existir num ciclo de implementação diferente, deverá estar noutro grupo de recursos.

* Cada recurso só pode existir num único grupo de recursos.

* Pode adicionar ou remover um recurso de um grupo de recursos em qualquer altura.

* Pode mover um recurso de um grupo de recursos para outro grupo. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

* Os recursos de um grupo de recursos podem ser localizados em diferentes regiões do que o grupo de recursos.

* Ao criar um grupo de recursos, deve fornecer uma localização para esse grupo de recursos. Pode perguntar-se, "Porque é que um grupo de recursos necessita de uma localização? E, se os recursos podem ter diferentes localizações em relação ao grupo de recursos, por que motivo é que a localização do grupo de recursos é sequer relevante?" O grupo de recursos armazena metadados sobre os recursos. Quando especificar uma localização para o grupo de recursos, está a especificar onde esses metadados são armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

   Se a região do grupo de recursos estiver temporariamente indisponível, não é possível atualizar recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos noutras regiões continuarão a funcionar como esperado, mas não é possível atualizá-los. Para obter mais informações sobre a construção de aplicações fiáveis, consulte [conceber aplicações Azure fiáveis.](/azure/architecture/checklist/resiliency-per-service)

* Um grupo de recursos pode ser utilizado para definir o âmbito do controlo de acesso para ações administrativas. Para gerir um grupo de recursos, pode atribuir [Políticas Azure,](../../governance/policy/overview.md) [funções Azure](../../role-based-access-control/role-assignments-portal.md)ou [bloqueios de recursos](lock-resources.md).

* Pode [aplicar etiquetas](tag-resources.md) a um grupo de recursos. Os recursos do grupo de recursos não herdam essas etiquetas.

* Um recurso pode ligar-se a recursos em outros grupos de recursos. Este cenário é comum quando os dois recursos estão relacionados, mas não partilham o mesmo ciclo de vida. Por exemplo, pode ter uma aplicação web que se conecta a uma base de dados num grupo de recursos diferente.

* Quando elimina um grupo de recursos, todos os recursos do grupo de recursos também são eliminados. Para obter informações sobre como o Azure Resource Manager orquestra essas supressões, consulte [o grupo de recursos Azure Resource Manager e a supressão de recursos.](delete-resource-group.md)

* Pode implementar até 800 instâncias de um tipo de recurso em cada grupo de recursos. Alguns tipos de recursos estão [isentos do limite de 800 instâncias.](resources-without-resource-group-limit.md)

* Alguns recursos podem existir fora de um grupo de recursos. Estes recursos são mobilizados para a [subscrição,](../templates/deploy-to-subscription.md) [grupo de gestão,](../templates/deploy-to-management-group.md)ou [inquilino.](../templates/deploy-to-tenant.md) Apenas tipos específicos de recursos são suportados nestes âmbitos.

* Para criar um grupo de recursos, pode utilizar o [portal](manage-resource-groups-portal.md#create-resource-groups), [PowerShell,](manage-resource-groups-powershell.md#create-resource-groups) [Azure CLI](manage-resource-groups-cli.md#create-resource-groups)ou um [modelo Azure Resource Manager (ARM).](../templates/deploy-to-subscription.md#resource-groups)

## <a name="resiliency-of-azure-resource-manager"></a>Resiliência do Gestor de Recursos Azure

O serviço Azure Resource Manager foi concebido para resiliência e disponibilidade contínua. As operações do Gestor de Recursos e do avião de controlo (pedidos enviados para management.azure.com) na API REST são:

* Distribuído por regiões. Alguns serviços são regionais.

* Distribuído por Zonas de Disponibilidade (bem como regiões) em locais que têm múltiplas Zonas de Disponibilidade.

* Não dependente de um único centro de dados lógico.

* Nunca foi retirado para atividades de manutenção.

Esta resiliência aplica-se aos serviços que recebem pedidos através do Gestor de Recursos. Por exemplo, o Cofre-Chave beneficia desta resiliência.

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a movimentação de recursos, consulte [mover recursos para novo grupo de recursos ou subscrição.](move-resource-group-and-subscription.md)

* Para aprender a marcar recursos, consulte [use tags para organizar os seus recursos Azure.](tag-resources.md)

* Para aprender a bloquear recursos, consulte [os recursos do Lock para evitar alterações inesperadas](lock-resources.md).
