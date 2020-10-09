---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76545223"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configure a sessão de âncora espacial em nuvem

Vamos configurar a sessão de âncora espacial em nuvem a seguir. Na primeira linha, definimos o fornecedor de sensores na sessão. A partir de agora, qualquer âncora que criarmos durante a sessão será associada a um conjunto de leituras de sensores. Em seguida, instantaneamos um critério de localização de quase dispositivo e inicializamo-lo para corresponder aos requisitos de aplicação. Finalmente, instruímos a sessão a utilizar dados de sensores ao localizar âncoras, criando um observador a partir dos nossos critérios de quase-dispositivo.