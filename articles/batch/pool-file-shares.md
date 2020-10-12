---
title: Azure partilha de ficheiros para piscinas Azure Batch
description: Como montar uma partilha de Ficheiros Azure a partir de nós de computação num conjunto Linux ou Windows em Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 6bbaba20512a17de563e74ba095057c5c3f71f6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87986428"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Use uma partilha de ficheiros Azure com uma piscina de Lote

[O Azure Files](../storage/files/storage-files-introduction.md) oferece ações de ficheiros totalmente geridas na nuvem que estão acessíveis através do protocolo Bloco de Mensagens do Servidor (SMB). Este artigo fornece informações e exemplos de código para montagem e utilização de uma partilha de ficheiros Azure nos nós de cálculo de piscina.

## <a name="considerations-for-use-with-batch"></a>Considerações a utilizar com Batch

* Considere usar uma partilha de ficheiros Azure quando tiver piscinas que executam um número relativamente baixo de tarefas paralelas se utilizar ficheiros Azure não premium. Reveja os alvos de desempenho e escala para determinar se os Ficheiros Azure (que utiliza uma conta de Armazenamento Azure) devem ser [utilizados,](../storage/files/storage-files-scale-targets.md) dado o tamanho esperado da piscina e o número de ficheiros de ativos. 

* As ações de ficheiros Azure são [rentáveis](https://azure.microsoft.com/pricing/details/storage/files/) e podem ser configuradas com a replicação de dados para outra região, pelo que são globalmente redundantes. 

* Pode montar uma partilha de ficheiros Azure simultaneamente a partir de um computador no local. No entanto, certifique-se de que compreende [as implicações de conuscção,](../storage/common/storage-concurrency.md) especialmente quando utilizar ASP de REST.

* Consulte também as [considerações gerais de planeamento](../storage/files/storage-files-planning.md) das ações de ficheiros Azure.


## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

[Crie uma parte de ficheiro](../storage/files/storage-how-to-create-file-share.md) numa conta de armazenamento que esteja ligada à sua conta Batch ou numa conta de armazenamento separada.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Monte uma partilha de arquivo Azure em uma piscina de lote

Consulte a documentação sobre como [montar um sistema de ficheiros virtual numa piscina de Lote](virtual-file-mount.md).

## <a name="next-steps"></a>Passos seguintes

* Para outras opções para ler e escrever dados em Batch, consulte [a saída de trabalho e tarefa de Persist](batch-task-output.md).
* Consulte também o conjunto de [ferramentas](https://github.com/Azure/batch-shipyard/tree/master/recipes) [do Estaleiro batch,](https://github.com/Azure/batch-shipyard) que inclui receitas de estaleiro para implantar sistemas de ficheiros para cargas de carga de contentores de lotes.
