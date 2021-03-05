---
title: Atualizar alvos de armazenamento Azure HPC Cache
description: Como editar alvos de armazenamento Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: f97ff1c20b7edbf24e5a2c58e22097f88883ae4f
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204036"
---
# <a name="edit-storage-targets"></a>Adicionar destinos de armazenamento

Pode remover ou modificar alvos de armazenamento com o portal Azure ou utilizando o CLI Azure.

Dependendo do tipo de armazenamento, pode modificar estes valores-alvo de armazenamento:

* Para os alvos de armazenamento blob, pode alterar o caminho do espaço de nome.

* Para os alvos de armazenamento NFS, pode alterar estes valores:

  * Caminhos do espaço de nome
  * O subdiretório de exportação ou exportação de armazenamento associado a um caminho de espaço de nome
  * Modelo de utilização

Não é possível editar o nome, o tipo ou o sistema de armazenamento de um alvo de armazenamento (recipiente Blob ou endereço NFS hostname/IP). Se precisar de alterar estas propriedades, elimine o alvo de armazenamento e crie uma substituição pelo novo valor.

> [!TIP]
> O [vídeo Managing Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra como editar um alvo de armazenamento no portal Azure.

## <a name="remove-a-storage-target"></a>Remover um alvo de armazenamento

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover um alvo de armazenamento, abra a página **de alvos de Armazenamento.** Selecione o alvo de armazenamento da lista e clique no botão **Eliminar.**

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

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

A eliminação de um alvo de armazenamento remove a associação do sistema de armazenamento com este sistema Azure HPC Cache, mas não altera o sistema de armazenamento back-end. Por exemplo, se utilizar um recipiente de armazenamento Azure Blob, o recipiente e o seu conteúdo ainda existem depois de o eliminar da cache. Pode adicionar o recipiente a uma cache Azure HPC diferente, adicioná-lo novamente a esta cache ou eliminá-lo com o portal Azure.

Quaisquer alterações de ficheiros armazenadas na cache são escritas no sistema de armazenamento de back-end antes de o alvo de armazenamento ser removido. Este processo pode demorar uma hora ou mais se muitos dados alterados estiverem na cache.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Alterar o caminho do espaço de nome de um alvo de armazenamento de bolhas

Os caminhos do espaço de nome são os caminhos que os clientes usam para montar este alvo de armazenamento. (Para saber mais, leia [Planeie o espaço de nome agregado](hpc-cache-namespace.md) e crie o espaço de nome [agregado).](add-namespace-paths.md)

O caminho do espaço de nome é a única atualização que pode fazer num alvo de armazenamento Azure Blob. Utilize o portal Azure ou o CLI Azure para alterá-lo.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Utilize a página **Namespace** para a sua Cache Azure HPC. A página do espaço de nome é descrita com mais detalhe no artigo [Configurar o espaço de nome agregado](add-namespace-paths.md).

Clique no nome do caminho que pretende alterar e crie o novo caminho na janela de edição que aparece.

![Screenshot da página do espaço de nome depois de clicar num caminho de espaço de nome Blob - os campos de edição aparecem num painel à direita](media/edit-namespace-blob.png)

Depois de escoar as alterações, clique **em OK** para atualizar o alvo de armazenamento ou clique em **Cancelar** para descartar alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

Para alterar o espaço de nome de um alvo de armazenamento de bolhas com o CLI Azure, utilize a [atualização do alvo blob-armazenamento de cache de comando az hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). Só o `--virtual-namespace-path` valor pode ser alterado.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Atualizar um alvo de armazenamento NFS

Para os alvos de armazenamento NFS, pode alterar ou adicionar caminhos virtuais de espaço de nome, alterar os valores de exportação ou subdiretórios NFS que um caminho de espaço de nome aponta e alterar o modelo de utilização.

Os detalhes estão abaixo:

* [Alterar valores agregados de espaço de nome](#change-aggregated-namespace-values) (caminho virtual do espaço de nome, exportação e subdiretório de exportação)
* [Alterar o modelo de utilização](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Alterar valores agregados de espaço de nome

Pode utilizar o portal Azure ou o CLI Azure para alterar a trajetória do espaço de identificação voltado para o cliente, a exportação de armazenamento e o subdiretório de exportação (se utilizado).

Leia as diretrizes em [Add NFS namespace paths](add-namespace-paths.md#nfs-namespace-paths) se precisar de um lembrete sobre como criar vários caminhos válidos em um alvo de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Utilize a página **Namespace** para a sua Cache Azure HPC para atualizar os valores do espaço de nome. Esta página é descrita com mais detalhe no artigo [Configurar o espaço de nome agregado](add-namespace-paths.md).

![screenshot da página do espaço de nome do portal com a página de atualização NFS aberta à direita](media/update-namespace-nfs.png)

1. Clique no nome do caminho que pretende alterar.
1. Utilize a janela de edição para escrever novos valores virtuais, de exportação ou de subdiretores.
1. Depois de escoar as alterações, clique **em OK** para atualizar o alvo de armazenamento ou **cancelar** para descartar alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

Utilize a ``--junction`` opção no comando [de atualização az hpc-cache nfs-storage-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) para alterar a trajetória do espaço de nome, exportação de NFS ou subdiretório de exportação.

O ``--junction`` parâmetro utiliza estes valores:

* ``namespace-path`` - O caminho de ficheiro virtual voltado para o cliente
* ``nfs-export`` - Exportação do sistema de armazenamento para associar-se ao caminho voltado para o cliente
* ``target-path`` (opcional) - Uma subdiretória da exportação, se necessário

Exemplo: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Deve fornecer os três valores para cada caminho na ``--junction`` declaração. Utilize os valores existentes para quaisquer valores que não queira alterar.

O nome do cache, o nome do alvo de armazenamento e o grupo de recursos também são necessários em todos os comandos de atualização.

Comando de exemplo:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Alterar o modelo de utilização

O modelo de utilização influencia a forma como a cache retém dados. Leia [Escolha um modelo de utilização](hpc-cache-add-storage.md#choose-a-usage-model) para saber mais.

Para alterar o modelo de utilização para um alvo de armazenamento NFS, utilize um destes métodos.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Altere o modelo de utilização a partir da página **de alvos de Armazenamento** no portal Azure. Clique no nome do alvo de armazenamento para alterar.

![screenshot da página de edição para um alvo de armazenamento NFS](media/edit-storage-nfs.png)

Utilize o seletor drop-down para escolher um novo modelo de utilização. Clique **em OK** para atualizar o alvo de armazenamento ou clique em **Cancelar** para descartar alterações.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

Utilize o comando [de atualização az hpc-cache nfs-target-target.](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update)

O comando de atualização é quase idêntico ao comando que utiliza para adicionar um alvo de armazenamento NFS. Consulte para [Criar um alvo de armazenamento NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) para obter detalhes e exemplos.

Para alterar o modelo de utilização, atualize a ``--nfs3-usage-model`` opção. Exemplo: ``--nfs3-usage-model WRITE_WORKLOAD_15``

O nome da cache, o nome do alvo de armazenamento e os valores do grupo de recursos também são necessários.

Se pretender verificar os nomes dos modelos de utilização, utilize a lista de modelos de utilização de cache de comando [az hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Se a cache for parada ou não em estado saudável, a atualização aplicar-se-á depois de a cache estar saudável.

---

## <a name="next-steps"></a>Passos seguintes

* Leia [Os alvos de armazenamento](hpc-cache-add-storage.md) para saber mais sobre estas opções.
* Leia [Plano do espaço de nome agregado](hpc-cache-namespace.md) para mais dicas sobre a utilização de caminhos virtuais.
