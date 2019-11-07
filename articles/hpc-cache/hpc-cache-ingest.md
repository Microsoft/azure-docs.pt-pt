---
title: Mover dados para um contêiner de nuvem do cache HPC do Azure
description: Como popular o armazenamento de BLOBs do Azure para uso com o cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a206b63b03bcb3bb17e201487f0e00bcb3926151
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582231"
---
# <a name="move-data-to-azure-blob-storage"></a>Mover dados para o armazenamento de BLOBs do Azure

Se o fluxo de trabalho incluir a movimentação de dados para o armazenamento de BLOBs do Azure, verifique se você está usando uma estratégia eficiente. Você pode pré-carregar dados em um novo contêiner de blob antes de defini-lo como um destino de armazenamento ou adicionar o contêiner e, em seguida, copiar seus dados usando o cache do HPC do Azure.

Este artigo explica as melhores maneiras de mover dados para o armazenamento de BLOBs para uso com o cache do HPC do Azure.

Tenha esses fatos em mente:

* O cache HPC do Azure usa um formato de armazenamento especializado para organizar dados no armazenamento de BLOBs. É por isso que um destino de armazenamento de BLOBs deve ser um contêiner novo, vazio ou um contêiner de BLOBs que foi usado anteriormente para dados de cache do Azure HPC. (O[avere vFXT para Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) também usa esse sistema de arquivos de nuvem.)

* A cópia de dados por meio do cache do Azure HPC para um destino de armazenamento de back-end é mais eficiente quando você usa vários clientes e operações paralelas. Um comando de cópia simples de um cliente moverá os dados de forma lenta.

Um utilitário baseado em Python está disponível para carregar conteúdo em um contêiner de armazenamento de BLOBs. Leia [pré-carregar dados no armazenamento de BLOBs](#pre-load-data-in-blob-storage-with-clfsload) para saber mais.

Se você não quiser usar o utilitário de carregamento ou se quiser adicionar conteúdo a um destino de armazenamento existente, siga as dicas de ingestão de dados paralelos em [copiar dados por meio do cache do HPC do Azure](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Pré-carregar dados no armazenamento de BLOBs com CLFSLoad

Você pode usar o <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> Avere CLFSLoad Utility para copiar dados para um novo contêiner de armazenamento de BLOBs antes de adicioná-lo como um destino de armazenamento. Esse utilitário é executado em um único sistema Linux e grava dados no formato proprietário necessário para o cache do HPC do Azure. O CLFSLoad é a maneira mais eficiente de preencher um contêiner de armazenamento de BLOBs para uso com o cache.

O utilitário avere CLFSLoad está disponível por solicitação de sua equipe de cache do Azure HPC. Peça para o contato da sua equipe ou abra um [tíquete de suporte](hpc-cache-support-ticket.md) para solicitar assistência.

Essa opção funciona apenas com contêineres novos e vazios. Crie o contêiner antes de usar avere CLFSLoad.

Informações detalhadas estão incluídas na distribuição do avere CLFSLoad, que está disponível na solicitação da equipe de cache do HPC do Azure. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

Uma visão geral do processo:

1. Prepare um sistema Linux (VM ou físico) com o Python versão 3,6 ou posterior. O Python 3,7 é recomendado para melhorar o desempenho.
1. Instale o software avere-CLFSLoad no sistema Linux.
1. Execute a transferência na linha de comando do Linux.

O utilitário avere CLFSLoad precisa das seguintes informações:

* A ID da conta de armazenamento que contém o contêiner de armazenamento de BLOBs
* O nome do contêiner de armazenamento de BLOBs vazio
* Um token SAS (assinatura de acesso compartilhado) que permite que o utilitário grave no contêiner
* Um caminho local para a fonte de dados – um diretório local que contém os dados a serem copiados ou um caminho local para um sistema remoto montado com os dados

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Copiar dados por meio do cache HPC do Azure

Se você não quiser usar o utilitário avere CLFSLoad ou se quiser adicionar uma grande quantidade de dados a um destino de armazenamento de BLOBs existente, poderá copiá-lo por meio do cache. O cache HPC do Azure foi projetado para atender a vários clientes simultaneamente, por isso, para copiar dados por meio do cache, você deve usar gravações paralelas de vários clientes.

![Diagrama mostrando a movimentação de dados multithreads com vários clientes: na parte superior esquerda, um ícone para o armazenamento de hardware local tem várias setas provenientes dele. As setas apontam para quatro computadores cliente. De cada computador cliente, três setas apontam para o cache do HPC do Azure. No cache do HPC do Azure, várias setas apontam para o armazenamento de BLOBs.](media/hpc-cache-parallel-ingest.png)

Os comandos ``cp`` ou ``copy`` que você normalmente usa para transferir dados de um sistema de armazenamento para outro são processos de thread único que copiam apenas um arquivo por vez. Isso significa que o servidor de arquivos está ingerindo apenas um arquivo por vez, o que é um desperdício dos recursos do cache.

Esta seção explica estratégias para criar um sistema de cópia de arquivos multifuncional multifuncional para mover dados para o armazenamento de BLOBs com o cache do Azure HPC. Ele explica os conceitos de transferência de arquivos e os pontos de decisão que podem ser usados para a cópia eficiente de dados usando vários clientes e comandos de cópia simples.

Ele também explica alguns utilitários que podem ajudar. O utilitário ``msrsync`` pode ser usado para automatizar parcialmente o processo de dividir um conjunto de um em buckets e usar comandos rsync. O script ``parallelcp`` é outro utilitário que lê o diretório de origem e emite comandos de cópia automaticamente.

### <a name="strategic-planning"></a>Planejamento estratégico

Ao criar uma estratégia para copiar dados em paralelo, você deve entender as compensações no tamanho do arquivo, contagem de arquivos e profundidade de diretório.

* Quando os arquivos são pequenos, a métrica de interesse é de arquivos por segundo.
* Quando os arquivos são grandes (10MiBi ou superior), a métrica de interesse é de bytes por segundo.

Cada processo de cópia tem uma taxa de produtividade e uma taxa de transferência de arquivos, que pode ser medida por cronometrar o comprimento do comando de cópia e fatorar o tamanho do arquivo e a contagem de arquivos. Explicar como medir as tarifas está fora do escopo deste documento, mas é imperativo entender se você estará lidando com arquivos pequenos ou grandes.

As estratégias para ingestão de dados paralelos com o cache HPC do Azure incluem:

* Cópia manual – você pode criar manualmente uma cópia multi-threaded em um cliente executando mais de um comando de cópia de uma vez em segundo plano em relação a conjuntos predefinidos de arquivos ou caminhos. Leia o [método ingestão de dados do cache HPC do Azure – cópia manual](hpc-cache-ingest-manual.md) para obter detalhes.

* Cópia parcialmente automatizada com ``msrsync`` - ``msrsync`` é um utilitário de wrapper que executa vários processos de ``rsync`` paralelos. Para obter detalhes, leia o [método ingestão de dados do cache HPC do Azure-msrsync](hpc-cache-ingest-msrsync.md).

* Cópia com script com ``parallelcp``-saiba como criar e executar um script de cópia paralela no [método de script de cópia paralela de ingestão de dados do cache HPC do Azure](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o armazenamento, saiba como os clientes podem montar o cache.

* [Acessar o sistema de cache HPC do Azure](hpc-cache-mount.md)
