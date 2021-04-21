---
title: Transferir artefactos
description: Transferir coleções de imagens ou outros artefactos de um registo de contentores para outro registo, criando um gasoduto de transferência utilizando contas de armazenamento Azure
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: c966600b0ca9d65cf533c3c2f0aca211c84917bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780780"
---
# <a name="transfer-artifacts-to-another-registry"></a>Transferir artefactos para outro registo

Este artigo mostra como transferir coleções de imagens ou outros artefactos de registo de um registo de contentores Azure para outro registo. Os registos de origem e alvo podem estar nas mesmas ou diferentes subscrições, inquilinos do Ative Directory, nuvens Azure ou nuvens fisicamente desligadas. 

Para transferir artefactos, cria-se um *gasoduto de transferência* que replica artefactos entre dois registos utilizando o armazenamento de [bolhas:](../storage/blobs/storage-blobs-introduction.md)

* Os artefactos de um registo de origem são exportados para uma bolha numa conta de armazenamento de fontes 
* O blob é copiado da conta de armazenamento de origem para uma conta de armazenamento alvo
* A bolha na conta de armazenamento alvo é importada como artefactos no registo alvo. Pode configurar o gasoduto de importação para ativar sempre que o artefacto blob atualiza no armazenamento do alvo.

A transferência é ideal para copiar conteúdo entre dois registos de contentores Azure em nuvens fisicamente desligadas, mediados por contas de armazenamento em cada nuvem. Se em vez disso pretender copiar imagens de registos de contentores em nuvens conectadas, incluindo Docker Hub e outros vendedores de nuvem, [recomenda-se a importação de imagens.](container-registry-import-images.md)

Neste artigo, utiliza as implementações do modelo do Azure Resource Manager para criar e executar o pipeline de transferência. O CLI Azure é utilizado para a provisionar os recursos associados, tais como segredos de armazenamento. Recomenda-se a versão 2.2.0 ou posterior do Azure CLI. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure][azure-cli].

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo, consulte [os níveis de registo do contentor Azure](container-registry-skus.md).

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

* **Registos de contentores** - É necessário um registo de origem existente com artefactos para transferir e um registo-alvo. A transferência de ACR destina-se ao movimento através de nuvens fisicamente desligadas. Para testes, os registos de origem e alvo podem estar na mesma ou numa subscrição Azure diferente, inquilino do Ative Directory ou nuvem. 

   Se precisar de criar um registo, consulte [Quickstart: Crie um registo de contentores privados utilizando o Azure CLI](container-registry-get-started-azure-cli.md). 
* **Contas de armazenamento** - Crie contas de armazenamento de origem e alvo numa subscrição e localização à sua escolha. Para efeitos de teste, pode utilizar a mesma subscrição ou subscrições que os registos de origem e alvo. Para cenários de nuvem cruzada, normalmente cria-se uma conta de armazenamento separada em cada nuvem. 

  Se necessário, crie as contas de armazenamento com o [CLI Azure](../storage/common/storage-account-create.md?tabs=azure-cli) ou outras ferramentas. 

  Crie um recipiente blob para transferência de artefactos em cada conta. Por exemplo, criar um contentor denominado *transferência*. Dois ou mais oleodutos de transferência podem partilhar a mesma conta de armazenamento, mas devem utilizar diferentes âmbitos de contentores de armazenamento.
* **Cofres-chave** - Os cofres-chave são necessários para armazenar segredos simbólicos SAS usados para aceder a contas de armazenamento de fontes e alvos. Crie os cofres-chave de origem e alvo na mesma subscrição ou subscrições do Azure que os registos de origem e alvo. Para fins de demonstração, os modelos e comandos utilizados neste artigo também assumem que os cofres-chave de origem e alvo estão localizados nos mesmos grupos de recursos que os registos de origem e alvo, respectivamente. Esta utilização de grupos de recursos comuns não é necessária, mas simplifica os modelos e comandos utilizados neste artigo.

   Se necessário, crie cofres-chave com o [CLI Azure](../key-vault/secrets/quick-create-cli.md) ou outras ferramentas.

