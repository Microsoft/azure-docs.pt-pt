---
title: DevOps para um pipeline de ingestão de dados
titleSuffix: Azure Machine Learning
description: Aprenda a aplicar práticas de DevOps para construir um pipeline de ingestão de dados para preparar dados usando Azure Data Factory e Azure Databricks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: fe2f35708f6a148f8db9ef6fd0a598e19e746fbd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358631"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps para um pipeline de ingestão de dados

Na maioria dos cenários, uma solução de ingestão de dados é uma composição de scripts, invocações de serviços e um pipeline orquestrando todas as atividades. Neste artigo, aprende-se a aplicar práticas de DevOps ao ciclo de vida de desenvolvimento de um oleoduto comum de ingestão de dados que prepara dados para a formação de modelos de aprendizagem automática. O gasoduto é construído utilizando os seguintes serviços Azure:

* __Azure Data Factory__ : Lê os dados brutos e orquestra a preparação de dados.
* __Azure Databricks__ : Executa um caderno Python que transforma os dados.
* __Gasodutos Azure__ : Automatiza um processo de integração e desenvolvimento contínuo.

## <a name="data-ingestion-pipeline-workflow"></a>Fluxo de trabalho do gasoduto de ingestão de dados

O gasoduto de ingestão de dados implementa o seguinte fluxo de trabalho:

1. Os dados brutos são lidos num oleoduto da Azure Data Factory (ADF).
1. O oleoduto ADF envia os dados para um cluster Azure Databricks, que executa um caderno Python para transformar os dados.
1. Os dados são armazenados num recipiente de bolhas, onde podem ser utilizados pela Azure Machine Learning para treinar um modelo.

