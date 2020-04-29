---
title: Mova os dados para um recipiente de nuvem Azure HPC Cache
description: Como povoar o armazenamento Azure Blob para uso com cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fd21a78d0271f91d334bba5aba748f3770ad38cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537938"
---
# <a name="move-data-to-azure-blob-storage"></a>Mova dados para o armazenamento de Blob Azure

Se o seu fluxo de trabalho incluir a transferência de dados para o armazenamento do Azure Blob, certifique-se de que está a usar uma estratégia eficiente. Pode pré-carregar dados num novo recipiente Blob antes de defini-lo como um alvo de armazenamento, ou adicionar o recipiente e, em seguida, copiar os seus dados usando a Cache Azure HPC.

Este artigo explica as melhores formas de mover dados para o armazenamento Blob para uso com cache Azure HPC.

Tenha em mente estes factos:

* A Azure HPC Cache utiliza um formato de armazenamento especializado para organizar dados no armazenamento blob. É por isso que um alvo de armazenamento Blob deve ser um recipiente novo e vazio, ou um recipiente Blob que foi anteriormente utilizado para dados de Cache Azure HPC.

* Copiar dados através do Cache Azure HPC para um alvo de armazenamento final é mais eficiente quando se utiliza vários clientes e operações paralelas. Um simples comando de cópia de um cliente irá mover os dados lentamente.

Um utilitário baseado em Python está disponível para carregar conteúdo num recipiente de armazenamento Blob. Leia [os dados de pré-carregamento no armazenamento blob](#pre-load-data-in-blob-storage-with-clfsload) para saber mais.

Se não quiser utilizar o utilitário de carregamento, ou se pretender adicionar conteúdo a um alvo de armazenamento existente, siga as dicas paralelas de ingerir dados em [Dados de Cópia através da Cache Azure HPC](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Dados de pré-carga no armazenamento blob com CLFSLoad

Pode utilizar o utilitário Avere CLFSLoad para copiar dados para um novo recipiente de armazenamento Blob antes de o adicionar como alvo de armazenamento. Este utilitário funciona num único sistema Linux e escreve dados no formato proprietário necessário para o Cache Azure HPC. CLFSLoad é a forma mais eficiente de povoar um recipiente de armazenamento Blob para utilização com a cache.

O utilitário Avere CLFSLoad está disponível medendo da sua equipa Azure HPC Cache. Peça contacto com a sua equipa ou abra um bilhete de [apoio](hpc-cache-support-ticket.md) para solicitar assistência.

Esta opção funciona apenas com recipientes novos e vazios. Crie o recipiente antes de utilizar a Avere CLFSLoad.

Informações detalhadas estão incluídas na distribuição Avere CLFSLoad, que está disponível a pedido da equipa azure HPC Cache.

Uma visão geral do processo:

1. Prepare um sistema Linux (VM ou físico) com a versão 3.6 ou posterior da Python. Python 3.7 é recomendado para um melhor desempenho.
1. Instale o software Avere-CLFSLoad no sistema Linux.
1. Execute a transferência da linha de comando Linux.

O utilitário Avere CLFSLoad necessita das seguintes informações:

* O ID da conta de armazenamento que contém o seu recipiente de armazenamento Blob
* O nome do recipiente de armazenamento blob vazio
* Um símbolo de assinatura de acesso partilhado (SAS) que permite ao utilitário escrever para o recipiente
* Um caminho local para a fonte de dados - ou um diretório local que contém os dados para copiar, ou um caminho local para um sistema remoto montado com os dados

## <a name="copy-data-through-the-azure-hpc-cache"></a>Copiar dados através da Cache Azure HPC

Se não quiser utilizar o utilitário Avere CLFSLoad, ou se quiser adicionar uma grande quantidade de dados a um alvo de armazenamento Blob existente, pode copiá-lo através da cache. A Azure HPC Cache foi concebida para servir vários clientes simultaneamente, para copiar dados através da cache, deve utilizar escritos paralelos de vários clientes.

![Diagrama mostrando movimento de dados multi-cliente, multi-threaded: Na parte superior esquerda, um ícone para armazenamento de hardware no local tem várias setas provenientes dele. As setas apontam para quatro máquinas de clientes. De cada máquina cliente três setas apontam para o Cache Azure HPC. A partir do Cache Azure HPC, várias setas apontam para o armazenamento blob.](media/hpc-cache-parallel-ingest.png)

Os ``cp`` ``copy`` comandos ou comandos que normalmente utiliza para transferir dados de um sistema de armazenamento para outro são processos de roscar único que copiam apenas um ficheiro de cada vez. Isto significa que o servidor de ficheiros está a ingerir apenas um ficheiro de cada vez - o que é um desperdício dos recursos da cache.

Esta secção explica estratégias para criar um sistema multi-cliente de cópia de ficheiros multi-threaded para mover dados para o armazenamento Blob com cache Azure HPC. Explica conceitos de transferência de ficheiros e pontos de decisão que podem ser usados para copiar dados eficientes usando vários clientes e comandos de cópia simples.

Também explica alguns serviços que podem ajudar. O ``msrsync`` utilitário pode ser utilizado para automatizar parcialmente o processo de divisão de um conjunto de dados em baldes e utilizando comandos rsync. O ``parallelcp`` script é outro utilitário que lê o diretório de origem e emite comandos de cópia automaticamente.

### <a name="strategic-planning"></a>Planeamento estratégico

Ao construir uma estratégia para copiar dados em paralelo, deve compreender as trocas em tamanho de ficheiro, contagem de ficheiros e profundidade de diretório.

* Quando os ficheiros são pequenos, a métrica de interesse é de ficheiros por segundo.
* Quando os ficheiros são grandes (10MiBi ou mais), a métrica de interesse é bytes por segundo.

Cada processo de cópia tem uma taxa de entrada e uma taxa transferida por ficheiros, que pode ser medida cronometrando o comprimento do comando de cópia e fatorizando o tamanho do ficheiro e a contagem de ficheiros. Explicar como medir as taxas está fora do âmbito deste documento, mas é imperativo perceber se vai lidar com ficheiros pequenos ou grandes.

As estratégias para ingerir dados paralelos com a Cache Azure HPC incluem:

* Cópia manual - Pode criar manualmente uma cópia multi-roscada num cliente executando mais do que um comando de cópia ao mesmo tempo em segundo plano contra conjuntos predefinidos de ficheiros ou caminhos. Leia [a ingestão de dados azure HPC Cache - método](hpc-cache-ingest-manual.md) de cópia manual para mais detalhes.

* A cópia parcialmente ``msrsync``  -  ``msrsync`` automatizada é um utilitário ``rsync`` de invólucro que executa vários processos paralelos. Para mais detalhes, leia [a ingerir dados azure HPC Cache - método msrsync](hpc-cache-ingest-msrsync.md).

* Cópia escrita com ``parallelcp`` - Aprenda a criar e executar um roteiro de cópia paralela em [Azure HPC Cache ingerir dados - método de script](hpc-cache-ingest-parallelcp.md)de cópia paralela .

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o seu armazenamento, saiba como os clientes podem montar a cache.

* [Aceda ao sistema de cache Azure HPC](hpc-cache-mount.md)
