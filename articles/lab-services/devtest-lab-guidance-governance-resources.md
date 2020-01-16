---
title: Governança da infraestrutura de Azure DevTest Labs – recurso
description: Este artigo aborda o alinhamento e o gerenciamento de recursos para Azure DevTest Labs em sua organização.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dc65d368eb4c1beb57099cb6c16fb33bbe5c1f79
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981493"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governança de Azure DevTest Labs infraestrutura – recursos
Este artigo aborda o alinhamento e o gerenciamento de recursos para DevTest Labs em sua organização. 

## <a name="align-within-an-azure-subscription"></a>Alinhar em uma assinatura do Azure 

### <a name="question"></a>Pergunta
Como fazer alinhar os recursos do DevTest Labs em uma assinatura do Azure?

### <a name="answer"></a>Resposta
Antes que uma organização comece a usar o Azure para o desenvolvimento geral de aplicativos, os planejadores de ti devem primeiro examinar como apresentar a funcionalidade como parte de seu portfólio geral de serviços. As áreas para revisão devem resolver as seguintes preocupações:

- Como medir o custo associado ao ciclo de vida do desenvolvimento de aplicativos?
- Como a organização alinha a oferta de serviço proposta com a política de segurança corporativa? 
- A segmentação é necessária para separar os ambientes de desenvolvimento e produção? 
- Quais controles são introduzidos para a facilidade de gerenciamento, estabilidade e crescimento de longo prazo?

