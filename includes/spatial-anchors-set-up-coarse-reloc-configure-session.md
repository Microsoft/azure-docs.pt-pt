---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999764"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configure a sessão de âncora espacial em nuvem

Vamos configurar a sessão de âncora espacial em nuvem a seguir. Na primeira linha, definimos o fornecedor de sensores na sessão. A partir de agora, qualquer âncora que criarmos durante a sessão será associada a um conjunto de leituras de sensores. Em seguida, instantaneamos um critério de localização de quase dispositivo e inicializamo-lo para corresponder aos requisitos de aplicação. Finalmente, instruímos a sessão a utilizar dados de sensores ao localizar âncoras, criando um observador a partir dos nossos critérios de quase-dispositivo.