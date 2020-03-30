---
title: Revogar uma subscrição de ações na Azure Data Share
description: Saiba como revogar uma subscrição de ações de um destinatário usando a Partilha de Dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476385"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Como revogar a subscrição de ações de um consumidor na Azure Data Share

Este artigo explica como revogar uma subscrição de ações de um ou mais dos seus consumidores usando a Azure Data Share. Isto impede que um consumidor desencadeie mais instantâneos. Se o consumidor ainda não tiver desencadeado uma imagem instantânea, nunca receberá os dados uma vez revogada a subscrição de ações. Se já tiverem desencadeado um instantâneo, os dados mais recentes que têm permanecerão na sua conta.

## <a name="navigate-to-a-sent-data-share"></a>Navegue para uma partilha de dados enviada

No Azure Data Share, navegue para a sua parte enviada e selecione o separador **Subscrições de Partilha.**

![Revogar subscrição de ações](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Verifique as caixas ao lado dos destinatários cujas subscrições de ações gostaria de eliminar e, em seguida, clique em **Revogar**. O consumidor deixará de receber atualizações dos seus dados.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [monitorizar as suas partilhas](how-to-monitor.md)de dados.