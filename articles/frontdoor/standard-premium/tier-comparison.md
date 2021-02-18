---
title: Comparação Azure Front Door Standard/Premium SKU
description: Este artigo fornece uma visão geral do Azure Front Door Standard e Premium SKU e apresenta diferenças entre eles.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 0f5ecef1716a503b7d27d0f5cdde15a4560c0e61
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100250"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Visão geral do Azure Front Door Standard/Premium SKU (Pré-visualização)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

A azure Front Door é oferecida para 3 SKUs diferentes, [Azure Front Door,](../front-door-overview.md)Azure Front Door Standard (Preview) e Azure Front Door Premium (Preview). Azure Front Door Standard/Premium SKUs combina capacidades de Azure Front Door, Azure CDN Standard da Microsoft, Azure WAF em uma única plataforma segura de CDN em nuvem com proteção inteligente de ameaças.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [**termos de utilização suplementares para pré-visualizações do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* **Azure Front Door Standard SKU** é:

    * Entrega de conteúdo otimizada
    * Oferecendo aceleração de conteúdo estática e dinâmica
    * Equilíbrio global da carga
    * Descarga SSL
    * Gestão de domínios e certificados
    * Análise de tráfego melhorada 
    * Capacidades básicas de segurança

* **AZure Front Door Premium SKU** baseia-se nas capacidades da Standard SKU e adiciona:

    * Extensas capacidades de segurança em toda a WAF
    * Proteção BOT
    * Suporte private Link
    * Integração com a Microsoft Threat Intelligence e análise de segurança. 

![Diagrama mostrando uma comparação entre SKUs front door.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Comparação de funcionalidades

| Funcionalidade |      Standard      |  Premium |
|----------|:-------------:|------:|
| Domínios personalizados | Yes | Yes |
| Descarga SSL | Yes | Yes |
| Colocação em cache |  Yes  | Yes |
| Compressão | Yes | Yes   |
| Equilíbrio global da carga | Yes  | Yes |
| Encaminhamento da camada 7 | Yes | Yes |
| Reescrever URL | Yes | Yes |
| Motor de Regras | Yes | Yes |
| Origem Privada (Ligação Privada) | No | Yes |
| WAF | No | Yes |
| Proteção de Bots | No | Yes |
| Métricas e diagnósticos melhorados | Yes | Yes |
| Relatórios de tráfego | Yes | Yes |
| Relatório de Segurança | No | Sim | 

## <a name="next-steps"></a>Próximos passos

Saiba como [criar uma Porta da Frente](create-front-door-portal.md)
