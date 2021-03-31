---
title: Mova os dados para um recipiente de nuvem cache Azure HPC
description: Como povoar o armazenamento Azure Blob para uso com cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 0da8a4fc1b59976c50cd96f2155715a4cb178cc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87072763"
---
# <a name="move-data-to-azure-blob-storage"></a>Mover dados para o armazenamento da Azure Blob

Se o seu fluxo de trabalho incluir a transferência de dados para o armazenamento do Azure Blob, certifique-se de que está a utilizar uma estratégia eficiente. Pode pré-carregar dados num novo recipiente Blob antes de defini-lo como alvo de armazenamento, ou adicionar o recipiente e, em seguida, copiar os seus dados utilizando a Cache Azure HPC.

Este artigo explica as melhores formas de mover dados para o armazenamento blob para uso com Azure HPC Cache.

Tenha estes factos em mente:

* A Azure HPC Cache utiliza um formato de armazenamento especializado para organizar dados no armazenamento blob. É por isso que um alvo de armazenamento Blob deve ser um recipiente novo e vazio, ou um recipiente Blob que foi usado anteriormente para os dados da Cache Azure HPC.

* Copiar dados através da Cache Azure HPC para um alvo de armazenamento back-end é mais eficiente quando utiliza vários clientes e operações paralelas. Um simples comando de cópia de um cliente move os dados lentamente.

Um utilitário baseado em Python está disponível para carregar o conteúdo num recipiente de armazenamento Blob. Leia [os dados de pré-carregamento no armazenamento blob](#pre-load-data-in-blob-storage-with-clfsload) para saber mais.

Se não quiser utilizar o utilitário de carregamento, ou se pretender adicionar conteúdo a um alvo de armazenamento existente, siga as dicas paralelas de ingestão de dados em [dados de cópia através da Cache Azure HPC](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Pré-carregar dados no armazenamento blob com CLFSLoad

Pode utilizar o utilitário Avere CLFSLoad para copiar dados para um novo recipiente de armazenamento Blob antes de o adicionar como alvo de armazenamento. Este utilitário funciona num único sistema Linux e escreve dados no formato proprietário necessário para a Cache Azure HPC. O CLFSLoad é a forma mais eficiente de preencher um recipiente de armazenamento Blob para utilização com a cache.

O utilitário Avere CLFSLoad está disponível mediante pedido da sua equipa de Cache Azure HPC. Peça à sua equipa contacto ou abra um [bilhete de apoio](hpc-cache-support-ticket.md) para solicitar assistência.

Esta opção funciona apenas com recipientes novos e vazios. Crie o recipiente antes de utilizar o Avere CLFSLoad.

Informações detalhadas estão incluídas na distribuição Avere CLFSLoad, que está disponível a pedido da equipa Azure HPC Cache.

Uma visão geral do processo:

1. Prepare um sistema Linux (VM ou físico) com a versão Python 3.6 ou posterior. Python 3.7 é recomendado para um melhor desempenho.
1. Instale o software Avere-CLFSLoad no sistema Linux.
1. Execute a transferência a partir da linha de comando Linux.

O utilitário Avere CLFSLoad necessita das seguintes informações:

* O ID da conta de armazenamento que contém o seu recipiente de armazenamento Blob
* O nome do recipiente de armazenamento blob vazio
* Um símbolo de assinatura de acesso partilhado (SAS) que permite que o utilitário escreva para o recipiente
* Um caminho local para a fonte de dados - ou um diretório local que contém os dados para copiar, ou um caminho local para um sistema remoto montado com os dados

## <a name="copy-data-through-the-azure-hpc-cache"></a>Copiar dados através da Cache Azure HPC

Se não quiser utilizar o utilitário Avere CLFSLoad, ou se pretender adicionar uma grande quantidade de dados a um alvo de armazenamento Blob existente, pode copiá-lo através da cache. A Azure HPC Cache foi projetado para servir vários clientes simultaneamente, de modo a copiar dados através da cache, deve utilizar escritas paralelas de vários clientes.

![Diagrama que mostra movimento de dados multi-clientes e multi-threaded: Na parte superior esquerda, um ícone para armazenamento de hardware no local tem várias setas provenientes dele. As setas apontam para quatro máquinas de clientes. De cada máquina cliente três setas apontam para a Cache Azure HPC. A partir da Cache Azure HPC, várias setas apontam para o armazenamento blob.](media/hpc-cache-parallel-ingest.png)

Os ``cp`` ``copy`` comandos que normalmente utiliza para transferir dados de um sistema de armazenamento para outro são processos de rosca única que copiam apenas um ficheiro de cada vez. Isto significa que o servidor de ficheiros está a ingerir apenas um ficheiro de cada vez - o que é um desperdício dos recursos da cache.

Esta secção explica estratégias para a criação de um sistema multi-cliente de cópia de ficheiros multi-threaded para mover dados para o armazenamento blob com cache Azure HPC. Explica conceitos de transferência de ficheiros e pontos de decisão que podem ser usados para copiar dados eficientes usando vários clientes e comandos de cópia simples.

Também explica alguns utilitários que podem ajudar. O ``msrsync`` utilitário pode ser usado para automatizar parcialmente o processo de dividir um conjunto de dados em baldes e usar comandos rsync. O ``parallelcp`` script é outro utilitário que lê o diretório de origem e emite comandos de cópia automaticamente.

### <a name="strategic-planning"></a>Planeamento estratégico

Ao construir uma estratégia para copiar dados em paralelo, deve entender as trocas no tamanho dos ficheiros, contagem de ficheiros e profundidade de diretório.

* Quando os ficheiros são pequenos, a métrica de juros são ficheiros por segundo.
* Quando os ficheiros são grandes (10MiBi ou superior), a métrica de interesse é bytes por segundo.

Cada processo de cópia tem uma taxa de produção e uma taxa de transferência de ficheiros, que pode ser medida através do tempo do comando da cópia e factoring do tamanho do ficheiro e contagem de ficheiros. Explicar como medir as taxas está fora do âmbito deste documento, mas é imperativo entender se vai lidar com ficheiros pequenos ou grandes.

As estratégias para a ingestão de dados paralelos com a Cache Azure HPC incluem:

* Cópia manual - Pode criar manualmente uma cópia multi-roscadas num cliente executando mais do que um comando de cópia ao mesmo tempo em segundo plano contra conjuntos de ficheiros ou caminhos predefinidos. Leia [os dados da Cache Azure HPC - método de cópia manual](hpc-cache-ingest-manual.md) para mais detalhes.

* Cópia parcialmente automatizada com ``msrsync``  -  ``msrsync`` um utilitário de invólucro que executa vários ``rsync`` processos paralelos. Para mais detalhes, leia [os dados da Cache Azure HPC - método msrsync](hpc-cache-ingest-msrsync.md).

* Cópia escrita com ``parallelcp`` - Aprenda a criar e executar um script de cópia paralelo em [Azure HPC Cache data ingeste - método de script de cópia paralelo](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o seu armazenamento, saiba como os clientes podem montar a cache.

* [Aceda ao sistema Azure HPC Cache](hpc-cache-mount.md)
