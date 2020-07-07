---
title: Revogar uma subscrição de ações no Azure Data Share
description: Saiba como revogar uma subscrição de ações de um destinatário utilizando a Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73476385"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Como revogar a subscrição de uma participação do consumidor na Azure Data Share

Este artigo explica como revogar uma subscrição de ações de um ou mais dos seus consumidores usando a Azure Data Share. Isto impede que um consumidor desencadeie mais instantâneos. Se o consumidor ainda não tiver ativado uma imagem instantânea, nunca receberá os dados uma vez revogada a subscrição da ação. Se já terem ativado uma fotografia, os dados mais recentes que têm permanecerão na sua conta.

## <a name="navigate-to-a-sent-data-share"></a>Navegue para uma partilha de dados enviada

Na Azure Data Share, navegue para a sua partilha enviada e selecione o **separador Subscrições de Partilha.**

![Revogar a subscrição de ações](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Verifique as caixas ao lado dos destinatários cujas subscrições de partilha pretende eliminar e, em seguida, clique em **Revogar**. O consumidor deixará de receber atualizações dos seus dados.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como monitorizar as [suas partilhas de dados.](how-to-monitor.md)