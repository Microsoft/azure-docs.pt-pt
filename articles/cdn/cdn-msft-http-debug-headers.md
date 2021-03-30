---
title: Cabeçalhos Debug HTTP para Azure CDN da Microsoft | Microsoft Docs
description: Os cabeçalhos de pedido de cache de depurado fornecem informações adicionais sobre a política de cache aplicada ao ativo solicitado. Estes cabeçalhos são específicos do Azure CDN da Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "81260419"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Cabeçalho Debug HTTP para Azure CDN da Microsoft
O cabeçalho de resposta depurado, `X-Cache` fornece detalhes sobre a camada da pilha CDN de que o conteúdo foi servido. Este cabeçalho é específico do Azure CDN da Microsoft.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

Cabeçalho | Description
-------|------------
X-Cache: TCP_HIT | Este cabeçalho é devolvido quando o conteúdo é servido a partir da cache de borda CDN. 
X-Cache: TCP_REMOTE_HIT | Este cabeçalho é devolvido quando o conteúdo é servido a partir da cache regional cdN (camada de escudo de origem)
X-Cache: TCP_MISS | Este cabeçalho é devolvido quando há uma falha de cache, e o conteúdo é servido a partir da Origem. 


