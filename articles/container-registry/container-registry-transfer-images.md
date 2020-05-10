---
title: Transferir artefactos
description: Transfira coleções de imagens ou outros artefactos de um registo de contentores para outro registo através da criação de um gasoduto de transferência utilizando contas de armazenamento Azure
ms.topic: article
ms.date: 05/08/2020
ms.custom: ''
ms.openlocfilehash: fd551671422931a51f5aa6468de87e28e3a81b5b
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006329"
---
# <a name="transfer-artifacts-to-another-registry"></a>Transferir artefactos para outro registo

Este artigo mostra como transferir coleções de imagens ou outros artefactos de registo de um registo de contentores Azure para outro registo. A fonte e os registos-alvo podem estar nas mesmas ou diferentes subscrições, inquilinos do Diretório Ativo, nuvens Azure ou nuvens fisicamente desligadas. 

Para transferir artefactos, cria-se um pipeline de *transferência* que replica artefactos entre dois registos utilizando [o armazenamento de blob:](../storage/blobs/storage-blobs-introduction.md)

* Os artefactos de um registo de origem são exportados para uma bolha numa conta de armazenamento de fonte 
* A bolha é copiada da conta de armazenamento de origem para uma conta de armazenamento alvo
* A bolha na conta de armazenamento alvo é importada como artefactos no registo-alvo. Pode configurar o gasoduto de importação para acionar sempre que a bolha do artefacto atualiza no armazenamento do alvo.

A transferência é ideal para copiar conteúdo entre dois registos de contentores Azure em nuvens fisicamente desligadas, mediadas por contas de armazenamento em cada nuvem. Para cópia de imagem de registos de contentores em nuvens conectadas, incluindo Docker Hub e outros fornecedores de nuvem, é recomendado a importação de [imagem.](container-registry-import-images.md)

Neste artigo, você usa implementações de modelo seleção de recursos azure para criar e executar o pipeline de transferência. O Azure CLI é utilizado para fornecer os recursos associados, tais como segredos de armazenamento. Recomenda-se a versão Azure CLI 2.2.0 ou posterior. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure][azure-cli].

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte os níveis de registo de [contentores de Azure](container-registry-skus.md).

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

* **Registos de contentores** - É necessário um registo de origem existente com artefactos para transferir e um registo-alvo. A transferência de ACR destina-se a movimentos através de nuvens fisicamente desligadas. Para testes, a fonte e os registos-alvo podem estar na mesma ou numa subscrição Azure diferente, inquilino do Diretório Ativo ou nuvem. Se precisar de criar um registo, consulte [Quickstart: Crie um registo de contentores privado suster o Azure CLI](container-registry-get-started-azure-cli.md). 
* **Contas de armazenamento** - Crie contas de armazenamento de origem e alvo numa subscrição e localização à sua escolha. Para efeitos de teste, pode utilizar as mesmas subscrições ou subscrições que a sua fonte e registos-alvo. Para cenários de nuvem cruzada, normalmente cria-se uma conta de armazenamento separada em cada nuvem. Se necessário, crie as contas de armazenamento com o [AZURE CLI](../storage/common/storage-account-create.md?tabs=azure-cli) ou outras ferramentas. 

  Crie um recipiente de bolha para transferência de artefactos em cada conta. Por exemplo, criar um recipiente chamado *transferência*. Dois ou mais gasodutos de transferência podem partilhar a mesma conta de armazenamento, mas devem utilizar diferentes miras de contentores de armazenamento.
* **Cofres-chave** - São necessários cofres chave para armazenar segredos de token SAS usados para aceder a fontes e contas de armazenamento de alvos. Crie os cofres chave de origem e alvo na mesma subscrição ou subscrições do Azure que a sua fonte e registos de alvos. Se necessário, crie cofres chave com o [AZURE CLI](../key-vault/quick-create-cli.md) ou outras ferramentas.
* **Variáveis ambientais** - Por exemplo, os comandos neste artigo, definir as seguintes variáveis ambientais para a fonte e ambientes-alvo. Todos os exemplos estão formatados para a concha bash.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

Cria-se os seguintes três recursos de pipeline para transferência de imagem entre registos. Todos são criados usando operações PUT. Estes recursos operam na sua *fonte* e *registos-alvo* e contas de armazenamento. 

