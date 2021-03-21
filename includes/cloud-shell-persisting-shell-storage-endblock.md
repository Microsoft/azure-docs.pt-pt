---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184596"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Transfira ficheiros locais para Cloud Shell
O `clouddrive` diretório sincroniza-se com a lâmina de armazenamento do portal Azure. Utilize esta lâmina para transferir ficheiros locais para ou da sua parte do ficheiro. A atualização de ficheiros a partir de Cloud Shell reflete-se no GUI de armazenamento de ficheiros quando atualiza a lâmina.

### <a name="download-files"></a>Transferir ficheiros

![Lista de ficheiros locais](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. No portal Azure, vá para a partilha de ficheiros montado.
2. Selecione o ficheiro alvo.
3. Selecione o botão **Descarregar.**

### <a name="upload-files"></a>Carregar ficheiros

![Ficheiros locais a serem carregados](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Vá para a sua partilha de ficheiros montado.
2. Selecione o botão **Carregar**.
3. Selecione o ficheiro ou ficheiros que pretende carregar.
4. Confirme o upload.

Deve agora ver os ficheiros que estão acessíveis no seu `clouddrive` diretório em Cloud Shell.