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
ms.openlocfilehash: 7832691812d8f10342dc7df20a7cfab7265f2d9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775717"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governação da infraestrutura do Azure DevTest Labs - gerir o custo e a propriedade
Custo e a propriedade são principais preocupações, considere a criação de seu desenvolvimento e teste de ambientes. Nesta secção, encontrará informações que ajuda a otimizar o custo e alinhar a propriedade em todo o ambiente.

## <a name="optimize-for-cost"></a>Otimizar o custo

### <a name="question"></a>Pergunta
Como posso otimizar custo no meu ambiente de laboratórios DevTest?

### <a name="answer"></a>Resposta
Existem inúmeros recursos internos do DevTest Labs, que o ajudam a otimizar o custo. Ver [gestão de custos, limiares](devtest-lab-configure-cost-management.md) [e as políticas](devtest-lab-set-lab-policy.md) artigos para limitar as atividades dos seus utilizadores. 

Como utilizar DevTest Labs para um cargas de trabalho de desenvolvimento e teste, pode considerar a utilização a [benefício de subscrição Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/), como parte do seu contrato Enterprise. Em alternativa, se for um cliente de Pay as you Go, deve considerar a [Pay as you aceda DevTest oferta](https://azure.microsoft.com/offers/ms-azr-0023p/).

Esta abordagem fornece várias vantagens:

- Tarifas especiais mais Dev/Test baixas em máquinas de virtuais do Windows, serviços cloud, HDInsight, serviço de aplicações e Logic Apps
- Excelentes tarifas de Enterprise Agreement (EA) em outros serviços do Azure
- Acesso a imagens exclusivas de Programador/Teste na Galeria, incluindo o Windows 8.1 e o Windows 10
 
Apenas os subscritores ativos do Visual Studio (subscrições standard, subscrições anuais da cloud e subscrições mensais da cloud) podem utilizar recursos do Azure em execução dentro de uma empresa de subscrição de programador/teste. No entanto, os utilizadores finais podem aceder à aplicação para fornecer comentários ou executar testes de aceitação. A utilização de recursos nesta subscrição é restrita à programação e ao teste de aplicações e não é oferecida uma garantia de tempo de atividade.

Se optar por utilizar a oferta de Dev/Test, tenha em atenção que este benefício é destinado para programar e testar as suas aplicações. Utilização na subscrição não implica um SLA com apoio financeiro, exceto para o uso de DevOps do Azure e HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definir um acesso baseado em funções na sua organização
### <a name="question"></a>Pergunta
Como definir o controlo de acesso baseado em funções para meu DevTest Labs ambientes para se certificar de que o departamento de TI pode governam enquanto os desenvolvedores/teste pode fazer seu trabalho? 

### <a name="answer"></a>Resposta
Há um amplo padrão, no entanto, o detalhe depende da sua organização.

Central IT deve apenas o que é necessário o proprietário e ativar as equipes de projeto e a aplicação para ter o nível necessário de controle. Normalmente, isso significa que central IT proprietária da subscrição e as funções de TI, como configurações de redes de principais de identificadores. O conjunto de **proprietários** para uma subscrição deve ser pequena. Estes proprietários podem nomeie os proprietários adicionais quando for necessário, ou aplicar políticas ao nível da subscrição, por exemplo "sem IP público".

Pode haver um subconjunto de utilizadores que necessitam de acesso através de uma subscrição, como o suporte de nível 1 ou camada 2. Neste caso, recomendamos que dê estes utilizadores a **contribuinte** para que eles podem gerir os recursos, mas não fornecer acesso de utilizador ou ajustar as políticas de acesso.

O recurso de DevTest Labs deve ser propriedade de proprietários que estiverem prestes a equipe de projeto/aplicação. É porque entendem os respetivos requisitos em termos de máquinas e de software necessárias. Na maioria das organizações, o proprietário deste recurso do DevTest Labs geralmente é o líder de projeto/desenvolvimento. Este proprietário pode gerir utilizadores e as políticas de dentro do ambiente de laboratório e pode gerir todas as VMs no ambiente do DevTest Labs.

Os membros da equipe de projeto/aplicação devem ser adicionados à função de utilizadores de laboratórios DevTest. Estes utilizadores podem criar máquinas virtuais (em linha com o laboratório e as políticas ao nível da subscrição). Também podem gerir suas próprias máquinas virtuais. Eles não consegue gerir máquinas virtuais que pertencem a outros utilizadores.

Para obter mais informações, consulte [estrutura empresarial do Azure – governação de subscrições prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold) documentação.


## <a name="next-steps"></a>Passos Seguintes
Ver [política da empresa e a conformidade](devtest-lab-guidance-governance-policy-compliance.md).
