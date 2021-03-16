---
title: Tutorial - Rendere uma cena na nuvem
description: Saiba como fazer uma cena Autodesk 3ds Max com Arnold usando o Serviço de Renderização de Lote e Interface Azure Command-Line
ms.topic: tutorial
ms.date: 12/30/2020
ms.custom: mvc, devx-track-azurecli
ROBOTS: NOINDEX
ms.openlocfilehash: 5165e5feb566a4b9081f40b681b92aafa143869f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491747"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Tutorial: compor uma cena com o Azure Batch

O Azure Batch fornece capacidades de composição à escala da cloud num modelo de faturação de pagamento por utilização. O Azure Batch suporta aplicações de composição, incluindo o Autodesk Maya, 3ds Max, Arnold e V-Ray. Este tutorial mostra-lhe os passos para compor uma pequena cena com o Batch, através da Interface de Linha de Comandos do Azure. Saiba como:

> [!div class="checklist"]
> - Carregar uma cena para o armazenamento do Azure
> - Criar um conjunto do Batch para composição
> - Compor uma cena com um único fotograma
> - Dimensionar o conjunto e compor uma cena com vários fotogramas
> - Transferir o resultado composto

Neste tutorial, irá compor uma cena 3ds Max com o Batch, através do compositor [Arnold](https://www.autodesk.com/products/arnold/overview) de traçado de raios. O conjunto do Batch utiliza uma imagem do Microsoft Azure Marketplace com gráficos pré-instalados e aplicações de composição que oferecem licenciamento de pagamento por utilização.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma subscrição pay as you go ou de outra opção de compra do Azure para utilizar aplicações de composição no Batch no modelo de faturação de pagamento por utilização. **O licenciamento de pagamento por utilização não será suportado se utilizar uma oferta gratuita do Azure que oferece um crédito monetário.**

- A cena 3ds Max de exemplo para este tutorial encontra-se no [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene), em conjunto com um script Bash de exemplo e ficheiros de configuração JSON. A cena 3ds Max é proveniente dos [ficheiros de exemplo do Autodesk 3ds Max](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). (Os ficheiros de exemplo do Autodesk 3ds Max estão disponíveis com uma licença de Atribuição Semelhante à Partilha Não Comercial da Creative Commons. Copyright &copy; Autodesk, Inc.)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este tutorial requer a versão 2.0.20 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

> [!TIP]
> Pode ver [os modelos de emprego de Arnold](https://github.com/Azure/batch-extension-templates/tree/master/templates/arnold/render-windows-frames) no repositório de extensão do lote Azure GitHub.

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Se ainda não o tiver feito, crie um grupo de recursos, uma conta do Batch e uma conta de armazenamento ligada na sua subscrição.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus2*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus2
```

Utilize o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create) para criar uma conta de Armazenamento do Microsoft Azure no seu grupo de recursos. Para este tutorial, utilize a conta de armazenamento para armazenar uma cena 3ds Max de entrada e a saída composta.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Crie uma conta do Batch com o comando [az batch account create](/cli/azure/batch/account#az-batch-account-create). O exemplo seguinte cria uma conta do Batch com o nome *mybatchaccount* em *myResourceGroup* e associa a conta de armazenamento que criou.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Para criar e gerir conjuntos e tarefas de computação, tem de autenticar com o Batch. Inicie sessão na conta com o comando [az batch account login](/cli/azure/batch/account#az-batch-account-login). Depois de iniciar sessão, os comandos `az batch` utilizam este contexto de conta. O exemplo seguinte utiliza a autenticação de chave partilhada, com base no nome da conta e chave do Batch. O Batch também suporta a autenticação através do [Azure Active Directory](batch-aad-auth.md), para autenticar utilizadores individuais ou uma aplicação autónoma.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="upload-a-scene-to-storage"></a>Carregar uma cena para o armazenamento

Para carregar a cena de entrada para o armazenamento, tem de, em primeiro lugar, aceder à conta de armazenamento e criar um contentor de destino para os blobs. Para aceder à conta de armazenamento do Azure, exporte as variáveis de ambiente `AZURE_STORAGE_KEY` e `AZURE_STORAGE_ACCOUNT`. O primeiro comando da shell do Bash utiliza o comando [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) para obter a primeira chave de conta. Depois de definir estas variáveis de ambiente, os comandos de armazenamento utilizam este contexto de conta.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Agora, crie um contentor de blobs na conta de armazenamento para os ficheiros de cenas. O exemplo seguinte utiliza o comando [az storage container create](/cli/azure/storage/container#az-storage-container-create) para criar um contentor de blobs com o nome *scenefiles* que permita o acesso de leitura público.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Transfira a cena `MotionBlur-Dragon-Flying.max` do [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) para um diretório de trabalho local. Por exemplo:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Carregue o ficheiro de cenas do seu diretório de trabalho local para o contentor de blobs. O exemplo seguinte utiliza o comando [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch), que pode carregar vários ficheiros:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Criar um conjunto de composição

Crie um conjunto do Batch para composição, com o comando [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). Neste exemplo, especifique as definições do conjunto num ficheiro JSON. Na sua shell atual, crie um ficheiro com o nome *mypool.json* e, em seguida, copie e cole o seguinte conteúdo. Certifique-se de que copia todo o texto corretamente. (Pode transferir o ficheiro do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.8"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```

O Batch suporta nós dedicados e nós de [baixa prioridade](batch-low-pri-vms.md), e pode utilizar um ou ambos nos conjuntos. Os nós dedicados estão reservados para o conjunto. Os nós de prioridade baixa são disponibilizados a um preço reduzido a partir da capacidade excedente da VM no Azure. Os nós de prioridade baixa ficam indisponíveis se o Azure não tiver capacidade suficiente.

O conjunto especificado contém um único nó de baixa prioridade que executa uma imagem do Windows Server com o software para o serviço Batch Rendering. Este conjunto está licenciado para compor com 3ds Max e Arnold. Num passo posterior, irá dimensionar o conjunto para um grande número de nós.

Se ainda não tiver feito login na sua conta Batch, utilize o comando de login da [conta az batch](/cli/azure/batch/account#az-batch-account-login) para o fazer. Em seguida, crie a piscina passando o ficheiro JSON para o `az batch pool create` comando:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
```

Demora alguns minutos para aprovisionar o conjunto. Para ver o estado do conjunto, execute o comando [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). O comando seguinte obtém o estado de alocação do conjunto:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Continue para os passos seguintes para criar tarefas enquanto o estado do conjunto é alterado. O conjunto está completamente aprovisionado quando o estado de atribuição for `steady` e os nós estiverem em execução.  

## <a name="create-a-blob-container-for-output"></a>Criar um contentor de blobs para a saída

Nos exemplos neste tutorial, todas as tarefas no trabalho de composição criam um ficheiro de saída. Antes de agendar a tarefa, crie um contentor de blobs na sua conta de armazenamento como o destino para os ficheiros de saída. O exemplo seguinte utiliza o comando [az storage container create](/cli/azure/storage/container#az-storage-container-create) para criar o contentor *job-myrenderjob* com acesso de leitura público.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Para escrever ficheiros de saída para o contentor, o Batch tem de utilizar um token de Assinatura de Acesso Partilhado (SAS). Crie o token com o comando [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas). Este exemplo cria um símbolo para escrever a qualquer recipiente de bolhas na conta, e o token expira em 15 de novembro de 2021:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2021-11-15
```

Tome nota do token devolvido pelo comando, que é semelhante ao seguinte. Vais usar este símbolo num passo mais tarde.

`se=2021-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

## <a name="render-a-single-frame-scene"></a>Compor uma cena com um único fotograma

### <a name="create-a-job"></a>Criar uma tarefa

Crie uma tarefa de composição para executar no conjunto, com o comando [az batch job create](/cli/azure/batch/job#az-batch-job-create). Inicialmente, o trabalho não tem tarefas.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Criar uma tarefa

Utilize o comando [az batch task create](/cli/azure/batch/task#az-batch-task-create) para criar uma tarefa de composição no trabalho. Neste exemplo, especifique as definições da tarefa num ficheiro JSON. Na sua shell atual, crie um ficheiro com o nome *myrendertask.json* e, em seguida, copie e cole o seguinte conteúdo. Certifique-se de que copia todo o texto corretamente. (Pode transferir o ficheiro do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).)

A tarefa especifica um comando 3ds Max para compor um único fotograma da cena *MotionBlur-DragonFlying.max*.

Modifique os elementos `blobSource` e `containerURL` no ficheiro JSON, para que incluam o nome da sua conta de armazenamento e o token SAS. 

> [!TIP]
> O seu `containerURL` fim com o seu token SAS e é semelhante a: `https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Adicione a tarefa ao trabalho, com o seguinte comando:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

O Batch agenda a tarefa, e a tarefa é executada assim que um nó no conjunto estiver disponível.

### <a name="view-task-output"></a>Ver resultado das tarefas

A tarefa demora alguns minutos para ser executada. Utilize o comando [az batch task show](/cli/azure/batch/task#az-batch-task-show) para ver detalhes sobre a tarefa.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

A tarefa gera *dragon0001.jpg* no nó de computação e carrega-o para o contentor *job-myrenderjob* da sua conta de armazenamento. Para ver o resultado, transfira o ficheiro do armazenamento para o computador local, com o comando [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download).

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Abra *dragon.jpg* no seu computador. A imagem composta é semelhante à seguinte:

![Fotograma composto dragon 1](./media/tutorial-rendering-cli/dragon-frame.png) 

## <a name="scale-the-pool"></a>Dimensionar o conjunto

Agora, modifique o conjunto para preparar um trabalho de composição maior, com vários fotogramas. O Batch fornece várias formas de dimensionar os recursos de computação, incluindo o [dimensionamento automático](batch-automatic-scaling.md) que adiciona ou remove nós à medida que a tarefa requer alterações. Para este exemplo básico, utilize o comando [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) para aumentar o número de nós de baixa prioridade no conjunto para *6*:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

O conjunto demora alguns minutos a redimensionar. Durante o processo, configure as tarefas seguintes para serem executadas no trabalho de composição existente.

## <a name="render-a-multiframe-scene"></a>Compor uma cena com vários fotogramas

Tal como no exemplo de fotograma único, utilize o comando [az batch task create](/cli/azure/batch/task#az-batch-task-create) para criar tarefas de composição no trabalho com o nome *myrenderjob*. Aqui, especifique as definições de tarefas num ficheiro JSON denominado *myrendertask_multi.json*. (Pode descarregar o ficheiro a partir do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json).) Cada uma das seis tarefas especifica uma linha de comando Arnold para render um quadro da cena 3ds Max *MotionBlur-DragonFlying.max*.

Crie um ficheiro na sua shell atual com o nome *myrendertask_multi.json* e copie e cole o conteúdo do ficheiro transferido. Modifique os elementos `blobSource` e `containerURL` no ficheiro JSON, para incluir o nome da sua conta de armazenamento e o token SAS. Não se esqueça de alterar as definições para cada uma das seis tarefas. Guarde o ficheiro e execute o seguinte comando para colocar as tarefas em fila:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Ver resultado das tarefas

A tarefa demora alguns minutos para ser executada. Utilize o comando [az batch task list](/cli/azure/batch/task#az-batch-task-list) para ver o estado das tarefas. Por exemplo:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Utilize o comando [az batch task show](/cli/azure/batch/task#az-batch-task-show) para ver detalhes sobre tarefas individuais. Por exemplo:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```

As tarefas geram ficheiros de saída nomeados *dragon0002.jpg*  -  *dragon0007.jpg* nos nós de computação e enviam-nos para o contentor *de trabalho-myrenderjob* na sua conta de armazenamento. Para ver o resultado, transfira os ficheiros para uma pasta para o computador local, com o comando [az storage blob download-batch](/cli/azure/storage/blob). Por exemplo:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Abra um dos ficheiros no seu computador. O fotograma composto 6 é semelhante ao seguinte:

![Fotograma composto dragon 6](./media/tutorial-rendering-cli/dragon-frame6.png) 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, a conta do Batch, os conjuntos e todos os recursos relacionados. Elimine os recursos da seguinte forma:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Carregar cenas para o armazenamento do Azure
> - Criar um conjunto do Batch para composição
> - Compor uma cena com um único fotograma através do Arnold
> - Dimensionar o conjunto e compor uma cena com vários fotogramas
> - Transferir o resultado composto

Para saber mais sobre a renderização em escala de nuvem, consulte a documentação de renderização do Lote.

> [!div class="nextstepaction"]
> [Serviço de Composição do Batch](batch-rendering-service.md)
