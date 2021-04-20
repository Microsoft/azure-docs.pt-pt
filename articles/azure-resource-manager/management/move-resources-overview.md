---
title: Mover recursos Azure através de grupos de recursos, subscrições ou regiões.
description: Visão geral dos tipos de recursos Azure que podem ser movidos através de grupos de recursos, subscrições ou regiões.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730509"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Mover recursos Azure através de grupos de recursos, subscrições ou regiões

Os recursos azure podem ser transferidos para um novo grupo de recursos ou subscrição, ou em todas as regiões.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Mover recursos através de grupos de recursos ou subscrições

Pode mover os recursos da Azure para outra subscrição do Azure ou para outro grupo de recursos sob a mesma subscrição. Para mover recursos, pode utilizar o portal do Azure, o Azure PowerShell, a CLI do Azure ou a API REST. Para saber mais, consulte [mover recursos para um novo grupo de recursos ou subscrição.](move-resource-group-and-subscription.md)

### <a name="upgrade-a-subscription"></a>Atualizar uma subscrição

Se realmente pretender atualizar a sua subscrição Azure (como mudar de livre para pagar), tem de converter a sua subscrição.

- Para atualizar um teste gratuito, consulte [a atualização do seu Teste Gratuito ou subscrição do Microsoft Imagine Azure para Pay-As-You-Go](../../cost-management-billing/manage/upgrade-azure-subscription.md).
- Para alterar uma conta pay-as-you-go, consulte [alterar a sua subscrição Azure Pay-As-You-Go para uma oferta diferente](../../cost-management-billing/manage/switch-azure-offer.md).

Se não conseguir converter a subscrição, crie um pedido de [suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **Gestão de Subscrição** para o tipo de emissão.

## <a name="move-resources-across-regions"></a>Mover recursos através das regiões

Geografias, regiões e zonas de disponibilidade azure formam a base da infraestrutura global do Azure. As [geografias azul normalmente](https://azure.microsoft.com/global-infrastructure/geographies/) contêm duas ou mais [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Uma região é uma área dentro de uma geografia, contendo Zonas de Disponibilidade, e vários centros de dados.

Depois de mobilizar recursos na região específica de Azure, existem muitas razões pelas quais talvez queira mover recursos para uma região diferente.

- **Alinhe-se com um lançamento da região**: Mova os seus recursos para uma região de Azure recém-introduzida que não estava disponível anteriormente.
- **Alinhar para serviços/funcionalidades**: Mover recursos para tirar partido de serviços ou funcionalidades que estão disponíveis numa região específica.
- **Responder à evolução do negócio**: Mover recursos para uma região em resposta a mudanças de negócio, tais como fusões ou aquisições.
- **Alinhe-se para a proximidade**: Mova recursos para uma região local para o seu negócio.
- **Satisfazer os requisitos de dados**: Mover recursos para alinhar com os requisitos de residência de dados, ou necessidades de classificação de dados. [Saiba mais](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Responder aos requisitos de implantação**: Mover recursos que foram implantados por engano, ou mover-se em resposta às necessidades de capacidade.
- **Responder ao desmantelamento:** Mover recursos por causa de regiões desativadas.

### <a name="move-resources-with-resource-mover"></a>Mover recursos com o Mover de Recursos

Você pode mover recursos para uma região diferente com [Azure Resource Mover](../../resource-mover/overview.md). O Resource Mover fornece:

- Um único centro para movimentar recursos através das regiões.
- Tempo de movimento reduzido e complexidade. Tudo o que precisa está num único local.
- Uma experiência simples e consistente para mover diferentes tipos de recursos Azure.
- Uma maneira fácil de identificar dependências entre recursos que quer mover. Esta identificação ajuda-o a mover recursos relacionados, para que tudo funcione como esperado na região alvo, após a mudança.
- Limpeza automática dos recursos na região de origem, se quiser eliminá-los após a mudança.
- A testar. Podes experimentar um movimento, e depois deitá-lo fora se não quiseres fazer um movimento completo.

Você pode mover recursos para outra região usando um par de métodos diferentes:

- **Comece a mover recursos de um grupo de recursos**: Com este método, você inicia a mudança da região de dentro de um grupo de recursos. Depois de selecionar os recursos que pretende mover, o processo continua no hub de Resource Mover, para verificar as dependências de recursos e orquestrar o processo de movimento. [Saiba mais](../../resource-mover/move-region-within-resource-group.md).
- **Comece a mover recursos diretamente do centro de Recursos Mover**: Com este método, você inicia o processo de movimento da região diretamente no centro. [Saiba mais](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>Passos seguintes

- Para verificar se um tipo de recurso suporta a mudança, consulte [o suporte de operação move para obter recursos](move-support-resources.md).
- Para saber mais sobre o processo de mudança da região, consulte [Sobre o processo de mudança.](../../resource-mover/about-move-process.md)
