---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127366"
---
### <a name="retrieve-output-files"></a>Obter ficheiros de saída

Pode utilizar o portal do Azure para transferir os ficheiros MP3 de saída gerados pelas tarefas ffmpeg. 

1. Clique em **Todos os serviços** > **Contas de armazenamento** e, em seguida, clique no nome da sua conta de armazenamento.
2. Clique em **Blobs** > *saída*.
3. Clique com o botão direito do rato em um dos ficheiros MP3 de saída e, em seguida, clique em **Transferir**. Siga as instruções no seu browser para abrir ou guardar o ficheiro.

![Transfira o ficheiro de saída](./media/batch-common-tutorial-download/download.png)

Apesar de não estar mostrado neste exemplo, também pode transferir os ficheiros programaticamente a partir dos nós de computação ou do contentor de armazenamento.
