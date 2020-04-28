---
title: Gerir custos e propriedade em Azure DevTest Labs
description: Este artigo fornece informações que o ajudam a otimizar o custo e a alinhar a propriedade em todo o seu ambiente.
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
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74561667"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governação da infraestrutura azure DevTest Labs - Gerir custos e propriedade
O custo e a propriedade são as principais preocupações quando se considera construir os seus ambientes de desenvolvimento e teste. Nesta secção, encontra informações que o ajudam a otimizar o custo e a alinhar a propriedade em todo o seu ambiente.

## <a name="optimize-for-cost"></a>Otimizar para o custo

### <a name="question"></a>Pergunta
Como posso otimizar o custo dentro do meu ambiente de DevTest Labs?

### <a name="answer"></a>Resposta
Existem várias funcionalidades incorporadas dos Laboratórios DevTest que o ajudam a otimizar o custo. Consulte a gestão de [custos, limiares](devtest-lab-configure-cost-management.md) [e artigos](devtest-lab-set-lab-policy.md) de políticas para limitar as atividades dos seus utilizadores. 

Ao utilizar o DevTest Labs para um desenvolvimento e trabalhos de trabalho de teste, poderá considerar utilizar o [Enterprise Dev/Test Subscription Benefit,](https://azure.microsoft.com/offers/ms-azr-0148p/)como parte do seu Acordo de Empresa. Em alternativa, se for cliente Pay as You Go, poderá querer considerar a [oferta Pay-as-you Go DevTest](https://azure.microsoft.com/offers/ms-azr-0023p/).

Esta abordagem proporciona-lhe inúmeras vantagens:

- Taxas especiais de Dev/Test em máquinas virtuais do Windows, serviços na nuvem, HDInsight, App Service e Aplicações Lógicas
- Tarifas do Great Enterprise Agreement (EA) sobre outros serviços da Azure
- Acesso a imagens exclusivas de Dev/Test na Galeria, incluindo Windows 8.1 e Windows 10
 
Apenas os subscritores ativos do Visual Studio (subscrições padrão, subscrições anuais em nuvem e subscrições mensais em nuvem) podem utilizar os recursos do Azure dentro de uma subscrição de Dev/Teste da empresa. No entanto, os utilizadores finais podem aceder à aplicação para fornecer feedback ou realizar testes de aceitação. A utilização de recursos nesta subscrição limita-se ao desenvolvimento e teste de aplicações, e não é oferecida qualquer garantia de tempo de espera.

Se decidir utilizar a oferta DevTest, note que este benefício é exclusivamente para desenvolvimento e teste das suas aplicações. O uso dentro da subscrição não tem um SLA apoiado financeiramente, exceto para a utilização de Azure DevOps e HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Defina um acesso baseado em papéis em toda a sua organização
### <a name="question"></a>Pergunta
Como posso definir o controlo de acesso baseado em papéis para os meus ambientes DevTest Labs para garantir que as TI podem governar enquanto os desenvolvedores/testes podem fazer o seu trabalho? 

### <a name="answer"></a>Resposta
Há um padrão amplo, no entanto o detalhe depende da sua organização.

As TI centrais devem possuir apenas o necessário e permitir que as equipas de projeto e aplicação tenham o nível de controlo necessário. Normalmente, significa que o centro de TI é dono da subscrição e lida com funções de TI fundamentais, tais como configurações de networking. O conjunto de **proprietários** para uma subscrição deve ser pequeno. Estes proprietários podem nomear proprietários adicionais quando há necessidade, ou aplicar políticas de nível de subscrição, por exemplo "No PUBLIC IP".

Pode haver um subconjunto de utilizadores que requerem acesso através de uma subscrição, como suporte tier1 ou tier 2. Neste caso, recomendamos que dê a estes utilizadores o acesso ao **contribuinte** para que possam gerir os recursos, mas não fornecer acesso ao utilizador ou ajustar políticas.

O recurso DevTest Labs deve ser propriedade de proprietários próximos da equipa de projeto/aplicação. É porque compreendem os seus requisitos em termos de máquinas e software necessário. Na maioria das organizações, o proprietário deste recurso DevTest Labs é comumente o líder de projeto/desenvolvimento. Este proprietário pode gerir utilizadores e políticas dentro do ambiente de laboratório e pode gerir todos os VMs no ambiente DevTest Labs.

Os membros da equipa de projeto/aplicação devem ser adicionados à função de Utilizadores de DevTest Labs. Estes utilizadores podem criar máquinas virtuais (em linha com as políticas de laboratório e nível de subscrição). Também podem gerir as suas próprias máquinas virtuais. Não conseguem gerir máquinas virtuais que pertencem a outros utilizadores.

Para mais informações, consulte o andaime da empresa Azure – documentação prescritiva de [governação por subscrição.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


## <a name="next-steps"></a>Passos seguintes
Consulte [a política corporativa e o cumprimento.](devtest-lab-guidance-governance-policy-compliance.md)
