---
title: Implantação segura entre regiões – Azure Deployment Manager
description: Descreve como implantar um serviço em várias regiões com o Azure Deployment Manager. Ele mostra práticas de implantação segura para verificar a estabilidade de sua implantação antes de distribuir para todas as regiões.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 6cbbd2aef1911bdfe64e781a7cad64a64722192d
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532310"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Habilitar práticas de implantação segura com o Azure Deployment Manager (visualização pública)

Para implantar seu serviço em várias regiões e verificar se ele está sendo executado conforme o esperado em cada região, você pode usar o Azure Deployment Manager para coordenar uma distribuição em etapas do serviço. Da mesma forma que faria com qualquer implantação do Azure, você define os recursos para seu serviço em [modelos do Resource Manager](resource-group-authoring-templates.md). Depois de criar os modelos, você usa Deployment Manager para descrever a topologia do serviço e como ele deve ser distribuído.

Deployment Manager é um recurso do Resource Manager. Ele expande seus recursos durante a implantação. Use Deployment Manager quando você tiver um serviço complexo que precisa ser implantado em várias regiões. Ao fasear a implementação do serviço, poderá deparar-se com problemas potenciais antes de este ter sido implementado em todas as regiões. Se você não precisar das precauções adicionais de uma distribuição em etapas, use as [Opções de implantação](resource-group-template-deploy-portal.md) padrão para o Gerenciador de recursos. Deployment Manager integra-se perfeitamente a todas as ferramentas de terceiros existentes que dão suporte a implantações do Gerenciador de recursos, como ofertas de CI/CD (integração contínua e entrega contínua).