A autenticação de armazenamento utiliza tokens SAS, geridos como segredos em cofres chave. Os oleodutos usam identidades geridas para ler os segredos nos cofres.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** - Recurso duradouro que contém informações de alto nível sobre o registo de *origem* e a conta de armazenamento. Estas informações incluem o recipiente de armazenamento de fonte URI e o cofre chave que gere o token SAS de origem. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** - Recurso duradouro que contém informações de alto nível sobre o *registo-alvo* e a conta de armazenamento. Estas informações incluem o recipiente de armazenamento de armazenamento alvo URI e o cofre chave que gere o token SAS alvo. Um gatilho de importação é ativado por defeito, pelo que o gasoduto funciona automaticamente quando uma bolha de artefactos pousa no recipiente de armazenamento alvo. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** - Recurso utilizado para invocar um recurso ExportPipeline ou ImportPipeline.  
  * Executa manualmente o ExportPipeline criando um recurso PipelineRun e especificando os artefactos para exportar.  
  * Se um gatilho de importação estiver ativado, o ImportPipeline funciona automaticamente. Também pode ser executado manualmente utilizando um PipelineRun. 
  * Atualmente, um máximo de **10 artefactos** pode ser transferido com cada PipelineRun.

### <a name="things-to-know"></a>Aspetos importantes
* O ExportPipeline e o ImportPipeline estarão normalmente em diferentes inquilinos do Diretório Ativo associados à origem e às nuvens de destino. Este cenário requer identidades geridas separadas e cofres-chave para os recursos de exportação e importação. Para efeitos de teste, estes recursos podem ser colocados na mesma nuvem, partilhando identidades.
* Os exemplos do oleoduto criam identidades geridas atribuídas ao sistema para aceder aos segredos do cofre chave. ExportPipelines e ImportPipelines também suportam identidades atribuídas ao utilizador. Neste caso, deve configurar os cofres-chave com políticas de acesso para as identidades. 

## <a name="create-and-store-sas-keys"></a>Criar e armazenar chaves SAS

A transferência utiliza fichas de assinatura de acesso partilhado (SAS) para aceder às contas de armazenamento nos ambientes de origem e alvo. Gere e armazene tokens conforme descrito nas seguintes secções.  

### <a name="generate-sas-token-for-export"></a>Gerar ficha SAS para exportação

Executar o comando de [armazenamento az generate-sas][az-storage-container-generate-sas] para gerar um token SAS para o recipiente na conta de armazenamento de origem, usado para exportação de artefactos.

*Permissões simbólicas recomendadas*: Ler, Escrever, Listar, Adicionar. 

No exemplo seguinte, a saída de comando é atribuída à variável ambiente EXPORT_SAS, prefixada com o caráter '?'. Atualize `--expiry` o valor para o seu ambiente:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Armazenar ficha SAS para exportação

Guarde o token SAS no cofre chave Azure de origem utilizando [um conjunto secreto az keyvault:][az-keyvault-secret-set]

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Gerar ficha SAS para importação

Executar o comando de [armazenamento az generate-sas][az-storage-container-generate-sas] para gerar um token SAS para o recipiente na conta de armazenamento alvo, usado para a importação de artefactos.

*Permissões simbólicas recomendadas*: Ler, Excluir, Lista

No exemplo seguinte, a saída de comando é atribuída à variável ambiente IMPORT_SAS, prefixada com o caráter '?'. Atualize `--expiry` o valor para o seu ambiente:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Armazenar ficha SAS para importação

Guarde o token SAS no seu cofre chave Azure alvo utilizando [um conjunto secreto az keyvault:][az-keyvault-secret-set]

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Criar ExportPipeline com Gestor de Recursos

Crie um recurso ExportPipeline para o seu registo de contentores de origem utilizando a implementação do modelo do Gestor de Recursos Azure.

Copiar [ficheiros](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) de modelo de Gestor de Recursos exportpipeline para uma pasta local.

Introduza os seguintes valores do parâmetro no ficheiro: `azuredeploy.parameters.json`

|Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registo do seu contentor de origem      |
|exportaçãoPipelineName     |  Nome que escolheu para o oleoduto de exportação       |
|targetUri     |  URI do recipiente de armazenamento no seu ambiente de origem (alvo do gasoduto de exportação).<br/>Exemplo: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Nome do cofre da chave de origem  |
|sasTokenSecretName  | Nome do segredo simbólico da SAS no cofre da chave de origem <br/>Exemplo: acrexportsas

### <a name="export-options"></a>Opções de exportação

A `options` propriedade para os oleodutos de exportação suporta valores opcionais booleanos. Recomenda-se os seguintes valores:

|Parâmetro  |Valor  |
|---------|---------|
|opções | OverwriteBlobs - Sobrepor as bolhas-alvo existentes<br/>ContinueOnErrors - Continue a exportar artefactos restantes no registo de origem se uma exportação de artefactos falhar.

### <a name="create-the-resource"></a>Criar o recurso

