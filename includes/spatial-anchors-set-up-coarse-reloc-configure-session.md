---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76545223"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configure a sessão de âncora espacial da nuvem

Trataremos de configurar a sessão de âncora espacial em nuvem a seguir. Na primeira linha, definimos o fornecedor de sensores na sessão. A partir de agora, qualquer âncora que criarmos durante a sessão estará associada a um conjunto de leituras de sensores. Em seguida, instantaneamente um quase dispositivo localizar critérios e inicializá-lo para corresponder aos requisitos de aplicação. Finalmente, instruímos a sessão a utilizar dados de sensores ao localizar âncoras, criando um observador a partir dos nossos critérios de quase-dispositivo.