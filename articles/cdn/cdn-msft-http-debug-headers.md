---
title: Depurar cabeçalhos HTTP para a CDN do Azure da Microsoft | Microsoft Docs
description: Cabeçalhos de solicitação de cache de depuração fornece informações adicionais sobre a política de cache aplicada ao ativo solicitado. Esses cabeçalhos são específicos para a CDN do Azure da Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814069"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Depurar o cabeçalho HTTP para a CDN do Azure da Microsoft
O cabeçalho de resposta de `X-Cache`depuração,, fornece detalhes sobre a camada da pilha CDN da qual o conteúdo foi servido. Esse cabeçalho é específico para a CDN do Azure da Microsoft.

### <a name="response-header-format"></a>Formato de cabeçalho de resposta

Cabeçalho | Descrição
-------|------------
X-cache: TCP_HIT | Esse cabeçalho é retornado quando o conteúdo é servido do cache de borda da CDN. 
X-cache: TCP_REMOTE_HIT | Esse cabeçalho é retornado quando o conteúdo é servido do cache regional da CDN (camada de blindagem de origem)
X-cache: TCP_MISS | Esse cabeçalho é retornado quando há um erro de cache e o conteúdo é servido da origem. 


