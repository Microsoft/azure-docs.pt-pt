---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184699"
---
| Porto nº.| Dentro ou fora | Âmbito do porto| Necessário|   Notas |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Saída|WAN |No|A porta de saída é utilizada para o acesso à Internet para recuperar atualizações. <br>O proxy web de saída é configurável pelo utilizador. |
| TCP 443 (HTTPS)|Saída|WAN|Sim|A porta de saída é utilizada para aceder a dados na nuvem.<br>O proxy web de saída é configurável pelo utilizador.|
| UDP 123 (NTP)|Saída|WAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor NTP baseado na Internet.  |   
| UDP 53 (DNS)|Saída|WAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor DNS baseado na Internet.<br>Recomendamos a utilização de um servidor DNS local. |
| TCP 5985 (WinRM)|Out/In|LAN|Em alguns casos<br>Ver notas|Esta porta é necessária para se ligar ao dispositivo através da PowerShell remota sobre HTTP.  |
| UDP 67 (DHCP)|Saída|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a utilizar um servidor DHCP local.  |
| TCP 80 (HTTP)|Out/In|LAN|Sim|Este porto é o porto de entrada para UI local no dispositivo para gestão local. <br>Aceder ao UI local em HTTP redireciona automaticamente para HTTPS.  |
| TCP 443 (HTTPS)|Out/In|LAN|Sim|Este porto é o porto de entrada para UI local no dispositivo para gestão local. |
| TCP 445 (SMB)|Entrada|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Entrada|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |
