---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093140"
---
## <a name="configure-the-cloud-anchor-session"></a>Configurar a sessão de âncora de nuvem

Cuidaremos da configuração da sessão de âncora de nuvem em seguida. Na primeira linha, definimos o provedor de sensor na sessão. De agora em diante, qualquer âncora que criamos durante a sessão será associada a um conjunto de leituras de sensor. Em seguida, criamos uma instância de critérios de localização de dispositivo Near e o inicializamos para corresponder aos requisitos do aplicativo. Por fim, instrumos a sessão a usar dados de sensor ao localizar âncoras criando um observador de nossos critérios de dispositivo Near.