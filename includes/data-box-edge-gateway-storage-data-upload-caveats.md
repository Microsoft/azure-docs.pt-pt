---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184714"
---
As ressalvas seguintes aplicam-se aos dados à medida que se movem para Azure.

- Sugerimos que mais de um dispositivo não deve escrever para o mesmo recipiente.
- Se tiver um objeto Azure existente (como uma bolha ou um ficheiro) na nuvem com o mesmo nome que o objeto que está a ser copiado, o dispositivo substituirá o ficheiro na nuvem.
- Uma hierarquia de diretório vazio (sem ficheiros) criada sob pastas de partilha não é carregada para os recipientes blob.
- Pode copiar os dados utilizando arrastar e largar com o Explorador de Ficheiros ou através da linha de comando. Se o tamanho agregado dos ficheiros copiados for superior a 10 GB, recomendamos que utilize um programa de cópia a granel, como robocopia ou rsync. As ferramentas de cópia a granel reagem à operação de cópia por erros intermitentes e proporcionam resiliência adicional.
- Se a parte associada ao recipiente de armazenamento Azure carregar bolhas que não correspondem ao tipo de bolhas definidas para a partilha no momento da criação, então essas bolhas não são atualizadas. Por exemplo, cria-se uma partilha de blocos no dispositivo. Associe a partilha a um recipiente de nuvem existente que tenha bolhas de página. Refresque essa partilha para descarregar os ficheiros. Modifique alguns dos ficheiros renovados que já estão armazenados como bolhas de página na nuvem. Verá falhas no upload.
