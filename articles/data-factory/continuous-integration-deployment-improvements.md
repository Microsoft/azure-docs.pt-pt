---
title: Publicação automatizada para integração e entrega contínuas
description: Aprenda a publicar para integração contínua e entrega automaticamente.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b5becd8ddaf74ab2acd059054a095ce9d21c178f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366845"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Publicação automatizada para integração e entrega contínuas

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Descrição Geral

A integração contínua é a prática de testar cada alteração feita automaticamente na sua base de código e o mais cedo possível a entrega contínua segue o teste que ocorre durante a integração contínua e empurra alterações para um sistema de preparação ou produção.

Na Azure Data Factory, integração e entrega contínuas (CI/CD) significa mover os gasodutos data factory de um ambiente (desenvolvimento, teste, produção) para outro. A Azure Data Factory utiliza [modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para armazenar a configuração das suas várias entidades ADF (pipelines, conjuntos de dados, fluxos de dados, e assim por diante). Existem dois métodos sugeridos para promover uma fábrica de dados para outro ambiente:

- Implantação automatizada utilizando a integração da Data Factory com [a Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Carre faça o upload manual de um modelo de Gestor de Recursos utilizando a integração UX da Data Factory com o Azure Resource Manager.

Para mais informações, consulte [integração contínua e entrega na Azure Data Factory.](continuous-integration-deployment.md)

Neste artigo, focamo-nos nas melhorias contínuas da implementação e na funcionalidade de publicação automatizada para o CI/CD.

## <a name="continuous-deployment-improvements"></a>Melhorias contínuas da implementação

A funcionalidade "Publicação Automatizada" retira a *validação de todas as* funcionalidades do *modelo Azure Resource Manager (ARM)* do ADF UX e torna a lógica consumível através de um pacote npm disponível ao [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) público. Isto permite-lhe desencadear programáticamente estas ações em vez de ter de ir ao UI ADF e fazer um clique de botão. Isto dará aos seus oleodutos CI/CD uma experiência de integração contínua mais verdadeira.

### <a name="current-cicd-flow"></a>Fluxo atual de CI/CD

1. Cada utilizador faz alterações nos seus balcões privados.
2. É proibido empurrar para o domínio, os utilizadores devem criar um PR para dominar para fazer alterações.
3. Os utilizadores devem carregar ADF UI e clicar em publicar para implementar alterações na Data Factory e gerar os modelos ARM na sucursal publicar.
4. O pipeline de desbloqueio de devOps está configurado para criar uma nova versão e implementar o modelo ARM cada vez que uma nova alteração é empurrada para o ramo de publicação.

![Fluxo atual de CI/CD](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Passo manual

No fluxo atual de CI/CD, o UX é o intermediário para criar o modelo ARM, pelo que um utilizador deve ir para a UI ADF e clicar manualmente em publicar para iniciar a geração do modelo ARM e deixá-lo cair no ramo de publicação, que é um pouco de hack.

### <a name="the-new-cicd-flow"></a>O novo fluxo CI/CD

1. Cada utilizador faz alterações nos seus balcões privados.
2. É proibido empurrar para o domínio, os utilizadores devem criar um PR para dominar para fazer alterações.
3. **A construção do oleoduto Azure DevOps é desencadeada sempre que um novo compromisso é feito para dominar, valida os recursos e gera um modelo ARM como um artefacto se a validação for bem sucedida.**
4. O pipeline de desbloqueio de devOps está configurado para criar uma nova versão e implementar o modelo ARM sempre que estiver disponível uma nova construção. 

![Novo fluxo CI/CD](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>O que mudou?

- Temos agora um processo de construção usando um oleoduto de construção de DevOps.
- O gasoduto de construção utiliza o pacote NPM da ADFUtilities, que validará todos os recursos e gerará os modelos ARM (modelos individuais e ligados).
- O gasoduto de construção será responsável pela validação dos recursos ADF e pela geração do modelo ARM em vez de ADF UI (botão publicar).
- A definição de libertação de DevOps irá agora consumir este novo oleoduto de construção em vez do artefacto Git.

> [!NOTE]
> Pode continuar a utilizar o mecanismo existente (adf_publish ramo) ou utilizar o novo fluxo. Ambos são apoiados. 

## <a name="package-overview"></a>Descrição geral do pacote

Existem dois comandos atualmente disponíveis no pacote:
- Exportar o modelo do Resource Manager
- Validação

### <a name="export-arm-template"></a>Exportar o modelo do Resource Manager

Executar npm run iniciar a exportação <rootFolder> <factoryId> [outputFolder] para exportar o modelo ARM usando os recursos de uma determinada pasta. Este comando executa uma verificação de validação também antes de gerar o modelo ARM. Abaixo está um exemplo:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder é um campo obrigatório que representa onde estão localizados os recursos da Data Factory.
- FactoryId é um campo obrigatório que representa o ID de recursos de fábrica de dados no formato: "/subscrições/ <subId> /resourceGroups/ <rgName> /providers/Microsoft.DataFactory/fábricas/ <dfName> ".
- OutputFolder é um parâmetro opcional que especifica o caminho relativo para salvar o modelo ARM gerado.
 
> [!NOTE]
> O modelo ARM gerado não é publicado na `Live` versão da fábrica. A implantação deve ser efetuada utilizando um gasoduto CI/CD. 
 

### <a name="validate"></a>Validação

Executar npm run começar <rootFolder> <factoryId> a validar para validar todos os recursos de uma determinada pasta. Abaixo está um exemplo:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder é um campo obrigatório que representa onde estão localizados os recursos da Data Factory.
- FactoryId é um campo obrigatório que representa o ID de recursos de fábrica de dados no formato: "/subscrições/ <subId> /resourceGroups/ <rgName> /providers/Microsoft.DataFactory/fábricas/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Criar um gasoduto Azure

Enquanto os pacotes npm podem ser consumidos de várias maneiras, um dos principais benefícios está a ser consumido através de um [Gasoduto Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Em cada fusão no seu ramo de colaboração, pode ser acionado um gasoduto que primeiro valida todo o código e, em seguida, exporta o modelo ARM para um [artefacto de construção](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) que pode ser consumido por um oleoduto de libertação. **A forma como difere do processo atual de CI/CD é que irá apontar o seu oleoduto de libertação para este artefacto em vez do `adf_publish` ramo existente.**

Siga os passos abaixo para começar:

1.  Abra um projeto Azure DevOps e vá a "Pipelines". Selecione "Novo Oleoduto".

    ![Novo Pipeline](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Selecione o repositório onde deseja guardar o seu script Pipeline YAML. Recomendamos guardá-lo numa pasta de *construção* dentro do mesmo repositório dos seus recursos ADF. Certifique-se de que existe uma **package.jsno** ficheiro no repositório que contenha o nome do pacote (como mostrado a seguir).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  Selecione *o gasoduto starter*. Se tiver carregado ou fundido o ficheiro YAML (como mostrado no exemplo abaixo), também pode apontar diretamente para isso e editá-lo. 

    ![Gasoduto de arranque](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Introduza o seu código YAML. Recomendamos que tome o ficheiro YAML e o utilize como ponto de partida.
5.  Salve e corra. Se utilizar o YAML, será acionado sempre que o ramo "principal" for atualizado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais informações sobre integração contínua e entrega na Data Factory: 

- [Integração contínua e entrega na Azure Data Factory.](continuous-integration-deployment.md)
