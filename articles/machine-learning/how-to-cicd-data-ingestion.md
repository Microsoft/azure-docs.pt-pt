---
title: DevOps para um pipeline de ingestão de dados
titleSuffix: Azure Machine Learning
description: Aprenda a aplicar as práticas de DevOps a uma implementação de gasoduto de ingestão de dados usada para preparar dados para uma formação de modelos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247185"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps para um pipeline de ingestão de dados

Na maioria dos cenários, uma solução de ingestão de dados é uma composição de scripts, invocações de serviços e um pipeline orquestrando todas as atividades. Neste artigo, aprende-se a aplicar práticas de DevOps ao ciclo de vida de desenvolvimento de um gasoduto comum de ingestão de dados. O oleoduto prepara os dados para o treino do modelo de Aprendizagem automática.

## <a name="the-solution"></a>A solução

Considere o seguinte fluxo de trabalho de ingestão de dados:

![gasoduto de ingestão de dados](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

Nesta abordagem, os dados de formação são armazenados num armazenamento de blob Azure. Um oleoduto Azure Data Factory rebusca os dados de um recipiente de entrada blob, transforma-os e guarda os dados para o recipiente de saída blob. Este recipiente serve como armazenamento de [dados](concept-data.md) para o serviço de Aprendizagem automática Azure. Tendo os dados preparados, o pipeline Data Factory invoca um gasoduto de aprendizagem automática de formação para treinar um modelo. Neste exemplo específico, a transformação de dados é realizada por um caderno Python, em execução num cluster de Databricks Azure. 

## <a name="what-we-are-building"></a>O que estamos construindo

Como em qualquer solução de software, há uma equipa (por exemplo, Engenheiros de Dados) a trabalhar nela. 

![cicd-data-ingestion](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Colaboram e partilham os mesmos recursos Azure, como a Azure Data Factory, a Azure Databricks, a conta de Armazenamento Azure e tal. A recolha destes recursos é um ambiente de Desenvolvimento. Os engenheiros de dados contribuem para a mesma base de código fonte. O processo de Integração Contínua reúne o código, verifica-o com os testes de qualidade do código, testa a unidade e produz artefactos como código testado e modelos de Gestor de Recursos Azure. O processo de Entrega Contínua implanta os artefactos para os ambientes a jusante. Este artigo demonstra como automatizar os processos ci e CD com [os Gasodutos Azure](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Gestão do controlo de fontes

Os membros da equipa trabalham de formas ligeiramente diferentes para colaborar no código fonte do caderno Python e no código fonte da Azure Data Factory. No entanto, em ambos os casos o código é armazenado num repositório de controlo de fonte (por exemplo, Azure DevOps, GitHub, GitLab) e a colaboração é normalmente baseada em algum modelo de ramificação (por exemplo, [GitFlow).](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Código fonte do caderno Python

Os engenheiros de dados trabalham com o código fonte do caderno Python, quer localmente num IDE (por exemplo, [Código de Estúdio Visual)](https://code.visualstudio.com)quer diretamente no espaço de trabalho dos Databricks. Este último dá a capacidade de depurar o código sobre o ambiente de desenvolvimento. Em todo o caso, o código será fundido no repositório na sequência de uma política de ramificação. É altamente recomendado armazenar o `.py` código em `.ipynb` ficheiros e não em formato de portátil Jupyter. Melhora a legibilidade do código e permite controlos automáticos de qualidade de código no processo de CI.

### <a name="azure-data-factory-source-code"></a>Código Fonte da Fábrica de Dados Azure

O código fonte dos oleodutos Azure Data Factory é uma coleção de ficheiros JSON gerados por um espaço de trabalho. Normalmente, os engenheiros de dados trabalham com um designer visual no espaço de trabalho da Azure Data Factory e não com os ficheiros de código fonte diretamente. Configure o espaço de trabalho com um repositório de controlo de fonte, tal como descrito na documentação da Fábrica de [Dados Do Azure.](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration) Com esta configuração em vigor, os engenheiros de dados são capazes de colaborar no código fonte seguindo um fluxo de trabalho de ramificação preferido.    

## <a name="continuous-integration-ci"></a>Integração contínua (CI)

O objetivo final do processo de Integração Contínua é reunir o trabalho da equipa conjunta a partir do código fonte e prepará-lo para a implantação para os ambientes a jusante. Tal como acontece com a gestão do código fonte, este processo é diferente para os cadernos Python e os oleodutos Azure Data Factory. 

### <a name="python-notebook-ci"></a>Python Notebook CI

O processo CI para os Cadernos Python obtém o código do ramo de colaboração (por exemplo, ***mestre*** ou ***desenvolvimento)*** e realiza as seguintes atividades:
* Linha de código
* Teste de unidade
* Salvar o código como artefacto

O seguinte código de corte demonstra a implementação destes passos num gasoduto ***de yaml*** Azure DevOps:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

O gasoduto usa ***flocos 8*** para fazer o código Python. Executa os testes unitários definidos no código fonte e publica os resultados de linting e teste para que estejam disponíveis no ecrã de execução do Pipeline Azure:

![linting-unit-tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Se o sistema de fiação e o teste da unidade forem bem sucedidos, o gasoduto copiará o código fonte para o repositório do artefacto a utilizar pelas etapas de implantação subsequentes.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

O processo ci para um oleoduto Azure Data Factory é um estrangulamento em toda a história ci/CD para um pipeline de ingestão de dados. Não há integração ***contínua.*** Um artefacto desdobrável para a Azure Data Factory é uma coleção de modelos do Gestor de Recursos Azure. A única maneira de produzir estes modelos é clicar no botão ***de publicação*** no espaço de trabalho da Azure Data Factory. Não há automação aqui.
Os engenheiros de dados fundem o código fonte dos seus ramos de recurso no ramo de colaboração, por exemplo, ***mestre*** ou ***desenvolvimento.*** Em seguida, alguém com as permissões concedidas clica no botão ***de publicação*** para gerar modelos do Gestor de Recursos Azure a partir do código fonte no ramo de colaboração. Quando o botão é clicado, o espaço de trabalho valida os gasodutos (pense nele como de linhae e teste de unidade), gera modelos do Gestor de Recursos Azure (pense nele como de construção) e guarda os modelos gerados para um ramo técnico ***adf_publish*** no mesmo repositório de código (pense nele como artefactos de publicação). Este ramo é criado automaticamente pelo espaço de trabalho azure Data Factory. Este processo é descrito em detalhes na documentação da [Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)

É importante garantir que os modelos gerados do Gestor de Recursos Azure são agnósticos ambientais. Isto significa que todos os valores que podem diferir entre ambientes são parametrizados. A Azure Data Factory é inteligente o suficiente para expor a maioria dos valores como parâmetros. Por exemplo, no seguinte modelo, as propriedades de ligação a um espaço de trabalho azure machine learning são expostas como parâmetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

No entanto, poderá querer expor as suas propriedades personalizadas que não são manuseadas pelo espaço de trabalho da Azure Data Factory por defeito. No cenário deste artigo, um oleoduto Azure Data Factory invoca um caderno Python que processa os dados. O caderno aceita um parâmetro com o nome de um ficheiro de dados de entrada.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Este nome é diferente para ambientes ***Dev,*** ***QA,*** ***UAT***e ***PROD.*** Num complexo oleoduto com múltiplas atividades, pode haver várias propriedades personalizadas. É uma boa prática recolher todos esses valores num só lugar e defini-los como ***variáveis***de gasoduto:

![adf-variáveis](media/how-to-cicd-data-ingestion/adf-variables.png)

As atividades do gasoduto podem referir-se às variáveis do gasoduto, utilizando-as efetivamente:

![adf-notebook-parâmetros](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

O espaço de trabalho da Azure Data Factory ***não*** expõe variáveis de pipeline como modelos de modelos do Gestor de Recursos Azure por padrão. O espaço de trabalho utiliza o modelo de [parametrização predefinido](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) que dita quais as propriedades do gasoduto que devem ser expostas como parâmetros de modelo do Gestor de Recursos Azure. Para adicionar variáveis de pipeline à lista, atualize a secção "Microsoft.DataFactory/fábricas/pipelines" do Modelo de [Parametrização Predefinido](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) com o seguinte corte e coloque o ficheiro json resultado na raiz da pasta fonte:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Ao fazê-lo, o espaço de trabalho da Azure Data Factory irá adicionar as variáveis à lista de parâmetros quando o botão ***de publicação*** for clicado:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Os valores no ficheiro json são valores predefinidos configurados na definição do pipeline. Espera-se que sejam ultrapassados com os valores ambientais-alvo quando o modelo do Gestor de Recursos Azure for implantado.

## <a name="continuous-delivery-cd"></a>Entrega contínua (CD)

O processo de Entrega Contínua leva os artefactos e implanta-os para o primeiro ambiente alvo. Certifica-se de que a solução funciona através de testes. Se for bem sucedido, continua para o próximo ambiente. O Pipeline CD Azure é composto por múltiplas fases que representam os ambientes. Cada fase contém [destacamentos](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) e [trabalhos](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) que executam os seguintes passos:
* Implementar um Caderno Python para o espaço de trabalho de Azure Databricks
* Implementar um oleoduto Azure Data Factory 
* Executar o pipeline
* Verifique o resultado da ingestão de dados

As fases do gasoduto podem ser configuradas com [aprovações](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) e [portões](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) que fornecem um controlo adicional sobre como o processo de implantação evolui através da cadeia de ambientes.

### <a name="deploy-a-python-notebook"></a>Implementar um Caderno Python

O seguinte código de snippet define uma [implantação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) do Pipeline Azure que copia um caderno Python para um cluster databricks:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Os artefactos produzidos pelo CI são automaticamente copiados para o agente de implantação e estão disponíveis na pasta ***$(Pipeline.Workspace).*** Neste caso, a tarefa de implantação ***refere-se*** ao artefacto dos cadernos que contém o caderno Python. Esta [implementação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) utiliza a [extensão Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) para copiar os ficheiros do portátil para o espaço de trabalho databricks.
O palco ***Deploy_to_QA*** contém uma referência ao grupo variável ***devops-ds-qa-vg*** definido no projeto Azure DevOps. Os passos nesta fase referem-se às variáveis deste grupo variável (por exemplo, $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). A ideia é que a próxima fase (por exemplo, ***Deploy_to_UAT)*** funcione com os mesmos nomes variáveis definidos no seu próprio grupo variável com âmbito uat.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Implementar um oleoduto Azure Data Factory

Um artefacto desdobrável para a Azure Data Factory é um modelo de Gestor de Recursos Azure. Portanto, vai ser implantado com a tarefa de implantação do Grupo de ***Recursos Azure,*** tal como é demonstrado no seguinte corte:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
O valor do parâmetro de nome de ficheiro de dados provém da variável $(DATA_FILE_NAME) definida num grupo variável de fase QA. Da mesma forma, todos os parâmetros definidos em ***ARMTemplateForFactory.json*** podem ser ultrapassados. Se não forem, os valores predefinidos são utilizados.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Executar o gasoduto e verificar o resultado da ingestão de dados

O próximo passo é garantir que a solução implantada está a funcionar. A seguinte definição de trabalho executa um oleoduto Azure Data Factory com um [script PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) e executa um caderno Python num cluster Azure Databricks. O caderno verifica se os dados foram ingeridos corretamente e valida o ficheiro de dados de resultados com o nome $(bin_FILE_NAME).

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

A tarefa final no trabalho verifica o resultado da execução do caderno. Se devolver um erro, define o estado da execução do gasoduto como falhado.

## <a name="putting-pieces-together"></a>Juntar peças

O resultado deste artigo é um ci/CD Azure Pipeline que consiste nas seguintes fases:
* CI
* Desdobre para o QA
    * Implemente para Databricks + Deploy para ADF
    * Teste de Integração

Contém uma série de estágios de ***implantação*** iguais ao número de ambientes-alvo que você tem. Cada fase ***de implantação*** contém duas implementações que funcionam [paralelamente](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) e um [trabalho](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) que funciona após as implementações para testar a solução no ambiente.

Uma amostra de implementação do gasoduto é montada no seguinte corte de ***yaml:***

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Passos seguintes

* [Controlo de Origem no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Integração e entrega contínuas na Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps para Tijolos de Dados Azure](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
