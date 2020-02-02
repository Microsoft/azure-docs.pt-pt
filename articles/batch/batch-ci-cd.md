---
title: Utilize pipelines Azure para construir e implementar soluções HPC - Lote Azure
description: Saiba como implantar um pipeline de Build/versão para um aplicativo HPC em execução no lote do Azure.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: ee87cd7d80d4b24e8c52fb3c7dbb780d39071066
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935143"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usar Azure Pipelines para compilar e implantar soluções de HPC

Os serviços DevOpss do Azure fornecem uma variedade de ferramentas usadas pelas equipes de desenvolvimento ao criar um aplicativo personalizado. As ferramentas fornecidas pelo Azure DevOps podem traduzir para a criação automatizada e o teste de soluções de computação de alto desempenho. Este artigo demonstra como configurar uma CI (integração contínua) e um CD (implantação contínua) usando Azure Pipelines para uma solução de computação de alto desempenho implantada no lote do Azure.

O Azure Pipelines fornece uma variedade de processos de CI/CD modernos para compilar, implantar, testar e monitorar software. Esses processos aceleram sua entrega de software, permitindo que você se concentre em seu código em vez de oferecer suporte à infraestrutura e às operações.

## <a name="create-an-azure-pipeline"></a>Criar um pipeline do Azure

Neste exemplo, criaremos um pipeline de Build e versão para implantar uma infraestrutura do lote do Azure e liberarei um pacote de aplicativos. Supondo que o código seja desenvolvido localmente, esse é o fluxo de implantação geral:

