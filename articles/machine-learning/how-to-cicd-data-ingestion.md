---
title: DevOps para um oleoduto de ingestão de dados
titleSuffix: Azure Machine Learning
description: Saiba como aplicar práticas de DevOps a uma implementação do pipeline de ingestão de dados usada para preparar dados para uma formação de modelos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: c5ffd45eadfd2d1b0e455ea3de91bf91c11c2517
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434617"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps para um oleoduto de ingestão de dados

Na maioria dos cenários, uma solução de ingestão de dados é uma composição de scripts, invocações de serviços e um pipeline orquestrando todas as atividades. Neste artigo, aprende-se a aplicar práticas de DevOps ao ciclo de vida de desenvolvimento de um oleoduto comum de ingestão de dados. O pipeline prepara os dados para a formação do modelo de Machine Learning.

## <a name="the-solution"></a>A solução

Considere o seguinte fluxo de trabalho de ingestão de dados:

![dados-ingestão-pipeline](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

Nesta abordagem, os dados de formação são armazenados num armazenamento de bolhas Azure. Um gasoduto Azure Data Factory recolhe os dados de um recipiente de bolha de entrada, transforma-os e guarda os dados para o recipiente de bolhas de saída. Este recipiente serve como [um armazenamento de dados](concept-data.md) para o serviço Azure Machine Learning. Tendo os dados preparados, o pipeline da Data Factory invoca um pipeline de aprendizagem automática de formação para treinar um modelo. Neste exemplo específico, a transformação de dados é realizada por um caderno Python, que funciona num cluster Azure Databricks. 

## <a name="what-we-are-building"></a>O que estamos a construir

Como em qualquer solução de software, há uma equipa (por exemplo, Engenheiros de Dados) a trabalhar nela. 

![cicd-data-ingestão](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Colaboram e partilham os mesmos recursos Azure, tais como Azure Data Factory, Azure Databricks, conta de Armazenamento Azure e tal. A recolha destes recursos é um ambiente de Desenvolvimento. Os engenheiros de dados contribuem para a mesma base de código fonte. O processo de Integração Contínua monta o código, verifica-o com os testes de qualidade do código, testes unitários e produz artefactos como código testado e modelos de Gestor de Recursos Azure. O processo de Entrega Contínua implanta os artefactos para os ambientes a jusante. Este artigo demonstra como automatizar os processos de CI e CD com [a Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Gestão do controlo de fontes

Os membros da equipa trabalham de formas ligeiramente diferentes para colaborar no código-fonte do portátil Python e no código fonte da Azure Data Factory. No entanto, em ambos os casos, o código é armazenado num repositório de controlo de origem (por exemplo, Azure DevOps, GitHub, GitLab) e a colaboração é normalmente baseada em algum modelo de ramificação (por exemplo, [GitFlow).](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Código fonte do caderno python

Os engenheiros de dados trabalham com o código-fonte do portátil Python localmente num IDE (por exemplo, [Código do Estúdio Visual)](https://code.visualstudio.com)ou diretamente no espaço de trabalho databricks. Este último dá a capacidade de depurar o código sobre o ambiente de desenvolvimento. Em todo o caso, o código será fundido ao repositório na sequência de uma política de ramificação. É altamente recomendado armazenar o código em `.py` ficheiros e não no `.ipynb` formato de caderno Jupyter. Melhora a legibilidade do código e permite controlos automáticos de qualidade de código no processo de CI.

### <a name="azure-data-factory-source-code"></a>Código fonte da fábrica de dados Azure

O código fonte dos oleodutos Azure Data Factory é uma coleção de ficheiros json gerados por um espaço de trabalho. Normalmente, os engenheiros de dados trabalham com um designer visual no espaço de trabalho da Azure Data Factory em vez de com os ficheiros de código de origem diretamente. Configure o espaço de trabalho com um repositório de controlo de origem, tal como está descrito na documentação da [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). Com esta configuração em vigor, os engenheiros de dados são capazes de colaborar no código fonte seguindo um fluxo de trabalho de ramificação preferencial.    

## <a name="continuous-integration-ci"></a>Integração contínua (CI)

O objetivo final do processo de Integração Contínua é reunir o trabalho de equipa conjunta a partir do código fonte e prepará-lo para a implantação para os ambientes a jusante. Tal como acontece com a gestão do código fonte, este processo é diferente para os portáteis Python e os oleodutos Azure Data Factory. 

### <a name="python-notebook-ci"></a>Píton Notebook CI

O processo de CI para os Cadernos Python obtém o código do ramo de colaboração (por exemplo, ***dominar*** ou ***desenvolver)*** e realiza as seguintes atividades:
* Linça de código
* Teste de unidade
* Guardar o código como um artefacto

O seguinte corte de código demonstra a implementação destes passos num oleoduto Azure DevOps ***yaml:***

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

O oleoduto usa ***flocos 8*** para fazer o código Python. Executa os testes de unidade definidos no código-fonte e publica os resultados de linagem e teste para que estejam disponíveis no ecrã de execução do Gasoduto Azure:

![linting-unit-tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Se o ensaio de linco e unidade for bem sucedido, o gasoduto copiará o código de origem para o repositório de artefactos a utilizar pelas etapas de implantação subsequentes.

### <a name="azure-data-factory-ci"></a>CI da Fábrica de Dados Azure

O processo ci para um oleoduto Azure Data Factory é um estrangulamento em toda a história do CI/CD para um pipeline de ingestão de dados. Não há integração ***contínua.*** Um artefacto implantável para Azure Data Factory é uma coleção de modelos de Gestor de Recursos Azure. A única maneira de produzir esses modelos é clicar no botão ***de publicação*** no espaço de trabalho da Azure Data Factory. Não há automação aqui.
Os engenheiros de dados fundem o código fonte dos seus ramos de recurso no ramo de colaboração, por exemplo, ***dominar*** ou ***desenvolver.*** Em seguida, alguém com as permissões concedidas clica no botão ***de publicação*** para gerar modelos do Azure Resource Manager a partir do código fonte no ramo de colaboração. Quando o botão é clicado, o espaço de trabalho valida os gasodutos (pense nele como um lintamento e teste de unidade), gera modelos de Gestor de Recursos Azure (pense nisso como um edifício) e guarda os modelos gerados para um ramo técnico ***adf_publish*** no mesmo repositório de código (pense nele como artefactos de publicação). Este ramo é criado automaticamente pelo espaço de trabalho da Azure Data Factory. Este processo é descrito em detalhes na documentação da [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

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

Este nome é diferente para ***ambientes Dev,*** ***QA,*** ***UAT***e ***PROD.*** Num oleoduto complexo com múltiplas atividades, pode haver várias propriedades personalizadas. É uma boa prática recolher todos esses valores num só local e defini-los como ***variáveis***de gasoduto:

![adf-variáveis](media/how-to-cicd-data-ingestion/adf-variables.png)

As atividades do gasoduto podem referir-se às variáveis do gasoduto utilizando-as efetivamente:

![adf-notebook-parâmetros](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

O espaço de trabalho da Azure Data Factory não expõe variáveis ***de*** gasodutos como parâmetros de modelos do Azure Resource Manager por padrão. O espaço de trabalho utiliza o [Modelo de Parametização Padrão](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) ditando quais as propriedades do pipeline que devem ser expostas como parâmetros do modelo do Gestor de Recursos Azure. Para adicionar variáveis de gasoduto à lista, atualize a secção "Microsoft.DataFactory/fábricas/pipelines" do Modelo de [Parâmetros Padrão](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) com o seguinte corte e coloque o ficheiro de resultados json na raiz da pasta de origem:

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

Ao fazê-lo, forçará o espaço de trabalho da Azure Data Factory a adicionar as variáveis à lista de parâmetros quando o botão ***de publicação*** é clicado:

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

Os valores no ficheiro json são valores predefinidos configurados na definição do pipeline. Espera-se que sejam ultrapassados com os valores do ambiente-alvo quando o modelo do Gestor de Recursos Azure for implementado.

## <a name="continuous-delivery-cd"></a>Entrega contínua (CD)

O processo de Entrega Contínua leva os artefactos e implanta-os para o primeiro ambiente alvo. Certifica-se de que a solução funciona através de testes realizados. Se for bem sucedido, continua para o próximo ambiente. O Pipeline CD Azure consiste em múltiplas etapas que representam os ambientes. Cada fase contém [implantações](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) e [trabalhos](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) que executam os seguintes passos:
* Implementar um caderno python para o espaço de trabalho Azure Databricks
* Implementar um oleoduto Azure Data Factory 
* Executar o pipeline
* Verifique o resultado da ingestão de dados

As fases do gasoduto podem ser configuradas com [aprovações](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) e [portões](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) que proporcionam um controlo adicional sobre como o processo de implantação evolui através da cadeia de ambientes.

### <a name="deploy-a-python-notebook"></a>Implementar um caderno python

O seguinte corte de código define uma [implantação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) do Gasoduto Azure que copia um caderno Python para um cluster Databricks:

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

Os artefactos produzidos pelo CI são automaticamente copiados para o agente de implantação e estão disponíveis na pasta ***$(Pipeline.Workspace).*** Neste caso, a tarefa de implantação refere-se ao artefacto ***di-notebooks*** que contém o caderno Python. Esta [implementação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) utiliza a [extensão Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) para copiar os ficheiros do portátil para o espaço de trabalho databricks.
A ***fase Deploy_to_QA*** contém uma referência ao grupo variável ***devops-ds-qa-vg*** definido no projeto Azure DevOps. Os passos nesta fase referem-se às variáveis deste grupo variável (por exemplo, $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). A ideia é que a próxima fase (por exemplo, ***Deploy_to_UAT***) funcione com os mesmos nomes variáveis definidos no seu próprio grupo variável de aplicação UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Implementar um oleoduto Azure Data Factory

Um artefacto implantável para Azure Data Factory é um modelo de Gestor de Recursos Azure. Portanto, será implementado com a tarefa de implantação do ***Grupo de Recursos Azure,*** tal como é demonstrado no seguinte corte:

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
O valor do parâmetro do nome de ficheiros de dados provém da variável $(DATA_FILE_NAME) definida num grupo variável de estágio QA. Da mesma forma, todos os parâmetros definidos em ***ARMTemplateForFactory.json*** podem ser ultrapassados. Se não forem, os valores predefinidos são utilizados.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Executar o oleoduto e verificar o resultado da ingestão de dados

O próximo passo é garantir que a solução implementada está a funcionar. A seguinte definição de trabalho executa um oleoduto Azure Data Factory com um [script PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) e executa um caderno Python num cluster Azure Databricks. O portátil verifica se os dados foram ingeridos corretamente e valida o ficheiro de dados de resultados com o nome $(bin_FILE_NAME).

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

O resultado deste artigo é um Pipeline CI/CD Azure que consiste nas seguintes fases:
* CI
* Implementar para QA
    * Implementar para Databricks + Implementar para ADF
    * Teste de Integração

Contém uma série de fases de ***implantação*** iguais ao número de ambientes-alvo que tem. Cada fase ***de implantação*** contém [duas implementações](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) que funcionam em paralelo e um [trabalho](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) que corre após implementações para testar a solução no ambiente.

No seguinte corte ***yaml*** é montada uma amostra do gasoduto:

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
* [Integração contínua e entrega na Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps para Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
