---
title: Configure o espaço agregado de nomes Azure HPC Cache
description: Como criar caminhos voltados para o cliente para armazenamento com cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 090e3f93d025fe87ad5b89a98193574595f3d632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614633"
---
# <a name="set-up-the-aggregated-namespace"></a>Configurar o espaço de nome agregado

Depois de criar alvos de armazenamento, também deve criar caminhos de espaço para o nome para eles. As máquinas de clientes usam estes caminhos virtuais para aceder a ficheiros através da cache em vez de se conectarem diretamente ao armazenamento de back-end. Este sistema permite que os administradores de cache alterem os sistemas de armazenamento sem terem de reescrever as instruções do cliente.

Leia [Planeie o espaço de nome agregado](hpc-cache-namespace.md) para saber mais sobre esta funcionalidade.

A página **Namespace** no portal Azure mostra os caminhos que os clientes usam para aceder aos seus dados através da cache. Utilize esta página para criar, remover ou alterar os caminhos do espaço de nome. Também pode configurar caminhos de espaço de nome utilizando o Azure CLI.

Todos os caminhos existentes virados para o cliente estão listados na página **Namespace.** Se um alvo de armazenamento não tiver quaisquer caminhos, não aparece na tabela.

Pode separar as colunas de tabela clicando nas setas e entender melhor o espaço de nome agregado da sua cache.

![screenshot da página do espaço de nome do portal com dois caminhos em uma mesa. Cabeçalhos de coluna: Caminho do espaço de identificação, alvo de armazenamento, trajetória de exportação e subdiretório de exportação. Os itens na primeira coluna são links clicáveis. Botões superiores: Adicionar caminho de espaço de nome, refrescar, eliminar](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>Adicione ou edite caminhos de espaço de nome virados para o cliente

Deve criar pelo menos um caminho de espaço de nome antes que os clientes possam aceder ao alvo de armazenamento. (Leia [o Monte da Cache Azure HPC](hpc-cache-mount.md) para saber mais sobre o acesso ao cliente.)

### <a name="blob-namespace-paths"></a>Caminhos de espaço de nome blob

Um alvo de armazenamento Azure Blob pode ter apenas um caminho de espaço de nome.

Siga as instruções abaixo para definir ou alterar o caminho com o portal Azure ou O Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)

A partir do portal Azure, carregue a página de definições **do Namespace.** Pode adicionar, alterar ou eliminar os caminhos do espaço de nome desta página.

* **Adicione um novo caminho:** Clique no botão **+ Adicionar** na parte superior e preencha as informações no painel de edição.

  * Selecione o alvo de armazenamento da lista de drop-down. (Nesta imagem, o alvo de armazenamento de bolhas não pode ser selecionado porque já tem um caminho de espaço de nome.)

    ![Screenshot dos novos campos de edição de espaço de nome com o seletor de alvo de armazenamento exposto](media/namespace-select-storage-target.png)

  * Para um objetivo de armazenamento Azure Blob, os caminhos de exportação e subdiretórios são automaticamente definidos para ``/`` .

* **Alterar um caminho existente:** Clique no caminho do espaço de nome. O painel de edição abre e pode modificar o caminho.

  ![Screenshot da página do espaço de nome depois de clicar num caminho de espaço de nome Blob - os campos de edição aparecem num painel à direita](media/edit-namespace-blob.png)

* **Excluir um caminho de espaço de nome:** Selecione a caixa de verificação à esquerda do caminho e clique no botão **Eliminar.**

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Ao utilizar o Azure CLI, deve adicionar um caminho de espaço de nome quando criar o alvo de armazenamento. Leia [Adicione um novo alvo de armazenamento Azure Blob](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) para mais detalhes.

Para atualizar o caminho do espaço de nome do alvo, utilize o comando [de atualização az-cache blob-storage-target.](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) Os argumentos para o comando de atualização são semelhantes aos argumentos no comando criar, exceto que não passa o nome do recipiente ou a conta de armazenamento.

Não é possível apagar um caminho de espaço de nome de um alvo de armazenamento de bolhas com o CLI Azure, mas pode substituir o caminho com um valor diferente.

---

### <a name="nfs-namespace-paths"></a>Caminhos de espaço de nome NFS

Um alvo de armazenamento NFS pode ter vários caminhos virtuais, desde que cada caminho represente uma exportação ou subdiretório diferente no mesmo sistema de armazenamento.

