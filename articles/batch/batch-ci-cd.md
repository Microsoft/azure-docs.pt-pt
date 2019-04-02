---
title: Utilize Pipelines do Azure para criar e implementar soluções HPC - Azure Batch | Documentos da Microsoft
description: Saiba como implementar um pipeline de compilação/release para uma aplicação de HPC em execução no Azure Batch.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
services: batch
ms.openlocfilehash: 1e3b2d42ae390b9eb755e568b82fc1a90e0eae6a
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807050"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Utilize Pipelines do Azure para criar e implementar soluções HPC

Serviços de DevOps do Azure fornecem uma variedade de ferramentas utilizadas por equipes de desenvolvimento ao criar uma aplicação personalizada. Ferramentas fornecidas pelo Azure DevOps podem traduzir-se em automatizada de criação e teste das soluções de computação de alto desempenho. Este artigo demonstra como configurar uma integração contínua (CI) e a implementação contínua (CD) usando que solução implementada no Azure Batch de computação de Pipelines do Azure para um elevado desempenho.

Pipelines do Azure fornece um conjunto de processos de CI/CD modernos para criação, implementação, teste e software de monitorização. Estes processos acelerar a entrega de software, permitindo-lhe concentrar-se no seu código, em vez de oferecer suporte a infraestrutura e operações.

## <a name="create-an-azure-pipeline"></a>Criar um Pipeline do Azure

Neste exemplo, iremos criar uma compilação e versão de pipeline para implementar uma infraestrutura do Azure Batch e lançar um pacote de aplicação. Supondo que o código é desenvolvido localmente, este é o fluxo geral de implementação:

