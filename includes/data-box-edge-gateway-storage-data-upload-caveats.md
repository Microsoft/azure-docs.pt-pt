---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401042"
---
As ressalvas seguintes aplicam-se aos dados à medida que se movem para Azure.

- Sugerimos que mais de um dispositivo não deve escrever para o mesmo recipiente.
- Se tiver um objeto Azure existente (como uma bolha ou um ficheiro) na nuvem com o mesmo nome que o objeto que está a ser copiado, o dispositivo substituirá o ficheiro na nuvem.
- Uma hierarquia de diretório vazio (sem ficheiros) criada sob pastas de partilha não é carregada para os recipientes blob.
- Pode copiar os dados ao arrastar e soltar com o Explorador de Ficheiros ou através da linha de comandos. Se o tamanho agregado dos ficheiros que estão a ser copiados for superior a 10 GB, recomendamos que utilize um programa de cópia em massa, como o Robocopy ou rsync. As ferramentas de cópia em massa tentam a operação de cópia novamente para os erros intermitentes e proporcionam uma resiliência adicional.
- Se a parte associada ao recipiente de armazenamento Azure carregar bolhas que não correspondem ao tipo de bolhas definidas para a partilha no momento da criação, então essas bolhas não são atualizadas. Por exemplo, crie uma partilha de blobs de blocos no dispositivo. Associe a partilha a um contentor de cloud existente com blobs de páginas. Atualize essa partilha para transferir os ficheiros. Modifique alguns dos ficheiros atualizados que já estão armazenados como blobs de páginas na cloud. Vai observar falhas de carregamento.
- Depois de um ficheiro ser criado nas ações, a renomeação do ficheiro não é suportada.
- A eliminação de um ficheiro de uma partilha não elimina a entrada na conta de armazenamento.
- Se utilizar o rsync para copiar dados, então `rsync -a` a opção não é suportada.

