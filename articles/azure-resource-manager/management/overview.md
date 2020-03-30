---
title: Descrição geral
description: Descreve como utilizar o Azure Resource Manager para a implementação, a gestão e o controlo de acesso de recursos no Azure.
ms.topic: overview
ms.date: 03/25/2020
ms.openlocfilehash: 1e2a6959117749b4e7d08a9768b4189b97ef08bd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80258146"
---
# <a name="what-is-azure-resource-manager"></a>O que é o Gestor de Recursos Azure?

O Azure Resource Manager é o serviço de implementação e gestão do Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua subscrição Azure. Utiliza funcionalidades de gestão, como controlo de acesso, fechaduras e etiquetas, para proteger e organizar os seus recursos após a implantação.

Para saber sobre os modelos do Gestor de Recursos Azure, consulte a visão geral da [implementação](../templates/overview.md)do modelo .

## <a name="consistent-management-layer"></a>Camada de gestão consistente

Quando um utilizador envia um pedido de qualquer uma das ferramentas, APIs ou SDKs do Azure, o Gestor de Recursos recebe o pedido. Autentica e autoriza o pedido. O Gestor de Recursos envia o pedido para o serviço Azure, que toma a ação solicitada. Como todos os pedidos são tratados através da mesma API, você vê resultados e capacidades consistentes em todas as diferentes ferramentas.

A imagem seguinte mostra o papel que o Gestor de Recursos azure desempenha no tratamento dos pedidos do Azure.

![Modelo de pedido do Resource Manager](./media/overview/consistent-management-layer.png)

Todas as capacidades disponíveis no portal também estão disponíveis através de PowerShell, Azure CLI, REST APIs e SDKs de clientes. A funcionalidade inicialmente lançada através de APIs será representada no portal dentro de 180 dias do lançamento inicial.

## <a name="terminology"></a>Terminologia

Se é a primeira vez que utiliza o Azure Resource Manager, existem alguns termos com os quais poderá não estar familiarizado.

