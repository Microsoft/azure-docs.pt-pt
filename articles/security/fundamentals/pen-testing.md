---
title: Teste de caneta | Microsoft Docs
description: O artigo fornece uma visão geral do processo de teste de penetração (caneta) e como executar o caneta em seus aplicativos em execução na infraestrutura do Azure.
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
ms.author: barclayn
ms.openlocfilehash: 3a2addce83862ef109089f1474330f3821daaed7
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726706"
---
# <a name="penetration-testing"></a>Teste de Penetração
Um dos benefícios de usar o Azure para o teste e a implantação de aplicativos é que você pode rapidamente criar ambientes. Você não precisa se preocupar com a requisição, aquisição e "colocar em rack e empilhar" seu próprio hardware local.

Isso é ótimo, mas você ainda precisa ter certeza de que executou a auditoria normal de segurança. Uma das coisas que você provavelmente quer fazer é o teste de penetração dos aplicativos implantados no Azure.

Talvez você já saiba que a Microsoft executa [testes de penetração de nosso ambiente do Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isso ajuda a impulsionar as melhorias do Azure.

Não fazemos o teste de penetração de seu aplicativo para você, mas entendemos que você desejará e precisará executar testes em seus próprios aplicativos. Isso é bom, porque ao aprimorar a segurança de seus aplicativos, você ajuda a tornar todo o ecossistema do Azure mais seguro.

A partir de 15 de junho de 2017, a Microsoft não exige mais aprovação prévia para conduzir um teste de penetração nos recursos do Azure. Os clientes que desejam documentar formalmente os contratos de teste de penetração futuros em Microsoft Azure são incentivados a preencher o formulário de notificação de teste de penetração do [serviço do Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Esse processo está relacionado apenas a Microsoft Azure e não se aplica a nenhum outro serviço de Microsoft Cloud.

>[!IMPORTANT]
>Embora a notificação da Microsoft de atividades de teste de caneta não seja mais necessária, os clientes ainda devem estar em conformidade com as [regras de envolvimento de teste de penetração Unificada Microsoft Cloud](https://technet.microsoft.com/mt784683).

Os testes padrão que você pode executar incluem:

* Testes em seus pontos de extremidade para descobrir as [10 principais vulnerabilidades do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste difuso](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de seus pontos de extremidade
* [Verificação de porta](https://en.wikipedia.org/wiki/Port_scanner) de seus pontos de extremidade

Um tipo de teste que você não pode executar é qualquer tipo de ataque [dos (negação de serviço)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Isso inclui a inicialização de um ataque de DoS em si ou a execução de testes relacionados que podem determinar, demonstrar ou simular qualquer tipo de ataque de DoS.

## <a name="next-steps"></a>Passos Seguintes

- Se você quiser documentar formalmente um teste de penetração futuro em seus aplicativos hospedados na Microsoft Azure, vá até as [regras de teste de penetração do Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) e preencha o formulário de notificação de teste.