* **Variáveis ambientais** - Por exemplo, os comandos neste artigo, definir as seguintes variáveis ambientais para os ambientes de origem e alvo. Todos os exemplos são formatados para a casca bash.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

Cria-se os três recursos seguintes para transferência de imagem entre registos. Todos são criados usando operações PUT. Estes recursos operam na sua *fonte* e *registos-alvo* e contas de armazenamento. 

A autenticação de armazenamento utiliza fichas SAS, geridas como segredos em cofres-chave. Os oleodutos usam identidades geridas para ler os segredos nos cofres.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** - Recurso de longa duração que contém informações de alto nível sobre o registo *de origem* e a conta de armazenamento. Esta informação inclui o recipiente de armazenamento de fonte URI e o cofre-chave que gere o token SAS de origem. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** - Recurso de longa duração que contém informações de alto nível sobre o registo *alvo* e a conta de armazenamento. Esta informação inclui o contentor de armazenamento alvo URI e o cofre-chave que gere o símbolo SAS alvo. Um gatilho de importação é ativado por defeito, de modo que o gasoduto funciona automaticamente quando uma bolha de artefacto pousa no recipiente de armazenamento alvo. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** - Recurso utilizado para invocar um recurso ExportPipeline ou ImportPipeline.  
  * Você executou o ExportPipeline manualmente criando um recurso PipelineRun e especificando os artefactos para exportar.  
  * Se um gatilho de importação estiver ativado, o ImportPipeline funciona automaticamente. Também pode ser executado manualmente usando um PipelineRun. 
  * Atualmente, um máximo de **50 artefactos** pode ser transferido com cada PipelineRun.

### <a name="things-to-know"></a>Aspetos importantes
* O ExportPipeline e o ImportPipeline estarão tipicamente em diferentes inquilinos do Ative Directory associados às nuvens de origem e destino. Este cenário requer identidades geridas separadas e cofres-chave para os recursos de exportação e importação. Para efeitos de teste, estes recursos podem ser colocados na mesma nuvem, partilhando identidades.
* Por padrão, os modelos ExportPipeline e ImportPipeline permitem cada um um sistema de identidade gerida para aceder a segredos chave do cofre. Os modelos ExportPipeline e ImportPipeline também suportam uma identidade atribuída ao utilizador que fornece. 

## <a name="create-and-store-sas-keys"></a>Criar e armazenar chaves SAS

A transferência utiliza fichas de assinatura de acesso partilhado (SAS) para aceder às contas de armazenamento nos ambientes de origem e alvo. Gere e armazena fichas como descrito nas seguintes secções.  

### <a name="generate-sas-token-for-export"></a>Gerar ficha SAS para exportação

Executar o [comando de origem do recipiente de armazenamento az][az-storage-container-generate-sas] para gerar um token SAS para o recipiente na conta de armazenamento de origem, utilizado para exportação de artefactos.

*Permissões de token recomendadas*: Ler, Escrever, Listar, Adicionar. 

No exemplo seguinte, a saída de comando é atribuída à variável ambiente EXPORT_SAS, prefixada com o caráter '?'. Atualize o `--expiry` valor para o seu ambiente:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Loja SAS token para exportação

