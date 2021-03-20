---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86218297"
---
| Porto não.| Dentro ou fora | Âmbito portuário| Necessário|   Notas |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Saída|WAN |No|A porta de saída é utilizada para o acesso à Internet para recuperar atualizações. <br>O representante da web de saída é configurável pelo utilizador. |
| TCP 443 (HTTPS)|Saída|WAN|Yes|A porta de saída é utilizada para aceder a dados na nuvem.<br>O representante da web de saída é configurável pelo utilizador.|
| UDP 123 (NTP)|Saída|WAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor NTP baseado na Internet.  |   
| UDP 53 (DNS)|Saída|WAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor DNS baseado na Internet.<br>Recomendamos a utilização de um servidor DNS local. |
| TCP 5985 (winrm)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta é necessária para ligar ao dispositivo através do powershell remoto em HTTP.  |
| UDP 67 (DHCP)|Saída|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor DHCP local.  |
| TCP 80 (HTTP)|Fora/Dentro|LAN|Yes|Esta porta é a porta de entrada para uI local no dispositivo para gestão local. <br>O acesso à UI local sobre HTTP irá redirecionar automaticamente para HTTPS.  |
| TCP 443 (HTTPS)|Fora/Dentro|LAN|Yes|Esta porta é a porta de entrada para uI local no dispositivo para gestão local. |
| TCP 445 (SMB)|Em|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Em|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |
