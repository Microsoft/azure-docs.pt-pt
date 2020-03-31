---
title: Dimensione a sua infraestrutura azure DevTest Labs
description: Este artigo fornece orientação para escalar a sua infraestrutura Azure DevTest Labs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644839"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Dimensione a sua infraestrutura azure DevTest Labs
Antes de implementar o DevTest Labs à escala empresarial, existem vários pontos-chave de decisão. Compreender estes pontos de decisão a um nível elevado ajuda uma organização com decisões de design no futuro. No entanto, estes pontos não devem impedir uma organização de iniciar uma prova de conceito. As três principais áreas para o planeamento inicial da escala são:

- Networking e segurança
- Topologia de assinatura
- Funções e responsabilidades

## <a name="networking-and-security"></a>Networking e segurança
O networking e a segurança são pedras angulares para todas as organizações. Embora uma implementação em toda a empresa exija uma análise muito mais profunda, há um número reduzido de requisitos para realizar com sucesso uma prova de conceito. Algumas áreas-chave de foco incluem:

- **Assinatura Azure** – Para implementar o DevTest Labs, tem de ter acesso a uma Subscrição Azure com direitos adequados para criar recursos. Existem várias formas de ter acesso às subscrições do Azure, incluindo um Acordo de Empresa e Pay As You Go. Para obter mais informações sobre o acesso a uma Subscrição Azure, consulte [o Licenciamento Azure para a empresa.](https://azure.microsoft.com/pricing/enterprise-agreement/)
- **Acesso aos recursos no local** – Algumas organizações exigem que os seus recursos nos Laboratórios DevTest tenham acesso aos recursos no local. É necessária uma ligação segura do seu ambiente no local com o Azure. Por isso, é importante que configure/configure uma ligação VPN ou Express Route antes de começar. Para mais informações, consulte a [visão geral das Redes Virtuais.](../virtual-network/virtual-networks-overview.md)
- **Requisitos adicionais** de segurança – Outros requisitos de segurança, tais como políticas de máquinas, acesso a endereços IP públicos, ligação à internet são cenários que podem ter de ser revistos antes de implementar uma prova de conceito. 

## <a name="subscription-topology"></a>Topologia de assinatura
A Topologia de Assinatura é uma consideração crítica de design ao implementar devTest Labs para a Enterprise. No entanto, não é necessário solidificar todas as decisões até que uma prova de conceito tenha sido concluída. Ao avaliar o número de subscrições necessárias para uma implementação da empresa, existem dois extremos: 

- Uma subscrição para toda a organização
- Subscrição por utilizador

Em seguida, destacamos os Prós de cada abordagem.

### <a name="one-subscription"></a>Uma subscrição
Muitas vezes, a abordagem de uma subscrição não é gerível numa grande empresa. No entanto, limitar o número de assinaturas proporciona os seguintes benefícios:

- **Previsão de** custos para a empresa.  O orçamento torna-se muito mais fácil numa única subscrição, porque todos os recursos estão numa única piscina. Esta abordagem permite uma tomada de decisão mais simples sobre quando exercer medidas de controlo de custos em qualquer momento de um ciclo de faturação.
- **A gestão** de VMs, artefactos, fórmulas, configuração de rede, permissões, políticas, etc. é mais fácil, uma vez que todas as atualizações são necessárias apenas numa subscrição em oposição a fazer atualizações em muitas subscrições.
- O esforço de **networking** é muito simplificado numa única subscrição para as empresas onde a conectividade no local é um requisito. A ligação de redes virtuais através de subscrições (modelo de hub-spoke) é necessária com subscrições adicionais, que requerem configuração adicional, gestão, espaços de endereçoIP, etc.
- **A colaboração da equipa** é mais fácil quando todos trabalham na mesma subscrição – por exemplo, é mais fácil reatribuir um VM a um colega de trabalho, partilhar recursos da equipa, etc.

### <a name="subscription-per-user"></a>Subscrição por utilizador
Uma subscrição separada por utilizador proporciona igualdade de oportunidades ao espectro alternativo. Os benefícios de ter muitas subscrições incluem:

- As quotas de **escala azul** não vão impedir a adoção. Por exemplo, a partir desta escrita o Azure permite 200 contas de armazenamento por subscrição. Existem quotas operacionais para a maioria dos serviços em Azure (muitos podem ser personalizados, outros não conseguem). Neste modelo de subscrição por utilizador, é altamente improvável que a maioria das quotas sejam atingidas. Para obter mais informações sobre as quotas de escala atual do Azure, consulte [os limites de subscrição e serviço do Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- **Os encargos** para grupos ou desenvolvedores individuais tornam-se muito mais fáceis de permitir que as organizações possam responder pelos custos usando o seu modelo atual.
- **A propriedade & permissões** dos ambientes dos Laboratórios DevTest são simples. Você dá aos desenvolvedores o acesso ao nível de subscrição e eles são 100% responsáveis por tudo, incluindo a configuração de rede, políticas de laboratório e gestão vm.

Na Enterprise, pode haver constrangimentos suficientes nos extremos do espectro. Por isso, poderá ter de configurar subscrições de uma forma que caia no meio destes extremos. Como uma boa prática, o objetivo de uma organização deve ser usar o número mínimo de subscrições o mais possível tendo em conta as funções de força que aumentam o número total de subscrições. Para reiterar, a topologia de subscrição é fundamental para uma implementação da empresa da DevTest Labs, mas não deve atrasar uma prova de conceito. Existem detalhes adicionais no artigo [de Governação](devtest-lab-guidance-governance-policy-compliance.md) sobre como decidir sobre a subscrição e granularidade labnalita na organização.

## <a name="roles-and-responsibilities"></a>Funções e responsabilidades
A DevTest Labs prova de conceito tem três papéis primários com responsabilidades definidas – Proprietário de subscrição, proprietário de DevTest Labs, utilizador da DevTest Labs, e opcionalmente um Colaborador.

- Proprietário de **subscrição** – O proprietário de subscrição tem o direito de administrar uma Subscrição Azure, incluindo a atribuição de utilizadores, a gestão de políticas, a criação de & gestão de topologia de networking, solicitando aumentos de quota, etc. Para mais informações, consulte [este artigo.](../role-based-access-control/rbac-and-directory-admin-roles.md)
- **Dono da DevTest Labs** – O dono do DevTest Labs tem acesso administrativo total ao laboratório. Esta pessoa é responsável por adicionar/remover utilizadores, gerir as definições de custos, configurações gerais do laboratório e outras tarefas baseadas em VM/artefactos. Um dono de laboratório também tem todos os direitos de um Utilizador de Laboratórios DevTest.
- Utilizador do **DevTest Labs** – O utilizador da DevTest Labs pode criar e consumir as máquinas virtuais em laboratório. Estes indivíduos têm algumas capacidades administrativas mínimas em VMs que criam (iniciar/parar/excluir/configurar os seus VMs). Os utilizadores não conseguem gerir VMs de outros utilizadores.

## <a name="next-steps"></a>Passos seguintes
Veja o próximo artigo desta série: [Orquestrar a implementação da Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)