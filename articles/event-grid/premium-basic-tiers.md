---
title: Grelha de Eventos Azure Premium e níveis básicos
description: Este artigo descreve a diferença entre o Azure Event Grid Premium e os níveis Básicos e quando utilizar cada um
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300719"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid premium e níveis básicos
A Azure Event Grid tem dois níveis: **Premium** e **Basic**. O nível básico utiliza o consumo, ou o preço do pagamento. Dá-lhe todas as ferramentas básicas de pub/sub que precisa para usar a Grelha de Eventos para modelos de programação orientados para eventos. O nível premium dá um passo mais além com funcionalidades de segurança direcionadas para a empresa. O nível premium está em **pré-visualização** inicial com muitas das suas características ainda em desenvolvimento.

## <a name="overview"></a>Descrição geral
Todos os tópicos e domínios personalizados em Event Grid pertencem a um nível básico ou a um nível premium. Começando pela versão `2020-04-01-preview` API, pode escolher o nível desejado como parte da criação de um tópico ou domínio. O valor padrão é de nível básico. Os tópicos e domínios criados usando versões API mais antigas padrão para o nível básico. Para alterar o nível de preços dos seus tópicos e domínios, consulte como atualizar o [nível para tópicos e domínios](update-tier.md).

## <a name="capabilities-and-features"></a>Capacidades e funcionalidades

O quadro seguinte descreve diferenças entre os níveis:

|       &nbsp;                                           | Básica           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Regras de firewall IP para ingresso                          | Pré-visualização  | Pré-visualização |
| Etiquetas de serviço para saída                                | Pré-visualização  | Pré-visualização |
| Integração vNet de ponto final privado na entrada          | Não disponível   | Pré-visualização |

## <a name="availability"></a>Disponibilidade
Durante a pré-visualização inicial, os tópicos e domínios de nível premium com integração de pontos finais privados estão disponíveis nas seguintes regiões:

- EUA Leste
- EUA Oeste 2
- EUA Centro-Sul

## <a name="pricing-and-quotas"></a>Preços e quotas
Consulte os preços da Grelha de [Eventos](https://azure.microsoft.com/pricing/details/event-grid/) para obter os detalhes de preços da utilização do nível básico. O preço de nível Premium ainda não foi anunciado e é gratuito até que os preços estão disponíveis.

As quotas existentes sobre a contagem de tópicos e domínios aplicam-se tanto aos recursos premium como aos recursos de nível básico até que seja anunciado o preço de nível premium.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- Para atualizar de nível básico para nível premium, consulte o artigo do nível de [preços da Atualização.](update-tier.md) 
- Pode configurar a firewall IP para o seu recurso Da Rede de Eventos para restringir o acesso através da internet pública a partir de apenas um conjunto selecionado de endereços IP ou intervalos ip Address. Para obter instruções passo a passo, consulte [a firewall Configurar](configure-firewall.md).
- Pode configurar pontos finais privados para restringir o acesso a partir de redes virtuais selecionadas. Para obter instruções passo a passo, consulte [os pontos finais privados da Configuração](configure-private-endpoints.md).