Executar grupo de [implantação az criar][az-deployment-group-create] para criar o recurso. O exemplo seguinte designa a *exportação*de implantaçãoPipeline .

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

Na saída do comando, tome nota do ID do recurso (`id`) do gasoduto. Você pode armazenar este valor em uma variável ambiente para uso posterior, executando o show do grupo de [implementação az][az-deployment-group-show]. Por exemplo:

```azurecli
EXPORT_RES_ID=$(az group deployment show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Criar importpipeline com gestor de recursos 

Crie um recurso ImportPipeline no seu registo de contentores-alvo utilizando a implementação do modelo do Gestor de Recursos Azure. Por predefinição, o gasoduto está habilitado a importar automaticamente quando a conta de armazenamento no ambiente-alvo tiver uma bolha de artefactos.

Copiar [ficheiros](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) de modelo de Gestor de Recursos de ImportPipeline para uma pasta local.

Introduza os seguintes valores do parâmetro no ficheiro: `azuredeploy.parameters.json`

Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registo do seu contentor-alvo      |
|importPipelineName     |  Nome que escolheu para o oleoduto de importação       |
|fonteUri     |  URI do recipiente de armazenamento no seu ambiente-alvo (a fonte do gasoduto de importação).<br/>Exemplo: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Nome do cofre da chave alvo |
|sasTokenSecretName     |  Nome do segredo simbólico da SAS no cofre da chave alvo<br/>Exemplo: acr importsas |

### <a name="import-options"></a>Opções de importação

A `options` propriedade para o gasoduto de importação suporta valores booleanos opcionais. Recomenda-se os seguintes valores:

|Parâmetro  |Valor  |
|---------|---------|
|opções | OverwriteTags - Sobrepor as etiquetas-alvo existentes<br/>DeleteSourceBlobOnSuccess - Elimine a bolha de armazenamento de fonte após a importação bem sucedida para o registo-alvo<br/>ContinueOnErrors - Continue a importar artefactos restantes no registo-alvo se uma importação de artefactos falhar.

### <a name="create-the-resource"></a>Criar o recurso

Executar grupo de [implantação az criar][az-deployment-group-create] para criar o recurso.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json \
  --name importPipeline
```

Se pretender executar a importação manualmente, tome nota do ID do recurso (`id`) do gasoduto. Você pode armazenar este valor em uma variável ambiente para uso posterior, executando o show do grupo de [implementação az][az-deployment-group-show]. Por exemplo:

```azurecli
IMPORT_RES_ID=$(az group deployment show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Criar PipelineRun para exportação com Gestor de Recursos 

Crie um recurso PipelineRun para o seu registo de contentores de origem utilizando a implementação do modelo do Gestor de Recursos Azure. Este recurso executa o recurso ExportPipeline que criou anteriormente, e exporta artefactos especificados do seu registo de contentores como uma bolha para a sua conta de armazenamento de origem.

Copiar [ficheiros](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) de modelo de Gestor de Recursos PipelineRun para uma pasta local.

Introduza os seguintes valores do parâmetro no ficheiro: `azuredeploy.parameters.json`

|Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registo do seu contentor de origem      |
|pipelineRunName     |  Nome que escolher para a corrida       |
|pipelineResourceId     |  Identificação de recursos do oleoduto de exportação.<br/>Exemplo: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|nome alvo     |  Nome que escolha para os artefactos blob exportados para a sua conta de armazenamento de origem, como *myblob*
|artefactos | Conjunto de artefactos de origem para transferir, como tags ou manifestadigestões<br/>Exemplo: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Executar o grupo de [implementação az criar][az-deployment-group-create] para criar o recurso PipelineRun. O exemplo seguinte dá nomeà exportação de *implantaçãoPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Pode levar vários minutos para os artefactos exportarem. Quando a implantação estiver concluída com êxito, verifique a exportação de artefactos, enumerando a bolha exportada no recipiente de *transferência* da conta de armazenamento de origem. Por exemplo, executar o comando da lista de blob de [armazenamento az:][az-storage-blob-list]

```azurecli
az storage blob list \
  --account-name $SA_SOURCE
  --container transfer
  --output table