* **recurso** - Um item manejável que está disponível através do Azure. Máquinas virtuais, contas de armazenamento, aplicações web, bases de dados e redes virtuais são exemplos de recursos.
* **grupo de recursos** - um contentor que retém recursos relacionados para uma solução do Azure. O grupo de recursos inclui os recursos que pretende gerir como um grupo. Decide que recursos pertencem a um grupo de recursos com base no que faz mais sentido para a sua organização. Ver [Grupos de Recursos](#resource-groups).
* **fornecedor de recursos** - Um serviço que fornece recursos Azure. Por exemplo, um fornecedor comum de recursos é a Microsoft.Compute, que fornece o recurso virtual da máquina. Microsoft.Storage é outro fornecedor comum de recursos. Consulte [os fornecedores e tipos](resource-providers-and-types.md)de recursos.
* **Modelo** de Gestor de Recursos - Um ficheiro De Notação de Objetos JavaScript (JSON) que define um ou mais recursos para implantar para um grupo de recursos ou subscrição. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida. Ver [visão geral da implementação](../templates/overview.md)do modelo .
* **sintaxe declarativa** - sintaxe que permite afirmar "Aqui está o que pretendo criar" sem ter de escrever a sequência de comandos de programação para criá-la. O modelo do Resource Manager é um exemplo de sintaxe declarativa. No ficheiro, é possível definir as propriedades da infraestrutura de implementação para o Azure.  Ver [visão geral da implementação](../templates/overview.md)do modelo .

## <a name="the-benefits-of-using-resource-manager"></a>Vantagens da utilização do Resource Manager

Com o Gestor de Recursos, pode:

* Gerencie a sua infraestrutura através de modelos declarativos em vez de scripts.

* Implementar, gerir e monitorizar todos os recursos para a sua solução como um grupo, em vez de lidar com estes recursos individualmente.

* Recoloque a sua solução ao longo do ciclo de vida de desenvolvimento e tenha confiança de que os seus recursos são implantados num estado consistente.

* Defina as dependências entre recursos para que sejam implantados na ordem correta.

* Aplique o controlo de acesso a todos os serviços do seu grupo de recursos porque o Controlo de Acesso baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

* Aplique etiquetas em recursos para organizar logicamente todos os recursos da sua subscrição.

* Clarifique a faturação da sua organização visualizando os custos de um grupo de recursos que partilham a mesma etiqueta.

## <a name="understand-scope"></a>Compreender o âmbito

O Azure fornece quatro níveis de âmbito: grupos de [gestão,](../../governance/management-groups/overview.md)subscrições, [grupos de recursos](#resource-groups)e recursos. A imagem seguinte mostra um exemplo destas camadas.

![Âmbito](./media/overview/scope-levels.png)

Pode aplicar as definições de gestão em qualquer um destes níveis de âmbito. O nível que selecionar determina o quanto a definição é aplicada. Os níveis inferiores herdam as definições de níveis mais altos. Por exemplo, quando aplica uma [política](../../governance/policy/overview.md) à subscrição, a política é aplicada a todos os grupos e recursos de recursos na sua subscrição. Quando se aplica uma política sobre o grupo de recursos, essa política é aplicada ao grupo de recursos e a todos os seus recursos. No entanto, outro grupo de recursos não tem essa atribuição de política.

Pode implementar modelos para grupos de gestão, subscrições ou grupos de recursos.

## <a name="resource-groups"></a>Grupos de recursos

Existem alguns fatores importantes a considerar ao definir o grupo de recursos:

* Todos os recursos do grupo devem partilhar o mesmo ciclo de vida. Implemente-os, atualize-os e elimine-os em conjunto. Se um recurso, tal como um servidor de base de dados, tiver de existir num ciclo de implementação diferente, deve ser colocado noutro grupo de recursos.

* Cada recurso só pode existir num grupo de recursos.

* Pode adicionar ou remover um recurso de um grupo de recursos em qualquer altura.

* Pode mover um recurso de um grupo de recursos para outro grupo. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

* Um grupo de recursos pode conter recursos localizados em diferentes regiões.

* Um grupo de recursos pode ser utilizado para definir o âmbito do controlo de acesso para ações administrativas.

* Um recurso pode interagir com recursos de outros grupos de recursos. Esta interação é comum quando os dois recursos estão relacionados mas não partilham o mesmo ciclo de vida (por exemplo, aplicações Web a ligar a uma base de dados).

Ao criar um grupo de recursos, deve fornecer uma localização para esse grupo de recursos. Pode perguntar-se, "Porque é que um grupo de recursos necessita de uma localização? E, se os recursos podem ter diferentes localizações em relação ao grupo de recursos, por que motivo é que a localização do grupo de recursos é sequer relevante?" O grupo de recursos armazena metadados sobre os recursos. Quando especifica uma localização para o grupo de recursos, está a especificar onde os metadados são armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

Se a região do grupo de recursos estiver temporariamente indisponível, não pode atualizar os recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos noutras regiões continuarão a funcionar como esperado, mas não é possível atualizá-los. Para obter mais informações sobre a construção de aplicações fiáveis, consulte a Conceção de [aplicações fiáveis do Azure.](/azure/architecture/checklist/resiliency-per-service)

## <a name="resiliency-of-azure-resource-manager"></a>Resiliência do Gestor de Recursos Azure

O serviço Azure Resource Manager foi concebido para resiliência e disponibilidade contínua. Gestor de Recursos e operações de planos de controlo (pedidos enviados para management.azure.com) na API REST são:

* Distribuído por regiões. Alguns serviços são regionais.

* Distribuído sem ver zonas de disponibilidade (bem como regiões) em locais que têm múltiplas Zonas de Disponibilidade.

* Não depende de um único centro de dados lógico.

* Nunca foi detido para atividades de manutenção.

Esta resiliência aplica-se aos serviços que recebem pedidos através do Gestor de Recursos. Por exemplo, o Cofre Chave beneficia desta resiliência.

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a movimentação de recursos, consulte [o Move recursos para um novo grupo de recursos ou subscrição.](move-resource-group-and-subscription.md)

* Para aprender sobre a marcação de recursos, consulte [Use tags para organizar os seus recursos Azure.](tag-resources.md)

* Para aprender sobre o bloqueio de recursos, consulte [os recursos de bloqueio para evitar mudanças inesperadas](lock-resources.md).
