---
title: Fortaleça sua postura de segurança com a central de segurança do Azure | Microsoft Docs
description: Este artigo ajuda você a fortalecer sua postura de segurança monitorando seus recursos na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 57d47712fe20fac0bf370a9a100a07a37d7aa753
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996681"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Fortaleça sua postura de segurança com a central de segurança do Azure
Este artigo ajuda você a fortalecer sua postura de segurança. Use os recursos de monitoramento na central de segurança do Azure para garantir que a segurança dos recursos seja a mais rígida possível e monitore a conformidade com as políticas.

## <a name="how-do-you-strengthen-your-security-posture"></a>Como fortalecer sua postura de segurança?
Pensamos frequentemente que monitorizar é observar e esperar que um evento ocorra, para que possamos reagir à situação. Reforçar sua postura de segurança refere-se a ter uma estratégia proativa que audita seus recursos para identificar sistemas que não atendem aos padrões organizacionais ou práticas recomendadas.

Depois de ativar [políticas de segurança](tutorial-security-policy.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades. As informações sobre a configuração da rede estão disponíveis de forma instantânea. Consoante o número de VMs e de computadores em que tenha o agente instalado, a recolha das informações sobre a configuração das VMs e dos computadores, como, por exemplo, o estado de atualizações de segurança e a configuração do sistema operativo, pode demorar uma hora ou mais a ser disponibilizada. Você pode exibir uma lista completa de problemas e maneiras de proteger sua rede e corrigir o risco no bloco **recomendações** .

Você pode exibir o estado de segurança de seus recursos e quaisquer problemas por tipo de recurso:

- Para monitorar a integridade de seus recursos de computador e seus aplicativos e receber recomendações para melhorar sua segurança, consulte [protegendo seus computadores e aplicativos na central de segurança do Azure](security-center-virtual-machine-protection.md)
- Para monitorar os recursos de rede, como máquinas virtuais, grupos de segurança de rede e pontos de extremidade, e receber recomendações para melhorar sua segurança, consulte [protegendo sua rede na central de segurança do Azure](security-center-network-recommendations.md) para obter mais informações. 
- Para monitorar seus dados e recursos de armazenamento, como servidores SQL e contas de armazenamento, e receber recomendações para melhorar sua segurança, consulte [protegendo o serviço SQL do Azure e dados na central de segurança do Azure](security-center-sql-service-recommendations.md) para obter mais informações. 
- Para monitorar seus recursos de identidade e acesso, incluindo permissões de MFA e de conta, e receber recomendações para melhorar sua segurança, consulte [monitorar identidade e acesso na central de segurança do Azure](security-center-identity-access.md) para obter mais informações. 
- Para monitorar o acesso just-in-time aos seus recursos, consulte [gerenciar o acesso à máquina virtual usando o just-in-time](security-center-just-in-time.md) para obter mais informações. 


Para obter mais informações sobre como aplicar recomendações, leia [Implementing security recommendations in Azure Security Center (Implementar recomendações de segurança no Centro de Segurança do Azure)](security-center-recommendations.md).



![Mosaico de estado de funcionamento da segurança dos recursos](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Consulte também
Neste artigo, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md): Saiba como definir as configurações de segurança na central de segurança do Azure.
* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Monitorando soluções de parceiros com a central de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Perguntas frequentes da central de segurança do Azure](security-center-faq.md): Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
