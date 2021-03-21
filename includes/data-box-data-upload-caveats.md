---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792412"
---
- Não copie ficheiros diretamente para nenhuma das ações pré-fundamentadas. É necessário criar uma pasta por baixo da partilha e, em seguida, copiar ficheiros para essa pasta.
- Uma pasta sob o *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* é um recipiente. Por exemplo, os recipientes são criados como *StorageAccount_BlockBlob/contentor* e *StorageAccount_PageBlob/contentor*.
- Cada pasta criada diretamente sob *StorageAccount_AzureFiles* é traduzida para uma Azure File Share.
- Se um objeto que está a ser copiado tiver o mesmo nome que um objeto Azure, como uma bolha ou um ficheiro, que já está na nuvem, a Data Box substituirá o ficheiro na nuvem.
- Todos os ficheiros escritos em *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* ações são carregados como uma bolha de bloco e uma bolha de página, respectivamente.
- O Azure Blob Storage não suporta diretórios. Se criar uma pasta sob a pasta *StorageAccount_BlockBlob,* então as pastas virtuais são criadas no nome blob. Para os Ficheiros Azure, a estrutura real do diretório é mantida.
- Qualquer hierarquia de diretório vazio (sem ficheiros) criada sob *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* pastas não é carregada.
- Se houver erros ao enviar dados para o Azure, é criado um registo de erro na conta de armazenamento alvo. O caminho para este registo de erro está disponível quando o upload estiver completo, e pode rever o log para tomar medidas corretivas. Não elimine os dados da fonte sem verificar os dados enviados.
- Os metadados de ficheiros e as permissões NTFS podem ser preservados quando os dados são enviados para ficheiros Azure utilizando orientações em [ACLs, atributos e timetamps de conservação com Caixa de Dados Azure](../articles/databox/data-box-file-acls-preservation.md).