Guarde o token SAS no seu cofre de chave Azure fonte utilizando [um conjunto secreto de keyvault az][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Gerar ficha SAS para importação

Executar o [comando de gerado por um recipiente de armazenamento az][az-storage-container-generate-sas] para gerar um token SAS para o recipiente na conta de armazenamento alvo, utilizado para a importação de artefactos.

*Permissões de token recomendadas*: Ler, Eliminar, Lista

No exemplo seguinte, a saída de comando é atribuída à variável ambiente IMPORT_SAS, prefixada com o caráter '?'. Atualize o `--expiry` valor para o seu ambiente:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Ficha SAS da loja para importação

Guarde o token SAS no seu cofre de chave Azure alvo usando [conjunto secreto az keyvault][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Criar ExportPipeline com Gestor de Recursos

Crie um recurso ExportPipeline para o seu registo de contentores de origem utilizando a implementação do modelo do Gestor de Recursos Azure.

Copie [os ficheiros de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) do Gestor de Recursos ExportPipeline para uma pasta local.

Introduza os seguintes valores de parâmetro no `azuredeploy.parameters.json` ficheiro:

|Parâmetro  |Valor  |
|---------|---------|
|nome de registo     | Nome do seu registo de contentores de origem      |
|exportaçãoPipelineName     |  Nome que escolher para o oleoduto de exportação       |
|targetUri     |  URI do recipiente de armazenamento no seu ambiente de origem (o alvo do gasoduto de exportação).<br/>Exemplo: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Nome do cofre da chave de origem  |
|sasTokenSecretName  | Nome do segredo simbólico SAS no cofre da chave de origem <br/>Exemplo: acrexportsas

### <a name="export-options"></a>Opções de exportação

A `options` propriedade para os oleodutos de exportação suporta valores opcionais de boolean. Recomenda-se os seguintes valores:

|Parâmetro  |Valor  |
|---------|---------|
|opções | OverwriteBlobs - Overwrite blobs alvo existentes<br/>ContinueOnErrors - Continue a exportar os restantes artefactos no registo de origem se uma exportação de artefactos falhar.

### <a name="create-the-resource"></a>Criar o recurso

O [grupo de implementação run az cria][az-deployment-group-create] para criar um recurso chamado *exportPipeline,* como mostrado nos seguintes exemplos. Por padrão, com a primeira opção, o modelo de exemplo permite uma identidade atribuída ao sistema no recurso ExportPipeline. 

Com a segunda opção, pode fornecer ao recurso uma identidade atribuída ao utilizador. (Criação da identidade atribuída ao utilizador não mostrada.)

Com qualquer uma das opções, o modelo configura a identidade para aceder ao token SAS no cofre da chave de exportação. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opção 1: Criar recursos e permitir a identidade atribuída ao sistema

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opção 2: Criar recurso e fornecer identidade atribuída ao utilizador

Neste comando, forneça o ID de recursos da identidade atribuída ao utilizador como parâmetro adicional.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Na saída do comando, tome nota do ID do recurso `id` ( ) do gasoduto. Pode armazenar este valor numa variável ambiental para utilização posterior executando o show do [grupo de implantação az][az-deployment-group-show]. Por exemplo:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Criar ImportPipeline com Gestor de Recursos 

Crie um recurso ImportPipeline no registo do seu contentor-alvo utilizando a implementação do modelo do Azure Resource Manager. Por predefinição, o gasoduto é habilitado a importar automaticamente quando a conta de armazenamento no ambiente alvo tem uma bolha de artefacto.

Copie [os ficheiros de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) do Gestor de Recursos ImportPipeline para uma pasta local.

Introduza os seguintes valores de parâmetro no `azuredeploy.parameters.json` ficheiro:

Parâmetro  |Valor  |
|---------|---------|
|nome de registo     | Nome do seu registo de contentores-alvo      |
|importPipelineName     |  Nome que escolher para o gasoduto de importação       |
|fonteUri     |  URI do recipiente de armazenamento no seu ambiente-alvo (fonte para o gasoduto de importação).<br/>Exemplo: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Nome do cofre da chave alvo |
|sasTokenSecretName     |  Nome do segredo simbólico da SAS no cofre da chave alvo<br/>Exemplo: acr importsas |

### <a name="import-options"></a>Opções de importação

A `options` propriedade para o gasoduto de importação suporta valores opcionais de boolean. Recomenda-se os seguintes valores:

|Parâmetro  |Valor  |
|---------|---------|
|opções | OverwriteTags - Substitua as tags de alvo existentes<br/>DeleteSourceBlobOnSuccess - Eliminar a bolha de armazenamento de origem após importação bem sucedida para o registo-alvo<br/>ContinueOnErrors - Continue a importar os artefactos restantes no registo-alvo se uma importação de artefactos falhar.

### <a name="create-the-resource"></a>Criar o recurso

O [grupo de implementação executar az cria][az-deployment-group-create] para criar um recurso chamado *importPipeline,* como mostrado nos exemplos seguintes. Por padrão, com a primeira opção, o modelo de exemplo permite uma identidade atribuída ao sistema no recurso ImportPipeline. 

Com a segunda opção, pode fornecer ao recurso uma identidade atribuída ao utilizador. (Criação da identidade atribuída ao utilizador não mostrada.)

Com qualquer uma das opções, o modelo configura a identidade para aceder ao token SAS no cofre da chave de importação. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opção 1: Criar recursos e permitir a identidade atribuída ao sistema

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opção 2: Criar recurso e fornecer identidade atribuída ao utilizador

Neste comando, forneça o ID de recursos da identidade atribuída ao utilizador como parâmetro adicional.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Se pretender executar a importação manualmente, tome nota do ID do recurso ( `id` ) do gasoduto. Pode armazenar este valor numa variável ambiental para utilização posterior, executando o comando de exibição do [grupo de implantação az.][az-deployment-group-show] Por exemplo:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Criar PipelineRun para exportação com Gestor de Recursos 

Crie um recurso PipelineRun para o seu registo de contentores de origem utilizando a implementação do modelo do Azure Resource Manager. Este recurso executa o recurso ExportPipeline que criou anteriormente, e exporta artefactos especificados do seu registo de contentores como uma bolha para a sua conta de armazenamento de origem.

Copie os [ficheiros de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) do PipelineRun Resource Manager para uma pasta local.

Introduza os seguintes valores de parâmetro no `azuredeploy.parameters.json` ficheiro:

|Parâmetro  |Valor  |
|---------|---------|
|nome de registo     | Nome do seu registo de contentores de origem      |
|pipelineRunName     |  Nome que escolher para a corrida       |
|pipelineResourceId     |  Identificação de recursos do oleoduto de exportação.<br/>Exemplo: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|nome alvo     |  Nome que escolher para os artefactos blob exportados para a sua conta de armazenamento de origem, como *myblob*
|artefactos | Matriz de artefactos de origem para transferir, como tags ou reerntes manifesto<br/>Exemplo: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Se recolocar um recurso PipelineRun com propriedades idênticas, também deve utilizar a propriedade [forceUpdateTag.](#redeploy-pipelinerun-resource)

Executar [o grupo de implementação az criar][az-deployment-group-create] para criar o recurso PipelineRun. O exemplo a seguir dá nome à *exportação de implantaçãoPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Para uma utilização posterior, guarde o ID de recurso do gasoduto executado numa variável ambiental:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Pode levar vários minutos para os artefactos exportarem. Quando a implantação estiver concluída com sucesso, verifique a exportação de artefactos listando a bolha exportada no recipiente de *transferência* da conta de armazenamento de origem. Por exemplo, executar o comando da [lista de bolhas de armazenamento az:][az-storage-blob-list]

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Bolha de transferência (opcional) 

Utilize a ferramenta AzCopy ou outros métodos para [transferir dados blob](../storage/common/storage-use-azcopy-v10.md#transfer-data) da conta de armazenamento de origem para a conta de armazenamento alvo.

Por exemplo, o seguinte [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) comando copia o myblob do contentor de *transferência* na conta de origem para o contentor *de transferência* na conta-alvo. Se a bolha existe na conta alvo, está substituída. A autenticação utiliza fichas SAS com permissões apropriadas para os recipientes de origem e alvo. (Não são mostradas etapas para criar fichas.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Trigger ImportPipeline recurso

Se ativar o `sourceTriggerStatus` parâmetro da ImportPipeline (o valor predefinido), o gasoduto é acionado após a bolha ser copiada para a conta de armazenamento alvo. Pode levar vários minutos para os artefactos importarem. Quando a importação terminar com êxito, verifique a importação de artefactos enumerando os repositórios no registo do contentor-alvo. Por exemplo, executar [a lista de repositórios az acr][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Se não ativou o `sourceTriggerStatus` parâmetro do gasoduto de importação, execute manualmente o recurso ImportPipeline, como mostra a secção seguinte. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Criar PipelineRun para importação com Gestor de Recursos (opcional) 
 
Também pode utilizar um recurso PipelineRun para ativar uma ImportPipeline para a importação de artefactos para o registo de contentores-alvo.

Copie os [ficheiros de modelo](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) do PipelineRun Resource Manager para uma pasta local.

Introduza os seguintes valores de parâmetro no `azuredeploy.parameters.json` ficheiro:

|Parâmetro  |Valor  |
|---------|---------|
|nome de registo     | Nome do seu registo de contentores-alvo      |
|pipelineRunName     |  Nome que escolher para a corrida       |
|pipelineResourceId     |  Identificação de recursos do gasoduto de importação.<br/>Exemplo: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|nome de fonteName     |  Nome da bolha existente para artefactos exportados na sua conta de armazenamento, como *myblob*

Se recolocar um recurso PipelineRun com propriedades idênticas, também deve utilizar a propriedade [forceUpdateTag.](#redeploy-pipelinerun-resource)

Executar [o grupo de implementação az criar][az-deployment-group-create] para executar o recurso.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Para uma utilização posterior, guarde o ID de recurso do gasoduto executado numa variável ambiental:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Quando a implantação estiver concluída com sucesso, verifique a importação de artefactos listando os repositórios no registo do contentor-alvo. Por exemplo, executar [a lista de repositórios az acr][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Recurso PipelineRun de reexploração

Se recolocar um recurso PipelineRun com *propriedades idênticas,* deve aproveitar a propriedade **forceUpdateTag.** Esta propriedade indica que o recurso PipelineRun deve ser recriado mesmo que a configuração não tenha mudado. Certifique-se de que o ForceUpdateTag é diferente cada vez que recolocar o recurso PipelineRun. O exemplo abaixo recria um PipelineRun para exportação. A data atual é usada para definir forceUpdateTag, garantindo assim que esta propriedade é sempre única.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Eliminar recursos de gasoduto

Os seguintes comandos de exemplo utilizam [a az recursos para eliminar][az-resource-delete] os recursos do pipeline criados neste artigo. Os IDs de recursos foram previamente armazenados em variáveis ambientais.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Resolução de problemas

* **Falhas ou erros de implementação do modelo**
  * Se um gasoduto falhar, olhe para a `pipelineRunErrorMessage` propriedade do recurso de execução.
  * Para obter erros comuns de implementação do modelo, consulte [as implementações do modelo de braço de resolução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problemas de acesso ao armazenamento**<a name="problems-accessing-storage"></a>
  * Se vir um `403 Forbidden` erro do armazenamento, é provável que tenha um problema com o seu token SAS.
  * O símbolo SAS pode não ser atualmente válido. O token SAS pode ter expirado ou as chaves da conta de armazenamento podem ter mudado desde que o token SAS foi criado. Verifique se o token SAS é válido ao tentar utilizar o token SAS para autenticar para acesso ao recipiente da conta de armazenamento. Por exemplo, coloque um ponto final blob existente seguido do token SAS na barra de endereços de uma nova janela Microsoft Edge InPrivate ou carreme um blob para o recipiente com o token SAS utilizando `az storage blob upload` .
  * O token SAS pode não ter tipos de recursos permitidos suficientes. Verifique se o token SAS foi dado permissões ao Serviço, Ao Contentor e ao Objeto sob tipos de recursos permitidos `srt=sco` (no token SAS).
  * O símbolo da SAS pode não ter permissões suficientes. Para os oleodutos de exportação, as permissões necessárias para o token SAS são Ler, Escrever, Lista e Adicionar. Para os gasodutos de importação, as permissões necessárias para o token SAS são Ler, Eliminar e Lista. (A permissão de eliminação só é necessária se o gasoduto de importação tiver a `DeleteSourceBlobOnSuccess` opção ativada.)
  * O token SAS pode não estar configurado para trabalhar apenas com HTTPS. Verifique se o token SAS está configurado para funcionar apenas com HTTPS `spr=https` (no token SAS).
* **Problemas com a exportação ou importação de bolhas de armazenamento**
  * O token SAS pode ser inválido ou pode ter permissões insuficientes para a produção ou importação especificada. Ver [Problemas de acesso ao armazenamento](#problems-accessing-storage).
  * A bolha de armazenamento existente na conta de armazenamento de fontes pode não ser substituída durante vários períodos de exportação. Confirme que a opção OverwriteBlob está definida na fuga à exportação e que o token SAS tem permissões suficientes.
  * A bolha de armazenamento na conta de armazenamento-alvo pode não ser eliminada após uma execução bem sucedida das importações. Confirme que a opção DeleteBlobOnSuccess está definida na corrida de importação e que o token SAS tem permissões suficientes.
  * Bolha de armazenamento não criada ou apagada. Confirme que existe um contentor especificado na produção de exportação ou de importação, ou que exista uma bolha de armazenamento especificada para a importação manual. 
* **Problemas com a AzCopy**
  * Consulte [problemas de AzCopy de resolução de problemas](../storage/common/storage-use-azcopy-configure.md).  
* **Problemas de transferência de artefactos**
  * Nem todos os artefactos, ou nenhum, são transferidos. Confirme a ortografia dos artefactos em execução de exportação, e o nome do blob nas operações de exportação e importação. Confirme que está a transferir um máximo de 50 artefactos.
  * O gasoduto pode não ter terminado. Uma viagem de exportação ou importação pode demorar algum tempo. 
  * Para outras questões relacionadas com o gasoduto, fornecer o ID de [correlação](../azure-resource-manager/templates/deployment-history.md) de implantação da execução de exportação ou a importação para a equipa de registo de contentores de Azure.
* **Problemas em puxar a imagem num ambiente fisicamente isolado**
  * Se vir erros em relação a camadas estrangeiras ou tentativas de resolver mcr.microsoft.com quando tenta puxar uma imagem num ambiente fisicamente isolado, o seu manifesto de imagem provavelmente tem camadas não distribuíveis. Devido à natureza de um ambiente fisicamente isolado, estas imagens muitas vezes não conseguem puxar. Pode confirmar que é esse o caso, verificando o manifesto de imagem para quaisquer referências a registos externos. Se for esse o caso, terá de empurrar as camadas não distribuíveis para a sua nuvem pública ACR antes de implementar um gasoduto de exportação para essa imagem. Para obter orientações sobre como fazê-lo, veja [como empurro camadas não distribuíveis para um registo?](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Passos seguintes

Para importar imagens de um único contentor para um registo de contentores Azure a partir de um registo público ou de outro registo privado, consulte a referência do comando [de importação az acr.][az-acr-import]

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az_login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az_keyvault_secret_show
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az_storage_container_generate_sas
[az-storage-blob-list]: /cli/azure/storage/blob#az_storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-deployment-group-delete]: /cli/azure/deployment/group#az_deployment_group_delete
[az-deployment-group-show]: /cli/azure/deployment/group#az_deployment_group_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-resource-delete]: /cli/azure/resource#az_resource_delete
