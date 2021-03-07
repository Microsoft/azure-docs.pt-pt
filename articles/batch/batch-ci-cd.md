---
title: Utilize gasodutos Azure para construir & implementar soluções HPC
description: Saiba como implantar um gasoduto de construção/libertação para uma aplicação HPC em execução no Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435550"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Utilize gasodutos Azure para construir e implementar soluções HPC

As ferramentas fornecidas pela Azure DevOps podem traduzir-se em construção automatizada e testes de soluções de computação de alto desempenho (HPC). [A Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) fornece uma gama de processos modernos de integração contínua (CI) e implementação contínua (CD) para a construção, implantação, teste e monitorização de software. Estes processos aceleram a sua entrega de software, permitindo-lhe focar-se no seu código em vez de apoiar infraestruturas e operações.

Este artigo explica como configurar processos de CI/CD utilizando [gasodutos Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines) para soluções HPC implementadas no Azure Batch.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, precisa de uma [organização Azure DevOps.](/azure/devops/organizations/accounts/create-organization) Também terá de [criar um projeto em Azure DevOps.](/azure/devops/organizations/projects/create-project)

É útil ter uma compreensão básica do controlo de [Origem](/azure/devops/user-guide/source-control) e sintaxe do [modelo do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) antes de começar.

## <a name="create-an-azure-pipeline"></a>Criar um Gasoduto Azure

Neste exemplo, você vai criar um oleoduto de construção e libertação para implantar uma infraestrutura Azure Batch e lançar um pacote de aplicações. Assumindo que o código é desenvolvido localmente, este é o fluxo geral de implantação:

![Diagrama que mostra o fluxo de implantação no gasoduto,](media/batch-ci-cd/DeploymentFlow.png)

Esta amostra utiliza vários modelos de Gestor de Recursos Azure e binários existentes. Pode copiar estes exemplos no seu repositório e empurrá-los para Azure DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>Compreenda os modelos do Gestor de Recursos Azure

Este exemplo utiliza vários modelos do Gestor de Recursos Azure para implementar a solução. Três modelos de capacidade (semelhantes a unidades ou módulos) são usados para implementar uma peça de funcionalidade específica. Um modelo de solução de ponta a ponta (deployment.js) é então usado para implementar os modelos de capacidade subjacentes. Esta [estrutura de modelo ligada ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) permite que cada modelo de capacidade seja testado e reutilizado individualmente através de soluções.

