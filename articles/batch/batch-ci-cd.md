---
title: Utilize gasodutos Azure para construir & implementar soluções HPC
description: Saiba como implantar um gasoduto de construção/libertação para uma aplicação HPC em execução no Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: 4d758d4613f68450be9c444063d3a6188d1aa689
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337581"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Utilize gasodutos Azure para construir e implementar soluções HPC

Os Serviços Azure DevOps fornecem uma gama de ferramentas usadas pelas equipas de desenvolvimento na construção de uma aplicação personalizada. As ferramentas fornecidas pela Azure DevOps podem traduzir-se em construção automatizada e teste de soluções de computação de alto desempenho. Este artigo demonstra como configurar uma integração contínua (CI) e uma implementação contínua (CD) utilizando pipelines Azure para uma solução de computação de alto desempenho implantada no Azure Batch.

A Azure Pipelines fornece uma gama de processos modernos de CI/CD para construção, implantação, teste e monitorização de software. Estes processos aceleram a sua entrega de software, permitindo-lhe focar-se no seu código em vez de apoiar infraestruturas e operações.

## <a name="create-an-azure-pipeline"></a>Criar um Gasoduto Azure

Neste exemplo, criaremos um oleoduto de construção e libertação para implantar uma infraestrutura Azure Batch e lançar um pacote de aplicações. Assumindo que o código é desenvolvido localmente, este é o fluxo geral de implantação:

