---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184596"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Transfira ficheiros locais para Cloud Shell
O `clouddrive` diretório sincroniza-se com a lâmina de armazenamento do portal Azure. Utilize esta lâmina para transferir ficheiros locais para ou a partir da sua parte de ficheiro. A atualização de ficheiros de dentro da Cloud Shell reflete-se no ficheiro GUI de armazenamento quando refresca a lâmina.

### <a name="download-files"></a>Transferir ficheiros

![Lista de ficheiros locais](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. No portal Azure, vá à partilha de ficheiros montados.
2. Selecione o ficheiro alvo.
3. Selecione o botão **Descarregar.**

### <a name="upload-files"></a>Carregar ficheiros

![Ficheiros locais a serem carregados](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Vá para a sua parte de arquivo montada.
2. Selecione o botão **Carregar**.
3. Selecione o ficheiro ou ficheiros que pretende fazer upload.
4. Confirme o upload.

Agora deve ver os ficheiros `clouddrive` acessíveis no seu diretório na Cloud Shell.