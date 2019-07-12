---
title: Alertas de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Este tópico explica o que são alertas de segurança e os diferentes tipos disponíveis no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571676"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança no Centro de segurança do Azure

Este artigo apresenta os diferentes tipos de alertas de segurança disponíveis no Centro de segurança do Azure (ASC). Há uma variedade de alertas para muitos tipos de recursos diferente. ASC gera alertas para ambos os recursos implementados no Azure e implementar recursos no local e híbrido ambientes na cloud. 

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?

Os alertas são notificações de centro de segurança gera quando detetar ameaças nos seus recursos. Ele dá prioridade e apresenta uma lista de alertas, juntamente com as informações necessárias para investigar rapidamente o problema. Centro de segurança também fornece recomendações sobre como pode remediar um ataque.

## <a name="how-does-security-center-detect-threats"></a>Como é que o Centro de segurança detetar ameaças?

Para detetar ameaças reais e reduzir os falsos positivos, o Centro de segurança recolhe, analisa e integra-se os dados de registo de recursos do Azure, a rede e soluções de parceiros ligadas, como firewall e soluções de proteção de ponto final. Centro de segurança analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças.

ASC monitoriza os recursos, se implementada no Azure ou implementado em outros locais e híbridas ambientes na cloud. Para obter mais informações sobre a detetar e responder a ameaças, consulte o artigo [como o Centro de segurança Deteta e responde às ameaças](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Tipos de alertas de segurança

Os tópicos seguintes guiá-lo através de alertas do ASC diferentes, de acordo com os tipos de recursos:

* [Alertas de VMs de IaaS e servidores](security-center-alerts-iaas.md)
* [Alertas de computação nativa](security-center-alerts-compute.md)
* [Alertas de serviços de dados](security-center-alerts-data-services.md)

Os tópicos seguintes explicam como o Centro de segurança utiliza a telemetria de diferente que recolhe a integração com a infraestrutura do Azure para aplicar as camadas de proteção adicional para recursos implementados no Azure:

* [Alertas de camada de serviço](security-center-alerts-service-layer.md)
* [Integração com produtos de segurança do Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Quais são os incidentes de alertas?

Um incidente de segurança é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. Centro de segurança utiliza fusion correlacionar alertas diferentes e sinais de baixa fidelidade em incidentes de segurança.

Utilizar a incidentes, o Centro de segurança fornece uma vista única de uma campanha de ataque e todos os alertas relacionados. Esta vista permite-lhe compreender rapidamente quais ações realizadas pelo atacante e que recursos foram afetados. Para obter mais informações, consulte [correlação de alertas inteligentes de Cloud](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Comece com alertas

Consulte os tópicos seguintes, deve saber mais sobre os recursos monitorizados pelo ASC e para obter diretrizes sobre como responder a alertas apresentados pelo ASC.

* Para ver quais plataformas e funcionalidades que estão protegidas pelo ASC, consulte [plataformas e funcionalidades suportadas pelo centro de segurança do Azure](security-center-os-coverage.md).  
* Para compreender quais são os incidentes de segurança e como o ASC responde aos mesmos, consulte [como lidar com incidentes de segurança no Centro de segurança do Azure](security-center-incident.md). 
* Para saber como gerir alertas recebidos, veja [gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md).
* Para obter informações de sessão no como validar se o Centro de segurança está configurado corretamente e estimular um alerta de teste, consulte [validação de alertas no Centro de segurança do Azure](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Atualizar para Standard para as deteções avançadas

Para configurar as deteções avançadas, atualize para o Centro de Segurança do Azure Standard. 

1. No menu do Centro de segurança, selecione **política de segurança**.
2. Para as subscrições que pretende mover para o escalão Standard, clique em **editar as definições de**. 
3. Na página Definições, selecione **escalão de preço**. 
   Está disponível uma avaliação gratuita durante um mês. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu o que são alertas de segurança e os diferentes tipos de alertas disponíveis no Centro de segurança. Para obter mais informações, consulte os seguintes tópicos:

* [Guia de operações e planeamento do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [FAQ do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-faq): Encontre as perguntas mais frequentes acerca de como utilizar o serviço.

