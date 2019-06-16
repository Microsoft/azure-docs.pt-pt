---
title: Reforce a sua postura de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Este artigo ajuda-o a fortalecer a sua postura de segurança ao monitorizar os recursos no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 28b4667a9ceb4b3534d85ba28668f06c750e22c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703878"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Reforce a sua postura de segurança no Centro de segurança do Azure
Este artigo ajuda-o a fortalecer a sua postura de segurança. Utilize as capacidades de monitorização no Centro de segurança do Azure para se certificar de que a segurança do recurso é tão forte como possível e monitorizar a conformidade com as políticas.

## <a name="how-do-you-strengthen-your-security-posture"></a>Como reforce a sua postura de segurança?
Pensamos frequentemente que monitorizar é observar e esperar que um evento ocorra, para que possamos reagir à situação. Fortalecendo a sua postura de segurança refere-se para ter uma estratégia proativa que Audita os seus recursos para identificar sistemas que não cumprem padrões organizacionais ou melhores práticas.

Depois de ativar [políticas de segurança](tutorial-security-policy.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades. As informações sobre a configuração da rede estão disponíveis de forma instantânea. Consoante o número de VMs e de computadores em que tenha o agente instalado, a recolha das informações sobre a configuração das VMs e dos computadores, como, por exemplo, o estado de atualizações de segurança e a configuração do sistema operativo, pode demorar uma hora ou mais a ser disponibilizada. Pode ver uma lista completa de problemas e formas de proteger sua rede e remediar o risco dos **recomendações** mosaico.

Pode ver o estado de segurança dos seus recursos e todos os problemas por tipo de recurso:

- Para monitorizar o estado de funcionamento dos seus recursos de computador e as suas aplicações e receber recomendações para melhorar a segurança dos mesmos, consulte o artigo [proteger as máquinas e aplicações no Centro de segurança do Azure](security-center-virtual-machine-protection.md)
- Para monitorizar os seus recursos de rede, como máquinas virtuais, grupos de segurança de rede e os pontos finais e receber recomendações para melhorar a segurança dos mesmos, consulte [proteger sua rede no Centro de segurança do Azure](security-center-network-recommendations.md) para obter mais informações informações. 
- Para monitorizar os seus recursos de dados e armazenamento, como servidores SQL e contas de armazenamento e receber recomendações para melhorar a segurança dos mesmos, consulte [serviço de proteção de SQL do Azure e dados no Centro de segurança do Azure](security-center-sql-service-recommendations.md) para obter mais informações . 
- Para monitorizar os recursos de identidades e acessos, incluindo permissões de conta e de MFA e receber recomendações para melhorar a segurança dos mesmos, consulte [monitorizar a identidade e acesso no Centro de segurança do Azure](security-center-identity-access.md) para obter mais informações. 
- Para monitorizar apenas no momento exato o acesso aos seus recursos, consulte [gerir o acesso de máquina virtual através do just in time](security-center-just-in-time.md) para obter mais informações. 


Para obter mais informações sobre como aplicar recomendações, leia [Implementing security recommendations in Azure Security Center (Implementar recomendações de segurança no Centro de Segurança do Azure)](security-center-recommendations.md).



![Mosaico de estado de funcionamento da segurança dos recursos](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Consulte também
Neste artigo, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md): Saiba como configurar definições de segurança no Centro de segurança do Azure.
* [Gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros no Centro de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [FAQ do Centro de segurança do Azure](security-center-faq.md): Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
