---
title: Ingestão de dados de visualização do cache HPC do Azure – msrsync
description: Como usar o msrsync para mover dados para um destino de armazenamento de BLOBs no cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 265ec55a6e013a37cf963b6256e900c070311f72
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180948"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Ingestão de dados do cache HPC do Azure (visualização) – método msrsync

Este artigo fornece instruções detalhadas sobre como usar ``msrsync`` o utilitário para copiar dados para um contêiner de armazenamento de BLOBs do Azure para uso com o cache HPC do Azure.

Para saber mais sobre como mover dados para o armazenamento de BLOBs para o cache do Azure HPC, leia [mover dados para o armazenamento de BLOBs do Azure para o cache do Azure HPC](hpc-cache-ingest.md).

A ``msrsync`` ferramenta pode ser usada para mover dados para um destino de armazenamento de back-end para o cache do HPC do Azure. Essa ferramenta foi projetada para otimizar o uso da largura de banda ``rsync`` executando vários processos paralelos. Ele está disponível no GitHub em https://github.com/jbd/msrsync.

``msrsync``divide o diretório de origem em "buckets" separados e, em seguida ``rsync`` , executa processos individuais em cada Bucket.

Os testes preliminares usando uma VM de quatro núcleos mostraram a melhor eficiência ao usar os processos 64. Use a ``msrsync`` opção ``-p`` para definir o número de processos como 64.

Observe que ``msrsync`` o só pode gravar de e para volumes locais. A origem e o destino devem estar acessíveis como montagens locais na estação de trabalho usada para emitir o comando.

Siga estas instruções para usar ``msrsync`` o para preencher o armazenamento de BLOBs do Azure com o cache do HPC do Azure:

1. Instalar ``msrsync`` e seus pré-requisitos (``rsync`` e Python 2,6 ou posterior)
1. Determine o número total de arquivos e diretórios a serem copiados.

   Por exemplo, use o utilitário ``prime.py`` com argumentos ```prime.py --directory /path/to/some/directory``` (disponíveis por download <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Se não estiver ``prime.py``usando o, você poderá calcular o número de itens com ``find`` a ferramenta GNU da seguinte maneira:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens por 64 para determinar o número de itens por processo. Use esse número com a ``-f`` opção de definir o tamanho dos buckets ao executar o comando.

1. Emita o ``msrsync`` comando para copiar arquivos:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi projetado para mover arquivos 11.000 em 64 processos de/Test/Source-Repository para/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
