---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244603"
---
- Não copie os ficheiros diretamente a qualquer uma das partilhas de pré-criada. Precisa criar uma pasta sob a partilha e, em seguida, copiar ficheiros para essa pasta.
- Uma pasta sob o *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* é um contentor. Por exemplo, os contentores são criados como *StorageAccount_BlockBlob/contentores* e *StorageAccount_PageBlob/contentor*.
- Cada pasta criada diretamente sob *StorageAccount_AzureFiles* é convertida numa partilha de ficheiros do Azure.
- Se tiver um objeto do Azure existente (como um blob ou um arquivo) na cloud com o mesmo nome que o objeto que está a ser copiado, o Data Box irá substituir o ficheiro na cloud.
- Todos os ficheiros escritos na *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* partilhas é carregado como um blob de blocos e BLOBs de páginas, respectivamente.
- Armazenamento de Blobs do Azure não suporta diretórios. Se criar uma pasta sob o *StorageAccount_BlockBlob* pasta, em seguida, pastas virtuais são criadas no nome do blob. Para ficheiros do Azure, a estrutura de diretórios real é mantida.
- Esvaziar qualquer hierarquia de diretório (sem quaisquer ficheiros) criada *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* pastas não é carregado.
- Se houver algum erro ao carregar dados para o Azure, é criado um registo de erros na conta de armazenamento de destino. O caminho para este registo de erros está disponível quando o carregamento estiver concluído e pode rever o registo para tomar medidas corretivas. Não elimine dados da origem sem verificar os dados carregados.
