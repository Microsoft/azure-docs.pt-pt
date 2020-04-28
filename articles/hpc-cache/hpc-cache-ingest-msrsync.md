---
title: Ingerir dados azure HPC Cache - msrsync
description: Como utilizar o msrsync para mover dados para um alvo de armazenamento Blob em Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2e0442b6aa1404ae5f57445179979496faa09863
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194980"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Ingerir dados azure HPC Cache - método msrsync

Este artigo dá instruções ``msrsync`` detalhadas para utilizar o utilitário para copiar dados para um recipiente de armazenamento Azure Blob para utilização com cache Azure HPC.

Para saber mais sobre a mudança de dados para o armazenamento Blob para o seu Cache Azure HPC, leia [os dados do Move para o armazenamento da Blob Azure](hpc-cache-ingest.md).

A ``msrsync`` ferramenta pode ser usada para mover dados para um alvo de armazenamento traseiro para o Cache Azure HPC. Esta ferramenta foi concebida para otimizar o ``rsync`` uso da largura de banda executando vários processos paralelos. Está disponível no GitHub em https://github.com/jbd/msrsync.

``msrsync``separa o diretório de origem em "baldes" separados e, em seguida, executa processos individuais ``rsync`` em cada balde.

Os testes preliminares utilizando um VM de quatro núcleos mostraram a melhor eficiência ao utilizar 64 processos. Use ``msrsync`` a ``-p`` opção para definir o número de processos para 64.

Note ``msrsync`` que só pode escrever de e para os volumes locais. A fonte e o destino devem ser acessíveis à medida que os suportes locais na estação de trabalho utilizada para emitir o comando.

Siga estas instruções ``msrsync`` para a utilização para povoar o armazenamento azure blob com a Cache Azure HPC:

1. Instalar ``msrsync`` e os seus``rsync`` pré-requisitos (e Python 2.6 ou mais tarde)
1. Determine o número total de ficheiros e diretórios a copiar.

   Por exemplo, utilize ``prime.py`` o ```prime.py --directory /path/to/some/directory``` utilitário com argumentos <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>(disponíveis através do download).

   Se não ``prime.py``estiver a utilizar, pode calcular o ``find`` número de itens com a ferramenta GNU da seguinte forma:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens em 64 para determinar o número de itens por processo. Utilize este número ``-f`` com a opção de definir o tamanho dos baldes quando executar o comando.

1. Emita o ``msrsync`` comando para copiar ficheiros:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi concebido para mover 11.000 ficheiros em 64 processos de /teste/repositório de origem para /mnt/hpccache/repositório:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
