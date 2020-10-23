---
title: Adicione armazenamento a uma Cache Azure HPC
description: Como definir alvos de armazenamento para que o seu Cache Azure HPC possa utilizar o seu sistema NFS no local ou recipientes Azure Blob para armazenamento de ficheiros a longo prazo
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: c1edbaf394a4abd36e47843a6f419eb9d62f08d7
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340313"
---
# <a name="add-storage-targets"></a>Adicionar destinos de armazenamento

*Os alvos de armazenamento* são armazenamento de back-end para ficheiros que são acedidos através de uma Cache Azure HPC. Pode adicionar armazenamento NFS (como um sistema de hardware no local) ou armazenar dados em Azure Blob.

Pode definir até dez alvos de armazenamento diferentes para uma cache. A cache apresenta todos os alvos de armazenamento num espaço de nome agregado.

Os caminhos do espaço de nome são configurados separadamente depois de adicionar os alvos de armazenamento. Em geral, um alvo de armazenamento NFS pode ter até dez caminhos de espaço de nome, ou mais para algumas configurações grandes. Leia [os caminhos do espaço de nome NFS](add-namespace-paths.md#nfs-namespace-paths) para mais detalhes.

Lembre-se que as exportações de armazenamento devem estar acessíveis a partir da rede virtual do seu cache. Para armazenamento de hardware no local, poderá ser necessário configurar um servidor DNS que possa resolver os nomes de anfitriões para o acesso ao armazenamento NFS. Leia mais no [acesso ao DNS.](hpc-cache-prerequisites.md#dns-access)

Adicione os alvos de armazenamento depois de criar o seu cache. Acompanhe este processo:

1. [Criar a cache](hpc-cache-create.md)
1. Definir um alvo de armazenamento (informação neste artigo)
1. [Crie os caminhos voltados para o cliente](add-namespace-paths.md) (para o [espaço de nome agregado)](hpc-cache-namespace.md)

O procedimento para adicionar um alvo de armazenamento é ligeiramente diferente dependendo se você está adicionando armazenamento Azure Blob ou uma exportação NFS. Os detalhes para cada um estão abaixo.

Clique na imagem abaixo para ver uma [demonstração](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) de vídeo de criar uma cache e adicionar um alvo de armazenamento a partir do portal Azure.

[![miniatura de vídeo: Azure HPC Cache: Configuração (clique para visitar a página de vídeo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Adicione um novo alvo de armazenamento Azure Blob

Um novo alvo de armazenamento Blob precisa de um recipiente Blob vazio ou um recipiente que esteja povoado com dados no formato do sistema de ficheiros cloud Cache Azure HPC. Leia mais sobre o pré-carregamento de um recipiente Blob em [Move para o armazenamento de Azure Blob](hpc-cache-ingest.md).

O portal Azure **Adicionar página-alvo de armazenamento** inclui a opção de criar um novo recipiente Blob pouco antes de o adicionar.

### <a name="portal"></a>[Portal](#tab/azure-portal)

A partir do portal Azure, abra a sua instância de cache e clique em **Apontar os alvos de armazenamento** na barra lateral esquerda.

![screenshot das definições > página alvo de armazenamento, com dois alvos de armazenamento existentes numa tabela e um destaque em torno do botão alvo de armazenamento + adicionar acima da tabela](media/add-storage-target-button.png)

A página **de alvos de Armazenamento** lista todos os alvos existentes e dá um link para adicionar um novo.

Clique no botão **de destino de armazenamento Adicionar.**

![screenshot da página alvo de armazenamento de adicionar, preenchida com informações para um novo alvo de armazenamento Azure Blob](media/hpc-cache-add-blob.png)

Para definir um recipiente Azure Blob, insira esta informação.

* **Nome alvo de armazenamento** - Desconfie um nome que identifique este alvo de armazenamento na Cache Azure HPC.
* **Tipo alvo** - Escolha **Blob**.
* **Conta de armazenamento** - Selecione a conta que pretende utilizar.

  Terá de autorizar a cache para aceder à conta de armazenamento, tal como descrito no [Add the access roles](#add-the-access-control-roles-to-your-account).

  Para obter informações sobre o tipo de conta de armazenamento que pode utilizar, leia [os requisitos de armazenamento blob](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Recipiente de armazenamento** - Selecione o recipiente Blob para este alvo ou clique em **Criar novo**.

  ![screenshot do diálogo para especificar o nome e o nível de acesso (privado) para novo recipiente](media/add-blob-new-container.png)

Quando terminar, clique **em OK** para adicionar o alvo de armazenamento.

> [!NOTE]
> Se a firewall da sua conta de armazenamento estiver definida para restringir o acesso apenas a "redes selecionadas", utilize a solução temporária documentada no Trabalho em [torno das definições de firewall da conta de armazenamento Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Adicione as funções de controlo de acesso à sua conta

A Azure HPC Cache utiliza [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/index.yml) para autorizar o serviço de cache a aceder à sua conta de armazenamento para alvos de armazenamento Azure Blob.

O proprietário da conta de armazenamento deve adicionar explicitamente as funções [Contribuidor de Conta de Armazenamento](../role-based-access-control/built-in-roles.md#storage-account-contributor) e Colaborador de [Dados blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) para o utilizador "HPC Cache Resource Provider".

Pode fazê-lo com antecedência ou clicando num link na página onde adiciona um alvo de armazenamento Blob. Tenha em mente que pode levar até cinco minutos para que as definições de função se propaguem através do ambiente Azure, por isso deve esperar alguns minutos depois de adicionar as funções antes de criar um alvo de armazenamento.

Passos para adicionar os papéis do Azure:

1. Abra a página **de Controlo de Acesso (IAM)** para a conta de armazenamento. (O link na **página-alvo** de armazenamento adicionar abre automaticamente esta página para a conta selecionada.)

1. Clique **+** no topo da página e escolha **Adicionar uma atribuição de função**.

1. Selecione a função "Colaborador de Conta de Armazenamento" da lista.

1. No **acesso** do Designado ao campo, deixe o valor predefinido selecionado ("Utilizador Azure AD, grupo ou principal de serviço").  

1. No campo **Selecionar,** procure "hpc".  Esta cadeia deve coincidir com um principal de serviço, denominado "HPC Cache Resource Provider". Clique no principal para selecioná-lo.

   > [!NOTE]
   > Se uma pesquisa por "hpc" não funcionar, tente usar a cadeia "armazenamento" em vez disso. Os utilizadores que participaram em pré-visualizações (antes de GA) poderão ter de usar o nome mais antigo para o principal serviço.

1. Clique no botão **Guardar** na parte inferior.

1. Repita este processo para atribuir a função "Storage Blob Data Contributor".  

![screenshot de adicionar atribuição de função GUI](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>Pré-requisito: Acesso à conta de armazenamento

Antes de adicionar um alvo de armazenamento de bolhas, verifique se a cache tem as funções corretas para aceder à conta de armazenamento, e que as definições de firewall permitirão a criação do alvo de armazenamento.

A Azure HPC Cache utiliza [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/index.yml) para autorizar o serviço de cache a aceder à sua conta de armazenamento para alvos de armazenamento Azure Blob.

O proprietário da conta de armazenamento deve adicionar explicitamente as funções [Contribuidor de Conta de Armazenamento](../role-based-access-control/built-in-roles.md#storage-account-contributor) e Colaborador de [Dados blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) para o utilizador "HPC Cache Resource Provider".

A criação de alvos de armazenamento falhará se a cache não tiver estes papéis.

Pode levar até cinco minutos para que as definições de função se propaguem através do ambiente Azure, por isso deve esperar alguns minutos depois de adicionar as funções antes de criar um alvo de armazenamento.

Leia [Adicionar ou remover atribuições de funções Azure utilizando O Azure CLI](../role-based-access-control/role-assignments-cli.md) para obter instruções detalhadas.

Verifique também as definições de firewall da sua conta de armazenamento. Se a firewall estiver definida para restringir o acesso apenas a "redes selecionadas", a criação do alvo de armazenamento pode falhar. Utilize a solução-solução documentada em [Work around Blob storage account firewall .](hpc-cache-blob-firewall-fix.md)

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Adicione um alvo de armazenamento de bolhas com Azure CLI

Utilize a interface [az hpc-cache blob-storage-target adicionar](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) interface para definir um alvo de armazenamento Azure Blob.

> [!NOTE]
> Os comandos Azure CLI exigem atualmente que crie um caminho de espaço de nome quando adicionar um alvo de armazenamento. Isto é diferente do processo utilizado com a interface do portal Azure.

Além dos parâmetros padrão do grupo de recursos e do nome cache, deve fornecer estas opções para o alvo de armazenamento:

* ``--name`` - Definir um nome que identifique este alvo de armazenamento na Cache Azure HPC.

* ``--storage-account`` - O identificador de conta, neste formulário: /subscrições/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAcounts/*<account_name>*

  Para obter informações sobre o tipo de conta de armazenamento que pode utilizar, leia [os requisitos de armazenamento blob](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name`` - Especificar o nome do recipiente a utilizar para este alvo de armazenamento.

* ``--virtual-namespace-path`` - Desaponte o caminho do ficheiro virado para o cliente para este alvo de armazenamento. Encerra caminhos em aspas. Leia [Plano do espaço de nome agregado](hpc-cache-namespace.md) para saber mais sobre a funcionalidade de espaço de nome virtual.

Comando de exemplo:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Adicione um novo alvo de armazenamento NFS

Um alvo de armazenamento NFS tem configurações diferentes de um alvo de armazenamento Blob. A definição do modelo de utilização ajuda a cache a cache eficientemente cache dados deste sistema de armazenamento.

![Screenshot de adicionar página-alvo de armazenamento com o alvo NFS definido](media/add-nfs-target.png)

> [!NOTE]
> Antes de criar um alvo de armazenamento NFS, certifique-se de que o seu sistema de armazenamento está acessível a partir da Cache Azure HPC e cumpre os requisitos de permissão. A criação do alvo de armazenamento falhará se a cache não conseguir aceder ao sistema de armazenamento. Leia [os requisitos de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) e [a configuração do NAS de resolução de problemas e problemas de alvo de armazenamento NFS](troubleshoot-nas.md) para mais detalhes.

### <a name="choose-a-usage-model"></a>Escolha um modelo de utilização
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Quando cria um alvo de armazenamento que aponta para um sistema de armazenamento NFS, tem de escolher o modelo de utilização para esse alvo. Este modelo determina como os seus dados são em cache.

Existem três opções:

* **Leia escritas pesadas e pouco frequentes** - Utilize esta opção se quiser acelerar o acesso de leitura a ficheiros que são estáticos ou raramente alterados.

  Esta opção caches ficheiros que os clientes lêem, mas passa a escrever para o armazenamento back-end imediatamente. Os ficheiros armazenados na cache nunca são comparados com os ficheiros do volume de armazenamento NFS.

  Não utilize esta opção se existir o risco de um ficheiro poder ser modificado diretamente no sistema de armazenamento sem antes o escrever para a cache. Se isso acontecer, a versão em cache do ficheiro nunca será atualizada com alterações a partir da parte de trás, e o conjunto de dados pode tornar-se inconsistente.

* **Mais de 15% escreve** - Esta opção acelera tanto a leitura como a gravação. Ao utilizar esta opção, todos os clientes devem aceder aos ficheiros através da Cache Azure HPC em vez de montarem o armazenamento de back-end diretamente. Os ficheiros em cache terão alterações recentes que não são armazenadas na parte de trás.

  Neste modelo de utilização, os ficheiros na cache não são verificados com os ficheiros no armazenamento de back-end. Presume-se que a versão em cache do ficheiro é mais atual. Um ficheiro modificado na cache é escrito no sistema de armazenamento de back-end depois de ter estado na cache durante uma hora sem alterações adicionais.

* **Os clientes escrevem para o alvo NFS, contornando a cache** - Escolha esta opção se algum cliente no seu fluxo de trabalho escrever dados diretamente para o sistema de armazenamento sem primeiro escrever para a cache. Os ficheiros que os clientes solicitam estão em cache, mas quaisquer alterações nesses ficheiros do cliente são imediatamente repercutidos no sistema de armazenamento back-end.

  Com este modelo de utilização, os ficheiros na cache são frequentemente verificados com as versões back-end para obter atualizações. Esta verificação permite que os ficheiros sejam alterados fora da cache, mantendo a consistência dos dados.

Esta tabela resume as diferenças do modelo de utilização:

| Modelo de utilização                   | Modo caching | Verificação de back-end | Atraso máximo de desatrada |
|-------------------------------|--------------|-----------------------|--------------------------|
| Leia escritos pesados e pouco frequentes | Ler         | Nunca                 | Nenhum                     |
| Mais de 15% escreve       | Leitura/escrita   | Nunca                 | Uma hora                   |
| Os clientes contornam a cache      | Ler         | 30 segundos            | Nenhum                     |

### <a name="create-an-nfs-storage-target"></a>Criar um alvo de armazenamento NFS

### <a name="portal"></a>[Portal](#tab/azure-portal)

A partir do portal Azure, abra a sua instância de cache e clique em **Apontar os alvos de armazenamento** na barra lateral esquerda.

![screenshot das definições > página alvo de armazenamento, com dois alvos de armazenamento existentes numa tabela e um destaque em torno do botão alvo de armazenamento + adicionar acima da tabela](media/add-storage-target-button.png)

A página **de alvos de Armazenamento** lista todos os alvos existentes e dá um link para adicionar um novo.

Clique no botão **de destino de armazenamento Adicionar.**

![Screenshot de adicionar página-alvo de armazenamento com o alvo NFS definido](media/add-nfs-target.png)

Forneça estas informações para um alvo de armazenamento apoiado pela NFS:

* **Nome alvo de armazenamento** - Desconfie um nome que identifique este alvo de armazenamento na Cache Azure HPC.

* **Tipo alvo** - Escolha **NFS**.

* **Nome anfitrião** - Introduza o endereço IP ou o nome de domínio totalmente qualificado para o seu sistema de armazenamento NFS. (Utilize um nome de domínio apenas se o seu cache tiver acesso a um servidor DNS que possa resolver o nome.)

* **Modelo de utilização** - Escolha um dos perfis de caching de dados com base no seu fluxo de trabalho, descrito em [Escolha um modelo de utilização](#choose-a-usage-model) acima.

Quando terminar, clique **em OK** para adicionar o alvo de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilize o comando Azure CLI [az hpc-cache nfs-storage-target adicionar](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) para criar o alvo de armazenamento.

> [!NOTE]
> Os comandos Azure CLI exigem atualmente que crie um caminho de espaço de nome quando adicionar um alvo de armazenamento. Isto é diferente do processo utilizado com a interface do portal Azure.

Forneça estes valores para além do nome cache e do grupo de recursos cache:

* ``--name`` - Definir um nome que identifique este alvo de armazenamento na Cache Azure HPC.
* ``--nfs3-target`` - O endereço IP do seu sistema de armazenamento NFS. (Pode utilizar aqui um nome de domínio totalmente qualificado se o seu cache tiver acesso a um servidor DNS que possa resolver o nome.)
* ``--nfs3-usage-model`` - Um dos perfis de caching de dados, descritos em [Escolha um modelo de utilização,](#choose-a-usage-model)acima.

  Verifique os nomes dos modelos de utilização com a [lista de modelos de utilização az-cache de](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list)comando.

* ``--junction`` - O parâmetro de junção liga o caminho do ficheiro virtual voltado para o cliente com uma trajetória de exportação no sistema de armazenamento.

  Um alvo de armazenamento NFS pode ter vários caminhos virtuais, desde que cada caminho represente uma exportação ou subdiretório diferente no mesmo sistema de armazenamento. Crie todos os caminhos para um sistema de armazenamento num alvo de armazenamento.

  Pode [adicionar e editar os caminhos do espaço de nome](add-namespace-paths.md) num alvo de armazenamento a qualquer momento.

  O ``--junction`` parâmetro utiliza estes valores:

  * ``namespace-path`` - O caminho de ficheiro virtual voltado para o cliente
  * ``nfs-export`` - Exportação do sistema de armazenamento para associar-se ao caminho voltado para o cliente
  * ``target-path`` (opcional) - Uma subdiretória da exportação, se necessário

  Exemplo: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Leia [Configurar o espaço de nome agregado](hpc-cache-namespace.md) para saber mais sobre a funcionalidade de espaço de nome virtual.

Comando de exemplo:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Resultado:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="view-storage-targets"></a>Ver alvos de armazenamento

Pode utilizar o portal Azure ou o CLI Azure para mostrar os alvos de armazenamento já definidos para o seu cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

A partir do portal Azure, abra a sua instância de cache e clique em **Alvos de Armazenamento**, que está sob a direção de Definições na barra lateral esquerda. A página de alvos de armazenamento lista todos os alvos e controlos existentes para adicioná-los ou apagá-los.

Clique no nome de um alvo de armazenamento para abrir a página de detalhes.

Leia [os alvos de armazenamento editar](hpc-cache-edit-storage.md) para saber mais.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Utilize a opção [az hpc-cache-target-target](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) para mostrar os alvos de armazenamento existentes para uma cache. Forneça o nome da cache e o grupo de recursos (a não ser que o tenha definido globalmente).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Use [az hpc-cache-target-target para](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) ver detalhes sobre um alvo de armazenamento particular. (Especificar o alvo de armazenamento pelo nome.)

Exemplo:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Passos seguintes

Depois de criar metas de armazenamento, continue com estas tarefas para ter o seu cache pronto a usar:

* [Configurar o espaço de nome agregado](add-namespace-paths.md)
* [Montar o Azure HPC Cache](hpc-cache-mount.md)
* [Mover dados para o armazenamento da Azure Blob](hpc-cache-ingest.md)

Se precisar de atualizar quaisquer definições, pode [editar um alvo de armazenamento](hpc-cache-edit-storage.md).