![Diagrama mostrando o fluxo de implantação em nosso pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configuração

Para seguir as etapas neste artigo, você precisa de uma organização DevOps do Azure e um projeto de equipe.

* [Criar uma organização DevOps do Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Criar um projeto no Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controle do código-fonte do seu ambiente

O controle do código-fonte permite que as equipes acompanhem as alterações feitas na base de código e inspecione as versões anteriores dele.

Normalmente, o controle do código-fonte é considerado à mão com o código do software. E quanto à infraestrutura subjacente? Isso nos leva à infraestrutura como código, na qual usaremos Azure Resource Manager modelos ou outras alternativas de software livre para definir declarativamente nossa infraestrutura subjacente.

Esse exemplo conta muito com um número de modelos do Resource Manager (documentos JSON) e binários existentes. Você pode copiar esses exemplos em seu repositório e enviá-los por push para o Azure DevOps.

A estrutura de CodeBase usada neste exemplo é semelhante à seguinte;

* Uma pasta **ARM-templates** , que contém vários modelos de Azure Resource Manager. Os modelos são explicados neste artigo.
* Uma pasta **cliente-aplicativo** , que é uma cópia do [processamento de arquivos .net do lote do Azure com](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) o exemplo ffmpeg. Isso não é necessário para este artigo.
* Uma pasta **HPC-Application** , que é a versão do Windows 64 bits do [ffmpeg 3,4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Uma pasta **pipelines** . Ele contém um arquivo YAML que descreve nosso processo de compilação. Isso é discutido no artigo.

Esta seção pressupõe que você esteja familiarizado com o controle de versão e criando modelos do Resource Manager. Se você não estiver familiarizado com esses conceitos, consulte as páginas a seguir para obter mais informações.

* [O que é o controle do código-fonte?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Understand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/templates/template-syntax.md) (Compreender a estrutura e a sintaxe dos Modelos do Azure Resource Manager)

#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Este exemplo utiliza vários modelos do Resource Manager para implantar nossa solução. Para fazer isso, usamos vários modelos de recursos (semelhantes a unidades ou módulos) que implementam uma parte específica da funcionalidade. Também usamos um modelo de solução de ponta a ponta que é responsável por reunir esses recursos subjacentes. Há alguns benefícios para essa abordagem:

* Os modelos de funcionalidade subjacentes podem ser testados em unidade individualmente.
* Os modelos de funcionalidade subjacentes podem ser definidos como um padrão dentro de uma organização e reutilizados em várias soluções.

Para este exemplo, há um modelo de solução de ponta a ponta (Deployment. JSON) que implanta três modelos. Os modelos subjacentes são modelos de funcionalidade, responsáveis pela implantação de um aspecto específico da solução.

![Exemplo de estrutura de modelo vinculada usando modelos de Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

O primeiro modelo que iremos examinar é para uma conta de armazenamento do Azure. Nossa solução requer uma conta de armazenamento para implantar o aplicativo em nossa conta do lote. Vale a pena conhecer o guia de [referência do modelo do Resource Manager para os tipos de recurso Microsoft. Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) ao criar modelos do Resource Manager para contas de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Em seguida, veremos o modelo de conta do lote do Azure. A conta do lote do Azure atua como uma plataforma para executar vários aplicativos em pools (agrupamentos de computadores). Vale a pena conhecer o guia de [referência do modelo do Resource Manager para tipos de recursos Microsoft. batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Resource Manager para contas do lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

O próximo modelo mostra um exemplo de criação de um pool do lote do Azure (os computadores de back-end para processar nossos aplicativos). Vale a pena estar ciente do [Guia de referência do modelo do Resource Manager para tipos de recursos Microsoft. batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Resource Manager para pools de contas do lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Por fim, temos um modelo que funciona de forma semelhante a um orquestrador. Este modelo é responsável por implantar os modelos de funcionalidade.

Você também pode saber mais sobre como [criar modelos de Azure Resource Manager vinculados](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) em um artigo separado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>A solução HPC

A infraestrutura e o software podem ser definidos como código e colocalizados no mesmo repositório.

Para essa solução, o ffmpeg é usado como o pacote de aplicativos. O pacote ffmpeg pode ser baixado [aqui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Exemplo de estrutura de repositório git](media/batch-ci-cd/git-repository.jpg)

Há quatro seções principais para este repositório:

* A pasta **ARM-templates** que armazena nossa infraestrutura como código
* A pasta **HPC-Application** que contém os binários para ffmpeg
* A pasta **pipelines** que contém a definição para o pipeline de compilação.
* **Opcional**: a pasta **cliente-aplicativo** que armazenaria o código para o aplicativo .net. Não usamos isso no exemplo, mas em seu próprio projeto, talvez você queira executar execuções do aplicativo do lote HPC por meio de um aplicativo cliente.

> [!NOTE]
> Esse é apenas um exemplo de uma estrutura para uma base de código. Essa abordagem é usada para fins de demonstração de que o aplicativo, a infraestrutura e o código de pipeline são armazenados no mesmo repositório.

Agora que o código-fonte está configurado, podemos começar a primeira compilação.

## <a name="continuous-integration"></a>Integração contínua

[Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), em Azure DevOps Services, ajuda você a implementar um pipeline de compilação, teste e implantação para seus aplicativos.

Nesse estágio do pipeline, os testes são normalmente executados para validar o código e criar as partes apropriadas do software. O número e os tipos de testes e as tarefas adicionais que você executar dependerão da sua estratégia de Build e versão mais ampla.

## <a name="preparing-the-hpc-application"></a>Preparando o aplicativo HPC

Neste exemplo, iremos nos concentrar na pasta **HPC-Application** . A pasta **HPC-Application** é o software ffmpeg que será executado de dentro da conta do lote do Azure.

1. Navegue até a seção Builds de Azure Pipelines em sua organização DevOps do Azure. Crie um **novo pipeline**.

    ![Criar um novo pipeline de compilação](media/batch-ci-cd/new-build-pipeline.jpg)

1. Você tem duas opções para criar um pipeline de compilação:

    a. [Usando o designer visual](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Para usar isso, clique em "usar o designer visual" na página **novo pipeline** .

    b. [Usando compilações do YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Você pode criar um novo pipeline do YAML clicando na opção Azure Repos ou GitHub na página novo pipeline. Como alternativa, você pode armazenar o exemplo abaixo no controle do código-fonte e fazer referência a um arquivo YAML existente clicando no Visual Designer e, em seguida, usando o modelo YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Depois que a compilação for configurada conforme necessário, selecione **salvar & fila**. Se você tiver a integração contínua habilitada (na seção **disparadores** ), a compilação será disparada automaticamente quando uma nova confirmação para o repositório for feita, atendendo as condições definidas na compilação.

    ![Exemplo de um pipeline de compilação existente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Exiba atualizações dinâmicas no progresso de sua compilação no Azure DevOps navegando até a seção **Build** de Azure pipelines. Selecione a compilação apropriada de sua definição de compilação.

    ![Exibir saídas dinâmicas de sua compilação](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se você usar um aplicativo cliente para executar seu aplicativo do lote HPC, será necessário criar uma definição de compilação separada para esse aplicativo. Você pode encontrar vários guias de instruções na documentação do [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) .

## <a name="continuous-deployment"></a>Implementação contínua

Azure Pipelines também usado para implantar seu aplicativo e a infraestrutura subjacente. Os [pipelines de versão](https://docs.microsoft.com/azure/devops/pipelines/release) são o componente que permite a implantação contínua e automatiza o processo de liberação.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Implantando seu aplicativo e a infraestrutura subjacente

Há várias etapas envolvidas na implantação da infraestrutura. Como nós usamos [modelos vinculados](../azure-resource-manager/templates/linked-templates.md), esses modelos precisarão ser acessíveis de um ponto de extremidade público (http ou HTTPS). Isso pode ser um repositório no GitHub ou uma conta de armazenamento de BLOBs do Azure ou outro local de armazenamento. Os artefatos de modelo carregados podem permanecer seguros, pois podem ser mantidos em um modo privado, mas acessados usando alguma forma de token SAS (assinatura de acesso compartilhado). O exemplo a seguir demonstra como implantar uma infraestrutura com modelos de um blob de armazenamento do Azure.

1. Crie uma **nova definição de versão**e selecione uma definição vazia. Em seguida, precisamos renomear o ambiente recém-criado para algo relevante para nosso pipeline.

    ![Pipeline de liberação inicial](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência no pipeline de compilação para obter a saída para nosso aplicativo HPC.

    > [!NOTE]
    > Mais uma vez, observe o **alias de origem**, pois isso será necessário quando as tarefas forem criadas dentro da definição da versão.

    ![Criar um link de artefato para o HPCApplicationPackage no pipeline de compilação apropriado](media/batch-ci-cd/Release-1.jpg)

1. Crie um link para outro artefato, desta vez, um repositório do Azure. Isso é necessário para acessar os modelos do Resource Manager armazenados em seu repositório. Como os modelos do Resource Manager não exigem compilação, você não precisa enviá-los por meio de um pipeline de compilação.

    > [!NOTE]
    > Mais uma vez, observe o **alias de origem**, pois isso será necessário quando as tarefas forem criadas dentro da definição da versão.

    ![Criar um link de artefato para o Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Navegue até a seção **variáveis** . É recomendável criar um número de variáveis em seu pipeline, para que você não esteja inserindo as mesmas informações em várias tarefas. Essas são as variáveis usadas neste exemplo e como elas afetam a implantação.

    * **applicationStorageAccountName**: nome da conta de armazenamento para armazenar binários do aplicativo HPC
    * **batchAccountApplicationName**: nome do aplicativo na conta do lote do Azure
    * **batchAccountName**: nome da conta do lote do Azure
    * **batchAccountPoolName**: o nome do pool de VMs que executa o processamento
    * **batchApplicationId**: ID exclusiva para o aplicativo do lote do Azure
    * **batchApplicationVersion**: versão semântica do seu aplicativo do lote (ou seja, os binários do ffmpeg)
    * **local**: local para os recursos do Azure a serem implantados
    * **resourceGroupName**: o nome do grupo de recursos a ser criado e o local em que os recursos serão implantados
    * **storageAccountName**: nome da conta de armazenamento para manter modelos vinculados do Resource Manager

    ![Exemplo de variáveis definidas para a versão Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Navegue até as tarefas do ambiente de desenvolvimento. No instantâneo abaixo, você pode ver seis tarefas. Essas tarefas vão: baixar os arquivos ffmpeg compactados, implantar uma conta de armazenamento para hospedar os modelos aninhados do Resource Manager, copiar esses modelos do Resource Manager para a conta de armazenamento, implantar a conta do lote e as dependências necessárias, criar um aplicativo no a conta do lote do Azure e carregar o pacote de aplicativos para a conta do lote do Azure.

    ![Exemplo das tarefas usadas para liberar o aplicativo HPC para o lote do Azure](media/batch-ci-cd/Release-3.jpg)

1. Adicione a tarefa **baixar artefato de pipeline (versão prévia)** e defina as seguintes propriedades:
    * **Nome para exibição:** Baixar o ApplicationPackage para o agente
    * **O nome do artefato a ser baixado:** HPC-Application
    * **Caminho para baixar para**: $ (System. DefaultWorkingDirectory)

1. Crie uma conta de armazenamento para armazenar seus artefatos. Uma conta de armazenamento existente da solução pode ser usada, mas para o exemplo independente e isolamento de conteúdo, estamos criando uma conta de armazenamento dedicada para nossos artefatos (especificamente, os modelos do Resource Manager).

    Adicione a tarefa de **implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    * **Nome para exibição:** Implantar conta de armazenamento para modelos do Resource Manager
    * **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    * **Ação**: criar ou atualizar grupo de recursos
    * **Grupo de recursos**: $ (resourceGroupName)
    * **Local**: $ (local)
    * **Template**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **Substituir parâmetros de modelo**:-accountName $ (storageAccountName)

1. Carregue os artefatos do controle do código-fonte na conta de armazenamento. Há uma tarefa de pipeline do Azure para executar isso. Como parte dessa tarefa, a URL do contêiner da conta de armazenamento e o token SAS podem ser enviados para uma variável no Azure Pipelines. Isso significa que ele pode ser reutilizado em toda esta fase de agente.

    Adicione a tarefa **cópia de arquivo do Azure** e defina as seguintes propriedades:
    * **Source:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Tipo de conexão do Azure**: Azure Resource Manager
    * **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    * **Tipo de destino**: BLOB do Azure
    * **Conta de armazenamento do RM**: $ (storageAccountName)
    * **Nome do contêiner**: modelos
    * **URI do contêiner de armazenamento**: templateContainerUri
    * **Token SAS do contêiner de armazenamento**: templateContainerSasToken

1. Implante o modelo do Orchestrator. Lembre-se do modelo do Orchestrator anteriormente, você observará que houve parâmetros para a URL do contêiner da conta de armazenamento, além do token SAS. Você deve observar que as variáveis necessárias no modelo do Resource Manager são mantidas na seção de variáveis da definição de versão ou foram definidas de outra tarefa de Azure Pipelines (por exemplo, parte da tarefa cópia de blob do Azure).

    Adicione a tarefa de **implantação do grupo de recursos do Azure** e defina as seguintes propriedades:
    * **Nome para exibição:** Implantar o lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    * **Ação**: criar ou atualizar grupo de recursos
    * **Grupo de recursos**: $ (resourceGroupName)
    * **Local**: $ (local)
    * **Template**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **Substituir parâmetros de modelo**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Uma prática comum é usar Azure Key Vault tarefas. Se a entidade de serviço (conexão à sua assinatura do Azure) tiver um conjunto de políticas de acesso apropriado, ele poderá baixar segredos de um Azure Key Vault e ser usado como variáveis em seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword poderia ser referenciado com $ (sshPassword) na definição da versão.

1. As próximas etapas chamam o CLI do Azure. O primeiro é usado para criar um aplicativo no lote do Azure. e carregar pacotes associados.

    Adicione a tarefa de **CLI do Azure** e defina as seguintes propriedades:
    * **Nome para exibição:** Criar aplicativo na conta do lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    * **Local do script**: script embutido
    * **Script embutido**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. A segunda etapa é usada para carregar pacotes associados ao aplicativo. Em nosso caso, os arquivos ffmpeg.

    Adicione a tarefa de **CLI do Azure** e defina as seguintes propriedades:
    * **Nome para exibição:** Carregar pacote na conta do lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura do Azure apropriada
    * **Local do script**: script embutido
    * **Script embutido**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > O número de versão do pacote de aplicativos é definido como uma variável. Isso é conveniente se a substituição de versões anteriores do pacote funcionar para você e se você quiser controlar manualmente o número de versão do pacote enviado por push para o lote do Azure.

1. Crie uma nova versão selecionando **liberar > criar uma nova versão**. Depois de disparado, selecione o link para a nova versão para exibir o status.

1. Você pode exibir a saída ao vivo do agente selecionando o botão **logs** abaixo do seu ambiente.

    ![Exibir o status da sua versão](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testando o ambiente

Quando o ambiente estiver configurado, confirme se os testes a seguir podem ser concluídos com êxito.

Conecte-se à nova conta do lote do Azure usando o CLI do Azure de um prompt de comando do PowerShell.

* Entre em sua conta do Azure com `az login` e siga as instruções para autenticar.
* Agora, autentique a conta do lote: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Listar os aplicativos disponíveis

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verificar se o pool é válido

```azurecli
az batch pool list
```

Observe o valor de `currentDedicatedNodes` da saída desse comando. Esse valor é ajustado no próximo teste.

#### <a name="resize-the-pool"></a>Redimensionar o pool

Redimensione o pool para que haja nós de computação disponíveis para teste de trabalho e tarefa, verifique com o comando de lista de pools para ver o status atual até que o redimensionamento seja concluído e que haja nós disponíveis

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passos seguintes

Além deste artigo, há dois tutoriais que utilizam o ffmpeg, usando o .NET e o Python. Consulte estes tutoriais para obter mais informações sobre como interagir com uma conta do lote por meio de um aplicativo simples.

* [Executar uma carga de trabalho paralela com o lote do Azure usando a API do Python](tutorial-parallel-python.md)
* [Executar uma carga de trabalho paralela com o lote do Azure usando a API do .NET](tutorial-parallel-dotnet.md)
