---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89505897"
---
| Porto não.| Dentro ou fora | Âmbito portuário| Necessário| Notas |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Em|LAN|Yes|Esta porta é utilizada para ligar ao armazenamento de blocos de dados REST APIs sobre HTTP. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 443 (HTTPS)|Em|LAN|Yes|Esta porta é utilizada para ligar ao armazenamento de blob de caixa de dados APIs em HTTPS. Se não estiver ligado às APIs rest, este redireciona automaticamente para a UI web local sobre 8443. |
| TCP 8443 (HTTPS-Alt)|Em|LAN|Yes|Esta é uma porta alternativa para HTTPS e é usada ao ligar-se à UI web local para a gestão de dispositivos. |
| TCP 445 (SMB)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via SMB. |
| TCP 2049 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta só é necessária se estiver a ligar via NFS. |
| TCP 111 (NFS)|Fora/Dentro|LAN|Em alguns casos<br>Ver notas|Esta porta é utilizada para mapeamento rpcbind/porta e só é necessária se estiver a ligar via NFS.  |
