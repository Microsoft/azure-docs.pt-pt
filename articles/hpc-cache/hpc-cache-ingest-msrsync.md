---
title: Ingestão de dados de visualização do cache HPC do Azure – msrsync
description: Como usar o msrsync para mover dados para um destino de armazenamento de BLOBs no cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 6eac6c367be42021a4654f85c8f4ec980c9f6925
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255280"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Ingestão de dados do cache HPC do Azure (visualização) – método msrsync

Este artigo fornece instruções detalhadas sobre como usar o utilitário ``msrsync`` para copiar dados para um contêiner de armazenamento de BLOBs do Azure para uso com o cache do HPC do Azure.

Para saber mais sobre como mover dados para o armazenamento de BLOBs para o cache do Azure HPC, leia [mover dados para o armazenamento de BLOBs do Azure para o cache do Azure HPC](hpc-cache-ingest.md).

A ferramenta ``msrsync`` pode ser usada para mover dados para um destino de armazenamento de back-end para o cache do HPC do Azure. Essa ferramenta foi projetada para otimizar o uso da largura de banda executando vários processos paralelos ``rsync``. Ele está disponível no GitHub em https://github.com/jbd/msrsync.

``msrsync`` divide o diretório de origem em "buckets" separados e, em seguida, executa os processos individuais de ``rsync`` em cada Bucket.

Os testes preliminares usando uma VM de quatro núcleos mostraram a melhor eficiência ao usar os processos 64. Use a opção ``msrsync`` ``-p`` para definir o número de processos como 64.

Observe que ``msrsync`` só pode gravar de e para volumes locais. A origem e o destino devem estar acessíveis como montagens locais na estação de trabalho usada para emitir o comando.

Siga estas instruções para usar ``msrsync`` para popular o armazenamento de BLOBs do Azure com o cache do HPC do Azure:

1. Instalar ``msrsync`` e seus pré-requisitos (``rsync`` e Python 2,6 ou posterior)
1. Determine o número total de arquivos e diretórios a serem copiados.

   Por exemplo, use o utilitário ``prime.py`` com argumentos ```prime.py --directory /path/to/some/directory``` (disponível baixando <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Se não estiver usando ``prime.py``, você poderá calcular o número de itens com a ferramenta GNU ``find`` da seguinte maneira:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens por 64 para determinar o número de itens por processo. Use esse número com a opção ``-f`` para definir o tamanho dos buckets ao executar o comando.

1. Emita o comando ``msrsync`` para copiar arquivos:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi projetado para mover arquivos 11.000 em 64 processos de/Test/Source-Repository para/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
