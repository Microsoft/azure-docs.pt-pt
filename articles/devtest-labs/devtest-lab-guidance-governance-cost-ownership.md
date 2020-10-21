---
title: Gerir custos e propriedade na Azure DevTest Labs
description: Este artigo fornece informações que o ajudam a otimizar o custo e alinhar a propriedade em todo o seu ambiente.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dfac69a055c9b0c75032622caf7fb8502fad3406
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328271"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governação da infraestrutura Azure DevTest Labs - Gerir custos e propriedade
O custo e a propriedade são as principais preocupações quando se considera construir os seus ambientes de desenvolvimento e teste. Nesta secção, encontra informações que o ajudam a otimizar o custo e a alinhar a propriedade em todo o seu ambiente.

## <a name="optimize-for-cost"></a>Otimizar para o custo

### <a name="question"></a>Pergunta
Como posso otimizar o custo dentro do meu ambiente DevTest Labs?

### <a name="answer"></a>Resposta
Existem várias funcionalidades incorporadas da DevTest Labs que o ajudam a otimizar o custo. Consulte [a gestão de custos, limiares](devtest-lab-configure-cost-management.md) [e artigos de políticas](devtest-lab-set-lab-policy.md) para limitar as atividades dos seus utilizadores. 

Ao utilizar a DevTest Labs para uma carga de trabalho de desenvolvimento e teste, poderá considerar utilizar o [Benefício de Subscrição de Dev/Teste da Empresa,](https://azure.microsoft.com/offers/ms-azr-0148p/)como parte do seu Acordo de Empresa. Em alternativa, se for um Cliente Pay como você Go, poderá considerar a [oferta Pay-as-go DevTest](https://azure.microsoft.com/offers/ms-azr-0023p/).

Esta abordagem proporciona-lhe inúmeras vantagens:

- Taxas especiais de dev/teste mais baixas em máquinas virtuais windows, serviços na nuvem, HDInsight, App Service e Apps Lógicas
- Taxas do Great Enterprise Agreement (EA) em outros serviços da Azure
- Acesso a imagens exclusivas de Dev/Test na Galeria, incluindo o Windows 8.1 e Windows 10
 
Apenas os subscritores ativos do Visual Studio (subscrições padrão, subscrições anuais em nuvem e subscrições mensais em nuvem) podem utilizar recursos Azure em execução dentro de uma subscrição de Dev/Test da empresa. No entanto, os utilizadores finais podem aceder à aplicação para fornecer feedback ou realizar testes de aceitação. A utilização de recursos dentro desta subscrição limita-se ao desenvolvimento e teste de aplicações, e não é oferecida nenhuma garantia de tempo útil.

Se decidir utilizar a oferta DevTest, note que este benefício é exclusivamente para desenvolvimento e teste das suas aplicações. A utilização dentro da subscrição não tem um SLA apoiado financeiramente, exceto para o uso de Azure DevOps e HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Defina o acesso baseado em funções em toda a sua organização
### <a name="question"></a>Pergunta
Como posso definir o controlo de acesso baseado em funções da Azure para os meus ambientes DevTest Labs para garantir que as TI possam governar enquanto os desenvolvedores/teste podem fazer o seu trabalho? 

### <a name="answer"></a>Resposta
Há um padrão amplo, no entanto o detalhe depende da sua organização.

A Central IT deve possuir apenas o que for necessário e permitir que as equipas de projeto e de candidatura tenham o nível de controlo necessário. Normalmente, significa que a TI central é proprietária da subscrição e lida com funções de TI fundamentais, tais como configurações de networking. O conjunto de **proprietários** para uma subscrição deve ser pequeno. Estes proprietários podem nomear proprietários adicionais quando há necessidade, ou aplicar políticas de nível de subscrição, por exemplo "No Public IP".

Pode haver um subconjunto de utilizadores que requerem acesso através de uma subscrição, como suporte Tier1 ou Tier 2. Neste caso, recomendamos que dê a estes utilizadores o acesso ao **contribuinte** para que possam gerir os recursos, mas não fornecer acesso ao utilizador ou ajustar políticas.

O recurso DevTest Labs deve ser propriedade de proprietários próximos da equipa de projeto/aplicação. É porque entendem os seus requisitos em termos de máquinas, e software necessário. Na maioria das organizações, o proprietário deste recurso DevTest Labs é geralmente o líder do projeto/desenvolvimento. Este proprietário pode gerir utilizadores e políticas dentro do ambiente de laboratório e pode gerir todos os VMs no ambiente DevTest Labs.

Os membros da equipa de projeto/aplicação devem ser adicionados à função de Utilizadores da DevTest Labs. Estes utilizadores podem criar máquinas virtuais (em linha com as políticas de laboratório e nível de subscrição). Também podem gerir as suas próprias máquinas virtuais. Não conseguem gerir máquinas virtuais que pertencem a outros utilizadores.

Para mais informações, consulte o andaime da empresa Azure – documentação [prescritiva de governação de assinaturas.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


## <a name="next-steps"></a>Passos seguintes
Consulte [a política corporativa e a conformidade.](devtest-lab-guidance-governance-policy-compliance.md)
