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
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181810"
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
| Domínios personalizados | Sim | Sim |
| Descarga SSL | Sim | Sim |
| Colocação em cache |  Sim  | Sim |
| Compressão | Sim | Sim   |
| Equilíbrio global da carga | Sim  | Sim |
| Encaminhamento da camada 7 | Sim | Sim |
| Reescrever URL | Sim | Sim |
| Motor de Regras | Sim | Sim |
| Origem Privada (Ligação Privada) | Não | Sim |
| WAF | Regras personalizadas apenas | Sim |
| Proteção de Bots | Não | Sim |
| Métricas e diagnósticos melhorados | Sim | Sim |
| Relatório de tráfego | Sim | Sim |
| Relatório de Segurança | Não | Sim | 

## <a name="next-steps"></a>Próximos passos

Saiba como [criar uma Porta da Frente](create-front-door-portal.md)
