---
title: Fortaleça a sua postura de segurança com o Azure Security Center . Microsoft Docs
description: Este artigo ajuda-o a fortalecer a sua postura de segurança monitorizando os seus recursos no Centro de Segurança Azure.
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
ms.openlocfilehash: d18258d62267c931a39947611dda038ff6e65205
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84718704"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Reforce a sua postura de segurança com o Centro de Segurança do Azure
Este artigo ajuda-o a fortalecer a sua postura de segurança. Utilize as capacidades de monitorização no Azure Security Center para garantir que a segurança dos recursos é o mais apertada possível e monitorizar o cumprimento das políticas.

## <a name="how-do-you-strengthen-your-security-posture"></a>Como fortalece a sua postura de segurança?
Pensamos frequentemente que monitorizar é observar e esperar que um evento ocorra, para que possamos reagir à situação. Fortalecer a sua postura de segurança refere-se a ter uma estratégia proativa que audita os seus recursos para identificar sistemas que não cumprem padrões organizacionais ou boas práticas.

Depois de ativar [políticas de segurança](tutorial-security-policy.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades. As informações sobre a configuração da rede estão disponíveis de forma instantânea. Consoante o número de VMs e de computadores em que tenha o agente instalado, a recolha das informações sobre a configuração das VMs e dos computadores, como, por exemplo, o estado de atualizações de segurança e a configuração do sistema operativo, pode demorar uma hora ou mais a ser disponibilizada. Pode ver uma lista completa de questões e formas de endurecer a sua rede e corrigir o risco no azulejo das **Recomendações.**

Pode ver o estado de segurança dos seus recursos e quaisquer problemas por tipo de recurso:

- Para monitorizar a saúde dos seus recursos informáticos e das suas aplicações, e receber recomendações para melhorar a sua segurança, consulte [proteger as suas máquinas e aplicações no Azure Security Center](security-center-virtual-machine-protection.md)
- Para monitorizar os recursos da sua rede, como máquinas virtuais, grupos de segurança de rede e pontos finais, e receber recomendações para melhorar a sua segurança, consulte [proteger a sua rede no Azure Security Center](security-center-network-recommendations.md) para obter mais informações. 
- Para monitorizar os seus dados e recursos de armazenamento, tais como servidores SQL e contas de armazenamento, e receber recomendações para melhorar a sua segurança, consulte [o serviço Protect Azure SQL e dados no Azure Security Center](security-center-sql-service-recommendations.md) para obter mais informações. 
- Para monitorizar os seus recursos de identidade e acesso, incluindo permissões de MFA e conta, e receber recomendações para melhorar a sua segurança, consulte [a identidade do Monitor e o acesso no Centro de Segurança Azure](security-center-identity-access.md) para obter mais informações. 
- Para monitorizar o acesso just-in-time aos seus recursos, consulte [gerir o acesso à máquina virtual utilizando o acesso just-in-time](security-center-just-in-time.md) para obter mais informações.


Para obter mais informações sobre como aplicar recomendações, leia [Implementing security recommendations in Azure Security Center (Implementar recomendações de segurança no Centro de Segurança do Azure)](security-center-recommendations.md).



![Mosaico de estado de funcionamento da segurança dos recursos](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Veja também
Neste artigo, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md): saiba como configurar definições de segurança no Centro de Segurança do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.