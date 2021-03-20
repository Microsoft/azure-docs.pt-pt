---
title: Governação da infraestrutura Azure DevTest Labs - Recursos
description: Este artigo aborda o alinhamento e gestão de recursos para a Azure DevTest Labs dentro da sua organização.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480920"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governação da infraestrutura Azure DevTest Labs - Recursos
Este artigo aborda o alinhamento e gestão de recursos para a DevTest Labs dentro da sua organização. 

## <a name="align-within-an-azure-subscription"></a>Alinhar dentro de uma subscrição do Azure 

### <a name="question"></a>Pergunta
Como alinhar os recursos da DevTest Labs numa subscrição do Azure?

### <a name="answer"></a>Resposta
Antes de uma organização começar a usar o Azure para o desenvolvimento geral de aplicações, os planeadores de TI devem primeiro rever como introduzir a capacidade como parte do seu portfólio global de serviços. As áreas de revisão devem dar resposta às seguintes preocupações:

- Como medir o custo associado ao ciclo de vida de desenvolvimento da aplicação?
- Como é que a organização alinha a oferta de serviços proposta com a política de segurança corporativa? 
- A segmentação é necessária para separar os ambientes de desenvolvimento e produção? 
- Que controlos são introduzidos para facilitar a longo prazo a gestão, estabilidade e crescimento?

