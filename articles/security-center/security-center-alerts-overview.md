---
title: Alertas de segurança na central de segurança do Azure | Microsoft Docs
description: Este tópico explica o que são os alertas de segurança e os diferentes tipos disponíveis na central de segurança do Azure.
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
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013286"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança no Centro de Segurança do Azure

Na central de segurança do Azure, há uma variedade de alertas para vários tipos de recursos diferentes. A central de segurança gera alertas para recursos implantados no Azure e também para recursos implantados em ambientes de nuvem híbrida e locais. 

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?

Alertas são as notificações que a central de segurança gera quando detecta ameaças em seus recursos. Ele prioriza e lista os alertas, juntamente com as informações necessárias para que você investigue rapidamente o problema. A central de segurança também fornece recomendações sobre como você pode corrigir um ataque.

## <a name="how-does-security-center-detect-threats"></a>Como a central de segurança detecta ameaças?

Para detectar ameaças reais e reduzir falsos positivos, a central de segurança coleta, analisa e integra dados de log de seus recursos do Azure e da rede. Ele também funciona com soluções de parceiros conectadas, como firewall e soluções de proteção de ponto de extremidade. A central de segurança analisa essas informações, geralmente correlacionando informações de várias fontes, para identificar ameaças.

A central de segurança monitora os recursos em qualquer ambiente que eles possam estar. Para saber mais sobre como detectar e responder a ameaças, consulte [como a central de segurança detecta e responde a ameaças](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Tipos de alertas de segurança

Os tópicos a seguir orientam você pelos diferentes alertas, de acordo com os tipos de recursos:

* [Alertas de VMs e servidores IaaS](security-center-alerts-iaas.md)
* [Alertas de computação nativos](security-center-alerts-compute.md)
* [Alertas de serviços de dados](security-center-alerts-data-services.md)

Os tópicos a seguir explicam como a central de segurança usa a telemetria diferente que ela coleta da integração com a infraestrutura do Azure, para aplicar camadas de proteção adicionais para recursos implantados no Azure:

* [Alertas da camada de serviço](security-center-alerts-service-layer.md)
* [Integração com os produtos de segurança do Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>O que são incidentes de segurança?

Um incidente de segurança é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. A central de segurança usa a [correlação de alertas inteligentes na nuvem](security-center-alerts-cloud-smart.md) para correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

Usando incidentes, a central de segurança fornece uma única exibição de uma campanha de ataque e todos os alertas relacionados. Essa exibição permite que você entenda rapidamente as ações tomadas pelo invasor e quais recursos foram afetados. Para obter mais informações, consulte [correlação de alertas inteligentes na nuvem](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Introdução aos alertas

Aqui estão artigos que ajudam você a entender mais sobre os recursos monitorados pela central de segurança e fornecem diretrizes sobre como responder aos alertas apresentados.

* [Plataformas e funcionalidades suportadas pelo Centro de Segurança do Azure](security-center-os-coverage.md)  
* [Como lidar com incidentes de segurança na central de segurança do Azure](security-center-incident.md) 
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alerts Validation in Azure Security Center](security-center-alert-validation.md) (Validação de Alertas no Centro de Segurança do Azure)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Atualizar para Standard para detecções avançadas

Para configurar as deteções avançadas, atualize para o Centro de Segurança do Azure Standard. 

1. No menu **central de segurança** , selecione **política de segurança**.
2. Para as assinaturas que você deseja mover para a camada Standard, selecione **Editar configurações**. 
3. Na página configurações, selecione **tipo de preço**. 
   Uma avaliação gratuita está disponível por um mês. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre os diferentes tipos de alertas disponíveis na central de segurança. Para obter mais informações, consulte:

* [Guia de operações e planeamento do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [FAQ do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)