Ao planear o seu espaço de nome para um alvo de armazenamento NFS, lembre-se que cada caminho deve ser único, e não pode ser um subdireccionário de outro caminho de espaço de nome. Por exemplo, se tiver um caminho de espaço de nome que é ``/parent-a`` chamado, também não pode criar caminhos de espaço de nome como ``/parent-a/user1`` e ``/parent-a/user2`` . Esses caminhos de diretório já estão acessíveis no espaço de nomes como subdiretorias de ``/parent-a`` .

Todos os caminhos do espaço de nome para um sistema de armazenamento NFS são criados num alvo de armazenamento. A maioria das configurações de cache pode suportar até dez caminhos de espaço de nome por alvo de armazenamento, mas configurações maiores podem suportar até 20.

Esta lista mostra o número máximo de caminhos de espaço de nome por configuração.

* Até 2 GB/s de produção:

  * Cache 3 TB - 10 caminhos de espaço de nome
  * Cache 6 TB - 10 caminhos de espaço de nome
  * Cache 23 TB - 20 caminhos de espaço de nome

* Até 5 GB/s de produção:

  * Cache 6 TB - 10 caminhos de espaço de nome
  * 12 Cache TB - 10 caminhos de espaço de nome
  * 24 cache TB -20 caminhos de espaço de nome

* Até 8 GB/s de produção:

  * 12 Cache TB - 10 caminhos de espaço de nome
  * Cache 24 TB - 10 caminhos de espaço de nome
  * Cache 48 TB - 20 caminhos de espaço de nome

Para cada caminho de espaço de nome NFS, forneça o caminho voltado para o cliente, a exportação do sistema de armazenamento e, opcionalmente, uma subdiretório de exportação.

### <a name="portal"></a>[Portal](#tab/azure-portal)

A partir do portal Azure, carregue a página de definições **do Namespace.** Pode adicionar, editar ou eliminar os caminhos do espaço de nome desta página.

* **Para adicionar um novo caminho:** Clique no botão **+ Adicionar** na parte superior e preencha as informações no painel de edição.
* **Para mudar um caminho existente:** Clique no caminho do espaço de nome. O painel de edição abre e pode modificar o caminho.
* **Para eliminar um caminho de espaço de nome:** Selecione a caixa de verificação à esquerda do caminho e clique no botão **Eliminar.**

Preencha estes valores para cada caminho do espaço de nome:

* **Caminho do espaço de** nome - O caminho do ficheiro virado para o cliente.

* **Alvo de armazenamento** - Se criar um novo caminho de espaço de nome, selecione um alvo de armazenamento a partir do menu suspenso.

* **Caminho de exportação** - Insira o caminho para a exportação da NFS. Certifique-se de que digita corretamente o nome de exportação - o portal valida a sintaxe para este campo, mas não verifica a exportação até submeter a alteração.

* **Subdiretório de exportação** - Se quiser que este caminho monte um subdiretório específico da exportação, insira-o aqui. Se não, deixe este campo em branco.

![screenshot da página do espaço de nome portal com a página de atualização aberta à direita](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Ao utilizar o Azure CLI, deve adicionar pelo menos um caminho de espaço de nome quando criar o alvo de armazenamento. Leia [Adicione um novo alvo de armazenamento NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) para mais detalhes.

Para atualizar o caminho do espaço de nome do alvo ou para adicionar caminhos adicionais, utilize o comando [de atualização az hpc-cache nfs-storage-target.](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) Utilize a ``--junction`` opção para especificar todos os caminhos do espaço de nome que deseja.

As opções utilizadas para o comando de atualização são semelhantes ao comando "criar", exceto que não passas as informações do sistema de armazenamento (endereço IP ou nome de anfitrião), e o modelo de utilização é opcional. Leia [Adicione um novo alvo de armazenamento NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) para mais detalhes sobre a sintaxe da ``--junction`` opção.

---

## <a name="next-steps"></a>Passos seguintes

Depois de criar o espaço de nome agregado para os seus alvos de armazenamento, pode montar clientes na cache. Leia estes artigos para saber mais.

* [Montar o Azure HPC Cache](hpc-cache-mount.md)
* [Mover dados para o armazenamento da Azure Blob](hpc-cache-ingest.md)
