---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184452"
---
## <a name="preparing-for-updates"></a>Preparação para atualizações
Terá de realizar os seguintes passos antes de digitalizar e aplicar a atualização:

1. Tire uma foto em nuvem dos dados do dispositivo.
2. Certifique-se de que os IPs fixos do controlador são repreensíveis e podem ligar-se à Internet. Estes IPs fixos serão utilizados para atender atualizações ao seu dispositivo. Pode testar isto executando o seguinte cmdlet em cada controlador a partir da interface Windows PowerShell do dispositivo:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Saída de amostra para ligação de teste quando os IPs fixos podem ligar-se à Internet**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Depois de ter concluído com sucesso estas pré-verificações manuais, pode proceder à verificação e instalação das atualizações.

