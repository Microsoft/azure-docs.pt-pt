---
title: Cache purga em Azure Front Door Standard/Premium (Pré-visualização)
description: Este artigo ajuda-o a entender como limpar cache num Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099814"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Cache purga em Azure Front Door Standard/Premium (Pré-visualização)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Azure Front Door Standard/Premium caches ativos até que o tempo de vida do ativo (TTL) expire. Sempre que um cliente solicita um ativo com TTL caducado, o ambiente Azure Front Door recupera uma nova cópia atualizada do ativo para servir o pedido e, em seguida, armazena a cache renovada.

A melhor prática é garantir que os seus utilizadores obtenham sempre a cópia mais recente dos seus bens. A forma de o fazer é ver versão dos seus ativos para cada atualização e publicá-los como novos URLs. A Azure Front Door Standard/Premium irá imediatamente recuperar os novos ativos para os próximos pedidos do cliente. Por vezes, pode querer expurgar o conteúdo em cache de todos os nós de borda e forçá-los a todos a recuperar novos ativos atualizados. A razão pela qual pretende expurgar o conteúdo em cache é porque fez novas atualizações para a sua aplicação ou pretende atualizar ativos que contenham informações incorretas.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

[Reveja o Caching da Porta frontal Azure](concept-caching.md) para entender como funciona o caching.

## <a name="configure-cache-purge"></a>Configure a purga da cache

1. Vá à página geral do perfil da Porta Frontal Azure com os ativos que pretende purgar e, em seguida, **selecione cache purga**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Screenshot da purga de cache na página geral.":::

1. Selecione o ponto final e o domínio que pretende limpar dos nós de borda. *(Pode selecionar mais de um domínio)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Screenshot da página de purga de cache.":::

1. Para limpar todos os ativos, **selecione Purgar todos os ativos para os domínios selecionados**. Caso contrário, em **Caminhos,** entre no caminho de cada ativo que pretende purgar.

Estes formatos são suportados nas listas de caminhos para purgar:

* **Purga de caminho único**: Purgue os ativos individuais especificando o caminho completo do ativo (sem o protocolo e domínio), com a extensão do ficheiro, por exemplo, /imagens/strasbourg.png.
* **Purga do domínio raiz**: Purgue a raiz do ponto final com "/*" no caminho.

As purgas de cache no Azure Front Door Standard/Preium são insensíveis a caso. Além disso, são agnósticos de cadeia de consulta, o que significa que purgar um URL irá purgar todas as variações de cadeias de consulta do mesmo. 

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)
