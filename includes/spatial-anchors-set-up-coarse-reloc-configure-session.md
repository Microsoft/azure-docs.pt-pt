---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545223"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurar a sessão de âncora espacial da nuvem

Cuidaremos da configuração da sessão de âncora espacial da nuvem em seguida. Na primeira linha, definimos o provedor de sensor na sessão. De agora em diante, qualquer âncora que criamos durante a sessão será associada a um conjunto de leituras de sensor. Em seguida, criamos uma instância de critérios de localização de dispositivo Near e o inicializamos para corresponder aos requisitos do aplicativo. Por fim, instrumos a sessão a usar dados de sensor ao localizar âncoras criando um observador de nossos critérios de dispositivo Near.