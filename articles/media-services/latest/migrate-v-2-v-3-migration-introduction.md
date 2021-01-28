---
title: Migrar dos Serviços de Comunicação v2 para a introdução v3
description: Este artigo é uma introdução à migração dos Serviços de Comunicação Social v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 3514a7c809e939ea2c45afa5ab60539232b8781f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953296"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Migrar dos Serviços de Comunicação v2 para a introdução v3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

O guia de migração dos Media Services ajuda-o a migrar dos Serviços de Media V2 APIs para APIs V3 com base numa migração que tira partido das novas funcionalidades e funções que estão agora disponíveis. Deve usar o seu melhor julgamento e determinar qual o melhor que se adequa ao seu cenário.

## <a name="how-to-use-this-guide"></a>Como utilizar este guia

### <a name="navigating"></a>Navegando

Ao longo do guia, verá o seguinte gráfico.

![passos de migração](./media/migration-guide/steps.svg)<br/>

O passo em que está será indicado por uma mudança de cor no número do passo, assim:

![etapas de migração 2](./media/migration-guide/steps-2.svg)<br/>

No final de cada página, você verá links para o resto dos documentos de migração que você pode ler sob o título **seguinte.**

### <a name="guidance"></a>Orientação

As orientações aqui fornecidas são *gerais.* Inclui conteúdo para melhorar a sua consciência do que está agora disponível na V3, bem como o que mudou nos fluxos de trabalho dos Media Services.

Para uma orientação mais detalhada, incluindo screenshots e gráficos conceptuais, existem ligações a conceitos, tutoriais, quickstarts, amostras e referências API em cada tópico baseado em cenários. Também listamos amostras que o ajudam a comparar a API V2 com a API V3.

## <a name="migration-guidance-overview"></a>Visão geral da orientação da migração

Há quatro passos gerais a seguir durante a sua migração:

## <a name="step-1-benefits"></a>Passo 1 Benefícios

<hr color="#5ea0ef" size="10">

[Compreender os benefícios](migrate-v-2-v-3-migration-benefits.md) da migração para a API V3 dos Serviços de Comunicação Social.

## <a name="step-2-differences"></a>Passo 2 Diferenças

<hr color="#5ea0ef" size="10">

Compreenda as diferenças entre a API dos Serviços de Comunicação Social V2 e a API V3.

- [Acesso api](migrate-v-2-v-3-differences-api-access.md)
- [Lacunas de recursos](migrate-v-2-v-3-differences-feature-gaps.md)
- [Terminologia e alterações de entidades](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Configuração do Passo 3 SDK

<hr color="#5ea0ef" size="10">

Compreenda as diferenças de SDK e [crie-se para migrar para a V3 REST API ou para o cliente SDK.](migrate-v-2-v-3-migration-setup.md)

## <a name="step-4-scenario-based-guidance"></a>Passo 4 Orientação baseada em cenário

<hr color="#5ea0ef" size="10">

A sua aplicação de Media Services V2 pode ser única. Por isso, fornecemos orientações baseadas em cenários com base na forma como *pode ter* usado os serviços de comunicação social no passado e os passos para cada característica do serviço, tais como:

- [Encoding](migrate-v-2-v-3-migration-scenario-based-encoding.md) (Codificação)
- [Transmissão em direto](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Empacotamento e entrega](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Proteção de conteúdos](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Unidades reservadas para meios de comunicação (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
