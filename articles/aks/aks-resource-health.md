---
title: Verifique se há eventos de saúde de recursos com impacto no seu cluster AKS (Pré-visualização)
description: Verifique o estado de saúde do seu cluster AKS utilizando a Azure Resource Health.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070661"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Verifique se há eventos de saúde de recursos com impacto no seu cluster AKS (Pré-visualização)


Ao executar as cargas de trabalho do seu contentor em AKS, pretende garantir que pode resolver problemas e corrigir problemas assim que estes surgirem para minimizar o impacto na disponibilidade das suas cargas de trabalho. [A Azure Resource Health](../service-health/resource-health-overview.md) dá-lhe visibilidade em vários eventos de saúde que podem fazer com que o seu cluster AKS esteja indisponível.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Saúde de Recursos Abertos

### <a name="to-access-resource-health-for-your-aks-cluster"></a>Para aceder à Saúde dos Recursos para o seu cluster AKS:

- Navegue para o seu cluster AKS no [portal Azure](https://portal.azure.com).
- Selecione **Resource Health** na navegação à esquerda.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>Para aceder à Saúde dos Recursos para todos os clusters da sua subscrição:

- Procure a **Saúde do Serviço** no [portal Azure](https://portal.azure.com) e navegue até ele.
- Selecione **a saúde do recurso** na navegação à esquerda.
- Selecione a sua subscrição e desenrânea o tipo de recurso para O Serviço Azure Kubernetes (AKS).

![A screenshot mostra a saúde do recurso para os seus clusters A K S.](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Verifique o estado de saúde

A Azure Resource Health ajuda-o a diagnosticar e a obter apoio para problemas de serviço que afetam os seus recursos Azure. Resource Health reporta sobre a saúde atual e passada dos seus recursos e ajuda-o a determinar se o problema é causado por uma ação iniciada pelo utilizador ou por um evento de plataforma.

A Resource Health recebe sinais para o seu cluster gerido para determinar o estado de saúde do cluster. Examina o estado de saúde do seu cluster AKS e reporta as ações necessárias para cada sinal de saúde. Estes sinais vão desde problemas de resolução automática, atualizações planeadas, eventos de saúde não planeados e indisponibilidade causada por ações iniciadas pelo utilizador. Estes sinais são classificados utilizando o estado de saúde do Recurso Azure: *Disponível,* *Indisponível,* *Desconhecido* e *Degradado*.

- **Disponível**: Quando não existem questões conhecidas que afetem a saúde do seu cluster, a Resource Health informa o seu cluster como *Disponível*.

- **Indisponível**: Quando existe um evento de plataforma ou não-plataforma que afete a saúde do seu cluster, a Resource Health informa o seu cluster como *Indisponível*.

- **Desconhecido**: Quando há uma perda temporária de ligação às métricas de saúde do seu cluster, a Resource Health informa o seu cluster como *Desconhecido*.

- **Degradado**: Quando há um problema de saúde que requer a sua ação, a Resource Health reporta o seu cluster como *degradado*.

Para mais detalhes sobre o que cada estado de saúde indica, visite a [visão geral da Saúde dos Recursos](../service-health/resource-health-overview.md#health-status).

### <a name="view-historical-data"></a>Ver dados históricos

Pode ainda ver os últimos 30 dias de informação histórica sobre saúde em recursos na secção história da **Saúde.**

## <a name="next-steps"></a>Passos seguintes

Verifique o seu cluster para resolver problemas com o cluster utilizando [diagnósticos AKS](./concepts-diagnostics.md).