![fluxo de trabalho do gasoduto de ingestão de dados](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>Visão geral de integração e entrega contínua

Tal como acontece com muitas soluções de software, existe uma equipa (por exemplo, Engenheiros de Dados) a trabalhar nele. Colaboram e partilham os mesmos recursos Azure, tais como Azure Data Factory, Azure Databricks e Azure Storage. A recolha destes recursos é um ambiente de Desenvolvimento. Os engenheiros de dados contribuem para a mesma base de código fonte.

Um sistema de integração e entrega contínuo automatiza o processo de construção, teste e entrega (implantação) da solução. O processo de Integração Contínua (CI) executa as seguintes tarefas:

* Monta o código
* Verifica-o com os testes de qualidade do código
* Executa testes de unidade
* Produz artefactos como código testado e modelos de Gestor de Recursos Azure

O processo de Entrega Contínua (CD) implanta os artefactos para os ambientes a jusante.

![diagrama de ingestão de dados cíd](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Este artigo demonstra como automatizar os processos de CI e CD com [a Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Gestão do controlo de fontes

A gestão do controlo de fontes é necessária para acompanhar as mudanças e permitir a colaboração entre os membros da equipa.
Por exemplo, o código seria armazenado num repositório de Azure DevOps, GitHub ou GitLab. O fluxo de trabalho de colaboração baseia-se num modelo de ramificação. Por exemplo, [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Código fonte do caderno python

Os engenheiros de dados trabalham com o código-fonte do portátil Python localmente num IDE (por exemplo, [Código do Estúdio Visual)](https://code.visualstudio.com)ou diretamente no espaço de trabalho databricks. Uma vez que o código é concluído, eles são fundidos ao repositório seguindo uma política de ramificação.

> [!TIP] 
> Recomendamos armazenar o código em `.py` ficheiros e não no `.ipynb` formato Jupyter Notebook. Melhora a legibilidade do código e permite controlos automáticos de qualidade de código no processo de CI.

### <a name="azure-data-factory-source-code"></a>Código fonte da fábrica de dados Azure

O código fonte dos oleodutos Azure Data Factory é uma coleção de ficheiros JSON gerados por um espaço de trabalho da Azure Data Factory. Normalmente, os engenheiros de dados trabalham com um designer visual no espaço de trabalho da Azure Data Factory em vez de com os ficheiros de código de origem diretamente. 

Para configurar o espaço de trabalho para utilizar um repositório de controlo de origem, consulte [Autor com integração Azure Repos Git](../data-factory/source-control.md#author-with-azure-repos-git-integration).   

## <a name="continuous-integration-ci"></a>Integração contínua (CI)

O objetivo final do processo de Integração Contínua é reunir o trabalho de equipa conjunta a partir do código fonte e prepará-lo para a implantação para os ambientes a jusante. Tal como acontece com a gestão do código fonte, este processo é diferente para os portáteis Python e os oleodutos Azure Data Factory. 

### <a name="python-notebook-ci"></a>Píton Notebook CI

O processo ci para os Cadernos Python obtém o código do ramo de colaboração (por exemplo, * **mestre** _ ou _*_desenvolver)_*_ e executa as seguintes atividades: _ Codificação de código
* Teste de unidades
* Guardar o código como um artefacto

O seguinte corte de código demonstra a implementação destes passos num gasoduto Azure DevOps * **yaml** _ :

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
    testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

O oleoduto usa [flocos 8](https://pypi.org/project/flake8/) para fazer o código Python. Executa os testes de unidade definidos no código-fonte e publica os resultados de linagem e teste para que estejam disponíveis no ecrã de execução do Gasoduto Azure:

![testes de unidade de linting](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Se o ensaio de linco e unidade for bem sucedido, o gasoduto copiará o código de origem para o repositório de artefactos a utilizar pelas etapas de implantação subsequentes.

### <a name="azure-data-factory-ci"></a>CI da Fábrica de Dados Azure

O processo CI para um oleoduto Azure Data Factory é um estrangulamento para um oleoduto de ingestão de dados. Não há integração contínua. Um artefacto implantável para Azure Data Factory é uma coleção de modelos de Gestor de Recursos Azure. A única maneira de produzir esses modelos é clicar no botão * **publicar** _ no espaço de trabalho da Azure Data Factory.

1. Os engenheiros de dados fundem o código fonte dos seus ramos de recurso no ramo de colaboração, por exemplo, _*_dominar_*_ ou _*_desenvolver._*_ 
1. Alguém com as permissões concedidas clica no botão _*_de publicação_*_ para gerar modelos do Gestor de Recursos Azure a partir do código fonte no ramo de colaboração. 
1. O espaço de trabalho valida os oleodutos (pense-se nele como um lintamento e teste de unidade), gera modelos de Gestor de Recursos Azure (pense nisso como um edifício) e guarda os modelos gerados para um ramo técnico _*_adf_publish_*_ no mesmo repositório de código (pense nele como artefactos de publicação). Este ramo é criado automaticamente pelo espaço de trabalho da Azure Data Factory. 

Para obter mais informações sobre este processo, consulte [integração contínua e entrega na Azure Data Factory.](../data-factory/continuous-integration-deployment.md)

É importante ter certeza de que os modelos gerados do Gestor de Recursos Azure são agnósticos do ambiente. Isto significa que todos os valores que podem diferir entre ambientes são parametrizados. A Azure Data Factory é inteligente o suficiente para expor a maioria de valores como parâmetros. Por exemplo, no modelo a seguir, as propriedades de ligação a um espaço de trabalho de aprendizagem de máquinas Azure são expostas como parâmetros:

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

No entanto, pode querer expor as suas propriedades personalizadas que não são tratadas pelo espaço de trabalho da Azure Data Factory por padrão. No cenário deste artigo, um oleoduto Azure Data Factory invoca um portátil Python que processa os dados. O caderno aceita um parâmetro com o nome de um ficheiro de dados de entrada.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Este nome é diferente para _*_ambientes Dev,_*_ _*_QA,_*_ _*_UAT_*_ e _*_PROD._*_ Num oleoduto complexo com múltiplas atividades, pode haver várias propriedades personalizadas. É uma boa prática recolher todos esses valores num só local e defini-los como _*_variáveis_*_ de gasoduto:

![O Screenshot mostra um Portátil chamado PrepareData e M L Execute Pipeline chamado M L Execute Pipeline no topo com o separador Variáveis selecionado abaixo com a opção de adicionar novas variáveis, cada uma com um nome, tipo e valor predefinido.](media/how-to-cicd-data-ingestion/adf-variables.png)

As atividades do gasoduto podem referir-se às variáveis do gasoduto utilizando-as efetivamente:

![O Screenshot mostra um portátil chamado PrepareData e M L Execute Pipeline chamado M L Execute Pipeline na parte superior com o separador Definições selecionado abaixo.](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

O espaço de trabalho da Azure Data Factory não expõe variáveis _*_de_*_ gasodutos como parâmetros de modelos do Azure Resource Manager por padrão. O espaço de trabalho utiliza o [Modelo de Parametização Padrão](../data-factory/continuous-integration-deployment.md#default-parameterization-template) ditando quais as propriedades do pipeline que devem ser expostas como parâmetros do modelo do Gestor de Recursos Azure. Para adicionar variáveis de pipeline à lista, atualize a `"Microsoft.DataFactory/factories/pipelines"` secção do Modelo de [Parâmetroção Padrão](../data-factory/continuous-integration-deployment.md#default-parameterization-template) com o seguinte corte e coloque o ficheiro de resultados json na raiz da pasta de origem:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "_": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Ao fazê-lo, forçará o espaço de trabalho da Azure Data Factory a adicionar as variáveis à lista de parâmetros quando o botão * **publicar** _ é clicado:

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

Os valores no ficheiro JSON são valores predefinidos configurados na definição do pipeline. Espera-se que sejam ultrapassados com os valores do ambiente-alvo quando o modelo do Gestor de Recursos Azure for implementado.

## <a name="continuous-delivery-cd"></a>Entrega contínua (CD)

O processo de Entrega Contínua leva os artefactos e implanta-os para o primeiro ambiente alvo. Certifica-se de que a solução funciona através de testes realizados. Se for bem sucedido, continua para o próximo ambiente. 

O Pipeline CD Azure consiste em múltiplas etapas que representam os ambientes. Cada fase contém [implantações](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) e [trabalhos](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) que executam os seguintes passos:

_ Implementar um caderno python para o espaço de trabalho Azure Databricks
* Implementar um oleoduto Azure Data Factory 
* Executar o pipeline
* Verifique o resultado da ingestão de dados

As fases do gasoduto podem ser configuradas com [aprovações](/azure/devops/pipelines/process/approvals?tabs=check-pass&view=azure-devops&preserve-view=true) e [portões](/azure/devops/pipelines/release/approvals/gates?view=azure-devops&preserve-view=true) que proporcionam um controlo adicional sobre como o processo de implantação evolui através da cadeia de ambientes.

### <a name="deploy-a-python-notebook"></a>Implementar um caderno python

O seguinte corte de código define uma [implantação](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) do Gasoduto Azure que copia um caderno Python para um cluster Databricks:

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

Os artefactos produzidos pelo CI são automaticamente copiados para o agente de implantação e estão disponíveis na `$(Pipeline.Workspace)` pasta. Neste caso, a tarefa de implantação refere-se ao `di-notebooks` artefacto que contém o caderno Python. Esta [implementação](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) utiliza a [extensão Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) para copiar os ficheiros do portátil para o espaço de trabalho databricks.

O `Deploy_to_QA` palco contém uma referência ao grupo `devops-ds-qa-vg` variável definido no projeto Azure DevOps. Os passos nesta fase referem-se às variáveis deste grupo variável (por exemplo, `$(DATABRICKS_URL)` e `$(DATABRICKS_TOKEN)` ). A ideia é que a próxima fase (por `Deploy_to_UAT` exemplo,) funcione com os mesmos nomes variáveis definidos no seu próprio grupo variável de aplicação UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Implementar um oleoduto Azure Data Factory

Um artefacto implantável para Azure Data Factory é um modelo de Gestor de Recursos Azure. Será implantado com a tarefa * **Azure Resource Group Deployment** _ como é demonstrado no seguinte corte:

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
O valor do parâmetro data filename provém da `$(DATA_FILE_NAME)` variável definida num grupo variável de fase QA. Da mesma forma, todos os parâmetros definidos em _*_ARMTemplateForFactory.js_*_ podem ser ultrapassados. Se não forem, os valores predefinidos são utilizados.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Executar o oleoduto e verificar o resultado da ingestão de dados

O próximo passo é garantir que a solução implementada está a funcionar. A seguinte definição de trabalho executa um oleoduto Azure Data Factory com um [script PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) e executa um caderno Python num cluster Azure Databricks. O portátil verifica se os dados foram ingeridos corretamente e valida o ficheiro de dados de resultados com `$(bin_FILE_NAME)` o nome.

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

A tarefa final no trabalho verifica o resultado da execução do caderno. Se der uma posição errada, define o estado da execução do gasoduto como falhado.

## <a name="putting-pieces-together"></a>Juntando peças

O gasoduto CI/CD Azure completo consiste nas seguintes fases: _ CI
* Implementar para QA
    * Implementar para Databricks + Implementar para ADF
    * Teste de Integração

Contém um número de fases * **Implementar** _ iguais ao número de ambientes-alvo que tem. Cada fase _*_de implantação_*_ contém [duas implementações](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops&preserve-view=true) que funcionam em paralelo e um [trabalho](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops&preserve-view=true) que corre após implementações para testar a solução no ambiente.

No seguinte corte _*_yaml_*_ é montada uma amostra do gasoduto:

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
        testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
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

## <a name="next-steps"></a>Próximos passos

* [Controlo de Origem no Azure Data Factory](../data-factory/source-control.md)
* [Integração contínua e entrega na Azure Data Factory](../data-factory/continuous-integration-deployment.md)
* [DevOps para Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)