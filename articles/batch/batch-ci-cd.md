---
title: Utilizar gasodutos Azure para construir & implementar soluções HPC
description: Aprenda a implementar um pipeline de construção/libertação para uma aplicação HPC em execução no Lote Azure.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.openlocfilehash: 79c4e74086acc0f74bcc43f6b4543afe12916364
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117391"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Utilizar os oleodutos Azure para construir e implementar soluções HPC

A Azure DevOps Services fornece uma gama de ferramentas utilizadas pelas equipas de desenvolvimento na construção de uma aplicação personalizada. As ferramentas fornecidas pela Azure DevOps podem traduzir-se em construção automatizada e testes de soluções computacionais de alto desempenho. Este artigo demonstra como configurar uma integração contínua (CI) e uma implantação contínua (CD) utilizando pipelines Azure para uma solução de computação de alto desempenho implantada no Lote Azure.

A Azure Pipelines fornece uma gama de processos modernos de CI/CD para a construção, implantação, teste e monitorização de software. Estes processos aceleram a entrega do seu software, permitindo-lhe focar-se no seu código em vez de apoiar infraestruturas e operações.

## <a name="create-an-azure-pipeline"></a>Criar um Oleoduto Azure

Neste exemplo, criaremos um oleoduto de construção e lançamento para implantar uma infraestrutura de Lote Azure e lançar um pacote de aplicações. Assumindo que o código é desenvolvido localmente, este é o fluxo geral de implantação:

