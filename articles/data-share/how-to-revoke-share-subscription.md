---
title: Revogar uma assinatura de compartilhamento na visualização do compartilhamento de dados do Azure
description: Saiba como revogar uma assinatura de compartilhamento de um destinatário usando a visualização do compartilhamento de dados do Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: bd8ef23ead500acb9e403b38fd52a2d980d12bf4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169060"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Como revogar a assinatura de compartilhamento de um consumidor na versão prévia do compartilhamento de dados do Azure

Este artigo explica como revogar uma assinatura de compartilhamento de um ou mais dos seus consumidores usando a visualização do compartilhamento de dados do Azure. Isso impede que um consumidor acione mais instantâneos. Se o consumidor ainda não tiver disparado um instantâneo, ele nunca receberá os dados depois que a assinatura de compartilhamento for revogada. Se eles tiverem disparado um instantâneo anteriormente, os dados mais recentes que eles permanecerão em sua conta.

## <a name="navigate-to-a-sent-data-share"></a>Navegar até um compartilhamento de dados enviado

Na visualização do compartilhamento de dados do Azure, navegue até o compartilhamento enviado e selecione a guia **compartilhar assinaturas** .

![Revogar assinatura de compartilhamento](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Marque as caixas ao lado dos destinatários cujas assinaturas de compartilhamento você gostaria de excluir e, em seguida, clique em **revogar**. O consumidor não receberá mais atualizações de seus dados.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [monitorar seus compartilhamentos de dados](how-to-monitor.md).