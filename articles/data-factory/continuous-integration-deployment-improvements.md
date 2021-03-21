---
title: Publicação automatizada para integração e entrega contínuas
description: Aprenda a publicar para integração contínua e entrega automaticamente.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 496d2b6b3d669013c8174e9bc961d0a2f640bed3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462087"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Publicação automatizada para integração e entrega contínuas

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Descrição geral

A integração contínua é a prática de testar automaticamente cada alteração feita ao código base. O mais cedo possível, a entrega contínua segue o teste que acontece durante a integração contínua e empurra alterações para um sistema de encenação ou produção.

Na Azure Data Factory, integração contínua e entrega contínua (CI/CD) significa mover os gasodutos da Data Factory de um ambiente, como o desenvolvimento, teste e produção, para outro. A Data Factory utiliza [modelos de Gestor de Recursos Azure (modelos ARM)](../azure-resource-manager/templates/overview.md) para armazenar a configuração das suas várias entidades da Data Factory, tais como oleodutos, conjuntos de dados e fluxos de dados.

Existem dois métodos sugeridos para promover uma fábrica de dados para outro ambiente:

- Implantação automatizada utilizando a integração da Data Factory com [gasodutos Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Carregar manualmente um modelo ARM utilizando a integração do utilizador da Data Factory com o Azure Resource Manager.

Para mais informações, consulte [integração contínua e entrega na Azure Data Factory.](continuous-integration-deployment.md)

Este artigo centra-se nas melhorias contínuas da implementação e na funcionalidade de publicação automatizada para o CI/CD.

## <a name="continuous-deployment-improvements"></a>Melhorias contínuas da implementação

A funcionalidade de publicação automatizada retira as funcionalidades do modelo **Validate all** and **Export ARM** da experiência do utilizador data Factory e torna a lógica consumível através de um pacote npm disponível ao [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) público. Por esta razão, pode desencadear programáticamente estas ações em vez de ter de ir à UI da Data Factory e selecionar manualmente um botão. Esta capacidade dará aos seus oleodutos CI/CD uma experiência de integração contínua mais verdadeira.

### <a name="current-cicd-flow"></a>Fluxo atual de CI/CD

1. Cada utilizador faz alterações nos seus balcões privados.
1. Empurrar para o mestre não é permitido. Os utilizadores devem criar um pedido de puxar para fazer alterações.
1. Os utilizadores devem carregar o UI da Fábrica de Dados e selecionar **publicar** para implementar alterações na Data Factory e gerar os modelos ARM na sucursal de publicação.
1. O pipeline DevOps Release está configurado para criar uma nova versão e implementar o modelo ARM cada vez que uma nova alteração é empurrada para o ramo de publicação.

![Diagrama que mostra o fluxo atual de CI/CD.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Passo manual

No fluxo atual de CI/CD, a experiência do utilizador é o intermediário para criar o modelo ARM. Como resultado, um utilizador deve ir à UI da Data Factory e selecionar manualmente **Publicar** para iniciar a geração do modelo ARM e deixá-la cair no ramo de publicação.

### <a name="the-new-cicd-flow"></a>O novo fluxo CI/CD

1. Cada utilizador faz alterações nos seus balcões privados.
1. Empurrar para o mestre não é permitido. Os utilizadores devem criar um pedido de puxar para fazer alterações.
1. A construção do oleoduto Azure DevOps é desencadeada sempre que um novo compromisso é feito para dominar. Valida os recursos e gera um modelo ARM como artefacto se a validação for bem sucedida.
1. O pipeline DevOps Release está configurado para criar uma nova versão e implementar o modelo ARM sempre que estiver disponível uma nova construção.

![Diagrama que mostra o novo fluxo ci/CD.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>O que mudou?

- Temos agora um processo de construção que usa um oleoduto de construção de DevOps.
- O gasoduto de construção utiliza o pacote NPM da ADFUtilities, que validará todos os recursos e gerará os modelos ARM. Estes modelos podem ser solteiros e ligados.
- O pipeline de construção é responsável por validar os recursos da Data Factory e gerar o modelo ARM em vez do UI da Fábrica de Dados ( Botão **Publicar).**
- A definição de libertação de DevOps irá agora consumir este novo oleoduto de construção em vez do artefacto Git.

> [!NOTE]
> Pode continuar a utilizar o mecanismo existente, que é o `adf_publish` ramo, ou pode utilizar o novo fluxo. Ambos são apoiados.

## <a name="package-overview"></a>Descrição geral do pacote

Dois comandos estão atualmente disponíveis no pacote:

- Exportar o modelo do Resource Manager
- Validação

### <a name="export-arm-template"></a>Exportar o modelo do Resource Manager

Corra `npm run start export <rootFolder> <factoryId> [outputFolder]` para exportar o modelo ARM utilizando os recursos de uma determinada pasta. Este comando também executa uma verificação de validação antes de gerar o modelo ARM. Eis um exemplo:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` é um campo obrigatório que representa onde estão localizados os recursos da Data Factory.
- `FactoryId` é um campo obrigatório que representa o ID de recursos da Data Factory no formato `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` é um parâmetro opcional que especifica o caminho relativo para salvar o modelo ARM gerado.
 
> [!NOTE]
> O modelo ARM gerado não é publicado para a versão ao vivo da fábrica. A implantação deve ser efetuada utilizando um gasoduto CI/CD.
 
### <a name="validate"></a>Validação

Corra `npm run start validate <rootFolder> <factoryId>` para validar todos os recursos de uma determinada pasta. Eis um exemplo:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` é um campo obrigatório que representa onde estão localizados os recursos da Data Factory.
- `FactoryId` é um campo obrigatório que representa o ID de recursos da Data Factory no formato `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Criar um gasoduto Azure

Enquanto os pacotes npm podem ser consumidos de várias maneiras, um dos principais benefícios está a ser consumido através do [Gasoduto Azure.](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0) Em cada fusão no seu ramo de colaboração, pode ser acionado um gasoduto que primeiro valida todo o código e, em seguida, exporta o modelo ARM para um [artefacto de construção](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) que pode ser consumido por um oleoduto de libertação. A forma como difere do atual processo ci/CD é que irá *apontar o seu oleoduto de libertação para este artefacto em vez do `adf_publish` ramo existente*.

Siga estes passos para começar:

1.  Abra um projeto Azure DevOps e vá a **Pipelines.** Selecione **Novo Pipeline**.

    ![Screenshot que mostra o novo botão do gasoduto.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Selecione o repositório onde pretende guardar o seu script YAML do pipeline. Recomendamos guardá-lo numa pasta de construção no mesmo repositório dos seus recursos de Data Factory. Certifique-se de que há uma *package.jsno* arquivo no repositório que contém o nome do pacote, como mostrado no seguinte exemplo:

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  Selecione **o gasoduto starter**. Se tiver carregado ou fundido o ficheiro YAML, como mostrado no exemplo seguinte, também pode apontar diretamente para isso e editá-lo.

    ![Screenshot que mostra o gasoduto Starter.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
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
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Insira o seu código YAML. Recomendamos que utilize o ficheiro YAML como ponto de partida.
1.  Salve e corra. Se utilizar o YAML, é acionado sempre que o ramo principal é atualizado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais informações sobre integração contínua e entrega na Data Factory:

- [Integração contínua e entrega na Azure Data Factory.](continuous-integration-deployment.md)