O Deployment Manager do Azure está em versão prévia. Ajude-nos a melhorar o recurso fornecendo [comentários](https://aka.ms/admfeedback).

Para usar Deployment Manager, você precisa criar quatro arquivos:

* Modelo de topologia
* Modelo de distribuição
* Arquivo de parâmetro para topologia
* Arquivo de parâmetro para distribuição

Você implanta o modelo de topologia antes de implantar o modelo de distribuição.

Recursos adicionais:

- A [referência da API REST do Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Tutorial: usar o Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md).
- [Tutorial: usar a verificação de integridade no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Um exemplo de Deployment Manager do Azure](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identidade e acesso

Com o Deployment Manager, uma [identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/overview.md) executa as ações de implantação. Você cria essa identidade antes de iniciar a implantação. Ele deve ter acesso à assinatura na qual você está implantando o serviço e a permissão suficiente para concluir a implantação. Para obter informações sobre as ações concedidas por meio de funções, consulte [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md).

A identidade deve residir no mesmo local que a distribuição.

## <a name="topology-template"></a>Modelo de topologia

O modelo de topologia descreve os recursos do Azure que compõem o serviço e onde implantá-los. A imagem a seguir mostra a topologia para um serviço de exemplo:

![Hierarquia da topologia de serviço para serviços a unidades de serviço](./media/deployment-manager-overview/service-topology.png)

O modelo de topologia inclui os seguintes recursos:

* Origem do artefato-onde os modelos e parâmetros do Resource Manager são armazenados
* Topologia de serviço – aponta para a origem do artefato
  * Serviços-especifica a localização e a ID da assinatura do Azure
    * Unidades de serviço-especifica o grupo de recursos, o modo de implantação e o caminho para o modelo e o arquivo de parâmetro

Para entender o que acontece em cada nível, é útil ver quais valores você fornece.

![Valores para cada nível](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Origem do artefato para modelos

No modelo de topologia, você cria uma fonte de artefato que contém os arquivos de modelos e parâmetros. A origem do artefato é uma maneira de extrair os arquivos para implantação. Você verá outra fonte de artefato para binários mais adiante neste artigo.

O exemplo a seguir mostra o formato geral da origem do artefato.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

Para obter mais informações, consulte [artifactSources template Reference](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologia de serviço

O exemplo a seguir mostra o formato geral do recurso de topologia de serviço. Você fornece a ID de recurso da origem do artefato que contém os modelos e arquivos de parâmetro. A topologia de serviço inclui todos os recursos de serviço. Para verificar se a origem do artefato está disponível, a topologia do serviço depende dele.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

Para obter mais informações, consulte [referência de modelo de Pertopologias](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Serviços

O exemplo a seguir mostra o formato geral do recurso de serviços. Em cada serviço, você fornece o local e a ID de assinatura do Azure para usar para implantar seu serviço. Para implantar em várias regiões, você define um serviço para cada região. O serviço depende da topologia do serviço.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

Para obter mais informações, consulte [referência de modelo de serviços](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Unidades de Serviço

O exemplo a seguir mostra o formato geral do recurso de unidades de serviço. Em cada unidade de serviço, você especifica o grupo de recursos, o [modo de implantação](deployment-modes.md) a ser usado para implantação e o caminho para o arquivo de modelo e parâmetro. Se você especificar um caminho relativo para o modelo e os parâmetros, o caminho completo será construído a partir da pasta raiz na origem dos artefatos. Você pode especificar um caminho absoluto para o modelo e os parâmetros, mas perde a capacidade de fazer a versão de suas versões com facilidade. A unidade de serviço depende do serviço.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

Cada modelo deve incluir os recursos relacionados que você deseja implantar em uma única etapa. Por exemplo, uma unidade de serviço pode ter um modelo que implanta todos os recursos para o front-end do serviço.

Para obter mais informações, consulte [referência de modelo de Perunits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Modelo de distribuição

O modelo de distribuição descreve as etapas a serem seguidas ao implantar seu serviço. Especifique a topologia de serviço a ser usada e defina a ordem de implantação de unidades de serviço. Ele inclui uma fonte de artefato para armazenar binários para a implantação. No modelo de distribuição, você define a seguinte hierarquia:

* Origem do artefato
* Passo
* Profundo
  * Grupos de etapas
    * Operações de implantação

A imagem a seguir mostra a hierarquia do modelo de distribuição:

![Hierarquia de distribuição para etapas](./media/deployment-manager-overview/Rollout.png)

Cada distribuição pode ter muitos grupos de etapas. Cada grupo de etapas tem uma operação de implantação que aponta para uma unidade de serviço na topologia do serviço.

### <a name="artifact-source-for-binaries"></a>Origem do artefato para binários

No modelo de distribuição, você cria uma fonte de artefato para os binários que precisa implantar no serviço. Essa origem do artefato é semelhante à [origem do artefato para modelos](#artifact-source-for-templates), exceto que ele contém os scripts, as páginas da Web, o código compilado ou outros arquivos necessários para seu serviço.

### <a name="steps"></a>Passos

Você pode definir uma etapa para executar antes ou depois da operação de implantação. Atualmente, somente a etapa `wait` e a etapa ' healthCheck ' estão disponíveis.

A etapa de espera pausa a implantação antes de continuar. Ele permite que você verifique se o serviço está sendo executado conforme o esperado antes de implantar a próxima unidade de serviço. O exemplo a seguir mostra o formato geral de uma etapa de espera.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

A Propriedade Duration usa o [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). O exemplo anterior especifica uma espera de um minuto.

Para obter mais informações sobre a etapa de verificação de integridade, consulte [introduzir implantação de integração de integridade para o Azure Deployment Manager](./deployment-manager-health-check.md) e [tutorial: usar a verificação de integridade no Deployment Manager do Azure](./deployment-manager-tutorial-health-check.md).

Para obter mais informações, consulte [referência de modelo de etapas](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Implementações

Para verificar se a origem do artefato está disponível, a distribuição depende dela. A distribuição define os grupos de etapas para cada unidade de serviço implantada. Você pode definir ações a serem executadas antes ou após a implantação. Por exemplo, você pode especificar que a implantação aguarde após a implantação da unidade de serviço. Você pode definir a ordem dos grupos de etapas.

O objeto de identidade especifica a [identidade gerenciada atribuída pelo usuário](#identity-and-access) que executa as ações de implantação.

O exemplo a seguir mostra o formato geral da distribuição.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

Para obter mais informações, consulte [referência de modelo de distribuições](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Arquivo de parâmetro

Você cria dois arquivos de parâmetro. Um arquivo de parâmetro é usado ao implantar a topologia de serviço e o outro é usado para a implantação de distribuição. Há alguns valores que você precisa garantir que sejam os mesmos em ambos os arquivos de parâmetro.

## <a name="containerroot-variable"></a>variável containerRoot

Com as implantações com controle de versão, o caminho para seus artefatos é alterado com cada nova versão. Na primeira vez que você executar uma implantação, o caminho poderá ser `https://<base-uri-blob-container>/binaries/1.0.0.0`. A segunda vez que pode ser `https://<base-uri-blob-container>/binaries/1.0.0.1`. Deployment Manager simplifica obter o caminho raiz correto para a implantação atual usando a variável `$containerRoot`. Esse valor é alterado com cada versão e não é conhecido antes da implantação.

Use a variável `$containerRoot` no arquivo de parâmetro para o modelo para implantar os recursos do Azure. No momento da implantação, essa variável é substituída pelos valores reais da distribuição.

Por exemplo, durante a distribuição, você cria uma fonte de artefato para os artefatos binários.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

Observe as propriedades `artifactRoot` e `sasUri`. A raiz do artefato pode ser definida como um valor como `binaries/1.0.0.0`. O URI de SAS é o URI para seu contêiner de armazenamento com um token SAS para acesso. Deployment Manager constrói automaticamente o valor da variável `$containerRoot`. Ele combina esses valores no formato `<container>/<artifactRoot>`.

Seu modelo e arquivo de parâmetro precisam saber o caminho correto para obter os binários com versão. Por exemplo, para implantar arquivos para um aplicativo Web, crie o arquivo de parâmetro a seguir com a variável $containerRoot. Você deve usar duas barras invertidas (`\\`) para o caminho porque o primeiro é um caractere de escape.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

Em seguida, use esse parâmetro em seu modelo:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

Você gerencia implantações com versão criando novas pastas e passando essa raiz durante a distribuição. O caminho flui para o modelo que implanta os recursos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre Deployment Manager. Vá para o próximo artigo para aprender a implantar com o Deployment Manager.

> [!div class="nextstepaction"]
> [Tutorial: usar o Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md)