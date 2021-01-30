---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214377"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Configure o fornecedor de impressões digitais do sensor

Vamos começar por criar e configurar um fornecedor de impressões digitais sensorial. O fornecedor de impressões digitais do sensor cuidará de ler os sensores específicos da plataforma no seu dispositivo e converter as suas leituras numa representação comum consumida pela sessão de âncora espacial em nuvem.

> [!IMPORTANT]
> [Certifique-se de verificar aqui](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) se os sensores que está a ativar estão disponíveis na sua plataforma.