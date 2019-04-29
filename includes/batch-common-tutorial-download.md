---
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a8ea55a40f1ee4681b6aec147e02b7bce6f1d7cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616623"
---
### <a name="retrieve-output-files"></a>Obter ficheiros de saída

Pode utilizar o portal do Azure para transferir os ficheiros MP3 de saída gerados pelas tarefas ffmpeg. 

1. Clique em **Todos os serviços** > **Contas de armazenamento** e, em seguida, clique no nome da sua conta de armazenamento.
2. Clique em **Blobs** > *saída*.
3. Clique com o botão direito do rato em um dos ficheiros MP3 de saída e, em seguida, clique em **Transferir**. Siga as instruções no seu browser para abrir ou guardar o ficheiro.

![Transfira o ficheiro de saída](./media/batch-common-tutorial-download/download.png)

Apesar de não estar mostrado neste exemplo, também pode transferir os ficheiros programaticamente a partir dos nós de computação ou do contentor de armazenamento.
