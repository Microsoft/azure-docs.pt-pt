---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505897"
---
| Porto não.| Dentro ou fora | Âmbito portuário| Necessário| Notas |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de blocos de dados REST APIs sobre HTTP. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 443 (HTTPS)|Em|LAN|Sim|Esta porta é utilizada para ligar ao armazenamento de blob de caixa de dados APIs em HTTPS. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 8443 (HTTPS-Alt)|Em|LAN|Sim|Esta é uma porta alternativa para HTTPS e é usada ao ligar-se à UI web local para a gestão de dispositivos. |
| TCP 445 (SMB)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |
| TCP 111 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta é utilizada para mapeamento rpcbind/porta e só é necessária se estiver a ligar via NFS.  |