A **primeira prática recomendada** é examinar a taxonomia do Azure das organizações, em que as divisões entre as assinaturas de produção e de desenvolvimento são descritas. No diagrama a seguir, a taxonomia sugerida permite uma separação lógica de ambientes de desenvolvimento/teste e produção. Com essa abordagem, uma organização pode introduzir códigos de cobrança para controlar os custos associados a cada ambiente separadamente. Para obter mais informações, consulte [governança de assinatura prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Além disso, você pode usar as [marcas do Azure](../azure-resource-manager/management/tag-resources.md) para organizar os recursos para acompanhamento e fins de cobrança.

A **segunda prática recomendada** é habilitar a assinatura DevTest no portal empresarial do Azure. Ele permite que uma organização execute sistemas operacionais cliente que não estão normalmente disponíveis em uma assinatura do Azure Enterprise. Em seguida, use o software corporativo no qual você paga apenas pela computação e não se preocupe com o licenciamento. Ele garante que a cobrança de serviços designados, incluindo imagens da galeria em IaaS, como Microsoft SQL Server, seja baseada apenas no consumo. Detalhes sobre a assinatura do DevTest do Azure podem ser encontrados [aqui](https://azure.microsoft.com/offers/ms-azr-0148p/) para clientes de Enterprise Agreement (ea) e [aqui](https://azure.microsoft.com/offers/ms-azr-0023p/) para clientes de pagamento ao uso.

![Alinhamento de recursos com assinaturas](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Esse modelo fornece à organização a flexibilidade para implantar Azure DevTest Labs em escala. Uma organização pode dar suporte a centenas de laboratórios para várias unidades de negócios com 100 a 1000 máquinas virtuais em execução em paralelo. Ele promove a noção de uma solução de laboratório empresarial centralizada que pode compartilhar os mesmos princípios de gerenciamento de configuração e controles de segurança.

Esse modelo também garante que a organização não esgote seus limites de recursos associados à sua assinatura do Azure. Para obter detalhes sobre os limites de serviço e assinatura, consulte [assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). O processo de provisionamento do DevTest Labs pode consumir um grande número de grupos de recursos. Você pode solicitar que os limites sejam aumentados por meio de uma solicitação de suporte na assinatura do Azure DevTest. Os recursos na assinatura de produção não são afetados à medida que a assinatura de desenvolvimento cresce em uso. Para obter mais informações sobre o dimensionamento de DevTest Labs, consulte [dimensionar cotas e limites em DevTest Labs](devtest-lab-scale-lab.md).

Um limite de nível de assinatura comum que precisa ser contabilizado é como as atribuições de intervalo de IP de rede são alocadas para dar suporte a assinaturas de desenvolvimento e de produção. Essas atribuições devem considerar o crescimento ao longo do tempo (supondo conectividade local ou outra topologia de rede que exija que a empresa Gerencie sua pilha de rede, em vez de padronizar para a implementação do Azure). A prática recomendada é ter algumas redes virtuais que tenham um prefixo de endereço IP grande atribuído e dividido com muitas sub-redes grandes, em vez de ter várias redes virtuais com sub-redes pequenas. Por exemplo, com 10 assinaturas, você pode definir 10 redes virtuais (uma para cada assinatura). Todos os laboratórios que não exigem isolamento podem compartilhar a mesma sub-rede na vnet da assinatura.

## <a name="maintain-naming-conventions"></a>Manter convenções de nomenclatura

### <a name="question"></a>Pergunta
Como fazer manter uma Convenção de nomenclatura em meu ambiente do DevTest Labs?

### <a name="answer"></a>Resposta
Talvez você queira estender as atuais convenções de nomenclatura corporativa para operações do Azure e torná-las consistentes em todo o ambiente do DevTest Labs.

Ao implantar o DevTest Labs, recomendamos que você tenha políticas de início específicas. Você implanta essas políticas por um script central e modelos JSON para impor a consistência. As políticas de nomenclatura podem ser implementadas por meio de políticas do Azure aplicadas no nível da assinatura. Para obter exemplos de JSON para Azure Policy, consulte [Azure Policy exemplos](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Número de usuários por laboratório e laboratórios por organização

### <a name="question"></a>Pergunta 
Como fazer determinar a taxa de usuários por laboratório e o número total de laboratórios necessários em uma organização?

### <a name="answer"></a>Resposta
Recomendamos que as unidades de negócios e os grupos de desenvolvimento associados ao mesmo projeto de desenvolvimento estejam associados ao mesmo laboratório. Ele permite que os mesmos tipos de políticas, imagens e políticas de desligamento sejam aplicados a ambos os grupos. 

Talvez você também precise considerar os limites geográficos. Por exemplo, os desenvolvedores no leste do Norte Estados Unidos (EUA) podem usar um laboratório provisionado no leste dos EUA 2. Além de desenvolvedores em Dallas, Texas e Denver, a Colorado pode ser direcionada para usar um recurso no centro-sul dos EUA. Se houver um esforço colaborativo com terceiros externos, eles poderão ser atribuídos a um laboratório que não é usado por desenvolvedores internos. 

Você também pode usar um laboratório para um projeto específico dentro dos projetos do Azure DevOps. Em seguida, aplique a segurança por meio de um grupo de Azure Active Directory especificado, que permite o acesso a ambos os conjuntos de recursos. A rede virtual atribuída ao laboratório pode ser outro limite para consolidar usuários.

## <a name="deletion-of-resources"></a>Exclusão de recursos

### <a name="question"></a>Pergunta
Como podemos impedir a exclusão de recursos em um laboratório?

### <a name="answer"></a>Resposta
Recomendamos que você defina as permissões adequadas no nível de laboratório para que somente usuários autorizados possam excluir recursos ou alterar políticas de laboratório. Os desenvolvedores devem ser colocados dentro do grupo **usuários do DevTest Labs** . O desenvolvedor líder ou o líder da infra-estrutura deve ser o **proprietário do DevTest Labs**. É recomendável que você tenha apenas dois proprietários de laboratório. Essa política se estende até o repositório de códigos para evitar corrupção. Os usos de laboratório têm direitos para usar recursos, mas não podem atualizar as políticas de laboratório. Consulte o seguinte artigo que lista as funções e os direitos que cada grupo interno tem dentro de um laboratório: [Adicionar proprietários e usuários no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Mover o laboratório para outro grupo de recursos 

### <a name="question"></a>Pergunta
Há suporte para mover um laboratório para outro grupo de recursos?

### <a name="answer"></a>Resposta
Sim. Navegue até a página do grupo de recursos do home page para seu laboratório. Em seguida, selecione **mover** na barra de ferramentas e selecione o laboratório que você deseja mover para um grupo de recursos diferente. Quando você cria um laboratório, um grupo de recursos é criado automaticamente para você. No entanto, talvez você queira mover o laboratório para um grupo de recursos diferente que siga as convenções de nomenclatura da empresa. 

## <a name="next-steps"></a>Passos seguintes
Consulte [gerenciar custo e Propriedade](devtest-lab-guidance-governance-cost-ownership.md).
