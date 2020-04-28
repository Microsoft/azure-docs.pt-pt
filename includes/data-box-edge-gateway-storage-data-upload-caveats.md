---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184714"
---
As seguintes ressalvas aplicam-se aos dados à medida que se movem para O Azure.

- Sugerimos que mais de um dispositivo não deve escrever para o mesmo recipiente.
- Se tiver um objeto Azure existente (como uma bolha ou um ficheiro) na nuvem com o mesmo nome que o objeto que está a ser copiado, o dispositivo irá substituir o ficheiro na nuvem.
- Uma hierarquia de diretório vazia (sem ficheiros) criada sob pastas de ações não é enviada para os recipientes blob.
- Pode copiar os dados utilizando arrasto e queda com o File Explorer ou através da linha de comando. Se o tamanho agregado dos ficheiros que estão a ser copiados for superior a 10 GB, recomendamos que utilize um programa de cópias a granel, como Robocopy ou rsync. As ferramentas de cópia a granel retentam o funcionamento da cópia para erros intermitentes e proporcionam resiliência adicional.
- Se a parte associada ao contentor de armazenamento Azure carregar bolhas que não correspondam ao tipo de bolhas definidas para a partilha no momento da criação, então tais bolhas não são atualizadas. Por exemplo, cria-se uma partilha de blocos no dispositivo. Associe a parte a um recipiente de nuvem existente que tem bolhas de página. Refresque essa partilha para descarregar os ficheiros. Modifique alguns dos ficheiros renovados que já estão armazenados como bolhas de página na nuvem. Verá falhas no upload.
