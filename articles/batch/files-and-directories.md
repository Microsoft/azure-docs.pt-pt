---
title: Arquivos e diretórios em Azure Batch
description: Saiba mais sobre ficheiros e diretórios e como são usados num fluxo de trabalho do Azure Batch do ponto de vista do desenvolvimento.
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: eafea6c234c3b261521f8a791b7a03e25388f02a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87552646"
---
# <a name="files-and-directories-in-azure-batch"></a>Arquivos e diretórios em Azure Batch

Em Azure Batch, cada tarefa tem um diretório de trabalho no qual pode criar ficheiros e diretórios. Este diretório de trabalho pode ser utilizado para armazenar o programa que é executado pela tarefa, os dados que processa e o resultado do processamento feito. Todos os ficheiros e diretórios de uma tarefa são propriedade do utilizador da tarefa.

O serviço Batch expõe uma parte do sistema de ficheiros num nó como o *diretório de raiz*. Este diretório de raiz está localizado na unidade de armazenamento temporário do VM, e não diretamente na unidade de sistema operativo.

As tarefas podem consultar a variável de ambiente `AZ_BATCH_NODE_ROOT_DIR` para aceder ao diretório de raiz. Para obter mais informações sobre como utilizar variáveis de ambiente, consulte [Definições de ambiente para tarefas](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="root-directory-structure"></a>Estrutura de diretório de raiz

O diretório de raiz contém a seguinte estrutura de diretórios:

![Screenshot da estrutura do diretório do nó de computação.](media\files-and-directories\node-folder-structure.png)

- **aplicações**: Contém informações sobre os detalhes dos pacotes de aplicações instalados no nó de cálculo. As tarefas podem consultar a variável de ambiente `AZ_BATCH_APP_PACKAGE` para aceder a este diretório.

- **fsmounts**: O diretório contém quaisquer sistemas de ficheiros que sejam montados num nó de computação. As tarefas podem consultar a variável de ambiente `AZ_BATCH_NODE_MOUNTS_DIR` para aceder a este diretório. Para obter mais informações, consulte [um sistema de ficheiros virtual numa piscina de Lote.](virtual-file-mount.md)

- **partilhado**: este diretório proporciona acesso de leitura/escrita a *todas* as tarefas executadas num nó. Qualquer tarefa executada no nó pode criar, ler, atualizar e eliminar ficheiros neste diretório. As tarefas podem consultar a variável de ambiente `AZ_BATCH_NODE_SHARED_DIR` para aceder a este diretório.

- **arranque**: este diretório é utilizado pelas tarefas de início como o diretório de trabalho. Todos os ficheiros que tenham sido transferidos para o nó pela tarefa de início são armazenados aqui. A tarefa de início pode criar, ler, atualizar e eliminar ficheiros neste diretório. As tarefas podem consultar a variável de ambiente `AZ_BATCH_NODE_STARTUP_DIR` para aceder a este diretório.

- **volátil**: Este diretório é para fins internos. Não há garantias de que quaisquer ficheiros neste diretório ou que o próprio diretório existam no futuro.

- **workitems**: Este diretório contém os diretórios para empregos e as suas tarefas no nó de computação.

    Dentro do **diretório de workitems,** é criado um diretório **de Tarefas** para cada tarefa que executa no nó. Este diretório pode ser acedido referindo a `AZ_BATCH_TASK_DIR` variável ambiental.

    Dentro de cada **diretório de Tarefas,** o serviço Batch cria um diretório de trabalho ( `wd` ) cujo caminho único é especificado pela `AZ_BATCH_TASK_WORKING_DIR` variável ambiental. Este diretório proporciona acesso de leitura/escrita à tarefa. A tarefa pode criar, ler, atualizar e eliminar ficheiros neste diretório. Este diretório é mantido com base na restrição *RetentionTime* especificada para a tarefa.

    Os `stdout.txt` `stderr.txt` ficheiros e ficheiros são escritos na pasta **Tarefas** durante a execução da tarefa.

> [!IMPORTANT]
> Quando um nó é removido do conjunto, todos os ficheiros que estão armazenados no nó são removidos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o manuseamento e deteção de erros](error-handling.md) no Azure Batch.