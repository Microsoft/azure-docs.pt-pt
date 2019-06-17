---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161001"
---
| Porta não.| Dentro ou para fora | Âmbito de porta| Necessário|   Notas |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|horizontalmente|WAN |Não|Porta de saída é utilizada para acesso à internet para obter atualizações. <br>O proxy da web de saída é configurável de utilizador. |
| TCP 443 (HTTPS)|horizontalmente|WAN|Sim|Porta de saída é utilizada para aceder aos dados na cloud.<br>O proxy da web de saída é configurável de utilizador.|
| UDP 123 (NTP)|horizontalmente|WAN|Em alguns casos<br>Consulte as notas|Esta porta é necessária apenas se estiver a utilizar um servidor NTP baseado na internet.  |   
| UDP 53 (DNS)|horizontalmente|WAN|Em alguns casos<br>Consulte as notas|Esta porta é necessária apenas se estiver a utilizar um servidor DNS baseado na internet.<br>Recomendamos que utilize um servidor DNS local. |
| TCP 5985 (WinRM)|Out/In|LAN|Em alguns casos<br>Consulte as notas|Esta porta é necessário para ligar ao dispositivo através do PowerShell remoto através de HTTP.  |
| UDP 67 (DHCP)|horizontalmente|LAN|Em alguns casos<br>Consulte as notas|Esta porta é necessária apenas se estiver a utilizar um servidor DHCP local.  |
| TCP 80 (HTTP)|Out/In|LAN|Sim|Esta porta é a porta de entrada para a interface do Usuário local no dispositivo para gestão local. <br>Acessar a interface do Usuário local através de HTTP será automaticamente redirecionada para HTTPS.  |
| TCP 443 (HTTPS)|Out/In|LAN|Sim|Esta porta é a porta de entrada para a interface do Usuário local no dispositivo para gestão local. |
| TCP 445 (SMB)|Em|LAN|Em alguns casos<br>Consulte as notas|Esta porta é necessária apenas se estiver a ligar através de SMB. |
| TCP 2049 (NFS)|Em|LAN|Em alguns casos<br>Consulte as notas|Esta porta é necessária apenas se estiver a ligar através do NFS. |