![Diagrama que mostra o fluxo de implementação em nosso Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configurar

Para seguir os passos neste artigo, terá de uma organização de DevOps do Azure e um projeto de equipe.

* [Criar uma organização de DevOps do Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Criar um projeto no Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controlo de origem para o seu ambiente

Controle de origem permite que as equipes controlar as alterações efetuadas para a base de código e inspecionar as versões anteriores do código.

Normalmente, controlo de origem é considerado lado a lado com o código de software. E quanto a infraestrutura subjacente? Isso nos leva à infraestrutura como código, onde usaremos os modelos Azure Resource Manager ou outras alternativas de código-fonte aberto para definir de maneira declarativa nossa infraestrutura subjacente.

Este exemplo depende muito de um número de modelos do Resource Manager (documentos JSON) e binários existentes. Pode copiar estes exemplos no seu repositório e emiti-las para o Azure DevOps.

A estrutura de base de código usada neste exemplo é semelhante ao seguinte;

* Uma **modelos de arm** pasta, que contém um número de modelos Azure Resource Manager. Os modelos são explicados neste artigo.
* R **aplicação de cliente** pasta, que é uma cópia da [do Azure Batch .NET ficheiro processar com o ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) exemplo. Não é necessária para este artigo.
* Uma **aplicação hpc** pasta, o que é o Windows de 64 bits versão do [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* R **pipelines** pasta. Contém um ficheiro YAML descrevendo o nosso processo de compilação. Isso será discutido no artigo.

Esta secção pressupõe que esteja familiarizado com os modelos de Gestor de recursos de controle e design de versão. Se não estiver familiarizado com esses conceitos, consulte as seguintes páginas para obter mais informações.

* [O que é o controlo de origem?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Understand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/resource-group-authoring-templates.md) (Compreender a estrutura e a sintaxe dos Modelos do Azure Resource Manager)

#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Neste exemplo tira partido de vários modelos do Resource Manager para implementar a nossa solução. Para tal, utilizamos um número de capacidade modelos (semelhantes a unidades ou módulos) que implementam uma parte específica da funcionalidade. Também utilizamos um modelo de solução ponto a ponto que é responsável por colocar tais subjacentes recursos em conjunto. Existem alguns benefícios dessa abordagem:

* Os modelos de capacidade subjacente podem ser individualmente, testada de unidade.
* Os modelos de capacidade subjacente podem ser definidos como um padrão dentro de uma organização e ser reutilizados em múltiplas soluções.

Neste exemplo, existe um modelo de solução ponto a ponto (deployment.json) que implementa três modelos. Os modelos subjacentes são modelos de capacidade, responsáveis pela implantação de um aspecto específico da solução.

![Exemplo de estrutura de modelo ligado através de modelos do Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

O primeiro modelo que veremos destina-se uma conta de armazenamento do Azure. Nossa solução requer uma conta de armazenamento para implementar a aplicação em nossa conta do Batch. Vale a pena esteja ciente de que o [guia de referência de modelo do Resource Manager para tipos de recurso Microsoft. Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) ao criar modelos do Resource Manager para contas de armazenamento.

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

Em seguida, vamos ver o modelo de conta do Azure Batch. A conta do Batch do Azure funciona como uma plataforma para executar vários aplicativos em conjuntos (agrupamentos de máquinas). Vale a pena esteja ciente de que o [guia de referência de modelo do Resource Manager para tipos de recursos do batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Resource Manager para as contas do Batch.

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

O modelo seguinte mostra um exemplo de criação de um conjunto do Batch do Azure (as máquinas de back-end para processar a nossos aplicativos). Vale a pena esteja ciente de que o [guia de referência de modelo do Resource Manager para tipos de recursos do batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Resource Manager para conjuntos de conta do Batch.

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

Por fim, temos um modelo que age semelhante a um orquestrador. Este modelo é responsável por implementar os modelos de capacidade.

Também pode encontrar mais informações sobre [criação de modelos do Azure Resource Manager ligados](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) num artigo separado.

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

A infraestrutura e software podem ser definidos como o código e colocalizados no mesmo repositório.

Para esta solução, o ffmpeg é utilizado como o pacote de aplicação. Pode ser transferido o pacote do ffmpeg [aqui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Estrutura de repositório de Git de exemplo](media/batch-ci-cd/git-repository.jpg)

Existem quatro seções principais para este repositório:

* O **modelos de arm** pasta que armazena a nossa infraestrutura como código
* O **aplicação hpc** pasta que contém os binários do ffmpeg
* O **pipelines** pasta que contém a definição para nosso pipeline de compilação.
* **Opcional**: O **aplicação de cliente** pasta que seria armazenar o código do aplicativo .NET. Não usamos isso no exemplo, mas no seu próprio projeto, pode desejar executar execuções do aplicativo de Batch de HPC através de uma aplicação de cliente.

> [!NOTE]
> Este é apenas um exemplo de uma estrutura para uma base de código. Essa abordagem é utilizada para os fins de demonstração que o aplicativo, a infraestrutura e o código de pipeline são armazenados no mesmo repositório.

Agora que o código-fonte estiver configurado, podemos começar a primeira compilação.

## <a name="continuous-integration"></a>Integração contínua

[Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), nos serviços de DevOps do Azure, ajuda-o a implementar um pipeline de compilação, teste e implementação para as suas aplicações.

Nesta fase do seu pipeline, os testes são normalmente executados para validar o código e criar as partes apropriadas do software. O número e tipos de testes e quaisquer tarefas adicionais que executa dependem de sua compilação mais ampla e estratégia de versão.

## <a name="preparing-the-hpc-application"></a>Preparando o aplicativo HPC

Neste exemplo, nos concentraremos no **aplicação hpc** pasta. O **aplicação hpc** pasta é o software do ffmpeg que será executada a partir da conta do Azure Batch.

1. Navegue para a secção de compilações de Pipelines do Azure na sua organização de DevOps do Azure. Criar uma **novo pipeline**.

    ![Criar um novo Pipeline de compilação](media/batch-ci-cd/new-build-pipeline.jpg)

1. Tem duas opções para criar um pipeline de compilação:

    a. [Usando o Designer Visual](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Para utilizá-lo, clique em "Utilizar o estruturador visual" sobre o **novo pipeline** página.

    b. [Baseia-se usar YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Pode criar um novo pipeline YAML clicando no repositórios do Azure ou a opção do GitHub na nova página de pipeline. Em alternativa, pode armazenar o exemplo abaixo no seu controle de origem e fazer referência a um ficheiro YAML existente ao clicar no Visual Designer e, em seguida, usando o modelo YAML.

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

1. Assim que a compilação está configurada conforme necessário, selecione **guardar e colocar em fila**. Se tiver habilitada a integração contínua (no **Acionadores** secção), a compilação acionará automaticamente quando é feita uma nova consolidação no repositório, cumprir as condições definidas na compilação.

    ![Exemplo de um Pipeline de compilação existente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Ver as atualizações dinâmicas do progresso da sua compilação em DevOps do Azure ao navegar para o **criar** secção de Pipelines do Azure. Selecione a compilação apropriada de sua definição de compilação.

    ![Ver saídas em direto de sua compilação](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se utilizar uma aplicação de cliente para executar o aplicativo de Batch do HPC, terá de criar uma definição de compilação separado para essa aplicação. Pode encontrar um número de guias de procedimentos no [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) documentação.

## <a name="continuous-deployment"></a>Implementação contínua

Pipelines do Azure, também é utilizados para implementar a sua aplicação e a infraestrutura subjacente. [Lançar pipelines](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) é o componente que permite a implementação contínua e automatiza o processo de liberação.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Implementar a aplicação e infraestrutura subjacente

Existem vários passos envolvidos na implementação da infraestrutura. Como usamos [ligado modelos](../azure-resource-manager/resource-group-linked-templates.md), esses modelos têm de ser acessível a partir de um ponto final público (HTTP ou HTTPS). Isto pode ser um repositório no GitHub, ou uma conta de armazenamento de Blobs do Azure ou noutra localização de armazenamento. Os artefactos de modelo carregado podem permanecem seguros, como eles podem ser mantidos num modo privado mas acedidos através de alguma forma de token de assinatura (SAS) de acesso partilhado. O exemplo seguinte demonstra como implementar uma infraestrutura com modelos de um blob de armazenamento do Azure.

1. Criar uma **nova definição de versão**e selecione uma definição vazia. Em seguida, é necessário mudar o nome do ambiente recentemente criado para algo relevantes para nosso pipeline.

    ![Pipeline de lançamento inicial](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência no Pipeline de compilação para obter o resultado para a nossa aplicação HPC.

    > [!NOTE]
    > Mais uma vez, tenha em atenção a **Alias de origem**, como esse valor será necessário quando as tarefas são criadas dentro da definição de versão.

    ![Criar uma ligação de artefacto para o HPCApplicationPackage no pipeline de compilação apropriado](media/batch-ci-cd/Release-1.jpg)

1. Crie uma ligação para outro artefacto, desta vez, um repositório do Azure. Isto é necessário para aceder aos modelos do Resource Manager armazenados no seu repositório. Como os modelos do Resource Manager não necessitam de compilação, não precisa de emiti-las por meio de um pipeline de compilação.

    > [!NOTE]
    > Mais uma vez, tenha em atenção a **Alias de origem**, como esse valor será necessário quando as tarefas são criadas dentro da definição de versão.

    ![Criar uma ligação de artefacto para os repositórios do Azure](media/batch-ci-cd/Release-2.jpg)

1. Navegue para o **variáveis** secção. É recomendado para criar um número de variáveis no seu pipeline, pelo que não são de inserir as mesmas informações em várias tarefas. Estas são as variáveis utilizadas neste exemplo, e como eles afetam a implementação.

    * **applicationStorageAccountName**: Nome da conta do Storage para armazenar os binários de aplicativos de HPC
    * **batchAccountApplicationName**: Nome da aplicação da conta do Azure Batch
    * **batchAccountName**: Nome da conta do Batch do Azure
    * **batchAccountPoolName**: Nome do conjunto de VMs em execução do processamento
    * **batchApplicationId**: ID exclusivo para a aplicação do Azure Batch
    * **batchApplicationVersion**: Versão semântica da sua aplicação de lote (ou seja, os binários do ffmpeg)
    * **Localização**: Localização para os recursos do Azure ser implementada
    * **resourceGroupName**: Nome do grupo de recursos a ser criado, e em que os seus recursos serão implementados
    * **storageAccountName**: Nome da conta do Storage para armazenar modelos do Gestor de recursos ligados

    ![Exemplo de variáveis definidas para o lançamento de Pipelines do Azure](media/batch-ci-cd/Release-4.jpg)

1. Navegue para as tarefas no ambiente de desenvolvimento. Na captura de ecrã, abaixo pode ver seis tarefas. Estas tarefas serão: transferir os ficheiros do ffmpeg zipado, implemente uma conta de armazenamento para alojar os modelos do Resource Manager aninhados, copie os modelos do Resource Manager para a conta de armazenamento, implementar a conta do batch e as dependências necessárias, criar uma aplicação no a conta do Batch do Azure e carregar o pacote de aplicação para a conta do Batch do Azure.

    ![Exemplo das tarefas utilizadas para libertar o aplicativo HPC para o Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Adicionar a **transferir o artefacto de Pipeline (pré-visualização)** de tarefas e defina as propriedades seguintes:
    * **Nome a apresentar:** Transferir ApplicationPackage ao agente
    * **O nome do artefacto a transferir:** aplicação hpc
    * **Caminho para transferir para**: $(System.DefaultWorkingDirectory)

1. Crie uma conta de armazenamento para armazenar os artefactos. Uma conta de armazenamento existente da solução poderia ser usada, mas para a amostra autossuficiente e isolamento de conteúdo, estamos a efetuar uma conta de armazenamento dedicado para nosso artefactos (especificamente os modelos do Resource Manager).

    Adicionar a **implementação de grupo de recursos do Azure** de tarefas e defina as propriedades seguintes:
    * **Nome a apresentar:** Implementar a conta de armazenamento para modelos do Resource Manager
    * **Subscrição do Azure:** Selecione a subscrição do Azure adequada
    * **Ação**: Criar ou atualizar o grupo de recursos
    * **Grupo de recursos**: $(resourceGroupName)
    * **Localização**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Substitua os parâmetros de modelo**: - accountName $(storageAccountName)

1. Carregar os artefatos do controle de origem para a conta de armazenamento. Existe uma tarefa de Pipeline do Azure para realizar isso. Como parte desta tarefa, o URL de contentor da conta de armazenamento e o Token de SAS podem ter saída a uma variável em Pipelines do Azure. Isso significa que pode ser reutilizado durante esta fase de agente.

    Adicionar a **cópia de ficheiros do Azure** de tarefas e defina as propriedades seguintes:
    * **Source:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Tipo de ligação do Azure**: Azure Resource Manager
    * **Subscrição do Azure:** Selecione a subscrição do Azure adequada
    * **Tipo de destino**: Blob do Azure
    * **Conta de armazenamento de RM**: $(storageAccountName)
    * **Nome do contentor**: modelos
    * **URI do contentor de armazenamento**: templateContainerUri
    * **Token de SAS de contentor do armazenamento**: templateContainerSasToken

1. Implemente o modelo do orchestrator. Lembre-se o modelo de orchestrator de antes, notará que havia parâmetros para o URL de contentor de conta de armazenamento, além do token SAS. Deve observar que as variáveis necessárias no modelo do Resource Manager ou são mantidas na secção de variáveis da definição de versão ou que foram definidas de outra tarefa de Pipelines do Azure (por exemplo, a parte da tarefa de cópia de Blobs do Azure).

    Adicionar a **implementação de grupo de recursos do Azure** de tarefas e defina as propriedades seguintes:
    * **Nome a apresentar:** Implementar o Azure Batch
    * **Subscrição do Azure:** Selecione a subscrição do Azure adequada
    * **Ação**: Criar ou atualizar o grupo de recursos
    * **Grupo de recursos**: $(resourceGroupName)
    * **Localização**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Substitua os parâmetros de modelo**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Uma prática comum é utilizar tarefas do Azure Key Vault. Se o Principal de serviço (ligação à sua subscrição do Azure) tiver uma definir as políticas de acesso adequados, pode transferir segredos a partir de um Azure Key Vault e servir como variáveis no seu pipeline. O nome do segredo do será definido com o valor associado. Por exemplo, um segredo de sshPassword poderia ser referenciado com $(sshPassword) na definição de versão.

1. Os passos seguintes chamam a CLI do Azure. O primeiro é utilizado para criar uma aplicação no Azure Batch. e carregar pacotes associados.

    Adicionar a **CLI do Azure** de tarefas e defina as propriedades seguintes:
    * **Nome a apresentar:** Criar aplicação da conta de Batch do Azure
    * **Subscrição do Azure:** Selecione a subscrição do Azure adequada
    * **Localização do script**: Script inline
    * **O Script inline de**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. O segundo passo é utilizado para carregar os pacotes associados à aplicação. No nosso caso, os ficheiros do ffmpeg.

    Adicionar a **CLI do Azure** de tarefas e defina as propriedades seguintes:
    * **Nome a apresentar:** Carregar o pacote para a conta do Azure Batch
    * **Subscrição do Azure:** Selecione a subscrição do Azure adequada
    * **Localização do script**: Script inline
    * **O Script inline de**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > O número de versão do pacote de aplicação está definido como uma variável. Isso é conveniente se substituir as versões anteriores do pacote funcione para si, e se quiser controlar manualmente o número de versão do pacote enviado para o Azure Batch.

1. Criar uma nova versão, selecionando **versão > criar uma nova versão**. Quando acionado, selecione a ligação para a nova versão para ver o estado.

1. Pode ver a saída em direto do agente, selecionando o **registos** botão por baixo do seu ambiente.

    ![Ver o estado da sua liberação](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>O ambiente de teste

Assim que o ambiente estiver configurado, certifique-se que os seguintes testes podem ser concluídos com êxito.

Ligue-se para a nova conta de Batch do Azure com a CLI do Azure num prompt de comando do PowerShell.

* Inicie sessão na sua conta do Azure com `az login` e siga as instruções para se autenticar.
* Autenticar-se a conta do Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Lista as aplicações disponíveis

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verifique o conjunto é válido

```azurecli
az batch pool list
```

Tenha em atenção o valor de `currentDedicatedNodes` a partir da saída deste comando. Este valor é ajustado no próximo teste.

#### <a name="resize-the-pool"></a>Redimensione o conjunto

Redimensione o conjunto, pelo que existem disponíveis para o trabalho e tarefas de teste de nós de computação, verifique com o comando de lista de agrupamento para ver o estado atual até que o redimensionamento foi concluída e nós disponíveis

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passos Seguintes

Além deste artigo, existem dois tutoriais que utilizam o ffmpeg, usando o .NET e Python. Consulte estes tutoriais para obter mais informações sobre como interagir com uma conta do Batch através de um aplicativo simples.

* [Executar uma carga de trabalho paralela com o Azure Batch com a API Python](tutorial-parallel-python.md)
* [Executar uma carga de trabalho paralela com o Azure Batch com a API .NET](tutorial-parallel-dotnet.md)