![Diagrama mostrando uma estrutura de modelo ligada usando modelos de Gestor de Recursos Azure.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Este modelo define uma conta de armazenamento Azure, que é necessária para implementar a aplicação na conta Batch. Para obter informações detalhadas, consulte o [guia de referência do modelo do Gestor de Recursos para os tipos de recursos Microsoft.Storage](/azure/templates/microsoft.storage/allversions).

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

O próximo modelo define uma [conta Azure Batch](accounts.md). A conta Batch funciona como uma plataforma para executar numerosas aplicações através de [piscinas.](nodes-and-pools.md#pools) Para obter informações detalhadas, consulte o guia de referência do [modelo do Gestor de Recursos para Microsoft.Battipos de recursos ch](/azure/templates/microsoft.batch/allversions).

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

O próximo modelo cria uma piscina de lote na conta Lote. Para obter informações detalhadas, consulte o guia de referência do [modelo do Gestor de Recursos para Microsoft.Battipos de recursos ch](/azure/templates/microsoft.batch/allversions).

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

O modelo final atua como um orquestrador, implantando os três modelos de capacidade subjacentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
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

### <a name="understand-the-hpc-solution"></a>Compreender a solução HPC

Como já foi notado, esta amostra utiliza vários modelos do Gestor de Recursos Azure e binários existentes. Pode copiar estes exemplos no seu repositório e empurrá-los para Azure DevOps.

Para esta solução, o ffmpeg é usado como pacote de aplicação. Você pode [baixar o pacote ffmpeg](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) se você ainda não o tem.

![Screenshot da estrutura do repositório.](media/batch-ci-cd/git-repository.jpg)

Há quatro secções principais para este repositório:

- Uma pasta **de modelos de braço,** contendo os modelos do Gestor de Recursos Azure
- Uma pasta **de aplicação hpc,** contendo a versão do Windows 64-bit de [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
- Uma pasta **de gasodutos,** contendo um ficheiro YAML que define o processo de construção do gasoduto.
- Opcional: Uma pasta **de aplicação ao cliente,** que é uma cópia do Processamento de [Ficheiros Azure Batch .NET com amostra de ffmpeg.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Esta aplicação não é necessária para este artigo.


> [!NOTE]
> Este é apenas um exemplo de uma estrutura para uma base de código. Esta abordagem é utilizada para demonstrar que a aplicação, a infraestrutura e o código do gasoduto são armazenados no mesmo repositório.

Agora que o código fonte está configurado, pode começar a primeira construção.

## <a name="continuous-integration"></a>Integração contínua

[A Azure Pipelines](/azure/devops/pipelines/get-started/), dentro dos Serviços Azure DevOps, ajuda-o a implementar um oleoduto de construção, teste e implantação para as suas aplicações.

Nesta fase do seu pipeline, os testes são normalmente executados para validar código e construir as peças apropriadas do software. O número e tipos de testes e quaisquer tarefas adicionais que executar dependerão da sua estratégia de construção e libertação mais ampla.

## <a name="prepare-the-hpc-application"></a>Preparar a aplicação HPC

Nesta secção, trabalhará com a pasta **de aplicação hpc.** Esta pasta contém o software (ffmpeg) que será executado dentro da conta Azure Batch.

1. Navegue para a secção Builds of Azure Pipelines na sua organização Azure DevOps. Criar um **novo oleoduto**.

    ![Screenshot do novo ecrã do gasoduto.](media/batch-ci-cd/new-build-pipeline.jpg)

1. Tem duas opções para criar um pipeline Build:

    a. [Utilize o Designer Visual.](/azure/devops/pipelines/get-started-designer) Para tal, selecione "Use o designer visual" na página do **novo pipeline.**

    b. [Use construções YAML](/azure/devops/pipelines/get-started-yaml). Pode criar um novo oleoduto YAML clicando na opção Azure Repos ou GitHub na página do **novo pipeline.** Em alternativa, pode armazenar o exemplo abaixo no seu controlo de origem e fazer referência a um ficheiro YAML existente selecionando o Visual Designer e, em seguida, utilizando o modelo YAML.

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

1. Uma vez que a construção esteja configurada conforme necessário, **selecione Save & Queue**. Se tiver uma integração contínua ativada (na secção **Gatilhos),** a construção ativará automaticamente quando for feita uma nova comprometição com o repositório, cumprindo as condições definidas na construção.

    ![Screenshot de um pipeline de construção existente.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Veja atualizações em direto sobre o progresso da sua construção em Azure DevOps navegando para a secção **Build** of Azure Pipelines. Selecione a construção adequada a partir da sua definição de construção.

    ![Screenshot de saídas ao vivo a partir da construção em Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se utilizar uma aplicação do cliente para executar a sua solução HPC, tem de criar uma definição de construção separada para essa aplicação. Pode encontrar uma série de guias na documentação do [Azure Pipelines.](/azure/devops/pipelines/get-started/index)

## <a name="continuous-deployment"></a>Implementação contínua

A Azure Pipelines também é usada para implantar a sua aplicação e infraestrutura subjacente. [Os gasodutos de desbloqueio](/azure/devops/pipelines/release) permitem a implantação contínua e automatizam o seu processo de libertação.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Implemente a sua aplicação e infraestrutura subjacente

Há uma série de passos envolvidos na implantação da infraestrutura. Como esta solução utiliza [modelos ligados,](../azure-resource-manager/templates/linked-templates.md)esses modelos terão de ser acessíveis a partir de um ponto final público (HTTP ou HTTPS). Isto pode ser um repositório no GitHub, ou uma conta de armazenamento Azure Blob, ou outro local de armazenamento. Os artefactos do modelo carregados podem permanecer seguros, uma vez que podem ser mantidos em modo privado, mas acedidos usando algum tipo de sinal de assinatura de acesso partilhado (SAS).

O exemplo a seguir demonstra como implantar uma infraestrutura com modelos a partir de uma bolha de armazenamento Azure.

1. Crie uma **nova definição de desbloqueio** e, em seguida, selecione uma definição vazia. Mude o nome do ambiente recém-criado para algo relevante para o seu oleoduto.

    ![Screenshot do oleoduto de lançamento inicial.](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência do Pipeline Build para obter a saída para a aplicação HPC.

    > [!NOTE]
    > Tome nota do **Alias de Origem,** pois será necessário quando as tarefas forem criadas dentro da Definição de Lançamento.

    ![Screenshot mostrando uma ligação de artefacto ao HPCApplicationPackage no pipeline de construção apropriado.](media/batch-ci-cd/Release-1.jpg)

1. Criar uma ligação com outro artefacto, desta vez, um Azure Repo. Isto é necessário para aceder aos modelos do Gestor de Recursos armazenados no seu repositório. Como os modelos do Gestor de Recursos não requerem compilação, não é necessário empurrá-los através de um pipeline de construção.

    > [!NOTE]
    > Mais uma vez, note o **Alias Fonte**, uma vez que este será necessário mais tarde.

    ![Screenshot mostrando uma ligação de artefacto ao Azure Repos.](media/batch-ci-cd/Release-2.jpg)

1. Navegue para a secção **de variáveis.** Você vai querer criar uma série de variáveis no seu pipeline para que você não tenha que reintrodutar a mesma informação em várias tarefas. Este exemplo utiliza as seguintes variáveis:

   - **aplicaçõesStorageAccountName**: Nome da conta de armazenamento que detém as binárias de aplicação HPC
   - **batchAccountApplicationName**: Nome da aplicação na conta Lote
   - **batchAccountName**: Nome da conta Lote
   - **batchAccountPoolName**: Nome do pool de VMs que fazem o processamento
   - **batchApplicationId**: ID exclusivo para a aplicação Batch
   - **batchApplicationVersion**: Versão semântica da sua aplicação Batch (isto é, os binaries ffmpeg)
   - **localização**: Localização dos recursos Azure a implantar
   - **nome do Grupo de Recursos**: Nome do grupo de recursos a criar e onde os seus recursos serão implantados
   - **armazenamentoSAme de** número : Nome da conta de armazenamento que detém os modelos de Gestor de Recursos ligados

   ![Screenshot mostrando variáveis definidas para o lançamento dos Gasodutos Azure.](media/batch-ci-cd/Release-4.jpg)

1. Navegue para as tarefas para o ambiente Dev. Na foto abaixo, pode ver seis tarefas. Estas tarefas: descarregar os ficheiros ffmpeg com fecho, implementar uma conta de armazenamento para hospedar os modelos de Gestor de Recursos aninhados, copiar esses modelos de Gestor de Recursos para a conta de armazenamento, implementar a conta de lote e dependências necessárias, criar uma aplicação na Conta Azure Batch e fazer o upload do pacote de aplicações para a Conta Azure Batch.

    ![Screenshot mostrando as tarefas utilizadas para libertar a aplicação HPC para O Lote Azure.](media/batch-ci-cd/Release-3.jpg)

1. Adicione a tarefa **de Pipeline Artifact (Pré-visualização)** e desafie as seguintes propriedades:
    - **Nome do visor:** Baixar ApplicationPackage para Agente
    - **O nome do artefacto para descarregar:** hpc-application
    - **Caminho para download para**: $(System.DefaultWorkingDirectory)

1. Crie uma Conta de Armazenamento para armazenar os seus modelos de Gestor de Recursos Azure. Uma conta de armazenamento existente da solução poderia ser usada, mas para apoiar esta amostra autossuficiente e isolamento de conteúdos, você vai fazer uma conta de armazenamento dedicada.

    Adicione a tarefa **de implantação do Grupo de Recursos Azure** e desenrine as seguintes propriedades:
    - **Nome do visor:** Implementar conta de armazenamento para modelos de Gestor de Recursos
    - **Assinatura Azure:** Selecione a subscrição apropriada do Azure
    - **Ação**: Criar ou atualizar grupo de recursos
    - **Grupo de Recursos**: $(nome do Grupo de Recursos)
    - **Localização**: $(localização)
    - **Modelo**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    - **Parâmetros do modelo de substituição**: -contaName $(storageAccountName)

1. Faça o upload dos artefactos do controlo de origem para a conta de armazenamento utilizando os gasodutos Azure. Como parte desta tarefa da Azure Pipelines, o recipiente de conta de armazenamento URI e SAS Token pode ser produzido para uma variável em Pipelines Azure, permitindo que sejam reutilizados ao longo desta fase de agente.

    Adicione a tarefa **Azure File Copy** e desaveja as seguintes propriedades:
    - **Fonte:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    - **Tipo de ligação Azure**: Gestor de Recursos Azure
    - **Assinatura Azure:** Selecione a subscrição apropriada do Azure
    - **Tipo de destino**: Azure Blob
    - **Conta de Armazenamento RM**: $(storageAccountName)
    - **Nome do recipiente**: modelos
    - **Contentor de Armazenamento URI**: modeloContainerUri
    - **Contentor de armazenamento SAS Token**: modeloContainerSasToken

1. Desloque o modelo do orquestrador. Este modelo inclui parâmetros para o recipiente de conta de armazenamento URI e SAS token. As variáveis exigidas no modelo de Gestor de Recursos são mantidas na secção de variáveis da definição de libertação, ou foram definidas a partir de outra tarefa dos Gasodutos Azure (por exemplo, parte da tarefa Azure Blob Copy).

    Adicione a tarefa **de implantação do Grupo de Recursos Azure** e desenrine as seguintes propriedades:
    - **Nome do visor:** Implementar lote Azure
    - **Assinatura Azure:** Selecione a subscrição apropriada do Azure
    - **Ação**: Criar ou atualizar grupo de recursos
    - **Grupo de Recursos**: $(nome do Grupo de Recursos)
    - **Localização**: $(localização)
    - **Modelo**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    - **Parâmetros do modelo de substituição:**`-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Uma prática comum é usar tarefas do Cofre da Chave Azure. Se o principal de serviço ligado à sua subscrição Azure tiver um conjunto de políticas de acesso apropriadas, pode descarregar segredos de um Cofre de Chaves Azure e ser usado como variáveis no seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword poderia ser referenciado com $(sshPassword) na definição de lançamento.

1. Os próximos passos chamam o Azure CLI. O primeiro é usado para criar uma aplicação em Azure Batch e carregar pacotes associados.

    Adicione a tarefa **Azure CLI** e desapedaça as seguintes propriedades:
    - **Nome do visor:** Criar aplicação na conta Azure Batch
    - **Assinatura Azure:** Selecione a subscrição apropriada do Azure
    - **Localização do script**: Script inline
    - **Roteiro inline**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. O segundo passo é utilizado para carregar pacotes associados à aplicação (neste caso, os ficheiros ffmpeg).

    Adicione a tarefa **Azure CLI** e desapedaça as seguintes propriedades:
    - **Nome do visor:** Pacote de upload para a conta Azure Batch
    - **Assinatura Azure:** Selecione a subscrição apropriada do Azure
    - **Localização do script**: Script inline
    - **Roteiro inline**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > O número de versão do pacote de aplicações está definido como uma variável. Isto permite a sobreescrita de versões anteriores da embalagem e permite-lhe controlar manualmente o número de versão do pacote empurrado para O Lote Azure.

1. Crie uma nova versão selecionando **Lançamento > Crie uma nova versão**. Uma vez acionado, selecione o link para a sua nova versão para visualizar o estado.

1. Ver a saída ao vivo do agente selecionando o botão **'Logs'** por baixo do ambiente.

    ![Screenshot mostrando o estado do lançamento.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>Testar o ambiente

Uma vez configurado o ambiente, confirme se os seguintes testes podem ser concluídos com sucesso.

Ligue-se à nova Conta de Lote Azure, utilizando o CLI Azure a partir de um pedido de comando PowerShell.

- Inscreva-se na sua conta Azure `az login` e siga as instruções para autenticar.
- Agora autentica a conta Lote: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Listar as aplicações disponíveis

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verifique se a piscina é válida

```azurecli
az batch pool list
```

Note o valor `currentDedicatedNodes` da saída deste comando. Este valor é ajustado no teste seguinte.

#### <a name="resize-the-pool"></a>Redimensione a piscina

Redimensione a piscina para que existam nóns de computação disponíveis para testes de trabalho e tarefas, consulte o comando da lista de piscinas para ver o estado atual até que a redimensionamento esteja concluída e existam nos acenos disponíveis

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passos seguintes

Consulte estes tutoriais para aprender a interagir com uma conta Batch através de uma aplicação simples.

- [Executar uma carga de trabalho paralela com Azure Batch usando a API Python](tutorial-parallel-python.md)
- [Executar uma carga de trabalho paralela com o Azure Batch através da API .NET](tutorial-parallel-dotnet.md)
