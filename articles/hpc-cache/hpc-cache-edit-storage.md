---
title: Atualizar destinos de armazenamento do cache HPC do Azure
description: Como editar destinos de armazenamento do cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 757fbc166687110b9061ab95e96b0182e0ad3774
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622771"
---
# <a name="edit-storage-targets"></a>Editar destinos de armazenamento

Você pode editar os destinos de armazenamento para modificar algumas de suas propriedades. Propriedades diferentes são editáveis para um tipo diferente de armazenamento:

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
