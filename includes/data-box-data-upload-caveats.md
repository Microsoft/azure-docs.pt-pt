---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73800042"
---
- Não copie ficheiros diretamente para nenhuma das ações pré-criadas. É necessário criar uma pasta sob a parte e, em seguida, copiar ficheiros para essa pasta.
- Uma pasta sob o *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* é um recipiente. Por exemplo, os recipientes são criados como *StorageAccount_BlockBlob/contentor* e *StorageAccount_PageBlob/contentor*.
- Cada pasta criada diretamente sob *StorageAccount_AzureFiles* é traduzida para uma Partilha de Ficheiros Azure.
- Se tiver um objeto Azure existente (como uma bolha ou um ficheiro) na nuvem com o mesmo nome que o objeto que está a ser copiado, a Data Box irá sobrepor o ficheiro na nuvem.
- Todos os ficheiros escritos em *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* ações são carregados como uma bolha de bloco e uma bolha de página, respectivamente.
- O armazenamento de blob azure não suporta diretórios. Se criar uma pasta sob a pasta *StorageAccount_BlockBlob,* as pastas virtuais são criadas no nome blob. Para a Azure Files, a estrutura real do diretório é mantida.
- Qualquer hierarquia de diretório vazia (sem ficheiros) criada sob *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* pastas não é carregada.
- Se houver erros ao enviar dados para o Azure, é criado um registo de erro na conta de armazenamento do alvo. O caminho para este registo de erro está disponível quando o upload estiver completo e pode rever o registo para tomar medidas corretivas. Não elimine os dados da fonte sem verificar os dados enviados.
- Os metadados de ficheiros e as permissões NTFS não são preservados quando os dados são enviados para ficheiros Azure. Por exemplo, o *último atributo modificado* dos ficheiros não será conservado quando os dados forem copiados.
