---
title: Plano de Preparação do Centro de Segurança do Azure | Microsoft Docs
description: Este documento fornece-lhe um plano de preparação para incrementar no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: fcaf427c2e0ab275a5a6e08306dda785bca690d6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784172"
---
# <a name="azure-security-center-readiness-roadmap"></a>Roteiro de prontidão do Centro de Segurança Azure
Este documento fornece um plano de preparação que irá ajudá-lo a começar a utilizar o Centro de Segurança do Azure.

## <a name="understanding-security-center"></a>Compreender o Centro de Segurança
O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e noutras clouds. 

Utilize os seguintes recursos para começar a utilizar o Centro de Segurança.

Artigos
- [Introdução ao Centro de Segurança do Azure](security-center-introduction.md)
- [Manual de início rápido do Centro de Segurança do Azure](security-center-get-started.md)

Vídeos
- [Vídeo de Introdução Rápida](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Descrição Geral da Prevenção, Deteção e Capacidades de Resposta do Centro de Segurança](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planeamento e operações

Para tirar o máximo partido do Centro de Segurança, é importante compreender de que forma diferentes pessoas ou equipas na sua organização utilizam o serviço para dar resposta às necessidades de operações seguras, monitorização, governação e resposta a incidentes.

Utilize os seguintes recursos para ajudá-lo durante os processos de planeamento e operações.

- [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Inclusão de computadores no Centro de Segurança
O Security Center deteta automaticamente quaisquer subscrições ou espaços de trabalho Azure não protegidos pelo Azure Defender. Isto inclui subscrições Azure usando Security Center Free e espaços de trabalho que não têm a solução de segurança ativada.

Utilize os seguintes recursos para ajudá-lo durante os processos de inclusão.

- [Computadores não-Azure a bordo](quickstart-onboard-machines.md)
- [Centro de Segurança do Azure Híbrido - Descrição Geral](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Mitigar os problemas de segurança com o Centro de Segurança
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos.

Utilize os seguintes recursos para ajudá-lo a gerir alertas de segurança e proteger os seus recursos.

Artigos    
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](./security-center-monitoring.md)
- [Proteger a sua rede no Centro de Segurança do Azure](./security-center-network-recommendations.md)
- [Proteger o serviço SQL do Azure e os dados no Centro de Segurança do Azure](./security-center-remediate-recommendations.md)


Vídeo    
- [Mitigar os Problemas de Segurança com o Centro de Segurança do Azure](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Centro de Segurança para resposta a incidentes
Para reduzir custos e danos, é importante ter um plano de resposta a incidentes antes de um ataque ocorrer. Pode utilizar o Centro de Segurança do Azure em várias fases de uma resposta a incidentes.

Utilize os seguintes recursos para compreender como o Centro de Segurança pode ser incorporado no seu processo de resposta a incidentes.

Vídeos    
* [Centro de Segurança do Azure na Resposta a Incidentes](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Responder rapidamente a ameaças com a operação e investigação de segurança de próxima geração](https://youtu.be/e8iFCz5RM4g)

Artigos    
* [Utilizar o Centro de Segurança do Azure para responder a um incidente](./tutorial-security-incident.md)
* [Use a automatização para responder aos gatilhos do Centro de Segurança](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Defesa de cloud avançada

As VMs do Azure podem tirar partido das capacidades avançadas de defesa da cloud no Centro de Segurança. Estas capacidades incluem acesso de máquina virtual just-in-time (VM) e controlos de aplicações adaptativos.

Utilize os seguintes recursos para aprender a utilizar estas capacidades no Centro de Segurança.

Vídeos    
* [Centro de Segurança Azure – Acesso VM just-in-time](https://youtu.be/UOQb2FcdQnU)
* [Centro de Segurança do Azure - Controlos de Aplicação Adaptáveis](https://youtu.be/wWWekI1Y9ck)

Artigos    
* [Gerir o acesso à máquina virtual utilizando o tempo justo](./security-center-just-in-time.md)
* [Controlos de Aplicação Adaptáveis do Centro de Segurança do Azure](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Atividades práticas

* [Laboratório prático do Centro de Segurança](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook de recomendações da Firewall de Aplicações Web (WAF) no Centro de Segurança](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Manual de Procedimentos do Centro de Segurança do Azure: Alertas de Segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Recursos adicionais
* [Página de Documentação do Centro de Segurança](./index.yml)
* [Página de Documentação da API REST do Centro de Segurança](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](./faq-general.md)
* [Página de Preços do Centro de Segurança](https://azure.microsoft.com/pricing/details/security-center/)
* [Melhores práticas da segurança de identidade](../security/fundamentals/identity-management-best-practices.md)
* [Melhores práticas de segurança de rede](../security/fundamentals/network-best-practices.md)
* [Recomendações PaaS](../security/fundamentals/paas-deployments.md)
* [Conformidade](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Os clientes de log analytics podem agora usar o Azure Security Center para proteger as suas cargas de trabalho em nuvem híbrida](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Recursos da Comunidade

* [UserVoice do Centro de Segurança](https://feedback.azure.com/forums/347535-azure-security-center)
* [Q&Uma página para o Centro de Segurança](/answers/topics/azure-security-center.html)