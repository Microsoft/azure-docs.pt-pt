---
title: Testes de penetração Microsoft Docs
description: O artigo fornece uma visão geral do processo de teste de penetração e como realizar um teste de caneta contra a sua app em execução na infraestrutura Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: dfacf124f8db0e5323c9abff56c4a78f85f6f014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816138"
---
# <a name="penetration-testing"></a>Testes de penetração

Um dos benefícios de usar o Azure para testes de aplicações e implementação é que você pode obter rapidamente ambientes criados. Não tens de te preocupar com a requisição, aquisição e "racking e empilhar" o teu próprio hardware no local.

Criar rapidamente ambientes é ótimo – mas ainda precisa de ter a certeza de que realiza as suas diligências normais de segurança. Uma das coisas que provavelmente quer fazer é testar as aplicações que implementa no Azure.

Já deve saber que a Microsoft realiza [testes de penetração do nosso ambiente Azure.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) Este teste ajuda a impulsionar as melhorias do Azure.

Não realizamos testes de penetração da sua aplicação para si, mas entendemos que quer e precisa de realizar testes nas suas próprias aplicações. Isso é uma coisa boa, porque quando aumentas a segurança das tuas aplicações ajudas a tornar todo o ecossistema Azure mais seguro.

A partir de 15 de junho de 2017, a Microsoft já não necessita de pré-aprovação para realizar um teste de penetração contra os recursos da Azure. Este processo está apenas relacionado com o Microsoft Azure, e não é aplicável a qualquer outro Microsoft Cloud Service.

>[!IMPORTANT]
>Embora notificar a Microsoft das atividades de teste de canetas já não é necessário que os clientes continuem a cumprir [as regras de teste de penetração unificadas da Microsoft Cloud.](https://technet.microsoft.com/mt784683)

Os testes padrão que pode realizar incluem:

* Testes nos seus pontos finais para descobrir o [Open Web Application Security Project (OWASP) top 10 vulnerabilidades](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de fuzz dos seus pontos finais
* [Verificação portuária](https://en.wikipedia.org/wiki/Port_scanner) dos seus pontos finais

Um tipo de teste de caneta que não pode realizar é qualquer tipo de ataque [de Negação de Serviço (DoS).](https://en.wikipedia.org/wiki/Denial-of-service_attack) Este teste inclui iniciar um ataque do DoS em si, ou realizar testes relacionados que possam determinar, demonstrar ou simular qualquer tipo de ataque DoS.

>[!Note]
>A Microsoft estabeleceu uma parceria com a BreakingPoint Cloud para construir uma interface onde pode gerar tráfego contra endereços IP públicos ativados pela Proteção DDoS para simulações. Para saber mais sobre a simulação da Cloud BreakPoint, consulte [a deteção de DDoS validada.](../../virtual-network/manage-ddos-protection.md#validate-ddos-detection)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre as [Regras de Teste de Penetração de Engajamento.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)
