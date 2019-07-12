---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839744"
---
| Porta não.| Dentro ou para fora | Âmbito de porta| Necessário| Notas |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de dados caixa Blog REST APIs através de HTTP. Se não a ligar para REST APIs, isso redireciona automaticamente para uma IU web local mais 8443. |
| TCP 443 (HTTPS)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de dados caixa Blog REST APIs através de HTTPS. Se não a ligar para REST APIs, isso redireciona automaticamente para uma IU web local mais 8443. |
| TCP 8443 (HTTPS-Alt)|Em|LAN|Sim|Esta é uma porta alternativa para HTTPS e é utilizada ao estabelecer ligação com uma IU web local para gestão de dispositivos. |
| TCP 445 (SMB)|Out/In|LAN|Em alguns casos<br>Consulte as notas|Esta porta é necessária apenas se estiver a ligar através de SMB. |
| TCP 2049 (NFS)|Out/In|LAN|Em alguns casos<br>Consulte as notas|Esta porta é necessária apenas se estiver a ligar através do NFS. |
| TCP 111 (NFS)|Out/In|LAN|Em alguns casos<br>Consulte as notas|Esta porta é utilizada para mapeamento de rpcbind/portas e apenas é necessário se está se conectando através do NFS.  |
