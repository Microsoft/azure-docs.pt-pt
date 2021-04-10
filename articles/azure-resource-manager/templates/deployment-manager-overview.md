---
title: Implantação segura em regiões - Azure Deployment Manager
description: Saiba como implementar um serviço em muitas regiões com o Azure Deployment Manager e sobre práticas de implementação seguras.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 63553b0bbca031faa44e0d88480fcc08950a3e2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627504"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Permitir práticas de implementação seguras com O Gestor de Implementação Azure (visualização pública)

Para implementar o seu serviço em muitas regiões e certificar-se de que está a funcionar como esperado em cada região, pode utilizar o Azure Deployment Manager para coordenar um lançamento encenado do serviço. Tal como faria para qualquer implantação do Azure, define os recursos para o seu serviço nos [modelos do Gestor de Recursos.](template-syntax.md) Depois de criar os modelos, utilize o Gestor de Implementação para descrever a topologia do seu serviço e como deve ser lançado.

O Gestor de Implementação é uma característica do Gestor de Recursos. Expande as suas capacidades durante a implantação. Use o Gestor de Implementação quando tiver um serviço complexo que precisa de ser implantado em várias regiões. Ao fasear a implementação do serviço, poderá deparar-se com problemas potenciais antes de este ter sido implementado em todas as regiões. Se não precisar das precauções adicionais de um lançamento encenado, utilize as opções padrão de [implementação](deploy-portal.md) para o Gestor de Recursos. O Gestor de Implementação integra-se perfeitamente com todas as ferramentas de terceiros existentes que suportam implementações do Gestor de Recursos, tais como ofertas de integração contínua e entrega contínua (CI/CD).

