---
title: Gerenciar o custo e a propriedade no Azure DevTest Labs
description: Este artigo fornece informações que ajudam a otimizar o custo e a alinhar a propriedade em seu ambiente.
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
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561667"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governança da infraestrutura de Azure DevTest Labs-gerenciar custo e Propriedade
O custo e a propriedade são preocupações principais quando você considera a criação de seus ambientes de desenvolvimento e teste. Nesta seção, você encontrará informações que ajudam a otimizar o custo e a alinhar a propriedade em seu ambiente.

## <a name="optimize-for-cost"></a>Otimizar para custo

### <a name="question"></a>Pergunta
Como posso otimizar o custo no meu ambiente do DevTest Labs?

### <a name="answer"></a>Resposta
Há vários recursos internos do DevTest Labs que ajudam você a otimizar o custo. Consulte os artigos sobre [Gerenciamento de custos, limites](devtest-lab-configure-cost-management.md) [e políticas](devtest-lab-set-lab-policy.md) para limitar as atividades de seus usuários. 

Ao utilizar o DevTest Labs para cargas de trabalho de desenvolvimento e teste, você pode considerar a utilização do [desenvolvimento/teste Enterprise benefício de assinatura](https://azure.microsoft.com/offers/ms-azr-0148p/), como parte do seu Enterprise Agreement. Como alternativa, se você for um cliente pré-pago, talvez queira considerar a [oferta DevTest do pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/).

Essa abordagem oferece inúmeras vantagens:

- Taxas especiais de desenvolvimento/teste mais baixas em máquinas virtuais do Windows, serviços de nuvem, HDInsight, serviço de aplicativo e aplicativos lógicos
- Grandes taxas de Enterprise Agreement (EA) em outros serviços do Azure
- Acesso a imagens exclusivas de Programador/Teste na Galeria, incluindo o Windows 8.1 e o Windows 10
 
Somente assinantes ativos do Visual Studio (assinaturas padrão, assinaturas de nuvem anuais e assinaturas de nuvem mensais) podem usar recursos do Azure em execução em uma assinatura de desenvolvimento/teste Enterprise. No entanto, os usuários finais podem acessar o aplicativo para fornecer comentários ou executar testes de aceitação. A utilização de recursos nesta subscrição é restrita à programação e ao teste de aplicações e não é oferecida uma garantia de tempo de atividade.

Se você decidir usar a oferta DevTest, observe que esse benefício é exclusivamente para desenvolvimento e teste de seus aplicativos. O uso dentro da assinatura não tem um SLA com suporte financeiro, exceto pelo uso do Azure DevOps e HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definir um acesso baseado em função em sua organização
### <a name="question"></a>Pergunta
Como fazer definir o controle de acesso baseado em função para meus ambientes do DevTest Labs para garantir que ele possa ser controlado, enquanto os desenvolvedores/testes podem fazer seu trabalho? 

### <a name="answer"></a>Resposta
Há um padrão amplo, no entanto, os detalhes dependem de sua organização.

A ti central deve ter apenas o que é necessário e permitir que as equipes de projeto e de aplicativo tenham o nível de controle necessário. Normalmente, isso significa que a ti central possui a assinatura e manipula funções de ti essenciais, como configurações de rede. O conjunto de **proprietários** de uma assinatura deve ser pequeno. Esses proprietários podem indicar proprietários adicionais quando há uma necessidade ou aplicar políticas no nível da assinatura, por exemplo, "nenhum IP público".

Pode haver um subconjunto de usuários que exigem acesso em uma assinatura, como o suporte a nível 1 ou nível 2. Nesse caso, é recomendável conceder a esses usuários o acesso de **colaborador** para que eles possam gerenciar os recursos, mas não fornecer acesso ao usuário ou ajustar políticas.

O recurso DevTest Labs deve ser de propriedade dos proprietários que estão próximos da equipe de projeto/aplicativo. É porque eles entendem seus requisitos em termos de computadores e software necessário. Na maioria das organizações, o proprietário desse recurso do DevTest Labs normalmente é o líder de projeto/desenvolvimento. Esse proprietário pode gerenciar usuários e políticas no ambiente de laboratório e pode gerenciar todas as VMs no ambiente do DevTest Labs.

Os membros da equipe do projeto/aplicativo devem ser adicionados à função usuários do DevTest Labs. Esses usuários podem criar máquinas virtuais (em linha com o laboratório e as políticas de nível de assinatura). Eles também podem gerenciar suas próprias máquinas virtuais. Eles não podem gerenciar máquinas virtuais que pertencem a outros usuários.

Para obter mais informações, consulte [Azure Enterprise Scaffold – documentação de governança de assinatura prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold) .


## <a name="next-steps"></a>Passos seguintes
Consulte [conformidade e política corporativa](devtest-lab-guidance-governance-policy-compliance.md).
