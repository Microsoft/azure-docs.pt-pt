---
title: Escalar verticalmente sua infraestrutura de Azure DevTest Labs
description: Este artigo fornece diretrizes para escalar verticalmente sua infraestrutura de Azure DevTest Labs.
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
ms.openlocfilehash: 3a48cef2210721bf7116b1c4ad1169779288f47d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644839"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Escalar verticalmente sua infraestrutura de Azure DevTest Labs
Antes de implementar o DevTest Labs em escala empresarial, há vários pontos de decisão importantes. Entender esses pontos de decisão em um nível alto ajuda uma organização com decisões de design no futuro. No entanto, esses pontos não devem refazer uma organização de iniciar uma prova de conceito. As três principais áreas para o planejamento de expansão inicial são:

- Rede e segurança
- Topologia de assinatura
- Funções e responsabilidades

## <a name="networking-and-security"></a>Rede e segurança
Rede e segurança são as bases para todas as organizações. Embora uma implantação em toda a empresa exija uma análise muito mais profunda, há um número reduzido de requisitos para realizar uma prova de conceito com êxito. Algumas áreas-chave de foco incluem:

- **Assinatura do Azure** – para implantar o DevTest Labs, você deve ter acesso a uma assinatura do Azure com direitos apropriados para criar recursos. Há várias maneiras de obter acesso às assinaturas do Azure, incluindo um Enterprise Agreement e pré-pago. Para obter mais informações sobre como obter acesso a uma assinatura do Azure, consulte [Licenciamento do Azure para empresas](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Acesso a recursos locais** – algumas organizações exigem seus recursos no DevTest Labs têm acesso a recursos locais. É necessária uma conexão segura do seu ambiente local para o Azure. Portanto, é importante que você configure/configure uma conexão VPN ou de rota expressa antes de começar. Para obter mais informações, consulte [visão geral de redes virtuais](../virtual-network/virtual-networks-overview.md).
- **Requisitos de segurança adicionais** – outros requisitos de segurança, como políticas de computador, acesso a endereços IP públicos, conexão à Internet são cenários que talvez precisem ser revisados antes de implementar uma prova de conceito. 

## <a name="subscription-topology"></a>Topologia de assinatura
A topologia de assinatura é uma consideração de design crítica ao implantar o DevTest Labs na empresa. No entanto, não é necessário solidificar todas as decisões até que uma prova de conceito tenha sido concluída. Ao avaliar o número de assinaturas necessárias para uma implementação empresarial, há dois extremos: 

- Uma assinatura para toda a organização
- Assinatura por usuário

Em seguida, destacamos os prós de cada abordagem.

### <a name="one-subscription"></a>Uma assinatura
Geralmente, a abordagem de uma assinatura não é gerenciável em uma grande empresa. No entanto, limitar o número de assinaturas oferece os seguintes benefícios:

- **Previsão** de custos para empresas.  O orçamento torna-se muito mais fácil em uma única assinatura, pois todos os recursos estão em um único pool. Essa abordagem possibilita a tomada de decisões mais simples sobre quando exercitar medidas de controle de custo em um determinado momento em um ciclo de cobrança.
- A **capacidade de gerenciamento** de VMs, artefatos, fórmulas, configuração de rede, permissões, políticas etc. é mais fácil, já que todas as atualizações só são necessárias em uma assinatura em vez de fazer atualizações em várias assinaturas.
- O esforço de **rede** é bastante simplificado em uma única assinatura para empresas em que a conectividade local é um requisito. A conexão de redes virtuais entre assinaturas (modelo de hub spoke) é necessária com assinaturas adicionais, o que exige configuração adicional, gerenciamento, espaços de endereço IP, etc.
- A **colaboração em equipe** é mais fácil quando todos estão trabalhando na mesma assinatura – por exemplo, é mais fácil reatribuir uma VM a um colega de trabalho, compartilhar recursos da equipe, etc.

### <a name="subscription-per-user"></a>Assinatura por usuário
Uma assinatura separada por usuário fornece oportunidades iguais ao espectro alternativo. Os benefícios de ter várias assinaturas incluem:

- As **cotas de dimensionamento do Azure** não vão impedir a adoção. Por exemplo, no momento da elaboração deste artigo, o Azure permite contas de armazenamento 200 por assinatura. Há cotas operacionais para a maioria dos serviços no Azure (muitos podem ser personalizados, alguns não podem). Nesse modelo de uma assinatura por usuário, é altamente improvável que a maioria das cotas seja atingida. Para saber mais sobre as cotas de dimensionamento atuais do Azure, confira [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Os **estorno** para grupos ou desenvolvedores individuais tornam-se muito mais fáceis, permitindo que as organizações preparam custos usando seu modelo atual.
- A **propriedade & permissões** dos ambientes do DevTest Labs é simples. Você dá aos desenvolvedores o acesso em nível de assinatura e ele é de 100% responsável por tudo, incluindo a configuração de rede, as políticas de laboratório e o gerenciamento de VM.

Na empresa, pode haver restrições suficientes nos extremos do espectro. Portanto, talvez seja necessário configurar assinaturas de uma maneira que se enquadrar no meio desses extremos. Como prática recomendada, o objetivo de uma organização deve ser usar o número mínimo de assinaturas possível tendo em mente as funções que aumentam o número total de assinaturas. Para reiterar, a topologia de assinatura é essencial para uma implantação empresarial do DevTest Labs, mas não deve atrasar uma prova de conceito. Há detalhes adicionais no artigo de [governança](devtest-lab-guidance-governance-policy-compliance.md) sobre como decidir sobre a granularidade da assinatura e do laboratório na organização.

## <a name="roles-and-responsibilities"></a>Funções e responsabilidades
Uma prova de conceito do DevTest Labs tem três funções principais com responsabilidades definidas – proprietário da assinatura, proprietário do DevTest Labs, usuário do DevTest Labs e, opcionalmente, um colaborador.

- **Proprietário da assinatura** – o proprietário da assinatura tem direitos para administrar uma assinatura do Azure, incluindo a atribuição de usuários, o gerenciamento de políticas, a criação de & o gerenciamento de topologia de rede, a solicitação de aumento de cota, etc. Para obter mais informações, consulte [Este artigo](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Proprietário do DevTest Labs** – o proprietário do DevTest Labs tem acesso administrativo total ao laboratório. Essa pessoa é responsável por adicionar/remover usuários, gerenciar configurações de custo, configurações de laboratório gerais e outras tarefas baseadas em VM/artefato. Um proprietário de laboratório também tem todos os direitos de um usuário do DevTest Labs.
- **Usuário do DevTest Labs** – o usuário do DevTest Labs pode criar e consumir as máquinas virtuais no laboratório. Esses indivíduos têm alguns recursos administrativos mínimos em VMs que eles criam (iniciar/parar/excluir/configurar suas VMs). Os usuários não podem gerenciar VMs de outros usuários.

## <a name="next-steps"></a>Passos seguintes
Veja o próximo artigo desta série: [orquestrar a implementação do Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)