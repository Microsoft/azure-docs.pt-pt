---
title: Testes de intrusão | Documentos da Microsoft
description: O artigo fornece uma visão geral do processo (teste) de teste de penetração e como executar o teste em relação a seus aplicativos em execução na infraestrutura do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 8835c4534b6dab1e8dbfb3546257ae4bc3b9d7af
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588657"
---
# <a name="penetration-testing"></a>Testes de Penetração
Uma das vantagens da utilização do Azure para teste de aplicativos e a implantação é que pode obter rapidamente ambientes criados. Não precisa se preocupar sobre requisitioning, aquisição e "montagens e empilhamentos" seu próprio hardware no local.

Isso é ótimo, mas ainda tem de certificar-se de que executar a sua segurança normal auditoria detalhada. É uma das coisas que provavelmente desejará fazer penetração testar as aplicações que implementar no Azure.

Já deve saber que a Microsoft efetua [testes de penetração do nosso ambiente do Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isto ajuda a orientar melhorias do Azure.

Não temos o teste de penetração seu aplicativo para, mas estamos cientes de que irá desejar e tem de realizar os testes em seus próprios aplicativos. Isso é algo bom, porque quando é aprimorar a segurança das suas aplicações ajudam a tornar o todo o ecossistema do Azure mais segura.

A partir de 15 de Junho de 2017, a Microsoft já não requer aprovação prévia para conduzir um teste de penetração em recursos do Azure. Os clientes que pretendem formalmente documento futuros testes de penetração compromissos no Microsoft Azure são incentivados a preencher o [formulário de notificação de teste do Azure Service penetração](https://portal.msrc.microsoft.com/en-us/engage/pentest). Esse processo está relacionado apenas para o Microsoft Azure e não aplicável a qualquer outro serviço Cloud da Microsoft.

>[!IMPORTANT]
>Embora já não é necessário notificar a Microsoft de atividades de teste de caneta clientes ainda devem ser compatível com o [Microsoft Cloud Unified penetração teste regras de envolvimento](https://technet.microsoft.com/mt784683).

Pode realizar de testes padrão incluem:

* Testes nos seus pontos finais para descobrir o [Open Web Application Security Project (OWASP) top 10 vulnerabilidades](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste de difusão](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) dos seus pontos finais
* [Verificação de porta](https://en.wikipedia.org/wiki/Port_scanner) dos seus pontos finais

É de um tipo de teste que não é possível executar qualquer tipo de [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) ataque. Isto inclui a iniciar um ataque em si, ou realização de testes relacionados que possam determinar, demonstrar ou simular qualquer tipo de ataque DoS.

## <a name="next-steps"></a>Passos Seguintes

- Se desejar formalmente documentar uma futura cabo testes de penetração em seus aplicativos hospedados no Microsoft Azure, vá para o [penetração teste regras de envolvimento](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) e preencha o formulário de notificação de teste.
