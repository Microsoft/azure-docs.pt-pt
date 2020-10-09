---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184609"
---
### <a name="retrieve-output-files"></a>Obter ficheiros de saída

Pode utilizar o portal do Azure para transferir os ficheiros MP3 de saída gerados pelas tarefas ffmpeg. 

1. Clique em todas as contas de armazenamento de **todos os**  >  **serviços**e, em seguida, clique no nome da sua conta de armazenamento.
2. Clique na saída **Blobs**  >  *output*.
3. Clique com o botão direito do rato em um dos ficheiros MP3 de saída e, em seguida, clique em **Transferir**. Siga as instruções no seu browser para abrir ou guardar o ficheiro.

![Transfira o ficheiro de saída](./media/batch-common-tutorial-download/download.png)

Apesar de não estar mostrado neste exemplo, também pode transferir os ficheiros programaticamente a partir dos nós de computação ou do contentor de armazenamento.
