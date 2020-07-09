---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67839744"
---
| Porto não.| Dentro ou fora | Âmbito portuário| Necessário| Notas |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de blogs de dados REST APIs em HTTP. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 443 (HTTPS)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de blogs de dados REST APIs sobre HTTPS. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 8443 (HTTPS-Alt)|Em|LAN|Sim|Esta é uma porta alternativa para HTTPS e é usada ao ligar-se à UI web local para a gestão de dispositivos. |
| TCP 445 (SMB)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |
| TCP 111 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta é utilizada para mapeamento rpcbind/porta e só é necessária se estiver a ligar via NFS.  |
