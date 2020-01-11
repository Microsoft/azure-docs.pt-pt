---
title: Atualizar destinos de armazenamento do cache HPC do Azure
description: Como editar destinos de armazenamento do cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866995"
---
# <a name="edit-storage-targets"></a>Adicionar destinos de armazenamento

Você pode remover ou modificar um destino de armazenamento da página de **destinos de armazenamento** do cache.

## <a name="remove-a-storage-target"></a>Remover um destino de armazenamento

Para remover um destino de armazenamento, selecione-o na lista e clique no botão **excluir** .

Essa ação remove a associação de destino de armazenamento com este sistema de cache HPC do Azure, mas não altera o sistema de armazenamento de back-end. Por exemplo, se você usou um contêiner de armazenamento de BLOBs do Azure, o contêiner e seu conteúdo ainda existem depois de você excluí-lo do cache. Você pode adicionar o contêiner a um cache do HPC do Azure diferente, adicioná-lo novamente a esse cache ou excluí-lo com o portal do Azure.

Todas as alterações de arquivo armazenadas no cache são gravadas no sistema de armazenamento de back-end antes de o destino de armazenamento ser removido. Esse processo pode levar uma hora ou mais se muitos dados alterados estiverem no cache.

## <a name="update-storage-targets"></a>Atualizar destinos de armazenamento

Você pode editar os destinos de armazenamento para modificar algumas de suas propriedades. Propriedades diferentes são editáveis para diferentes tipos de armazenamento:

* Para destinos de armazenamento de BLOBs, você pode alterar o caminho do namespace.

* Para destinos de armazenamento NFS, você pode alterar essas propriedades:

  * Caminho do namespace
  * Modelo de uso
  * Exportar
  * Exportar subdiretório

Você não pode editar o nome, tipo ou sistema de armazenamento de back-end de um destino de armazenamento (contêiner de BLOB ou endereço IP/nome de host do NFS). Se você precisar alterar essas propriedades, exclua o destino de armazenamento e crie uma substituição com o novo valor.

Para modificar um destino de armazenamento, clique no nome do destino de armazenamento para abrir sua página de detalhes. Alguns campos na página são editáveis.

![captura de tela da página de edição de um destino de armazenamento NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Atualizar um destino de armazenamento NFS

Para um destino de armazenamento NFS, você pode atualizar várias propriedades. (Consulte a captura de tela acima para ver um exemplo de página de edição.)

* **Modelo de uso** -o modelo de uso influencia como o cache retém os dados. Leia [escolher um modelo de uso](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.
* **Caminho do namespace virtual** -o caminho que os clientes usam para montar esse destino de armazenamento. Leia [planejar o namespace agregado](hpc-cache-namespace.md) para obter detalhes.
* **Caminho de exportação de NFS** -a exportação do sistema de armazenamento a ser usada para este caminho de namespace.
* **Caminho de subdiretório** -o subdiretório (sob a exportação) a ser associado a este caminho de namespace. Deixe esse campo em branco se você não precisar especificar um subdiretório.

Cada caminho de namespace precisa de uma combinação exclusiva de exportação e subdiretório. Ou seja, você não pode fazer dois caminhos diferentes voltados para o cliente para exatamente o mesmo diretório no sistema de armazenamento de back-end.

Depois de fazer alterações, clique em **OK** para atualizar o destino de armazenamento ou clique em **Cancelar** para descartar as alterações.

## <a name="update-an-azure-blob-storage-target"></a>Atualizar um destino de armazenamento de BLOBs do Azure

A página de detalhes de um destino de armazenamento de BLOBs permite modificar o caminho do namespace virtual.

![captura de tela da página de edição de um destino de armazenamento de BLOBs](media/hpc-cache-edit-storage-blob.png)

Quando terminar, clique em **OK** para atualizar o destino de armazenamento ou clique em **Cancelar** para descartar as alterações.

## <a name="next-steps"></a>Passos seguintes

* Leia [Adicionar destinos de armazenamento](hpc-cache-add-storage.md) para saber mais sobre essas opções.
* Leia [planejar o namespace agregado](hpc-cache-namespace.md) para obter mais dicas sobre como usar caminhos virtuais.