```

## <a name="transfer-blob-optional"></a>Bolha de transferência (opcional) 

Utilize a ferramenta AzCopy ou outros métodos para [transferir dados blob](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts) da conta de armazenamento de origem para a conta de armazenamento alvo.

Por exemplo, [`azcopy copy`](/azure/storage/common/storage-ref-azcopy-copy) o comando seguinte copia myblob do recipiente de *transferência* na conta fonte para o recipiente *de transferência* na conta-alvo. Se a bolha existe na conta-alvo, está sobreescrita. A autenticação utiliza tokens SAS com permissões adequadas para a fonte e contentores-alvo. (Não são mostradas medidas para criar fichas.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Trigger ImportPipeline recurso

Se ativar `sourceTriggerStatus` o parâmetro do ImportPipeline (o valor predefinido), o gasoduto é acionado após a cópia da bolha para a conta de armazenamento-alvo. Pode levar vários minutos para os artefactos importarem. Quando a importação estiver concluída com êxito, verifique a importação de artefactos enumerando os repositórios no registo de contentores-alvo. Por exemplo, executar [az acr lista repositório:][az-acr-repository-list]

```azurecli
az acr repository list --name <target-registry-name>
```

Se não tiver ativado o `sourceTriggerStatus` parâmetro do gasoduto de importação, faça o recurso ImportPipeline manualmente, como mostra a secção seguinte. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Criar PipelineRun para importação com Gestor de Recursos (opcional) 
 
Também pode utilizar um recurso PipelineRun para desencadear um ImportPipeline para importação de artefactos para o seu registo de contentores-alvo.

Copiar [ficheiros](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) de modelo de Gestor de Recursos PipelineRun para uma pasta local.

Introduza os seguintes valores do parâmetro no ficheiro: `azuredeploy.parameters.json`

|Parâmetro  |Valor  |
|---------|---------|
|registryName     | Nome do registo do seu contentor-alvo      |
|pipelineRunName     |  Nome que escolher para a corrida       |
|pipelineResourceId     |  Identificação de recursos do oleoduto de importação.<br/>Exemplo: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|nome de origem     |  Nome da bolha existente para artefactos exportados na sua conta de armazenamento, como *myblob*

Executar grupo de [implantação az criar][az-deployment-group-create] para executar o recurso.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Quando a implantação estiver concluída com sucesso, verifique a importação de artefactos enumerando os repositórios no registo de contentores-alvo. Por exemplo, executar [az acr lista repositório:][az-acr-repository-list]

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="delete-pipeline-resources"></a>Eliminar recursos de gasoduto

Para eliminar um recurso de gasoduto, elimine a sua implementação do Gestor de Recursos utilizando o grupo de [implementação az eliminar][az-deployment-group-delete] o comando. Os seguintes exemplos apagam os recursos de gasoduto criados neste artigo:

```azurecli
az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipeline

az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun

az deployment group delete \
  --resource-group $TARGET_RG \
  --name importPipeline  
```

## <a name="troubleshooting"></a>Resolução de problemas

* **Falhas ou erros de implementação do modelo**
  * Se uma execução de gasoduto `pipelineRunErrorMessage` falhar, olhe para a propriedade do recurso de execução.
  * Para erros comuns de implementação do modelo, consulte implementações do [modelo ARM de resolução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problemas com a exportação ou importação de bolhas de armazenamento**
  * A ficha sas pode ser expirada ou pode ter permissões insuficientes para a exportação ou execução de importação especificada
  * A bolha de armazenamento existente na conta de armazenamento de fonte pode não ser substituída durante vários ensaios de exportação. Confirme que a opção OverwriteBlob está definida na corrida à exportação e que o token SAS tem permissões suficientes.
  * A bolha de armazenamento na conta de armazenamento-alvo não pode ser eliminada após uma corrida bem sucedida à importação. Confirme que a opção DeleteBlobOnSuccess está definida na corrida de importação e o token SAS tem permissões suficientes.
  * Blob de armazenamento não criado ou eliminado. Confirme que existe um contentor especificado no funcionamento da exportação ou da importação, ou exista uma bolha de armazenagem especificada para a importação manual. 
* **Problemas da AzCopy**
  * Consulte [problemas da AzCopy](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues)de Troubleshoot .  
* **Problemas de transferência de artefactos**
  * Nem todos os artefactos, ou nenhum, são transferidos. Confirme a ortografia dos artefactos em execução de exportação e o nome da bolha nas exportações e nas corridas de importação. Confirme que está a transferir um máximo de 10 artefactos.
  * A execução do gasoduto pode não ter terminado. Uma exportação ou uma importação podem demorar algum tempo. 
  * Para outras questões do gasoduto, forneça a identificação da correlação de [implantação](../azure-resource-manager/templates/deployment-history.md) da corrida de exportação ou da importação para a equipa de registo de contentores de Azure.


## <a name="next-steps"></a>Passos seguintes

Para importar imagens de contentores individuais para um registo de contentores Azure de um registo público ou de outro registo privado, consulte a referência do comando de [importação az acr.][az-acr-import]

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import



