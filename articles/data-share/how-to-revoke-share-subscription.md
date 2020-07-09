---
title: Revogar uma subscrição de ações no Azure Data Share
description: Saiba como revogar uma subscrição de ações de um destinatário utilizando a Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: c1c049d467cdf07962719f0dc257acbf47631aaf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119689"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Como revogar a subscrição de uma participação do consumidor na Azure Data Share

Este artigo explica como revogar uma subscrição de ações de um ou mais dos seus consumidores usando a Azure Data Share. Isto impede que um consumidor desencadeie mais instantâneos. Se o consumidor ainda não tiver ativado uma imagem instantânea, nunca receberá os dados uma vez revogada a subscrição da ação. Se já terem ativado uma fotografia, os dados mais recentes que têm permanecerão na sua conta.

## <a name="navigate-to-a-sent-data-share"></a>Navegue para uma partilha de dados enviada

Na Azure Data Share, navegue para a sua partilha enviada e selecione o **separador Subscrições de Partilha.**

![Revogar a subscrição de ações](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Verifique as caixas ao lado dos destinatários cujas subscrições de partilha pretende eliminar e, em seguida, clique em **Revogar**. O consumidor deixará de receber atualizações dos seus dados.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como monitorizar as [suas partilhas de dados.](how-to-monitor.md)