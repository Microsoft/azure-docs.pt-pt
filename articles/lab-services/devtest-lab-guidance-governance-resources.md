---
title: Governação da infraestrutura azure DevTest Labs - Recurso
description: Este artigo aborda o alinhamento e gestão de recursos para o Azure DevTest Labs dentro da sua organização.
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
ms.openlocfilehash: 8bb00c770c61a0a5462a01ae552bd7e40a7cdb36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470654"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governação da infraestrutura azure DevTest Labs - Recursos
Este artigo aborda o alinhamento e gestão de recursos para a DevTest Labs dentro da sua organização. 

## <a name="align-within-an-azure-subscription"></a>Alinhar dentro de uma subscrição do Azure 

### <a name="question"></a>Pergunta
Como alinhar os recursos da DevTest Labs dentro de uma subscrição do Azure?

### <a name="answer"></a>Resposta
Antes de uma organização começar a usar o Azure para o desenvolvimento geral de aplicações, os planeadores de TI devem primeiro rever como introduzir a capacidade como parte do seu portfólio global de serviços. As áreas de revisão devem abordar as seguintes preocupações:

- Como medir o custo associado ao ciclo de vida do desenvolvimento de aplicações?
- Como é que a organização alinha a oferta de serviços proposta com a política de segurança corporativa? 
- É necessária segmentação para separar os ambientes de desenvolvimento e produção? 
- Que controlos são introduzidos para a facilidade de gestão, estabilidade e crescimento a longo prazo?

A **primeira prática recomendada** é rever a taxonomia azure das organizações onde estão delineadas as divisões entre as assinaturas de produção e desenvolvimento. No diagrama seguinte, a taxonomia sugerida permite uma separação lógica dos ambientes de desenvolvimento/teste e produção. Com esta abordagem, uma organização pode introduzir códigos de faturação para acompanhar os custos associados a cada ambiente separadamente. Para mais informações, consulte a [governação prescritiva da subscrição.](/azure/architecture/cloud-adoption/appendix/azure-scaffold) Além disso, você pode usar [tags Azure](../azure-resource-manager/management/tag-resources.md) para organizar recursos para fins de rastreamento e faturação.

