---
title: Atualizar alvos de armazenamento de cache Azure HPC
description: Como editar alvos de armazenamento azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75866995"
---
# <a name="edit-storage-targets"></a>Adicionar destinos de armazenamento

Pode remover ou modificar um alvo de armazenamento da página de alvos de **armazenamento** da cache.

## <a name="remove-a-storage-target"></a>Remover um alvo de armazenamento

Para remover um alvo de armazenamento, selecione-o na lista e clique no botão **Eliminar.**

Esta ação remove a associação de alvos de armazenamento com este sistema de cache Azure HPC, mas não altera o sistema de armazenamento de back-end. Por exemplo, se utilizou um recipiente de armazenamento Azure Blob, o recipiente e o seu conteúdo ainda existem depois de o eliminar da cache. Pode adicionar o recipiente a uma cache Azure HPC diferente, adicioná-lo novamente a esta cache ou eliminá-lo com o portal Azure.

Quaisquer alterações de ficheiro armazenadas na cache são escritas no sistema de armazenamento traseiro antes de o alvo de armazenamento ser removido. Este processo pode demorar uma hora ou mais se muitos dados alterados estiverem na cache.

## <a name="update-storage-targets"></a>Atualizar alvos de armazenamento

Pode editar alvos de armazenamento para modificar algumas das suas propriedades. Diferentes propriedades são editáveis para diferentes tipos de armazenamento:

* Para alvos de armazenamento Blob, pode alterar o caminho do espaço de nome.

* Para os alvos de armazenamento NFS, pode alterar estas propriedades:

  * Caminho espaço de nome
  * Modelo de utilização
  * Exportar
  * Subdireção à exportação

Não é possível editar o nome, tipo ou sistema de armazenamento de um alvo de armazenamento (recipiente Blob ou endereço de anfitrião/IP NFS). Se precisar de alterar estas propriedades, elimine o alvo de armazenamento e crie uma substituição com o novo valor.

Para modificar um alvo de armazenamento, clique no nome do alvo de armazenamento para abrir a sua página de detalhes. Alguns campos na página são editáveis.

![screenshot da página de edição para um alvo de armazenamento NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Atualizar um alvo de armazenamento NFS

Para um alvo de armazenamento NFS, pode atualizar várias propriedades. (Consulte a imagem acima para uma página de edição de exemplo.)

* Modelo de **utilização** - O modelo de utilização influencia a forma como a cache retém dados. Leia Escolha um modelo de [utilização](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.
* **Caminho de espaço** de nome virtual - O caminho que os clientes usam para montar este alvo de armazenamento. Leia [Planilique o espaço](hpc-cache-namespace.md) de nome agregado para mais detalhes.
* **NFS caminho de exportação** - O sistema de armazenamento exporta para uso para este caminho espaço de nome.
* Caminho do **subdiretório** - O subdiretório (sob a exportação) para associar a este caminho espaço de nome. Deixe este campo em branco se não precisar especificar um subdiretório.

Cada caminho espaço-nome precisa de uma combinação única de exportação e subdiretório. Ou seja, não se pode fazer dois caminhos diferentes virados para o cliente para o mesmo diretório no sistema de armazenamento de back-end.

Depois de efeílio alterações, clique em **OK** para atualizar o alvo de armazenamento ou clique **em Cancelar** para descartar alterações.

## <a name="update-an-azure-blob-storage-target"></a>Atualizar um alvo de armazenamento De Blob Azure

A página de detalhes para um alvo de armazenamento Blob permite modificar o caminho do espaço de nome virtual.

![screenshot da página de edição para um alvo de armazenamento blob](media/hpc-cache-edit-storage-blob.png)

Quando terminar, clique em **OK** para atualizar o alvo de armazenamento ou clique **em Cancelar** para descartar alterações.

## <a name="next-steps"></a>Passos seguintes

* Leia [Adicionar alvos](hpc-cache-add-storage.md) de armazenamento para saber mais sobre estas opções.
* Leia [Planar o espaço](hpc-cache-namespace.md) de nome agregado para obter mais dicas sobre a utilização de caminhos virtuais.
