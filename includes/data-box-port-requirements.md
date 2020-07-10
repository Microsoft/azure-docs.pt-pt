---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 948f5399ceb931d701341c2cf378482ede94979d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200283"
---
| Porto não.| Dentro ou fora | Âmbito portuário| Obrigatório| Notas |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de blogs de dados REST APIs em HTTP. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 443 (HTTPS)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de blogs de dados REST APIs sobre HTTPS. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 8443 (HTTPS-Alt)|Em|LAN|Sim|Esta é uma porta alternativa para HTTPS e é usada ao ligar-se à UI web local para a gestão de dispositivos. |
| TCP 445 (SMB)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |
| TCP 111 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta é utilizada para mapeamento rpcbind/porta e só é necessária se estiver a ligar via NFS.  |
