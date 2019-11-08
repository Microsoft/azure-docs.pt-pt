---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800042"
---
- Não copie arquivos diretamente para nenhum dos compartilhamentos precriados. Você precisa criar uma pasta sob o compartilhamento e, em seguida, copiar os arquivos nessa pasta.
- Uma pasta sob a *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* é um contêiner. Por exemplo, os contêineres são criados como *StorageAccount_BlockBlob/container* e *StorageAccount_PageBlob/container*.
- Cada pasta criada diretamente em *StorageAccount_AzureFiles* é convertida em um compartilhamento de arquivos do Azure.
- Se você tiver um objeto do Azure existente (como um BLOB ou um arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, Data Box substituirá o arquivo na nuvem.
- Cada arquivo gravado em compartilhamentos *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* é carregado como blob de blocos e BLOB de páginas, respectivamente.
- O armazenamento de BLOBs do Azure não dá suporte a diretórios. Se você criar uma pasta na pasta *StorageAccount_BlockBlob* , as pastas virtuais serão criadas no nome do blob. Para arquivos do Azure, a estrutura de diretório real é mantida.
- Qualquer hierarquia de diretório vazia (sem nenhum arquivo) criada em pastas *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* não é carregada.
- Se houver erros ao carregar dados no Azure, um log de erros será criado na conta de armazenamento de destino. O caminho para esse log de erros está disponível quando o upload é concluído e você pode examinar o log para executar uma ação corretiva. Não exclua dados da fonte sem verificar os dados carregados.
- Os metadados de arquivo e as permissões NTFS não são preservados quando os dados são carregados nos arquivos do Azure. Por exemplo, o *último atributo modificado* dos arquivos não será mantido quando os dados forem copiados.