A **segunda prática recomendada** é ativar a subscrição DevTest dentro do portal Azure Enterprise. Permite que uma organização execute sistemas operativos de clientes que normalmente não estão disponíveis numa subscrição empresarial do Azure. Em seguida, use software empresarial onde paga apenas pela computação e não se preocupe com o licenciamento. Garante que a faturação de serviços designados, incluindo imagens de galerias em IaaS, como o Microsoft SQL Server, baseia-se apenas no consumo. Os detalhes sobre a subscrição Azure DevTest podem ser encontrados [aqui](https://azure.microsoft.com/offers/ms-azr-0148p/) para clientes do Enterprise Agreement (EA) e [aqui](https://azure.microsoft.com/offers/ms-azr-0023p/) para pagar como clientes Go.

![Alinhamento de recursos com subscrições](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Este modelo proporciona a uma organização a flexibilidade para implementar os Azure DevTest Labs em escala. Uma organização pode apoiar centenas de laboratórios para várias unidades de negócio com 100 a 1000 máquinas virtuais em paralelo. Promove a noção de uma solução centralizada do laboratório empresarial que pode partilhar os mesmos princípios de gestão de configuração e controlos de segurança.

Este modelo garante ainda que a organização não esgota os seus limites de recursos associados à sua subscrição Azure. Para mais detalhes sobre os limites de subscrição e serviço, consulte os limites de subscrição e serviço do [Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md) O processo de provisionamento de Laboratórios DevTest pode consumir um grande número de grupos de recursos. Pode solicitar que os limites sejam aumentados através de um pedido de suporte na subscrição DoVTest Azure. Os recursos dentro da subscrição de produção não são afetados à medida que a subscrição de desenvolvimento cresce em uso. Para obter mais informações sobre a escala de DevTest Labs, consulte [quotas e limites de escala em Laboratórios DevTest](devtest-lab-scale-lab.md).

Um limite comum de nível de subscrição que deve ser contabilizado é a forma como as atribuições de gama IP da rede são atribuídas para apoiar subscrições de produção e desenvolvimento. Estas atribuições devem explicar o crescimento ao longo do tempo (assumindo a conectividade no local ou outra topologia de networking que requer que a empresa gere a sua pilha de networking em vez de incumprimento da implementação do Azure). A prática recomendada é ter algumas redes virtuais que tenham um prefixo de endereço IP grande atribuído e dividido com muitas grandes subredes em vez de ter várias redes virtuais com pequenas subredes. Por exemplo, com 10 subscrições, pode definir 10 redes virtuais (uma para cada subscrição). Todos os laboratórios que não requerem isolamento podem partilhar a mesma sub-rede na vnet da subscrição.

## <a name="maintain-naming-conventions"></a>Manter convenções de nomeação

### <a name="question"></a>Pergunta
Como mantenho uma convenção de nomeação no meu ambiente de Laboratórios DevTest?

### <a name="answer"></a>Resposta
Você pode querer estender as convenções de nomeação da empresa atual para operações Azure e torná-las consistentes em todo o ambiente DevTest Labs.

Ao implementar o DevTest Labs, recomendamos que tenha políticas de início específicas. Você implementa estas políticas através de um script central e modelos JSON para impor a consistência. As políticas de nomeação podem ser implementadas através de políticas azure aplicadas ao nível da subscrição. Para obter amostras json para a Política Azure, consulte [as amostras da Política Azure](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Número de utilizadores por laboratório e laboratórios por organização

### <a name="question"></a>Pergunta 
Como determino a proporção de utilizadores por laboratório e o número total de laboratórios necessários numa organização?

### <a name="answer"></a>Resposta
Recomendamos que unidades de negócio e grupos de desenvolvimento associados ao mesmo projeto de desenvolvimento estejam associados ao mesmo laboratório. Permite que sejam aplicadas aos dois grupos os mesmos tipos de políticas, imagens e políticas de encerramento. 

Também pode ter de considerar os limites geográficos. Por exemplo, os desenvolvedores no nordeste dos Estados Unidos (EUA) podem usar um laboratório aprovisionado no Leste dos EUA. E, desenvolvedores em Dallas, Texas, e Denver, Colorado podem ser direcionados para usar um recurso na Central Sul dos EUA. Se houver um esforço colaborativo com um terceiro externo, eles podem ser atribuídos a um laboratório que não é usado por desenvolvedores internos. 

Você também pode usar um laboratório para um projeto específico dentro de projetos Azure DevOps. Em seguida, aplica a segurança através de um determinado grupo de DirectórioActivo Azure, que permite o acesso a ambos os conjuntos de recursos. A rede virtual atribuída ao laboratório pode ser outro limite para consolidar os utilizadores.

## <a name="deletion-of-resources"></a>Supressão de recursos

### <a name="question"></a>Pergunta
Como podemos impedir a supressão de recursos dentro de um laboratório?

### <a name="answer"></a>Resposta
Recomendamos que estabeleça permissões adequadas ao nível do laboratório para que apenas os utilizadores autorizados possam eliminar recursos ou alterar as políticas de laboratório. Os desenvolvedores devem ser colocados dentro do grupo **DevTest Labs Users.** O promotor principal ou o chumbo da infraestrutura devem ser o **Proprietário de Laboratórios DevTest.** Recomendamos que só tenha dois donos de laboratório. Esta política estende-se ao repositório de códigos para evitar a corrupção. Os usos do laboratório têm direitos de usar recursos, mas não conseguem atualizar as políticas de laboratório. Consulte o seguinte artigo que lista as funções e direitos que cada grupo incorporado tem dentro de um laboratório: [Adicione proprietários e utilizadores em Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Mude o laboratório para outro grupo de recursos 

### <a name="question"></a>Pergunta
É suportado para mover um laboratório para outro Grupo de Recursos?

### <a name="answer"></a>Resposta
Sim. Navegue para a página do Grupo de Recursos a partir da página inicial do seu laboratório. Em seguida, selecione **Mover** na barra de ferramentas e selecione o laboratório que pretende mover para um grupo de recursos diferente. Quando se cria um laboratório, um grupo de recursos é automaticamente criado para si. No entanto, você pode querer mover o laboratório para um grupo de recursos diferente que segue as convenções de nomeação da empresa. 

## <a name="next-steps"></a>Passos seguintes
Ver Gerir o custo e a [propriedade.](devtest-lab-guidance-governance-cost-ownership.md)