![Diagrama mostrando o fluxo de implantação no nosso Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configuração

Para seguir os passos deste artigo, precisa de uma organização Azure DevOps e de um projeto de equipa.

* [Criar uma Organização Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Criar um projeto em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controlo de fontes para o seu ambiente

O controlo de origem permite que as equipas rastreiem as alterações feitas na base de códigos e inspecionem as versões anteriores do código.

Tipicamente, o controlo de fontes é pensado de mãos dadas com código de software. E a infraestrutura subjacente? Isto leva-nos à Infraestruturas como Código, onde usaremos modelos do Gestor de Recursos Azure ou outras alternativas de código aberto para definir declarativamente a nossa infraestrutura subjacente.

Esta amostra baseia-se fortemente em vários modelos de gestor de recursos (Documentos JSON) e binários existentes. Pode copiar estes exemplos no seu repositório e empurrá-los para O Azure DevOps.

A estrutura de base de código utilizada nesta amostra assemelha-se ao seguinte;

* Uma pasta **de modelos de braço,** contendo vários modelos de Gestor de Recursos Azure. Os modelos são explicados neste artigo.
* Uma pasta **de aplicação de cliente,** que é uma cópia do Processamento de [Ficheiros Azure Batch .NET com amostra ffmpeg.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Isto não é necessário para este artigo.
* Uma pasta **de aplicação hpc,** que é a versão do Windows 64 bits de [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Uma pasta de **oleodutos.** Isto contém um ficheiro YAML que descreve o nosso processo de construção. Isto é discutido no artigo.

Esta secção pressupõe que está familiarizado com o controlo de versão e a conceção de modelos de Gestor de Recursos. Se não está familiarizado com estes conceitos, consulte as páginas seguintes para obter mais informações.

* [O que é o controlo de fontes?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Understand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/templates/template-syntax.md) (Compreender a estrutura e a sintaxe dos Modelos do Azure Resource Manager)

#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Este exemplo aproveita vários modelos do Gestor de Recursos para implementar a nossa solução. Para isso, utilizamos vários modelos de capacidade (semelhantes a unidades ou módulos) que implementam uma peça específica de funcionalidade. Também usamos um modelo de solução de ponta a ponta que é responsável por juntar essas capacidades subjacentes. Há alguns benefícios para esta abordagem:

* Os modelos de capacidade subjacentes podem ser testados individualmente por unidade.
* Os modelos de capacidade subjacentes podem ser definidos como um padrão dentro de uma organização, e ser reutilizados em múltiplas soluções.

Para este exemplo, existe um modelo de solução de ponta a ponta (deployment.json) que implementa três modelos. Os modelos subjacentes são modelos de capacidade, responsáveis pela implementação de um aspeto específico da solução.

![Exemplo da estrutura do modelo ligado usando modelos de gestor de recursos azure](media/batch-ci-cd/ARMTemplateHierarchy.png)

O primeiro modelo que vamos ver é para uma Conta de Armazenamento Azure. A nossa solução requer uma conta de armazenamento para implementar a aplicação na nossa Conta de Lote. Vale a pena estar ciente do guia de referência do modelo do Gestor de Recursos para a [Microsoft.Tipos](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) de recursos de armazenamento ao construir modelos de Gestor de Recursos para Contas de Armazenamento.

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

Em seguida, vamos olhar para o modelo de conta de lote Azure. A Conta de Lote Azure funciona como uma plataforma para executar numerosas aplicações em piscinas (agrupamentos de máquinas). Vale a pena estar ciente do guia de referência do modelo do Gestor de Recursos para os tipos de [recursos do Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos de Gestor de Recursos para Contas de Lote.

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

O próximo modelo mostra um exemplo criando um Pool de Lote Azure (as máquinas de backend para processar as nossas aplicações). Vale a pena estar ciente do guia de referência do modelo do Gestor de Recursos para os tipos de [recursos do Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao construir modelos de Gestor de Recursos para Piscinas de Conta de Lote.

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

Finalmente, temos um modelo que age semelhante a um orquestrador. Este modelo é responsável pela implementação dos modelos de capacidade.

Você também pode saber mais sobre a criação de [modelos ligados do Gestor](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) de Recursos Azure em um artigo separado.

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

#### <a name="the-hpc-solution"></a>A Solução HPC

A infraestrutura e o software podem ser definidos como código e co-localizados no mesmo repositório.

Para esta solução, o ffmpeg é usado como pacote de aplicação. O pacote ffmpeg pode ser descarregado [aqui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Exemplo Git Repositório Estrutura](media/batch-ci-cd/git-repository.jpg)

Há quatro secções principais para este repositório:

* A pasta **de modelos de braço** que armazena a nossa Infraestrutura como Código
* A pasta **hpc-aplicação** que contém os binários para ffmpeg
* A pasta **de pipelines** que contém a definição para o nosso pipeline de construção.
* **Opcional**: A pasta **de aplicação do cliente** que armazenaria código para aplicação .NET. Não o utilizamos na amostra, mas no seu próprio projeto, poderá desejar executar execuções da Aplicação de Lote HPC através de uma aplicação de cliente.

> [!NOTE]
> Este é apenas um exemplo de uma estrutura para uma base de códigos. Esta abordagem é utilizada para demonstrar que a aplicação, a infraestrutura e o código do gasoduto são armazenados no mesmo repositório.

Agora que o código fonte está configurado, podemos começar a primeira construção.

## <a name="continuous-integration"></a>Integração contínua

[Os Oleodutos Azure,](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)dentro dos Serviços Azure DevOps, ajudam-no a implementar um pipeline de construção, teste e implantação para as suas aplicações.

Nesta fase do seu pipeline, os testes são normalmente executados para validar código e construir as peças apropriadas do software. O número e tipos de testes, e quaisquer tarefas adicionais que executar, dependerão da sua estratégia de construção e lançamento mais ampla.

## <a name="preparing-the-hpc-application"></a>Preparação da aplicação HPC

Neste exemplo, vamos focar-nos na pasta de **aplicação hpc.** A pasta **de aplicação hpc** é o software ffmpeg que será executado a partir da conta Do Lote Azure.

1. Navegue para a secção Builds de Pipelines Azure na sua organização Azure DevOps. Criar um **novo oleoduto.**

    ![Criar um novo pipeline de construção](media/batch-ci-cd/new-build-pipeline.jpg)

1. Tem duas opções para criar um pipeline Build:

    a. [Usando o Designer Visual.](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav) Para utilizar isto, clique em "Use o designer visual" na página do **novo pipeline.**

    b. [Utilização de Construções YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Pode criar um novo pipeline YAML clicando na opção Azure Repos ou GitHub na página do novo pipeline. Em alternativa, pode armazenar o exemplo abaixo no seu controlo de origem e fazer referência a um ficheiro YAML existente clicando no Visual Designer e, em seguida, usando o modelo YAML.

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

1. Uma vez configurada a construção conforme necessário, selecione **Guardar & Fila**. Se tiver uma integração contínua ativada (na secção **Gatilhos),** a construção disparará automaticamente quando for feito um novo compromisso com o repositório, atendendo às condições estabelecidas na construção.

    ![Exemplo de um pipeline de construção existente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Veja em direto as atualizações sobre o progresso da sua construção em Azure DevOps navegando até à secção **build** de Pipelines Azure. Selecione a construção adequada a partir da sua definição de construção.

    ![Veja as saídas ao vivo da sua construção](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se utilizar uma aplicação de cliente para executar a sua Aplicação de Lote HPC, precisa de criar uma definição de construção separada para essa aplicação. Você pode encontrar uma série de guias de como-fazer na documentação [azure pipelines.](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)

## <a name="continuous-deployment"></a>Implementação contínua

Os Oleodutos Azure também utilizaram para implantar a sua aplicação e infraestruturas subjacentes. [Os gasodutos de desbloqueio](https://docs.microsoft.com/azure/devops/pipelines/release) são o componente que permite a implantação contínua e automatiza o seu processo de libertação.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Implantação da sua aplicação e infraestrutura subjacente

Há uma série de passos envolvidos na implantação da infraestrutura. Como usamos [modelos ligados,](../azure-resource-manager/templates/linked-templates.md)esses modelos terão de ser acessíveis a partir de um ponto final público (HTTP ou HTTPS). Este pode ser um repositório no GitHub, ou uma Conta de Armazenamento Azure Blob, ou outro local de armazenamento. Os artefactos do modelo carregado podem permanecer seguros, uma vez que podem ser mantidos em modo privado, mas acedidos usando alguma forma de assinatura de acesso partilhado (SAS). O exemplo seguinte demonstra como implantar uma infraestrutura com modelos de uma bolha de armazenamento Azure.

1. Crie uma **nova Definição**de Lançamento e selecione uma definição vazia. Precisamos então de mudar o nome do ambiente recém-criado para algo relevante para o nosso oleoduto.

    ![Gasoduto de lançamento inicial](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência do Pipeline Build para obter a saída para a nossa aplicação HPC.

    > [!NOTE]
    > Mais uma vez, note o **Pseudónimo Fonte**, pois isso será necessário quando as tarefas forem criadas dentro da Definição de Lançamento.

    ![Criar uma ligação de artefacto saqueao HPCApplicationPackage no pipeline de construção apropriado](media/batch-ci-cd/Release-1.jpg)

1. Crie uma ligação com outro artefacto, desta vez, um Azure Repo. Isto é necessário para aceder aos modelos do Gestor de Recursos armazenados no seu repositório. Como os modelos do Gestor de Recursos não requerem compilação, você não precisa empurrá-los através de um pipeline de construção.

    > [!NOTE]
    > Mais uma vez, note o **Pseudónimo Fonte**, pois isso será necessário quando as tarefas forem criadas dentro da Definição de Lançamento.

    ![Criar uma ligação de artefacto saca-se ao Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Navegue para a secção **de variáveis.** É recomendado criar uma série de variáveis no seu pipeline, para que não esteja a inserir a mesma informação em várias tarefas. Estas são as variáveis usadas neste exemplo, e como impactam a implantação.

    * **aplicaçãoStorageAccountName**: Nome da Conta de Armazenamento para manter binários de aplicação HPC
    * **loteAccountApplicationName**: Nome da aplicação na conta de lote azure
    * **loteNome de conta**: Nome da conta de lote azure
    * **batchAccountPoolName**: Nome da piscina de VMs que estão a fazer o processamento
    * **loteApplicationId**: ID único para a aplicação Do Lote Azure
    * **batchApplicationVersão**: Versão semântica da sua aplicação de lote (isto é, os binários ffmpeg)
    * **localização**: Localização para os Recursos Azure a serem implantados
    * **recursoGroupName**: Nome do Grupo de Recursos a criar e onde os seus recursos serão implantados
    * **nome da conta**de armazenamento : Nome da Conta de Armazenamento para manter modelos ligados do Gestor de Recursos

    ![Exemplo de variáveis definidas para a libertação dos Gasodutos Azure](media/batch-ci-cd/Release-4.jpg)

1. Navegue para as tarefas para o ambiente Dev. No instantâneo abaixo, pode ver seis tarefas. Estas tarefas irão: descarregar os ficheiros ffmpeg com fecho, implementar uma conta de armazenamento para alojar os modelos do Gestor de Recursos aninhados, copiar os modelos do Gestor de Recursos para a conta de armazenamento, implementar a conta de lote e dependências necessárias, criar uma aplicação na Conta de Lote Azure e enviar o pacote de aplicação para a Conta de Lote De Lote Azure.

    ![Exemplo das tarefas utilizadas para lançar a aplicação HPC para o Lote Azure](media/batch-ci-cd/Release-3.jpg)

1. Adicione a tarefa de **descarregamento pipeline Artifact (Pré-visualização)** e detetete as seguintes propriedades:
    * **Nome do ecrã:** Pacote de aplicações de descarregamento para agente
    * **O nome do artefacto para descarregar:** hpc-application
    * **Caminho para download para**: $(System.DefaultWorkingDirectory)

1. Crie uma Conta de Armazenamento para armazenar os seus artefactos. Uma conta de armazenamento existente da solução poderia ser usada, mas para a amostra autónoma e isolamento de conteúdo, estamos fazendo uma conta de armazenamento dedicada para os nossos artefactos (especificamente os modelos do Gestor de Recursos).

    Adicione a tarefa de implantação do Grupo de **Recursos Azure** e detetete as seguintes propriedades:
    * **Nome do ecrã:** Implementar conta de armazenamento para modelos de gestor de recursos
    * **Assinatura Azure:** Selecione a subscrição azure apropriada
    * **Ação**: Criar ou atualizar grupo de recursos
    * **Grupo de Recursos**: $(resourceGroupName)
    * **Localização**: $(localização)
    * **Modelo**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/modelos de braço/armazenamentoAccount.json
    * **Parâmetros**do modelo de sobreposição : -contaName $(storageAccountName)

1. Faça upload dos artefactos do Controlo de Origem para a Conta de Armazenamento. Há uma tarefa do Pipeline Azure para realizar isto. Como parte desta tarefa, o URL do Recipiente de Conta de Armazenamento e o Token SAS podem ser outputados para uma variável em Pipelines Azure. Isto significa que pode ser reutilizado durante toda esta fase do agente.

    Adicione a tarefa De Cópia de **Ficheiros Azure** e detete as seguintes propriedades:
    * **Fonte:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/modelos de braço/
    * **Tipo de ligação azure**: Gestor de recursos azure
    * **Assinatura Azure:** Selecione a subscrição azure apropriada
    * **Tipo de destino**: Azure Blob
    * **Conta de Armazenamento RM**: $(storageAccountName)
    * **Nome do recipiente**: modelos
    * **Contentor de armazenamento URI**: modeloContainerUri
    * **Armazenamento Contentor SAS Token**: modeloContainerSasToken

1. Implante o modelo de orquestrador. Lembre-se do modelo de orquestrador de há pouco, você vai notar que havia parâmetros para o URL do Recipiente de Conta de Armazenamento, além do token SAS. Deve notar que as variáveis exigidas no modelo do Gestor de Recursos são mantidas na secção variável da definição de lançamento, ou foram definidas a partir de outra tarefa de Pipelines Azure (por exemplo, parte da tarefa Azure Blob Copy).

    Adicione a tarefa de implantação do Grupo de **Recursos Azure** e detetete as seguintes propriedades:
    * **Nome do ecrã:** Implantar lote azure
    * **Assinatura Azure:** Selecione a subscrição azure apropriada
    * **Ação**: Criar ou atualizar grupo de recursos
    * **Grupo de Recursos**: $(resourceGroupName)
    * **Localização**: $(localização)
    * **Modelo**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/modelos de braço/implementação.json
    * **Parâmetros do modelo de sobreposição:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Uma prática comum é usar tarefas do Cofre chave Azure. Se o Diretor de Serviço (ligação à sua Subscrição Azure) tiver um conjunto adequado de políticas de acesso, pode descarregar segredos de um Cofre de Chave Azure e ser usado como variáveis no seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword poderia ser referenciado com $(sshPassword) na definição de lançamento.

1. Os próximos passos chamam o Azure CLI. O primeiro é usado para criar uma aplicação no Lote Azure. e carregar pacotes associados.

    Adicione a tarefa **Azure CLI** e detete as seguintes propriedades:
    * **Nome do ecrã:** Criar aplicação na Conta de Lote Azure
    * **Assinatura Azure:** Selecione a subscrição azure apropriada
    * **Localização do script**: Script inline
    * **Script inline**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. O segundo passo é usado para carregar pacotes associados à aplicação. No nosso caso, os ficheiros ffmpeg.

    Adicione a tarefa **Azure CLI** e detete as seguintes propriedades:
    * **Nome do ecrã:** Pacote de upload para conta de lote Azure
    * **Assinatura Azure:** Selecione a subscrição azure apropriada
    * **Localização do script**: Script inline
    * **Script inline**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > O número de versão do pacote de aplicações está definido para uma variável. Isto é conveniente se a sobreposição de versões anteriores do pacote funcionar para si, e se pretender controlar manualmente o número da versão do pacote empurrado para o Lote Azure.

1. Crie um novo lançamento selecionando **o lançamento > Criar um novo lançamento**. Uma vez acionado, selecione o link para a sua nova versão para visualizar o estado.

1. Pode visualizar a saída ao vivo do agente selecionando o botão **Logs** por baixo do seu ambiente.

    ![Veja o estado da sua libertação](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testar o ambiente

Uma vez configurado o ambiente, confirme que os seguintes testes podem ser concluídos com sucesso.

Ligue-se à nova Conta de Lote Azure, utilizando o ClI Azure a partir de um pedido de comando PowerShell.

* Inscreva-se na sua `az login` conta Azure e siga as instruções para autenticar.
* Agora autenticar a conta Lote:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Enumerar as candidaturas disponíveis

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verifique se a piscina é válida

```azurecli
az batch pool list
```

Note o `currentDedicatedNodes` valor da saída deste comando. Este valor é ajustado no próximo teste.

#### <a name="resize-the-pool"></a>Redimensionar a piscina

Redimensione a piscina para que existam nós de computação disponíveis para testes de trabalho e tarefas, consulte com o comando da lista de piscinas para ver o estado atual até que o redimensionamento esteja concluído e existam nós disponíveis

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passos seguintes

Além deste artigo, existem dois tutoriais que utilizam ffmpeg, utilizando .NET e Python. Consulte estes tutoriais para obter mais informações sobre como interagir com uma conta De lote através de uma aplicação simples.

* [Executar uma carga de trabalho paralela com o Lote Azure usando a API Python](tutorial-parallel-python.md)
* [Executar uma carga de trabalho paralela com o Azure Batch através da API .NET](tutorial-parallel-dotnet.md)
