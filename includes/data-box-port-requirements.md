---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839744"
---
| Porto nº.| Dentro ou fora | Âmbito do porto| Necessário| Notas |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Entrada|LAN|Sim|Esta porta é utilizada para se ligar ao armazenamento de data Box Blog REST APIs em HTTP. Se não ligar a REST APIs, isto redireciona automaticamente para a Web UI local em 8443. |
| TCP 443 (HTTPS)|Entrada|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de caixas de dados REST APIs em HTTPS. Se não ligar a REST APIs, isto redireciona automaticamente para a Web UI local em 8443. |
| TCP 8443 (HTTPS-Alt)|Entrada|LAN|Sim|Esta é uma porta alternativa para HTTPS e é usada na ligação à UI web local para gestão de dispositivos. |
| TCP 445 (SMB)|Out/In|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Out/In|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |
| TCP 111 (NFS)|Out/In|LAN|Em alguns casos<br>Ver notas|Esta porta é utilizada para mapeamento rpcbind/porta e só é necessária se estiver a ligar via NFS.  |
