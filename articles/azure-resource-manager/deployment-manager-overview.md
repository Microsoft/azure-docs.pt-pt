---
title: Implantação segura entre regiões – Azure Deployment Manager
description: Descreve como implementar um serviço ao longo de várias regiões com o Gestor de implementação do Azure. Ela mostra práticas recomendadas de implantação segura para verificar a estabilidade da sua implementação antes de implementar para todas as regiões.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 9b0e314b3911dcd3b96dc2b5b32081bd2e9c0c0c
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307039"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Habilitar práticas de implantação segura com o Azure Deployment Manager (visualização pública)

Para implementar o seu serviço entre várias regiões e certificar-se de que está a ser executado conforme esperado em cada região, pode utilizar o Gestor de implementação do Azure para coordenar uma implementação faseada do serviço. Da mesma forma que faria com qualquer implantação do Azure, você define os recursos para seu serviço em [modelos do Resource Manager](resource-group-authoring-templates.md). Depois de criar os modelos, utilize o Gestor de implementação para descrever a topologia para o seu serviço e como ele deve ser implementado.

Gestor de implementação é uma funcionalidade do Gestor de recursos. Se expandir as capacidades de durante a implementação. Utilize o Gestor de implementação quando tiver um serviço complexo que precisa para ser implementado em várias regiões. Ao fasear a implementação do serviço, poderá deparar-se com problemas potenciais antes de este ter sido implementado em todas as regiões. Se você não precisar das precauções adicionais de uma distribuição em etapas, use as [Opções de implantação](resource-group-template-deploy-portal.md) padrão para o Gerenciador de recursos. Gestor de implementação integram-se perfeitamente com todas as ferramentas de terceiros existentes que oferecem suporte a implementações do Resource Manager, como a integração contínua e ofertas de entrega contínua (CI/CD).

