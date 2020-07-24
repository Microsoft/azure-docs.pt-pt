---
title: Atualizar alvos de armazenamento Azure HPC Cache
description: Como editar alvos de armazenamento Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092461"
---
# <a name="edit-storage-targets"></a>Adicionar destinos de armazenamento

Pode remover ou modificar um alvo de armazenamento da página do portal dos **alvos** de armazenamento da cache ou utilizando o CLI Azure.

> [!TIP]
> O [vídeo Managing Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra como editar um alvo de armazenamento no portal Azure.

## <a name="remove-a-storage-target"></a>Remover um alvo de armazenamento

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover um alvo de armazenamento, selecione-o na lista e clique no botão **Eliminar.**

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilize [o alvo de armazenamento az hpc-cache para](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) eliminar um alvo de armazenamento da cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

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

No portal Azure, pode ver quais os campos que são editáveis clicando no nome alvo do armazenamento e abrindo a página de detalhes. Também pode modificar alvos de armazenamento com o Azure CLI.

![screenshot da página de edição para um alvo de armazenamento NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Atualizar um alvo de armazenamento NFS

Para um alvo de armazenamento NFS, pode atualizar várias propriedades. (Consulte a imagem acima para obter uma página de edição por exemplo.)

* **Modelo de utilização** - O modelo de utilização influencia a forma como a cache retém dados. Leia [Escolha um modelo de utilização](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.
* **Caminho virtual do espaço de nome** - O caminho que os clientes usam para montar este alvo de armazenamento. Leia [Plano do espaço de nome agregado](hpc-cache-namespace.md) para mais detalhes.
* **NFS rota de exportação** - O sistema de armazenamento exporta para usar para este caminho do espaço de nome.
* **Caminho subdirecional** - O subdirecional (sob a exportação) para associar a este caminho de espaço de nome. Deixe este campo em branco se não precisar de especificar uma subdirectória.

Cada caminho de espaço de nome precisa de uma combinação única de exportação e subdireção. Ou seja, não se pode fazer dois caminhos diferentes virados para o cliente para o mesmo diretório no sistema de armazenamento back-end.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Depois de escoar as alterações, clique **em OK** para atualizar o alvo de armazenamento ou clique em **Cancelar** para descartar alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilize o comando [az-nfs-target-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) para alterar o modelo de utilização, o caminho virtual do espaço de nome e os valores de exportação ou subdiretório NFS para um alvo de armazenamento.

* Para alterar o modelo de utilização, utilize a ``--nfs3-usage-model`` opção. Exemplo: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Para alterar a trajetória do espaço de nome, exportar ou exportar subdiretório, utilize a ``--junction`` opção.

  O ``--junction`` parâmetro utiliza estes valores:

  * ``namespace-path``- O caminho de ficheiro virtual voltado para o cliente
  * ``nfs-export``- Exportação do sistema de armazenamento para associar-se ao caminho voltado para o cliente
  * ``target-path``(opcional) - Uma subdiretória da exportação, se necessário

  Exemplo: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

O nome do cache, o nome do alvo de armazenamento e o grupo de recursos são necessários em todos os comandos de atualização.

Exemplo de comando: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Se a cache for parada ou não em estado saudável, a atualização aplica-se depois de a cache estar saudável.

---

## <a name="update-an-azure-blob-storage-target"></a>Atualize um alvo de armazenamento Azure Blob

Para um alvo de armazenamento de bolhas, pode modificar o caminho virtual do espaço de nome.

### <a name="portal"></a>[Portal](#tab/azure-portal)

A página de detalhes para um alvo de armazenamento Blob permite modificar o caminho virtual do espaço de nome.

![screenshot da página de edição para um alvo de armazenamento de bolhas](media/hpc-cache-edit-storage-blob.png)

Quando terminar, clique **em OK** para atualizar o alvo de armazenamento ou clique em **Cancelar** para descartar alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilize [a atualização az hpc-cache blob-storage-target](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) para atualizar o caminho do espaço de nome de um alvo.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Se a cache for parada ou não em estado saudável, a atualização aplicar-se-á depois de a cache estar saudável.

---

## <a name="next-steps"></a>Passos seguintes

* Leia [Os alvos de armazenamento](hpc-cache-add-storage.md) para saber mais sobre estas opções.
* Leia [Plano do espaço de nome agregado](hpc-cache-namespace.md) para mais dicas sobre a utilização de caminhos virtuais.
