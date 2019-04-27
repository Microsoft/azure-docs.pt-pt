---
title: Governação da infraestrutura do Azure DevTest Labs
description: Este artigo fornece orientações de governação da infraestrutura do Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: c5514a43602106cf045b575d289e02b591468359
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561664"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governação da infraestrutura do Azure DevTest Labs - recursos
Este artigo aborda o alinhamento e a gestão de recursos para laboratórios Dev/Test na sua organização. 

## <a name="align-within-an-azure-subscription"></a>Alinhar dentro de uma subscrição do Azure 

### <a name="question"></a>Pergunta
Como posso alinhar os recursos de DevTest Labs dentro de uma subscrição do Azure?

### <a name="answer"></a>Resposta
Antes de uma organização começa a utilizar o Azure para desenvolvimento de aplicativos gerais, planejadores de TI devem rever primeiro como introduzir o recurso como parte da sua carteira de geral de serviços. Áreas para revisão devem abordar as preocupações seguintes:

- Como avaliar o custo associado com o ciclo de vida do desenvolvimento de aplicações?
- Como a organização alinhar o proposta oferta de serviço com a política de segurança da empresa? 
- É necessária para separar os ambientes de desenvolvimento e produção de segmentação? 
- Quais controles são introduzidas para facilitar a longo prazo de gerenciamento, estabilidade e crescimento?

