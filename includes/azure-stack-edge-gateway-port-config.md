---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89085275"
---
| Porto não.| Dentro ou fora | Âmbito portuário| Necessário|   Notas |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Saída|WAN |Não|A porta de saída é utilizada para o acesso à Internet para recuperar atualizações. <br>O representante da web de saída é configurável pelo utilizador. |
| TCP 443 (HTTPS)|Saída|WAN|Sim|A porta de saída é utilizada para aceder a dados na nuvem.<br>O representante da web de saída é configurável pelo utilizador.|
| UDP 123 (NTP)|Saída|WAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor NTP baseado na Internet.  |   
| UDP 53 (DNS)|Saída|WAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor DNS baseado na Internet.<br>Recomendamos a utilização de um servidor DNS local. |
| TCP 5985 (winrm)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta é necessária para ligar ao dispositivo através do powershell remoto em HTTP.  |
| UDP 67 (DHCP)|Saída|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor DHCP local.  |
| TCP 80 (HTTP)|Fora/Dentro|LAN|Sim|Esta porta é a porta de entrada para uI local no dispositivo para gestão local. <br>O acesso à UI local sobre HTTP irá redirecionar automaticamente para HTTPS.  |
| TCP 443 (HTTPS)|Fora/Dentro|LAN|Sim|Esta porta é a porta de entrada para uI local no dispositivo para gestão local. Esta porta também é usada para ligar o Azure Resource Manager às APIs locais do dispositivo, para ligar o armazenamento blob através de REST APIs, e ao serviço de fichas de segurança (STS) para autenticar através de acesso e fichas de atualização.|
| TCP 445 (SMB)|Em|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Em|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |


