---
title: Teste de Canetas [ Teste de Canetas] Microsoft Docs
description: O artigo fornece uma visão geral do processo de teste de penetração (pentest) e como executar pentest contra as suas aplicações em execução na infraestrutura azure.
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
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549258"
---
# <a name="penetration-testing"></a>Teste de Penetração
Um dos benefícios da utilização do Azure para testes e implementação de aplicações é que pode rapidamente criar ambientes. Não tens de te preocupar com requisições, aquisição e "racking" no teu próprio hardware no local.

Isto é ótimo – mas ainda precisa de se certificar de que realiza a sua diligência normal de segurança. Uma das coisas que provavelmente quer fazer é testar a penetração das aplicações que implementa em Azure.

Já deve saber que a Microsoft realiza [testes de penetração do nosso ambiente Azure.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) Isto ajuda a impulsionar as melhorias do Azure.

Não testamos a sua aplicação de penetração para si, mas entendemos que vai querer e precisa de realizar testes nas suas próprias aplicações. Isso é uma coisa boa, porque quando aumentas a segurança das tuas aplicações ajudas a tornar todo o ecossistema Azure mais seguro.

A partir de 15 de junho de 2017, a Microsoft já não necessita de pré-aprovação para realizar um teste de penetração contra os recursos do Azure. Os clientes que desejem documentar formalmente os próximos compromissos de testes de penetração contra o Microsoft Azure são encorajados a preencher o formulário de notificação de teste de penetração do [serviço Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Este processo está apenas relacionado com o Microsoft Azure, e não é aplicável a qualquer outro Serviço Microsoft Cloud.

>[!IMPORTANT]
>Ao notificar a Microsoft de que as atividades de teste de canetas já não são necessárias, os clientes devem ainda cumprir as [Regras de Teste de Penetração Unificadas da Microsoft Cloud](https://technet.microsoft.com/mt784683).

Os testes padrão que pode realizar incluem:

* Testes nos seus pontos finais para descobrir o Projeto de Segurança de [Aplicações Abertas web (OWASP) top 10 vulnerabilidades](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste fuzz](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) dos seus pontos finais
* [Digitalização](https://en.wikipedia.org/wiki/Port_scanner) portuária dos seus pontos finais

Um tipo de teste que não pode realizar é qualquer tipo de ataque [de Negação de Serviço (DoS).](https://en.wikipedia.org/wiki/Denial-of-service_attack) Isto inclui o início de um ataque do DoS em si, ou a realização de testes relacionados que possam determinar, demonstrar ou simular qualquer tipo de ataque do DoS.

## <a name="next-steps"></a>Passos seguintes

- Se quiser documentar formalmente um próximo teste de penetração contra as suas aplicações hospedadas no Microsoft Azure, dirija-se às [Regras de Teste de Penetração de Envolvimento](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) e preencha o formulário de notificação de teste.