O Gestor de Implementação Azure está em pré-visualização. Ajude-nos a melhorar a funcionalidade fornecendo [feedback.](https://aka.ms/admfeedback)

Para utilizar o Gestor de Implementação, é necessário criar quatro ficheiros:

* Modelo de topologia.
* Modelo de lançamento.
* Arquivo de parâmetros para topologia.
* Ficheiro de parâmetro para o lançamento.

Você implementa o modelo de topologia antes de implementar o modelo de lançamento.

Recursos adicionais:

* [Referência Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Tutorial: Use o Gestor de Implementação Azure com modelos de Gestor de Recursos](./deployment-manager-tutorial.md).
* [Tutorial: Use o controlo de saúde no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
* [Amostra do Gestor de Implantação Azure](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identidade e acesso

Com o Gestor de Implementação, uma [identidade gerida atribuída pelo utilizador](../../active-directory/managed-identities-azure-resources/overview.md) realiza as ações de implementação. Cria esta identidade antes de iniciar a sua implantação. Deve ter acesso à subscrição a que está a implantar o serviço, e permissão suficiente para completar a implementação. Para obter informações sobre as ações concedidas através de funções, consulte [as funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md)

A identidade deve residir no mesmo local que o lançamento.

## <a name="topology-template"></a>Modelo de topologia

O modelo de topologia descreve os recursos Azure que compõem o seu serviço e onde implementá-los. A imagem a seguir mostra a topologia para um serviço de exemplo:

![Hierarquia da topologia de serviços aos serviços às unidades de serviço](./media/deployment-manager-overview/service-topology.png)

O modelo de topologia inclui os seguintes recursos:

* Fonte de artefacto - onde os modelos e parâmetros do seu Gestor de Recursos estão armazenados.
* Topologia de serviço - aponta para fonte de artefacto.
  * Serviços - especifica a localização e iD de assinatura Azure.
    * Unidades de serviço - especifica grupo de recursos, modo de implementação e caminho para os ficheiros de modelo e parâmetro.

Para entender o que acontece em cada nível, é útil ver quais os valores que fornece.

![Valores para cada nível](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Fonte de artefacto para modelos

No seu modelo de topologia, cria uma fonte de artefacto que contém os modelos e os ficheiros de parâmetros. A fonte de artefactos é uma forma de puxar os ficheiros para a implantação. Verá outra fonte de artefactos para binários mais tarde neste artigo.

O exemplo a seguir mostra o formato geral da fonte do artefacto.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
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

Para obter mais informações, consulte [a referência do modelo artifactSources](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologia de serviço

O exemplo a seguir mostra o formato geral do recurso de topologia de serviço. Fornece o ID de recurso da fonte de artefacto que contém os modelos e os ficheiros de parâmetros. A topologia do serviço inclui todos os recursos de serviço. Certifique-se de que a fonte de artefacto está disponível porque a topologia do serviço depende disso.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Para mais informações, consulte [a referência do modelo de serviceTopologies](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Serviços

O exemplo a seguir mostra o formato geral do recurso de serviços. Em cada serviço, fornece a localização e iD de assinatura Azure para usar para implantar o seu serviço. Para implantar em várias regiões, define um serviço para cada região. O serviço depende da topologia do serviço.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
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

Para mais informações, consulte a [referência do modelo de serviços.](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services)

### <a name="service-units"></a>Unidades de Serviço

O exemplo a seguir mostra o formato geral do recurso das unidades de serviço. Em cada unidade de serviço, especifique o grupo de recursos, o [modo de implantação](deployment-modes.md) a utilizar para implantação e o caminho para o modelo e o ficheiro de parâmetros. Se especificar um caminho relativo para o modelo e parâmetros, o caminho completo é construído a partir da pasta raiz na fonte dos artefactos. Pode especificar um caminho absoluto para o modelo e parâmetros, mas perde a capacidade de facilmente versar as suas versões. A unidade de serviço depende do serviço.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
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

Cada modelo deve incluir os recursos relacionados que pretende implementar num só passo. Por exemplo, uma unidade de serviço pode ter um modelo que implementa todos os recursos para a frente do seu serviço.

Para obter mais informações, consulte [a referência do modelo de unidades de serviço](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Modelo de lançamento

O modelo de lançamento descreve os passos a tomar ao implementar o seu serviço. Especifica a topologia de serviço para utilizar e definir a ordem para implantação de unidades de serviço. Inclui uma fonte de artefacto para armazenar binários para a implantação. No seu modelo de lançamento, define a seguinte hierarquia:

* Fonte de artefactos.
* Um passo.
* O lançamento.
  * Grupos de passos.
    * Operações de implantação.

A imagem a seguir mostra a hierarquia do modelo de lançamento:

![Hierarquia do rollout aos degraus](./media/deployment-manager-overview/Rollout.png)

Cada lançamento pode ter muitos grupos de passos. Cada grupo de passos tem uma operação de implantação que aponta para uma unidade de serviço na topologia de serviço.

### <a name="artifact-source-for-binaries"></a>Fonte de artefacto para binários

No modelo de lançamento, cria-se uma fonte de artefacto para os binários que precisa de implementar para o serviço. Esta fonte de artefacto é semelhante à fonte de [artefactos para modelos](#artifact-source-for-templates), exceto que contém os scripts, páginas web, código compilado ou outros ficheiros necessários pelo seu serviço.

### <a name="steps"></a>Passos

Pode definir um passo para realizar antes ou depois da sua operação de implantação. Atualmente, apenas o `wait` passo e o passo `healthCheck` estão disponíveis.

O `wait` passo faz uma pausa na colocação antes de continuar. Permite-lhe verificar se o seu serviço está a funcionar como esperado antes de implantar a próxima unidade de serviço. O exemplo a seguir mostra o formato geral de um `wait` passo.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
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

A propriedade de duração utiliza [a norma ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Durations) O exemplo anterior especifica uma espera de um minuto.

Para obter mais informações sobre os controlos de saúde, consulte [Introduzir o lançamento da integração de saúde no Azure Deployment Manager](./deployment-manager-health-check.md) e [tutorial: Use o exame de saúde no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Para obter mais informações, consulte a [referência do modelo de passos.](/azure/templates/Microsoft.DeploymentManager/steps)

### <a name="rollouts"></a>Implementações

Certifique-se de que a fonte de artefacto está disponível porque o lançamento depende disso. O lançamento define grupos de passos para cada unidade de serviço que é implantada. Pode definir ações a tomar antes ou depois da implantação. Por exemplo, pode especificar a implantação para aguardar a implantação da unidade de serviço. Pode definir a ordem dos grupos de passos.

O objeto de identidade especifica a [identidade gerida atribuída pelo utilizador](#identity-and-access) que executa as ações de implantação.

O exemplo a seguir mostra o formato geral do lançamento.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
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

Para obter mais informações, consulte [a referência do modelo de lançamentos](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Arquivo de parâmetros

Cria-se dois ficheiros de parâmetros. Um ficheiro de parâmetro é usado ao implementar a topologia de serviço, e o outro é usado para a implementação do lançamento. Há alguns valores que precisa de ter certeza de que são os mesmos em ambos os ficheiros de parâmetros.

## <a name="containerroot-variable"></a>container Variável de croá

Com as implementações em versão, o caminho para os seus artefactos muda a cada nova versão. A primeira vez que fizer uma implantação, o caminho pode `https://<base-uri-blob-container>/binaries/1.0.0.0` ser. A segunda vez pode `https://<base-uri-blob-container>/binaries/1.0.0.1` ser. O Gestor de Implementação simplifica a obtenção do caminho de raiz correto para a implementação atual utilizando a `$containerRoot` variável. Este valor muda a cada versão e não é conhecido antes da implementação.

Utilize a `$containerRoot` variável no ficheiro de parâmetros para o modelo que implementa os recursos Azure. No momento da implementação, esta variável é substituída pelos valores reais do lançamento.

Por exemplo, durante o lançamento cria-se uma fonte de artefactos para os artefactos binários.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
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

Reparem nas `artifactRoot` `sasUri` propriedades. A raiz do artefacto pode ser definida para um valor como `binaries/1.0.0.0` . O SAS URI é o URI do seu recipiente de armazenamento com um token SAS para acesso. O Gestor de Implementação constrói automaticamente o valor da `$containerRoot` variável. Combina estes valores no `<container>/<artifactRoot>` formato.

O seu modelo e ficheiro de parâmetros precisam de saber o caminho correto para obter os binários versados. Por exemplo, para implementar ficheiros para uma aplicação web, crie o seguinte ficheiro de parâmetro com a `$containerRoot` variável. Deve usar duas costas `\\` para o caminho porque a primeira é um personagem de fuga.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Em seguida, use esse parâmetro no seu modelo:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
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

Gere as implementações versadas criando novas pastas e passando por esse caminho de raiz durante o lançamento. O caminho flui até ao modelo que implementa os recursos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o Gestor de Implementação. Dirija-se ao próximo artigo para aprender a implementar com o Gestor de Implementação.

> [!div class="nextstepaction"]
> [Tutorial: Use gestor de implementação de Azure com modelos de gestor de recursos](./deployment-manager-tutorial.md)
>
> [Quickstart: Experimente o Gestor de Implementação Azure em apenas alguns minutos](https://github.com/Azure-Samples/adm-quickstart)
