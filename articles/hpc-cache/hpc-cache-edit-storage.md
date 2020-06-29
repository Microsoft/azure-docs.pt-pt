---
title: Atualizar alvos de armazenamento Azure HPC Cache
description: Como editar alvos de armazenamento Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 1/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0fa8be58db9754c652d6e1ee5349c950a1c19109
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513844"
---
# <a name="edit-storage-targets"></a>Adicionar destinos de armazenamento

Pode remover ou modificar um alvo de armazenamento da página de **alvos** de armazenamento da cache.

> [!TIP]
> O [vídeo Managing Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra como editar um alvo de armazenamento.

## <a name="remove-a-storage-target"></a>Remover um alvo de armazenamento

Para remover um alvo de armazenamento, selecione-o na lista e clique no botão **Eliminar.**

Esta ação remove a associação-alvo de armazenamento com este sistema Azure HPC Cache, mas não altera o sistema de armazenamento back-end. Por exemplo, se utilizar um recipiente de armazenamento Azure Blob, o recipiente e o seu conteúdo ainda existem depois de o eliminar da cache. Pode adicionar o recipiente a uma cache Azure HPC diferente, adicioná-lo novamente a esta cache ou eliminá-lo com o portal Azure.

Quaisquer alterações de ficheiros armazenadas na cache são escritas no sistema de armazenamento de back-end antes de o alvo de armazenamento ser removido. Este processo pode demorar uma hora ou mais se muitos dados alterados estiverem na cache.

## <a name="update-storage-targets"></a>Atualizar alvos de armazenamento

Pode editar alvos de armazenamento para modificar algumas das suas propriedades. Propriedades diferentes são editáveis para diferentes tipos de armazenamento:

* Para os alvos de armazenamento blob, pode alterar o caminho do espaço de nome.

* Para os alvos de armazenamento NFS, pode alterar estas propriedades:

  * Caminho do espaço de nome
  * Modelo de utilização
  * Exportar
  * Subdireorisivo de exportação

Não é possível editar o nome, o tipo ou o sistema de armazenamento de um alvo de armazenamento (recipiente Blob ou endereço NFS hostname/IP). Se precisar de alterar estas propriedades, elimine o alvo de armazenamento e crie uma substituição pelo novo valor.

Para modificar um alvo de armazenamento, clique no nome alvo do armazenamento para abrir a página de detalhes. Alguns campos na página são editáveis.

![screenshot da página de edição para um alvo de armazenamento NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Atualizar um alvo de armazenamento NFS

Para um alvo de armazenamento NFS, pode atualizar várias propriedades. (Consulte a imagem acima para obter uma página de edição por exemplo.)

* **Modelo de utilização** - O modelo de utilização influencia a forma como a cache retém dados. Leia [Escolha um modelo de utilização](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.
* **Caminho virtual do espaço de nome** - O caminho que os clientes usam para montar este alvo de armazenamento. Leia [Plano do espaço de nome agregado](hpc-cache-namespace.md) para mais detalhes.
* **NFS rota de exportação** - O sistema de armazenamento exporta para usar para este caminho do espaço de nome.
* **Caminho subdirecional** - O subdirecional (sob a exportação) para associar a este caminho de espaço de nome. Deixe este campo em branco se não precisar de especificar uma subdirectória.

Cada caminho de espaço de nome precisa de uma combinação única de exportação e subdireção. Ou seja, não se pode fazer dois caminhos diferentes virados para o cliente para o mesmo diretório no sistema de armazenamento back-end.

Depois de escoar as alterações, clique **em OK** para atualizar o alvo de armazenamento ou clique em **Cancelar** para descartar alterações.

## <a name="update-an-azure-blob-storage-target"></a>Atualize um alvo de armazenamento Azure Blob

A página de detalhes para um alvo de armazenamento Blob permite modificar o caminho virtual do espaço de nome.

![screenshot da página de edição para um alvo de armazenamento de bolhas](media/hpc-cache-edit-storage-blob.png)

Quando terminar, clique **em OK** para atualizar o alvo de armazenamento ou clique em **Cancelar** para descartar alterações.

## <a name="next-steps"></a>Passos seguintes

* Leia [Os alvos de armazenamento](hpc-cache-add-storage.md) para saber mais sobre estas opções.
* Leia [Plano do espaço de nome agregado](hpc-cache-namespace.md) para mais dicas sobre a utilização de caminhos virtuais.
