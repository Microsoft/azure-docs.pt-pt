---
title: Dimensione a sua infraestrutura Azure DevTest Labs
description: Este artigo fornece orientações para aumentar a sua infraestrutura Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478795"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Dimensione a sua infraestrutura Azure DevTest Labs
Antes de implementar a DevTest Labs à escala empresarial, existem vários pontos de decisão chave. Compreender estes pontos de decisão a um nível elevado ajuda uma organização com decisões de design no futuro. No entanto, estes pontos não devem impedir uma organização de iniciar uma prova de conceito. As três principais áreas para o planeamento inicial de escala são:

- Rede e segurança
- Topologia de assinatura
- Funções e responsabilidades

## <a name="networking-and-security"></a>Rede e segurança
O networking e a segurança são pedras angulares para todas as organizações. Embora uma implementação em toda a empresa exija uma análise muito mais profunda, há um número reduzido de requisitos para realizar com sucesso uma prova de conceito. Algumas áreas-chave de foco incluem:

- **Subscrição Azure** – Para implementar a DevTest Labs, tem de ter acesso a uma Subscrição Azure com direitos adequados para criar recursos. Existem várias formas de obter acesso a subscrições da Azure, incluindo um Acordo de Empresa e Pagamento À Sua Saída. Para obter mais informações sobre o acesso a uma Subscrição Azure, consulte [o Licensing Azure para a empresa.](https://azure.microsoft.com/pricing/enterprise-agreement/)
- **Acesso a recursos no local** – Algumas organizações exigem os seus recursos nos Laboratórios DevTest têm acesso a recursos no local. É necessária uma ligação segura do seu ambiente no local para a Azure. Por isso, é importante que crie/configuure uma ligação VPN ou Rota Expressa antes de começar. Para mais informações, consulte a [visão geral das Redes Virtuais.](../virtual-network/virtual-networks-overview.md)
- **Requisitos de segurança adicionais** – Outros requisitos de segurança, como políticas de máquinas, acesso a endereços IP públicos, ligação à internet são cenários que podem ter de ser revistos antes de implementar uma prova de conceito. 

## <a name="subscription-topology"></a>Topologia de assinatura
A Topologia de Subscrição é uma consideração crítica de design ao implementar a DevTest Labs para a Enterprise. No entanto, não é necessário solidificar todas as decisões até que uma prova de conceito esteja concluída. Ao avaliar o número de subscrições necessárias para uma implementação da empresa, existem dois extremos: 

- Uma subscrição para toda a organização
- Assinatura por utilizador

Em seguida, destacamos os Prós de cada abordagem.

### <a name="one-subscription"></a>Uma subscrição
Muitas vezes, a abordagem de uma subscrição não é gerível numa grande empresa. No entanto, a limitação do número de subscrições proporciona os seguintes benefícios:

- **Previsão de** custos para a empresa.  O orçamento torna-se muito mais fácil numa única subscrição porque todos os recursos estão num único pool. Esta abordagem permite uma tomada de decisão mais simples sobre quando exercer medidas de controlo de custos a qualquer momento num ciclo de faturação.
- **A gestão** de VMs, artefactos, fórmulas, configuração de rede, permissões, políticas, etc. é mais fácil, uma vez que todas as atualizações são necessárias apenas numa subscrição em vez de fazer atualizações em muitas subscrições.
- O esforço **de ligação** em rede é muito simplificado numa única subscrição para empresas em que a conectividade no local é um requisito. A ligação de redes virtuais através de subscrições (modelo de spoke-hub) é necessária com subscrições adicionais, que requer configuração adicional, gestão, espaços de endereço IP, etc.
- **A colaboração** em equipa é mais fácil quando todos estão a trabalhar na mesma subscrição – por exemplo, é mais fácil reatribuir um VM a um colega de trabalho, partilhar recursos da equipa, etc.

### <a name="subscription-per-user"></a>Assinatura por utilizador
Uma subscrição separada por utilizador proporciona igualdade de oportunidades ao espectro alternativo. Os benefícios de ter muitas subscrições incluem:

- **As quotas de escalonamento do Azure** não vão impedir a adoção. Por exemplo, a partir desta escrita, o Azure permite 200 contas de armazenamento por subscrição. Existem quotas operacionais para a maioria dos serviços em Azure (muitos podem ser personalizados, outros não). Neste modelo de subscrição por utilizador, é altamente improvável que a maioria das quotas sejam alcançadas. Para obter mais informações sobre as atuais quotas de escala do Azure, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- **As cobranças** a grupos ou desenvolvedores individuais tornam-se muito mais fáceis, permitindo que as organizações contassem os custos usando o seu modelo atual.
- **A propriedade & permissões** dos ambientes da DevTest Labs são simples. Você dá aos desenvolvedores o acesso ao nível da subscrição e eles são 100% responsáveis por tudo, incluindo a configuração de rede, políticas de laboratório e gestão de VM.

Na Enterprise, pode haver constrangimentos suficientes nos extremos do espectro. Portanto, poderá ter de configurar subscrições de uma forma que caia no meio destes extremos. Como uma boa prática, o objetivo de uma organização deve ser usar o número mínimo de subscrições possível, tendo em conta as funções de força que aumentam o número total de subscrições. Para reiterar, a topologia de subscrição é fundamental para uma implementação de empresas da DevTest Labs, mas não deve atrasar uma prova de conceito. Há detalhes adicionais no artigo de [Governação](devtest-lab-guidance-governance-policy-compliance.md) sobre como decidir sobre subscrição e granularidade de laboratório na organização.

## <a name="roles-and-responsibilities"></a>Funções e responsabilidades
Uma prova de conceito da DevTest Labs tem três funções primárias com responsabilidades definidas - Proprietário de subscrição, dono da DevTest Labs, utilizador da DevTest Labs e opcionalmente um Contribuinte.

- **Proprietário de subscrição** – O proprietário da subscrição tem o direito de administrar uma Subscrição Azure, incluindo atribuir utilizadores, gerir políticas, criar & gerir a topologia de rede, solicitar aumentos de quota, etc. Para mais informações, consulte [este artigo.](../role-based-access-control/rbac-and-directory-admin-roles.md)
- **Dono da DevTest Labs** – O dono da DevTest Labs tem acesso administrativo total ao laboratório. Esta pessoa é responsável por adicionar/remover utilizadores, gerir definições de custos, configurações gerais de laboratório e outras tarefas baseadas em VM/artefacto. Um dono de laboratório também tem todos os direitos de um utilizador da DevTest Labs.
- **Utilizador da DevTest Labs** – O utilizador da DevTest Labs pode criar e consumir as máquinas virtuais em laboratório. Estes indivíduos têm algumas capacidades administrativas mínimas em VMs que criam (iniciar/parar/eliminar/configurar os seus VMs). Os utilizadores não conseguem gerir VMs de outros utilizadores.

## <a name="next-steps"></a>Passos seguintes
Veja o próximo artigo desta série: [Orquestrou a implementação da Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)