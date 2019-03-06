---
title: Dimensionar a sua infraestrutura do Azure DevTest Labs
description: Este artigo fornece orientações para dimensionar a sua infraestrutura do Azure DevTest Labs.
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
ms.openlocfilehash: 25a088686c739c53feadd6354baf75f3147bdc33
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431203"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Dimensionar a sua infraestrutura do Azure DevTest Labs
Antes de implementar o DevTest Labs em dimensão empresarial, existem vários pontos de decisão principais. Noções básicas sobre esses pontos de decisão num alto nível ajuda uma organização com decisões de design no futuro. No entanto, esses pontos devem não conter uma organização de iniciar uma prova de conceito. As áreas de três principais para o planeamento de dimensionamento inicial são:

- Funcionamento em rede e segurança
- Topologia de subscrição
- Funções e responsabilidades

## <a name="networking-and-security"></a>Funcionamento em rede e segurança
Funcionamento em rede e segurança são os fundamentos para todas as organizações. Uma implementação de toda a empresa exige muito uma análise mais aprofundada, há um número reduzido de requisitos para realizar com êxito uma prova de conceito. Algumas áreas principais de foco incluem:

- **Subscrição do Azure** – para implementar o DevTest Labs, tem de ter acesso a uma subscrição do Azure com os direitos adequados para criar recursos. Existem várias formas de obter acesso a subscrições do Azure, incluindo um contrato Enterprise e Pay As You Go. Para obter mais informações sobre a obtenção de acesso a uma subscrição do Azure, consulte [licenciamento Azure nas empresas](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Acesso a recursos no local** – algumas organizações exigem que seus recursos no DevTest Labs tem acesso a recursos no local. É necessária uma ligação segura do seu ambiente no local para o Azure. Por conseguinte, é importante que defina up/configurar a ligação de uma VPN ou Expressroute antes de começar. Para obter mais informações, consulte [descrição geral de redes virtuais](../virtual-network/virtual-networks-overview.md).
- **Requisitos de segurança adicionais** – outros requisitos de segurança, tais como as políticas de máquina, acesso a endereços IP públicos, conexão com a internet são cenários que poderão ter de ser revisto antes de implementar uma prova de conceito. 

## <a name="subscription-topology"></a>Topologia de subscrição
Topologia de subscrição é uma consideração de design importantes ao implementar o DevTest Labs para a empresa. No entanto, não é necessário para se consolidar todas as decisões até depois de uma prova de conceito foi concluída. Ao avaliar o número de subscrições necessários para uma implementação empresarial, existem dois extremos: 

- Uma subscrição para toda a organização
- Subscrição por utilizador

Em seguida, podemos destacar os profissionais de TI de cada abordagem.

### <a name="one-subscription"></a>Uma subscrição
Muitas vezes, a abordagem de uma subscrição não é gerenciável numa grande empresa. No entanto, a limitar o número de subscrições fornece as seguintes vantagens:

- **Previsão** custos para a empresa.  Orçamento torna-se muito mais fácil numa única subscrição porque todos os recursos estão num único pool. Esta abordagem permite a mais simples de tomada de decisão em quando exercer controlo de custos mede a qualquer momento num ciclo de faturação.
- **Capacidade de gerenciamento** de VMs, artefatos, fórmulas, configuração de rede, permissões, as políticas, etc. é mais fácil, uma vez que todas as atualizações apenas são necessárias numa subscrição em vez de fazer atualizações entre várias subscrições.
- **Funcionamento em rede** esforço foi bastante simplificado numa única subscrição para as empresas em que a conectividade no local é um requisito. Ligar redes virtuais entre subscrições (modelo de hub-and-spoke) é necessário com as assinaturas adicionais, que requer configuração adicional, gestão, espaços de endereços IP, etc.
- **Colaboração da Equipe** é mais fácil quando todas as pessoas está a funcionar na mesma subscrição – por exemplo, é mais fácil reatribuir uma VM para um colega de trabalho, partilhar da equipe de recursos, etc.

### <a name="subscription-per-user"></a>Subscrição por utilizador
Uma subscrição separada por utilizador fornece oportunidades de igual à gama alternativa. As vantagens de ter o número de subscrições incluem:

- **Azure dimensionamento quotas** não vão impedir a adoção. Por exemplo, no momento da elaboração deste artigo, que o Azure permite-200 contas de armazenamento por subscrição. Existem quotas operacionais para a maioria dos serviços do Azure (muitos podem ser personalizados, algumas não seja possível). Neste modelo de uma subscrição por utilizador, é bastante improvável que a maioria das quotas são atingidas. Para obter mais informações sobre quotas de dimensionamento do Azure atuais, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
- **Estornos** a grupos ou individuais que os desenvolvedores se tornam muito mais fácil que permitem às organizações para levar em conta os custos com o modelo atual.
- **Propriedade & permissões** dos laboratórios DevTest ambientes são simples. Dar aos desenvolvedores o acesso de nível de assinatura e são 100% responsável por tudo, incluindo a configuração de rede, políticas de laboratório e gerenciamento de VM.

Na empresa, pode ser suficiente restrições em extremos do espectro. Por conseguinte, terá de configurar subscrições de forma que se situe no meio esses extremos. Como melhor prática, o objetivo de uma organização deve ser a utilizar o número mínimo de subscrições como possíveis tendo em mente as funções de imposição que aumentam o número total de subscrições. Para reiterar, topologia de subscrição é fundamental para uma implementação empresarial do DevTest Labs, mas não deve atrasar uma prova de conceito. Existem detalhes adicionais no [governação](devtest-lab-guidance-governance-policy-compliance.md) artigo sobre como decidir qual a granularidade de subscrição e laboratório na organização.

## <a name="roles-and-responsibilities"></a>Funções e responsabilidades
Uma prova de conceito do DevTest Labs tem três funções principais com responsabilidades definidas – proprietário da subscrição, proprietário de DevTest Labs, utilizador de DevTest Labs e, opcionalmente, um contribuinte.

- **Proprietário da subscrição** – o proprietário da subscrição tem direitos para administrar uma subscrição do Azure incluindo atribuir utilizadores, gestão de políticas, criar e gerir a topologia de redes, pedir aumentos de quota, etc. Para obter mais informações, consulte [este artigo](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Proprietário do DevTest Labs** – proprietário o DevTest Labs tem acesso administrativo total ao laboratório. Essa pessoa é responsável por adicionar/remover utilizadores, gestão de definições de custo, definições gerais de laboratório e outras tarefas baseadas em VM/artefacto. Proprietário de um laboratório também tem todos os direitos de um utilizador de DevTest Labs.
- **Utilizador de DevTest Labs** – utilizador o DevTest Labs, pode criar e consumir as máquinas virtuais no laboratório. Estas pessoas tem alguns recursos de administrativos mínimos em VMs que criaram (iniciar/parar/delete/configurar as respetivas VMs). Os utilizadores não podem gerir as VMs de outros utilizadores.

## <a name="next-steps"></a>Passos Seguintes
Consulte o artigo seguinte nesta série: [Orquestrar a implementação do Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)