A **primeira prática recomendada** é rever a taxonomia Azure das organizações, onde são delineadas as divisões entre subscrições de produção e desenvolvimento. No diagrama seguinte, a taxonomia sugerida permite uma separação lógica dos ambientes de desenvolvimento/teste e produção. Com esta abordagem, uma organização pode introduzir códigos de faturação para rastrear os custos associados a cada ambiente separadamente. Para obter mais informações, consulte [a governação de assinaturas Prescritivas](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Além disso, você pode usar [tags Azure](../azure-resource-manager/management/tag-resources.md) para organizar recursos para fins de rastreio e faturação.

A **segunda prática recomendada** é ativar a subscrição de DevTest dentro do portal Azure Enterprise. Permite que uma organização gere sistemas operativos de clientes que normalmente não estão disponíveis numa subscrição da empresa Azure. Em seguida, use software empresarial onde paga apenas pelo cálculo e não se preocupe com o licenciamento. Garante que a faturação dos serviços designados, incluindo imagens de galeria em IaaS, como o Microsoft SQL Server, baseia-se apenas no consumo. Detalhes sobre a subscrição Azure DevTest podem ser encontrados [aqui](https://azure.microsoft.com/offers/ms-azr-0148p/) para clientes do Enterprise Agreement (EA) e [aqui](https://azure.microsoft.com/offers/ms-azr-0023p/) para Pay como clientes go.

![Alinhamento de recursos com subscrições](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Este modelo proporciona a uma organização a flexibilidade para implantar a Azure DevTest Labs em escala. Uma organização pode apoiar centenas de laboratórios para várias unidades de negócio com 100 a 1000 máquinas virtuais em paralelo. Promove a noção de uma solução centralizada de laboratório empresarial que pode partilhar os mesmos princípios de gestão de configuração e controlos de segurança.

Este modelo também garante que a organização não esgota os seus limites de recursos associados à sua subscrição do Azure. Para obter detalhes sobre os limites de subscrição e serviço, consulte [os limites de subscrição e serviços da Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md) O processo de aprovisionamento da DevTest Labs pode consumir um grande número de grupos de recursos. Pode solicitar que os limites sejam aumentados através de um pedido de apoio na subscrição do Azure DevTest. Os recursos dentro da subscrição de produção não são afetados à medida que a subscrição de desenvolvimento cresce em uso. Para obter mais informações sobre a escala de Laboratórios DevTest, consulte [as quotas e limites de escala em DevTest Labs](devtest-lab-scale-lab.md).

Um limite comum de nível de subscrição que deve ser contabilizado é a forma como as atribuições de gama IP de rede são atribuídas para suportar assinaturas de produção e desenvolvimento. Estas atribuições devem ter em conta o crescimento ao longo do tempo (assumindo a conectividade no local ou outra topologia de networking que exija que a empresa gere a sua stack de networking em vez de estar em incumprimento da implementação do Azure). A prática recomendada é ter algumas redes virtuais que têm um grande prefixo de endereço IP atribuído e dividido com muitas grandes sub-redes em vez de ter múltiplas redes virtuais com pequenas sub-redes. Por exemplo, com 10 subscrições, pode definir 10 redes virtuais (uma para cada subscrição). Todos os laboratórios que não necessitam de isolamento podem partilhar a mesma sub-rede na vnet da subscrição.

## <a name="maintain-naming-conventions"></a>Manter convenções de nomeação

### <a name="question"></a>Pergunta
Como mantenho uma convenção de nomeação através do meu ambiente de DevTest Labs?

### <a name="answer"></a>Resposta
Você pode querer estender as convenções de nomeação de empresas atuais para as operações Azure e torná-las consistentes em todo o ambiente DevTest Labs.

Ao implementar a DevTest Labs, recomendamos que tenha políticas de partida específicas. Você implementa estas políticas por um script central e modelos JSON para impor a consistência. As políticas de nomeação podem ser implementadas através das políticas Azure aplicadas ao nível da subscrição. Para amostras JSON para Azure Policy, consulte [as amostras da Política Azure](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Número de utilizadores por laboratório e laboratórios por organização

### <a name="question"></a>Pergunta 
Como determino a relação entre os utilizadores por laboratório e o número total de laboratórios necessários em toda uma organização?

### <a name="answer"></a>Resposta
Recomendamos que unidades de negócio e grupos de desenvolvimento associados ao mesmo projeto de desenvolvimento estejam associados ao mesmo laboratório. Permite que os mesmos tipos de políticas, imagens e políticas de encerramento sejam aplicados a ambos os grupos. 

Também pode ter de considerar limites geográficos. Por exemplo, os desenvolvedores no nordeste dos Estados Unidos (EUA) podem usar um laboratório a provisionado no Leste dos EUA2. E, os desenvolvedores em Dallas, Texas, e Denver, Colorado podem ser direcionados para usar um recurso em US South Central. Se houver um esforço colaborativo com um terceiro externo, eles podem ser atribuídos a um laboratório que não é usado por desenvolvedores internos. 

Você também pode usar um laboratório para um projeto específico dentro de projetos Azure DevOps. Em seguida, aplica segurança através de um grupo de Diretório Azure Ative especificado, que permite o acesso a ambos os recursos. A rede virtual atribuída ao laboratório pode ser outra fronteira para consolidar os utilizadores.

## <a name="deletion-of-resources"></a>Supressão de recursos

### <a name="question"></a>Pergunta
Como podemos evitar a supressão de recursos dentro de um laboratório?

### <a name="answer"></a>Resposta
Recomendamos que estabeleça permissões adequadas ao nível do laboratório para que apenas os utilizadores autorizados possam eliminar recursos ou alterar as políticas de laboratório. Os desenvolvedores devem ser colocados dentro do grupo **de utilizadores da DevTest Labs.** O promotor principal ou o chumbo da infraestrutura deve ser o Dono da **DevTest Labs.** Recomendamos que só tenha dois donos de laboratório. Esta política estende-se ao repositório de códigos para evitar a corrupção. Os usos do laboratório têm direitos de usar recursos, mas não podem atualizar as políticas do laboratório. Consulte o seguinte artigo que lista as funções e direitos que cada grupo incorporado tem dentro de um laboratório: [Adicionar proprietários e utilizadores em Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Mover laboratório para outro grupo de recursos 

### <a name="question"></a>Pergunta
É apoiado para mover um laboratório para outro Grupo de Recursos?

### <a name="answer"></a>Resposta
Sim. Navegue na página do Grupo de Recursos a partir da página inicial do seu laboratório. Em seguida, **selecione Mover** na barra de ferramentas e selecione o laboratório que pretende mover-se para um grupo de recursos diferente. Quando crias um laboratório, um grupo de recursos é automaticamente criado para ti. No entanto, pode querer mover o laboratório para um grupo de recursos diferente que segue as convenções de nomeação da empresa. 

## <a name="next-steps"></a>Passos seguintes
Ver [Gerir custos e propriedade.](devtest-lab-guidance-governance-cost-ownership.md)