![Diagrama mostrando o fluxo de implantação no nosso Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configuração

Para seguir os passos deste artigo, você precisa de uma organização Azure DevOps e um projeto de equipe.

* [Criar uma Organização Azure DevOps](/azure/devops/organizations/accounts/create-organization)
* [Criar um projeto em Azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Controlo de fontes para o seu ambiente

O controlo de origem permite que as equipas rastreiem as alterações efetuadas na base de código e inspecionem versões anteriores do código.

Normalmente, o controlo de origem é pensado de mãos dadas com o código de software. E a infraestrutura subjacente? Isto leva-nos à Infraestrutura como Código, onde usaremos modelos de Gestor de Recursos Azure ou outras alternativas de código aberto para definir declarativamente a nossa infraestrutura subjacente.

Esta amostra depende fortemente de uma série de modelos de gestores de recursos (documentos JSON) e binários existentes. Pode copiar estes exemplos no seu repositório e empurrá-los para Azure DevOps.

A estrutura da base de código utilizada nesta amostra assemelha-se ao seguinte;

* Uma pasta **de modelos de braço,** contendo uma série de modelos do Gestor de Recursos Azure. Os modelos são explicados neste artigo.
* Uma pasta **de aplicação ao cliente,** que é uma cópia do Processamento de [Ficheiros Azure Batch .NET com amostra de ffmpeg.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Isto não é necessário para este artigo.
* Uma pasta **de aplicação hpc,** que é a versão do Windows 64-bit de [ffmpeg 4.3.1](https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-4.3.1-2020-10-01-essentials_build.7z).
* Uma pasta **de oleodutos.** Isto contém um ficheiro YAML que descreve o nosso processo de construção. Isto é discutido no artigo.

Esta secção pressupõe que está familiarizado com o controlo de versão e projetando modelos de Gestor de Recursos. Se não está familiarizado com estes conceitos, consulte as seguintes páginas para obter mais informações.

* [O que é controlo de fontes?](/azure/devops/user-guide/source-control)
* [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Este exemplo aproveita vários modelos de Gestor de Recursos para implementar a nossa solução. Para isso, usamos uma série de modelos de capacidade (semelhantes a unidades ou módulos) que implementam uma peça de funcionalidade específica. Também usamos um modelo de solução de ponta a ponta que é responsável por juntar as capacidades subjacentes. Há alguns benefícios para esta abordagem:

* Os modelos de capacidade subjacentes podem ser testados individualmente por unidade.
* Os modelos de capacidade subjacentes podem ser definidos como um padrão dentro de uma organização, e ser reutilizados em múltiplas soluções.

Para este exemplo, existe um modelo de solução de ponta a ponta (deployment.js) que implementa três modelos. Os modelos subjacentes são modelos de capacidade, responsáveis pela implementação de um aspeto específico da solução.

![Exemplo da estrutura do modelo ligado usando modelos de gestor de recursos Azure](media/batch-ci-cd/ARMTemplateHierarchy.png)

O primeiro modelo que vamos analisar é para uma conta de armazenamento Azure. A nossa solução requer uma conta de armazenamento para implementar a aplicação na nossa Conta Lote. Vale a pena estar ciente do guia de referência do modelo do Gestor de Recursos para os tipos de [recursos do Microsoft.Storage](/azure/templates/microsoft.storage/allversions) ao construir modelos de Gestor de Recursos para Contas de Armazenamento.

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

Em seguida, vamos olhar para o modelo de Conta de Lote Azure. A Conta Azure Batch funciona como uma plataforma para executar numerosas aplicações através de piscinas (agrupamentos de máquinas). Vale a pena estar ciente do guia de referência do modelo do Gestor de [Recursos para Microsoft.Battipos de recursos ch](/azure/templates/microsoft.batch/allversions) ao construir modelos de Gestor de Recursos para Contas de Lote.

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

O modelo seguinte mostra um exemplo de criação de um Pool de Lote Azure (as máquinas de backend para processar as nossas aplicações). Vale a pena estar ciente do guia de referência do modelo do Gestor de [Recursos para Microsoft.Battipos de recursos ch](/azure/templates/microsoft.batch/allversions) ao construir modelos de Gestor de Recursos para Piscinas de Contas de Lote.

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

Também pode saber mais sobre [a criação de modelos ligados ao Gestor de Recursos Azure](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) num artigo separado.

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

A infraestrutura e o software podem ser definidos como código e cotados no mesmo repositório.

Para esta solução, o ffmpeg é usado como pacote de aplicação. O pacote ffmpeg pode ser descarregado [aqui.](https://www.videohelp.com/software?d=ffmpeg-3.3.4-win64-static.zip)

![Estrutura repositória de Git exemplo](media/batch-ci-cd/git-repository.jpg)

Há quatro secções principais para este repositório:

* A pasta **de modelos de braço** que armazena a nossa Infraestrutura como Código
* A pasta **hpc-application** que contém os binários para ffmpeg
* A pasta **de gasodutos** que contém a definição para o nosso oleoduto de construção.
* **Opcional** : A pasta **de aplicação ao cliente** que armazenaria código para aplicação .NET. Não o utilizamos na amostra, mas no seu próprio projeto, poderá desejar executar as execuções da Aplicação do Lote HPC através de uma aplicação ao cliente.

> [!NOTE]
> Este é apenas um exemplo de uma estrutura para uma base de código. Esta abordagem é utilizada para demonstrar que a aplicação, a infraestrutura e o código do gasoduto são armazenados no mesmo repositório.

Agora que o código fonte está configurado, podemos começar a primeira construção.

## <a name="continuous-integration"></a>Integração contínua

[A Azure Pipelines](/azure/devops/pipelines/get-started/), dentro dos Serviços Azure DevOps, ajuda-o a implementar um oleoduto de construção, teste e implantação para as suas aplicações.

Nesta fase do seu pipeline, os testes são normalmente executados para validar código e construir as peças apropriadas do software. O número e tipos de testes e quaisquer tarefas adicionais que executar dependerão da sua estratégia de construção e libertação mais ampla.

## <a name="preparing-the-hpc-application"></a>Preparação da aplicação HPC

Neste exemplo, vamos focar-nos na pasta **de aplicação hpc.** A pasta **de aplicação hpc** é o software ffmpeg que será executado a partir da conta Azure Batch.

1. Navegue para a secção Builds of Azure Pipelines na sua organização Azure DevOps. Criar um **novo oleoduto**.

    ![Criar um novo gasoduto de construção](media/batch-ci-cd/new-build-pipeline.jpg)

1. Tem duas opções para criar um pipeline Build:

    a. [Utilização do Designer Visual.](/azure/devops/pipelines/get-started-designer) Para utilizar isto, clique em "Use o designer visual" na página do **novo pipeline.**

    b. [Utilizando construções YAML](/azure/devops/pipelines/get-started-yaml). Pode criar um novo oleoduto YAML clicando na opção Azure Repos ou GitHub na página do novo pipeline. Em alternativa, pode armazenar o exemplo abaixo no seu controlo de origem e fazer referência a um ficheiro YAML existente clicando no Visual Designer e, em seguida, utilizando o modelo YAML.

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

    ![Exemplo de um pipeline de construção existente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Veja atualizações em direto sobre o progresso da sua construção em Azure DevOps navegando para a secção **Build** of Azure Pipelines. Selecione a construção adequada a partir da sua definição de construção.

    ![Ver saídas ao vivo a partir da sua construção](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se utilizar uma aplicação do cliente para executar a aplicação do lote HPC, tem de criar uma definição de construção separada para essa aplicação. Pode encontrar uma série de guias na documentação do [Azure Pipelines.](/azure/devops/pipelines/get-started/index)

## <a name="continuous-deployment"></a>Implementação contínua

A Azure Pipelines também usado para implantar a sua aplicação e infraestrutura subjacente. [Os gasodutos de desbloqueio](/azure/devops/pipelines/release) são o componente que permite a implantação contínua e automatiza o seu processo de libertação.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Implantação da sua aplicação e infraestrutura subjacente

Há uma série de passos envolvidos na implantação da infraestrutura. Como utilizamos [modelos ligados,](../azure-resource-manager/templates/linked-templates.md)esses modelos terão de estar acessíveis a partir de um ponto final público (HTTP ou HTTPS). Isto pode ser um repositório no GitHub, ou uma conta de armazenamento Azure Blob, ou outro local de armazenamento. Os artefactos do modelo carregados podem permanecer seguros, uma vez que podem ser mantidos em modo privado, mas acedidos usando algum tipo de sinal de assinatura de acesso partilhado (SAS). O exemplo a seguir demonstra como implantar uma infraestrutura com modelos a partir de uma bolha de armazenamento Azure.

1. Crie uma **nova definição de lançamento** e selecione uma definição vazia. Em seguida, precisamos de renomear o ambiente recém-criado para algo relevante para o nosso oleoduto.

    ![Gasoduto de lançamento inicial](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência do Pipeline Build para obter a saída para a nossa aplicação HPC.

    > [!NOTE]
    > Mais uma vez, note o **Alias de Origem** , pois será necessário quando as tarefas forem criadas dentro da Definição de Lançamento.

    ![Criar uma ligação de artefactos ao HPCApplicationPackage no pipeline de construção apropriado](media/batch-ci-cd/Release-1.jpg)

1. Criar uma ligação com outro artefacto, desta vez, um Azure Repo. Isto é necessário para aceder aos modelos do Gestor de Recursos armazenados no seu repositório. Como os modelos do Gestor de Recursos não requerem compilação, não é necessário empurrá-los através de um pipeline de construção.

    > [!NOTE]
    > Mais uma vez, note o **Alias de Origem** , pois será necessário quando as tarefas forem criadas dentro da Definição de Lançamento.

    ![Criar uma ligação de artefactos ao Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Navegue para a secção **de variáveis.** É recomendado criar uma série de variáveis no seu oleoduto, por isso não está a inserir a mesma informação em várias tarefas. Estas são as variáveis utilizadas neste exemplo, e como impactam a implantação.

    * **aplicaçõesStorageAccountName** : Nome da Conta de Armazenamento para deter binários de aplicação HPC
    * **batchAccountApplicationName** : Nome da aplicação na Conta lote Azure
    * **batchAccountName** : Nome da conta Azure Batch
    * **batchAccountPoolName** : Nome do pool de VMs que fazem o processamento
    * **batchApplicationId** : ID exclusivo para a aplicação Azure Batch
    * **batchApplicationVersion** : Versão semântica da sua aplicação de lote (isto é, os binaries ffmpeg)
    * **localização** : Localização dos Recursos Azure a implantar
    * **nome do Grupo de Recursos** : Nome do Grupo de Recursos a criar e onde os seus recursos serão implantados
    * **armazenamentoSAme** de número : Nome da Conta de Armazenamento para deter modelos de gestor de recursos ligados

    ![Exemplo de variáveis definidas para a libertação dos Gasodutos Azure](media/batch-ci-cd/Release-4.jpg)

1. Navegue para as tarefas para o ambiente Dev. Na foto abaixo, pode ver seis tarefas. Estas tarefas: descarregar os ficheiros ffmpeg com fecho, implementar uma conta de armazenamento para hospedar os modelos de Gestor de Recursos aninhados, copiar esses modelos de Gestor de Recursos para a conta de armazenamento, implementar a conta de lote e dependências necessárias, criar uma aplicação na Conta Azure Batch e fazer o upload do pacote de aplicações para a Conta Azure Batch.

    ![Exemplo das tarefas utilizadas para libertar a aplicação HPC para o Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Adicione a tarefa **de Pipeline Artifact (Pré-visualização)** e desafie as seguintes propriedades:
    * **Nome do visor:** Baixar ApplicationPackage para Agente
    * **O nome do artefacto para descarregar:** hpc-application
    * **Caminho para download para** : $(System.DefaultWorkingDirectory)

1. Crie uma Conta de Armazenamento para armazenar os seus artefactos. Uma conta de armazenamento existente da solução poderia ser usada, mas para a amostra autossuficiente e isolamento de conteúdos, estamos fazendo uma conta de armazenamento dedicada para os nossos artefactos (especificamente os modelos do Gestor de Recursos).

    Adicione a tarefa **de implantação do Grupo de Recursos Azure** e desenrine as seguintes propriedades:
    * **Nome do visor:** Implementar conta de armazenamento para modelos de gestor de recursos
    * **Assinatura Azure:** Selecione a subscrição Azure apropriada
    * **Ação** : Criar ou atualizar grupo de recursos
    * **Grupo de Recursos** : $(nome do Grupo de Recursos)
    * **Localização** : $(localização)
    * **Modelo** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **Parâmetros do modelo de substituição** : -contaName $(storageAccountName)

1. Faça o upload dos artefactos do Controlo de Origem para a Conta de Armazenamento. Há uma tarefa do Azure Pipeline para executar isto. Como parte desta tarefa, o URL do Contentor de Conta de Armazenamento e o Token SAS podem ser desatados para uma variável em Pipelines Azure. Isto significa que pode ser reutilizado durante toda esta fase do agente.

    Adicione a tarefa **Azure File Copy** e desaveja as seguintes propriedades:
    * **Fonte:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Tipo de ligação Azure** : Gestor de Recursos Azure
    * **Assinatura Azure:** Selecione a subscrição Azure apropriada
    * **Tipo de destino** : Azure Blob
    * **Conta de Armazenamento RM** : $(storageAccountName)
    * **Nome do recipiente** : modelos
    * **Contentor de Armazenamento URI** : modeloContainerUri
    * **Contentor de armazenamento SAS Token** : modeloContainerSasToken

1. Desloque o modelo do orquestrador. Lembre-se do modelo do orquestrador de antes, você vai notar que havia parâmetros para o URL do recipiente de conta de armazenamento, além do token SAS. Deve notar que as variáveis exigidas no modelo de Gestor de Recursos são mantidas na secção de variáveis da definição de libertação, ou foram definidas a partir de outra tarefa dos Gasodutos Azure (por exemplo, parte da tarefa Azure Blob Copy).

    Adicione a tarefa **de implantação do Grupo de Recursos Azure** e desenrine as seguintes propriedades:
    * **Nome do visor:** Implementar lote Azure
    * **Assinatura Azure:** Selecione a subscrição Azure apropriada
    * **Ação** : Criar ou atualizar grupo de recursos
    * **Grupo de Recursos** : $(nome do Grupo de Recursos)
    * **Localização** : $(localização)
    * **Modelo** : $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **Parâmetros do modelo de substituição:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Uma prática comum é usar tarefas do Cofre da Chave Azure. Se o Diretor de Serviço (ligação à sua Subscrição Azure) tiver um conjunto de políticas de acesso adequadas, pode descarregar segredos a partir de um Cofre chave Azure e ser usado como variáveis no seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword poderia ser referenciado com $(sshPassword) na definição de lançamento.

1. Os próximos passos chamam o Azure CLI. O primeiro é usado para criar uma aplicação em Azure Batch. e carregar pacotes associados.

    Adicione a tarefa **Azure CLI** e desapedaça as seguintes propriedades:
    * **Nome do visor:** Criar aplicação na conta Azure Batch
    * **Assinatura Azure:** Selecione a subscrição Azure apropriada
    * **Localização do script** : Script inline
    * **Roteiro inline** : ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. O segundo passo é utilizado para carregar pacotes associados à aplicação. No nosso caso, os ficheiros ffmpeg.

    Adicione a tarefa **Azure CLI** e desapedaça as seguintes propriedades:
    * **Nome do visor:** Pacote de upload para a conta Azure Batch
    * **Assinatura Azure:** Selecione a subscrição Azure apropriada
    * **Localização do script** : Script inline
    * **Roteiro inline** : ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > O número de versão do pacote de aplicações está definido como uma variável. Isto é conveniente se a sobreposição de versões anteriores do pacote funcionar para si, e se quiser controlar manualmente o número de versão do pacote empurrado para O Azure Batch.

1. Crie uma nova versão selecionando **Lançamento > Crie uma nova versão**. Uma vez acionado, selecione o link para a sua nova versão para visualizar o estado.

1. Pode visualizar a saída ao vivo do agente selecionando o botão **'Logs'** por baixo do ambiente.

    ![Ver o estado da sua libertação](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testar o ambiente

Uma vez configurado o ambiente, confirme se os seguintes testes podem ser concluídos com sucesso.

Ligue-se à nova Conta de Lote Azure, utilizando o CLI Azure a partir de um pedido de comando PowerShell.

* Inscreva-se na sua conta Azure `az login` e siga as instruções para autenticar.
* Agora autentica a conta Lote: `az batch account login -g <resourceGroup> -n <batchAccount>`

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

Além deste artigo, existem dois tutoriais que utilizam o ffmpeg, utilizando .NET e Python. Consulte estes tutoriais para obter mais informações sobre como interagir com uma conta Batch através de uma aplicação simples.

* [Executar uma carga de trabalho paralela com Azure Batch usando a API Python](tutorial-parallel-python.md)
* [Executar uma carga de trabalho paralela com o Azure Batch através da API .NET](tutorial-parallel-dotnet.md)
