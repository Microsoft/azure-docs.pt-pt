---
title: Azure HPC Cache dados ingerir - msrsync
description: Como usar o msrsync para mover dados para um alvo de armazenamento blob em Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 323ecd6a2dd001c3c8df1b2ec15f0ae8402ec70c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87092410"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC Cache data ingestão - método msrsync

Este artigo dá instruções detalhadas para a utilização do ``msrsync`` utilitário para copiar dados para um recipiente de armazenamento Azure Blob para utilização com cache Azure HPC.

Para saber mais sobre a mudança de dados para o armazenamento blob para o seu Cache Azure HPC, leia [os dados do Move para o armazenamento Azure Blob](hpc-cache-ingest.md).

A ``msrsync`` ferramenta pode ser utilizada para mover dados para um alvo de armazenamento de back-end para a Cache Azure HPC. Esta ferramenta foi concebida para otimizar o uso da largura de banda executando ``rsync`` vários processos paralelos. Está disponível no GitHub em https://github.com/jbd/msrsync .

``msrsync`` rompe o diretório de origem em "baldes" separados e, em seguida, executa processos individuais ``rsync`` em cada balde.

Os testes preliminares utilizando um VM de quatro núcleos mostraram a melhor eficiência quando utilizaram 64 processos. Utilize a ``msrsync`` opção ``-p`` para definir o número de processos para 64.

Note que ``msrsync`` só pode escrever de e para os volumes locais. A fonte e o destino devem estar acessíveis como suportes locais no posto de trabalho utilizado para emitir o comando.

Siga estas instruções para utilizar ``msrsync`` para povoar o armazenamento Azure Blob com cache Azure HPC:

1. Instale ``msrsync`` e seus pré-requisitos ``rsync`` (e Python 2.6 ou posterior)
1. Determine o número total de ficheiros e diretórios a serem copiados.

   Por exemplo, utilize o utilitário ``prime.py`` com argumentos ```prime.py --directory /path/to/some/directory``` (disponíveis através do <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> download).

   Se não ``prime.py`` utilizar, pode calcular o número de itens com a ferramenta GNU ``find`` da seguinte forma:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Divida o número de itens por 64 para determinar o número de itens por processo. Utilize este número com a ``-f`` opção de definir o tamanho dos baldes quando executar o comando.

1. Emita o ``msrsync`` comando para copiar ficheiros:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Por exemplo, este comando foi concebido para mover 11.000 ficheiros em 64 processos de /teste/repositório de fontes para /mnt/hpccache/repositório:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
