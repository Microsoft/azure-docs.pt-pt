---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125223"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Transferir ficheiros locais para o Cloud Shell
O `clouddrive` sincronizações de diretório com o painel do portal de armazenamento do Azure. Utilize este painel para transferir ficheiros de locais de ou para a partilha de ficheiros. A atualizar ficheiros a partir de Cloud Shell é refletida no armazenamento de ficheiros GUI quando atualizar o painel.

### <a name="download-files"></a>Transferir ficheiros

![Lista de ficheiros locais](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. No portal do Azure, vá para a partilha de ficheiros instalado.
2. Selecione o ficheiro de destino.
3. Selecione o **transferir** botão.

### <a name="upload-files"></a>Carregar ficheiros

![Ficheiros locais para ser carregado](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Vá para a partilha de ficheiros instalado.
2. Selecione o botão **Carregar**.
3. Selecione o ficheiro ou ficheiros que pretende carregar.
4. Certifique-se o carregamento.

Agora, deverá ver os ficheiros que são acessíveis no seu `clouddrive` diretório no Cloud Shell.