O Deployment Manager do Azure está em versão prévia. Ajude-nos a melhorar o recurso fornecendo [comentários](https://aka.ms/admfeedback).

Para utilizar o Gestor de implementação, terá de criar quatro arquivos:

* Modelo de topologia
* Modelo de implementação
* Ficheiro de parâmetros para a topologia
* Ficheiro de parâmetros para a implementação

Implementar o modelo de topologia antes de implementar o modelo de implementação.

Recursos adicionais:

- A [referência da API REST do Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Tutorial: usar o Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md).
- [Tutorial: usar a verificação de integridade no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Um exemplo de Deployment Manager do Azure](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identidade e acesso

Com o Deployment Manager, uma [identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/overview.md) executa as ações de implantação. Criar esta identidade antes de iniciar a implementação. Tem de ter acesso à subscrição que estiver a implementar o serviço para e permissão suficiente para concluir a implementação. Para obter informações sobre as ações concedidas por meio de funções, consulte [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md).

A identidade deve residir no mesmo local que a distribuição.

## <a name="topology-template"></a>Modelo de topologia

O modelo de topologia descreve os recursos do Azure que constituem o seu serviço e onde pretende implementá-las. A imagem seguinte mostra a topologia para um serviço de exemplo:

![Hierarquia da topologia de serviço nos serviços de unidades de serviço](./media/deployment-manager-overview/service-topology.png)

O modelo de topologia inclui os seguintes recursos:

* Origem de artefacto - onde estão armazenados os seus parâmetros e modelos do Resource Manager
* Topologia de serviço - aponta para a origem de artefacto
  * Serviços - Especifica a localização e o ID de subscrição do Azure
    * Unidades de serviço - Especifica o grupo de recursos, o modo de implementação e o caminho do ficheiro de modelo e o parâmetro

Para compreender o que acontece em cada nível, é útil ver os valores que fornecer.

![Valores para cada nível](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Origem de artefacto para modelos

O modelo de topologia, vai criar uma origem de artefacto que contém os ficheiros de parâmetros e modelos. A origem de artefacto é uma forma de retirar os ficheiros para a implementação. Verá outra origem de artefacto para binários neste artigo.

O exemplo seguinte mostra o formato geral da origem de artefacto.

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

O exemplo seguinte mostra o formato geral do recurso de topologia de serviço. Forneça o ID de recurso da origem de artefacto que contém os ficheiros de parâmetros e modelos. A topologia do serviço inclui todos os recursos de serviço. Para certificar-se de que a origem de artefato está disponível, a topologia de serviço depende da mesma.

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

O exemplo seguinte mostra o formato geral do recurso de serviços. Em cada serviço, forneça o ID de subscrição de localização e o Azure a utilizar para implementar o seu serviço. Para implementar em várias regiões, definir um serviço para cada região. O serviço depende da topologia de serviço.

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

O exemplo seguinte mostra o formato geral do recurso de unidades de serviço. Em cada unidade de serviço, você especifica o grupo de recursos, o [modo de implantação](deployment-modes.md) a ser usado para implantação e o caminho para o arquivo de modelo e parâmetro. Se especificar um caminho relativo para o modelo e parâmetros, o caminho completo é construído da pasta raiz na origem de artefactos. Pode especificar um caminho absoluto para o modelo e parâmetros, mas perde a capacidade para a versão facilmente seus lançamentos. A unidade de serviço depende do serviço.

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

Cada modelo deve incluir os recursos relacionados que pretende implementar num único passo. Por exemplo, uma unidade de serviço pode ter um modelo que implementa todos os recursos para front-end do seu serviço.

Para obter mais informações, consulte [referência de modelo de Perunits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Modelo de implementação

O modelo de implementação descreve os passos a efetuar quando implementar o seu serviço. Especifica a topologia de serviço para utilizar e definir a ordem para a implementação de unidades de serviço. Ele inclui uma origem de artefacto para armazenar binários para a implementação. No seu modelo de implementação, é possível definir a seguinte hierarquia:

* Origem de artefacto
* Passo
* Lançar
  * Grupos de passo
    * Operações de implementação

A imagem seguinte mostra a hierarquia do modelo de implementação:

![Hierarquia de implementação para os passos](./media/deployment-manager-overview/Rollout.png)

Cada implementação pode ter muitos grupos de passo. Cada grupo de passo tem uma operação de implementação que aponta para uma unidade de serviço a topologia de serviço.

### <a name="artifact-source-for-binaries"></a>Origem de artefacto para binários

O modelo de implementação, vai criar uma origem de artefacto para os binários que necessita para implementar o serviço. Essa origem do artefato é semelhante à [origem do artefato para modelos](#artifact-source-for-templates), exceto que ele contém os scripts, as páginas da Web, o código compilado ou outros arquivos necessários para seu serviço.

### <a name="steps"></a>Passos

Pode definir um passo para executar antes ou depois da operação de implementação. Atualmente, somente a etapa `wait` e a etapa ' healthCheck ' estão disponíveis.

O passo de espera interrompe a implementação antes de continuar. Ele permite-lhe verificar que seu serviço está em execução conforme esperado antes de implementar a seguinte unidade de serviço. O exemplo seguinte mostra o formato geral de um passo de espera.

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

A Propriedade Duration usa o [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). O exemplo anterior Especifica uma espera de um minuto.

Para obter mais informações sobre a etapa de verificação de integridade, consulte [introduzir implantação de integração de integridade para o Azure Deployment Manager](./deployment-manager-health-check.md) e [tutorial: usar a verificação de integridade no Deployment Manager do Azure](./deployment-manager-tutorial-health-check.md).

Para obter mais informações, consulte [referência de modelo de etapas](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Implementações

Para certificar-se de que a origem de artefato está disponível, a implementação depende da mesma. A implementação define os grupos de passos para cada unidade de serviço que é implementada. Pode definir as ações a efetuar antes ou após a implementação. Por exemplo, pode especificar que a implementação de espera após a unidade de serviço ter sido implementada. Pode definir a ordem dos grupos de passo.

O objeto de identidade especifica a [identidade gerenciada atribuída pelo usuário](#identity-and-access) que executa as ações de implantação.

O exemplo seguinte mostra o formato geral da implementação do.

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

## <a name="parameter-file"></a>Ficheiro de parâmetros

Criar dois ficheiros de parâmetros. Um ficheiro de parâmetros é utilizado na implementação da topologia de serviço e a outra é usada para a implementação de implementação. Existem alguns valores que precisa garantir que são os mesmos em ambos os ficheiros de parâmetro.

## <a name="containerroot-variable"></a>variável de containerRoot

Com implementações com a versão, o caminho para as alterações de artefactos com cada nova versão. Na primeira vez que você executar uma implantação, o caminho poderá ser `https://<base-uri-blob-container>/binaries/1.0.0.0`. A segunda vez que pode ser `https://<base-uri-blob-container>/binaries/1.0.0.1`. Deployment Manager simplifica obter o caminho raiz correto para a implantação atual usando a variável `$containerRoot`. Este valor é alterado com cada versão e não é conhecido antes da implantação.

Use a variável `$containerRoot` no arquivo de parâmetro para o modelo para implantar os recursos do Azure. No momento da implementação, esta variável é substituída com os valores reais da implementação.

Por exemplo, durante a implementação cria uma origem de artefacto para os artefactos de binários.

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

Observe as propriedades `artifactRoot` e `sasUri`. A raiz do artefato pode ser definida como um valor como `binaries/1.0.0.0`. O URI de SAS é o URI para o contentor de armazenamento com um token SAS para o acesso. Deployment Manager constrói automaticamente o valor da variável `$containerRoot`. Ele combina esses valores no formato `<container>/<artifactRoot>`.

O ficheiro de modelo e o parâmetro tem de saber o caminho correto para apresentar os binários com a versão. Por exemplo, para implementar os ficheiros para uma aplicação web, crie o seguinte ficheiro de parâmetros com a variável $containerRoot. Você deve usar duas barras invertidas (`\\`) para o caminho porque o primeiro é um caractere de escape.

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

Em seguida, utilize esse parâmetro no seu modelo:

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

Gerir implementações com a versão ao criar novas pastas e passando dessa raiz durante a implementação. O caminho flui através de para o modelo que implementa os recursos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o Gestor de implementação. Avance para o artigo seguinte para saber como implementar com o Gestor de implementação.

> [!div class="nextstepaction"]
> [Tutorial: usar o Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md)
>
> [Início rápido: Experimente o Azure Deployment Manager em apenas alguns minutos](https://github.com/Azure-Samples/adm-quickstart)