O **primeiro recomendado prática** é examinar a taxonomia de organizações do Azure em que são descritas as divisões entre a produção e de subscrições de desenvolvimento. No diagrama seguinte, a taxonomia sugerida permite uma separação lógica de ambientes de desenvolvimento/teste e produção. Com esta abordagem, uma organização pode introduzir códigos de faturas para controlar os custos associados a cada ambiente separadamente. Para obter mais informações, consulte [governação de subscrições prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Além disso, pode usar [etiquetas do Azure](../azure-resource-manager/resource-group-using-tags.md) para organizar recursos para controlar e fins de faturação.

O **prática recomendada segundo** consiste em ativar a subscrição de Dev/Test no portal do Azure Enterprise. Ele permite que uma organização executar sistemas operativos cliente que não estão normalmente disponíveis numa subscrição do Azure enterprise. Em seguida, utilize o software empresarial em que paga apenas a computação e não não se preocupe sobre o licenciamento. Ele garante que serviços designados, incluindo imagens da galeria no IaaS, como o Microsoft SQL Server, a faturação baseia-se no consumo apenas. Pode encontrar detalhes sobre a subscrição do Azure DevTest [aqui](https://azure.microsoft.com/offers/ms-azr-0148p/) para clientes Enterprise Agreement (EA) e [aqui](https://azure.microsoft.com/offers/ms-azr-0023p/) para clientes Pay as you Go.

![Alinhamento de recursos com as assinaturas](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Esse modelo proporciona a flexibilidade de implementar o Azure DevTest Labs em escala de uma organização. Uma organização pode suportar centenas de laboratórios para várias unidades de negócios com as máquinas de virtuais de 100 a 1000 em execução em paralelo. Isso promove a noção de uma solução de laboratório de empresarial centralizada que pode partilhar os mesmos princípios de gerenciamento de configuração e controlos de segurança.

Este modelo também garante que a organização não esgotar os respetivos limites de recursos associados à sua subscrição do Azure. Para obter detalhes sobre a subscrição e limites do serviço, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md). O processo de aprovisionamento do DevTest Labs pode consumir grande número de grupos de recursos. Pode pedir para limites de ser aumentados através de um pedido de suporte na subscrição do Azure DevTest. Os recursos dentro da subscrição de produção não são afetados à medida que aumenta a subscrição de desenvolvimento em utilização. Para obter mais informações sobre como aumentar o DevTest Labs, consulte [quotas e limites de dimensionamento no DevTest Labs](devtest-lab-scale-lab.md).

Um limite ao nível da subscrição comuns que precisa ser levado é a forma como as atribuições de intervalo IP de rede são alocadas para suportar subscrições de desenvolvimento e produção. Estas atribuições devem em conta o crescimento ao longo do tempo (partindo do princípio de conectividade no local ou outra topologia de rede que requer que a empresa para gerir a sua pilha de rede em vez de utilizar a predefinição para a implementação do Azure). A prática recomendada é têm algumas redes virtuais que têm um prefixo de endereço IP grandes atribuído e dividido com várias sub-redes grandes em vez de ter várias redes virtuais com sub-redes pequeno. Por exemplo, com 10 subscrições, pode definir 10 redes virtuais (um para cada subscrição). Todos os laboratórios que não necessitam de isolamento podem partilhar a mesma sub-rede numa vnet da subscrição.

## <a name="maintain-naming-conventions"></a>Manter as convenções de nomenclatura

### <a name="question"></a>Pergunta
Como posso manter uma convenção de nomenclatura em meu ambiente de laboratórios DevTest?

### <a name="answer"></a>Resposta
Talvez queira estender atual convenções de nomenclatura de enterprise para operações do Azure e torná-las consistentes em todo o ambiente do DevTest Labs.

Ao implementar o DevTest Labs, recomendamos que tenha políticas específicas de partida. Implemente estas políticas por um script central e modelos JSON para impor a consistência. Políticas de atribuição de nomes pode ser implementada através de políticas do Azure aplicadas-se ao nível da subscrição. Para exemplos JSON para o Azure Policy, veja [exemplos do Azure Policy](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Número de utilizadores por laboratório e laboratórios por organização

### <a name="question"></a>Pergunta 
Como posso determinar a taxa de usuários por laboratório e o número geral de laboratórios necessários em toda a organização?

### <a name="answer"></a>Resposta
Recomendamos unidades de negócios e os grupos de desenvolvimento que estão associados com o mesmo projeto de desenvolvimento que estão associados com o mesmo laboratório. Permite mesmos tipos de políticas, imagens e políticas de encerramento para ser aplicada a ambos os grupos. 

Também poderá considerar limites geográficos. Por exemplo, os desenvolvedores no Leste do Norte EUA (US) pode usar um laboratório aprovisionado em e.u.a. Leste 2. Além disso, os desenvolvedores em Dallas, no Texas e Denver, Colorado podem ser direcionados para utilizar um recurso no e.u.a. centro-Sul. Se houver um esforço colaborativo com um terceiro externo, eles podem ser atribuídos a um laboratório que não seja utilizado por programadores internos. 

Também pode utilizar um laboratório para um projeto específico nos projetos de DevOps do Azure. Em seguida, aplicar segurança através de um grupo do Azure Active Directory especificado, que permite o acesso a ambos os conjunto de recursos. A rede virtual atribuída ao laboratório pode ser outro limite a consolidar os utilizadores.

## <a name="deletion-of-resources"></a>Eliminação de recursos

### <a name="question"></a>Pergunta
Como podemos impedir a eliminação de recursos dentro de um laboratório?

### <a name="answer"></a>Resposta
Recomendamos que defina permissões adequadas ao nível do laboratório, para que somente usuários autorizados possam eliminar recursos ou alterar as políticas de laboratório. Os desenvolvedores devem ser colocados dentro de **os utilizadores de laboratórios DevTest** grupo. O gerente de desenvolvimento ou o líder de infra-estrutura deve ser o **DevTest Labs proprietário**. Recomendamos que tenha apenas dois proprietários de laboratório. Esta política se estende para o repositório de código para evitar danos. Utilizações de laboratório tem direitos para utilizar recursos, mas não é possível atualizar as políticas de laboratório. Consulte o artigo seguinte que lista as funções e direitos de que tem de cada grupo incorporado dentro de um laboratório: [Adicionar proprietários e utilizadores no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Mover o laboratório para outro grupo de recursos 

### <a name="question"></a>Pergunta
É possível para mover um laboratório para outro grupo de recursos?

### <a name="answer"></a>Resposta
Sim. Navegue para a página de grupo de recursos a partir da home page para o laboratório. Em seguida, selecione **mover** na barra de ferramentas e selecione o laboratório de que pretende mover para um grupo de recursos diferente. Quando criar um laboratório, um grupo de recursos é criado automaticamente para. No entanto, pode querer mover o laboratório para um grupo de recursos diferentes que se segue as convenções de nomenclatura da empresa. 

## <a name="next-steps"></a>Passos Seguintes
Ver [gerir o custo e a propriedade](devtest-lab-guidance-governance-cost-ownership.md).
