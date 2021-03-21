---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582664"
---
As ressalvas seguintes aplicam-se aos dados à medida que se movem para Azure.

- Sugerimos que mais de um dispositivo não deve escrever para o mesmo recipiente.
- Se tiver um objeto Azure existente (como uma bolha ou um ficheiro) na nuvem com o mesmo nome que o objeto que está a ser copiado, o dispositivo substituirá o ficheiro na nuvem.
- Uma hierarquia de diretório vazio (sem ficheiros) criada sob pastas de partilha não é carregada para os recipientes blob.
- Pode copiar os dados utilizando arrastar e largar com o Explorador de Ficheiros ou através da linha de comando. Se o tamanho agregado dos ficheiros copiados for superior a 10 GB, recomendamos que utilize um programa de cópia a granel, como `Robocopy` ou `rsync` . As ferramentas de cópia a granel reagem à operação de cópia para erros intermitentes e proporcionam resiliência adicional.
- Se a parte associada ao recipiente de armazenamento Azure carregar bolhas que não correspondem ao tipo de bolhas definidas para a partilha no momento da criação, então essas bolhas não são atualizadas. Por exemplo, se criar uma partilha de blob de bloco no dispositivo, associar a partilha a um recipiente de nuvem existente que tenha bolhas de página, refrescar essa partilha para descarregar os ficheiros e, em seguida, modificar alguns dos ficheiros atualizados que já estão armazenados como bolhas de página na nuvem, verá falhas de upload.
- Depois de um ficheiro ser criado nas ações, a renomeação do ficheiro não é suportada.
- A eliminação de um ficheiro de uma partilha não elimina a entrada na conta de armazenamento.
- Se `rsync` utilizar para copiar dados, a `rsync -a` opção não é suportada.