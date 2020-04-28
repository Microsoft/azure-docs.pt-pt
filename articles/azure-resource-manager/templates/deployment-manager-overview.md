---
title: Implantação segura em todas as regiões - Gestor de Implantação Azure
description: Descreve como implementar um serviço em muitas regiões com o Gestor de Implantação azure. Mostra práticas de implantação seguras para verificar a estabilidade da sua implantação antes de partir para todas as regiões.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 424cd79a6c63200e1f101cf178b1fd2c9083161e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76152532"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Ativar práticas de implantação seguras com o Gestor de Implantação do Azure (pré-visualização pública)

Para implantar o seu serviço em muitas regiões e certificar-se de que está a funcionar como esperado em cada região, pode utilizar o Gestor de Implantação azure para coordenar um lançamento encenado do serviço. Tal como faria para qualquer implantação do Azure, define os recursos para o seu serviço nos [modelos de Gestor](template-syntax.md)de Recursos. Depois de criar os modelos, usa o Deployment Manager para descrever a topologia para o seu serviço e como deve ser lançado.

O Gestor de Implementação é uma característica do Gestor de Recursos. Expande as suas capacidades durante a implantação. Use o Gestor de Implantação quando tiver um serviço complexo que precisa de ser implantado em várias regiões. Ao fasear a implementação do serviço, poderá deparar-se com problemas potenciais antes de este ter sido implementado em todas as regiões. Se não precisar das precauções adicionais de um lançamento encenado, utilize as opções padrão de [implementação](deploy-portal.md) para O Gestor de Recursos. O Gestor de Implementação integra-se perfeitamente com todas as ferramentas existentes de terceiros que suportam implementações do Gestor de Recursos, tais como ofertas de integração contínua e entrega contínua (CI/CD).

O Gestor de Implantação Azure está em pré-visualização. Ajude-nos a melhorar a funcionalidade fornecendo [feedback](https://aka.ms/admfeedback).

Para utilizar o Gestor de Implementação, é necessário criar quatro ficheiros:

* Modelo de topologia
* Modelo de rollout
* Arquivo paraparâmetro para topologia
* Arquivo de parâmetro para lançamento

Implementa o modelo de topologia antes de implementar o modelo de lançamento.

Recursos adicionais:

- Referência a API do Gestor de [Destacamento azure](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Tutorial: Use O Gestor de Implantação Azure com modelos](./deployment-manager-tutorial.md)de Gestor de Recursos .
- [Tutorial: Utilize o exame de saúde no Gestor de Implantação azure](./deployment-manager-tutorial-health-check.md).
- Uma amostra do Gestor de [Implantação Azure.](https://github.com/Azure-Samples/adm-quickstart)

## <a name="identity-and-access"></a>Identidade e acesso

Com o Gestor de Implementação, uma [identidade gerida atribuída pelo utilizador](../../active-directory/managed-identities-azure-resources/overview.md) realiza as ações de implementação. Cria esta identidade antes de iniciar a sua implantação. Deve ter acesso à subscrição para a qual está a implementar o serviço e autorização suficiente para completar a implementação. Para obter informações sobre as ações concedidas através de funções, consulte [papéis incorporados para os recursos Azure.](../../role-based-access-control/built-in-roles.md)

A identidade deve residir no mesmo local que o lançamento.

## <a name="topology-template"></a>Modelo de topologia

O modelo de topologia descreve os recursos Azure que compõem o seu serviço e onde implantá-los. A imagem que se segue mostra a topologia para um serviço de exemplo:

![Hierarquia da topologia de serviço aos serviços até unidades de serviço](./media/deployment-manager-overview/service-topology.png)

O modelo de topologia inclui os seguintes recursos:

* Fonte de artefacto - onde os seus modelos e parâmetros do Gestor de Recursos são armazenados
* Topologia de serviço - aponta para fonte de artefactos
  * Serviços - especifica localização e ID de subscrição azure
    * Unidades de serviço - especifica grupo de recursos, modo de implantação e caminho para modelo e arquivo de parâmetros

Para entender o que acontece em cada nível, é útil ver que valores fornece.

![Valores para cada nível](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Fonte de artefacto para modelos

No seu modelo de topologia, cria-se uma fonte de artefacto que contém os modelos e os ficheiros de parâmetros. A fonte do artefacto é uma forma de retirar os ficheiros para serem implantados. Verá outra fonte de artefactos para binários mais tarde neste artigo.

O exemplo seguinte mostra o formato geral da fonte do artefacto.

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

Para mais informações, consulte a referência do [modelo de artefactoS](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologia de serviço

O exemplo seguinte mostra o formato geral do recurso de topoologia de serviço. Fornece a identificação de recursos da fonte de artefacto que contém os modelos e ficheiros de parâmetros. A topologia de serviço inclui todos os recursos de serviço. Para se certificar de que a fonte do artefacto está disponível, a topologia de serviço depende disso.

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

Para mais informações, consulte a referência do modelo de [serviçoTopologs](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Serviços

O exemplo que se segue mostra o formato geral do recurso de serviços. Em cada serviço, fornece a localização e o ID de subscrição azure para utilizar para implementar o seu serviço. Para implantar em várias regiões, define um serviço para cada região. O serviço depende da topologia de serviço.

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

Para mais informações, consulte a referência do modelo de [serviços](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Unidades de Serviço

O exemplo que se segue mostra o formato geral do recurso das unidades de serviço. Em cada unidade de serviço, especifica o grupo de recursos, o modo de [implementação](deployment-modes.md) a utilizar para a implementação e o caminho para o modelo e o ficheiro parâmetro. Se especificar um caminho relativo para o modelo e os parâmetros, o caminho completo é construído a partir da pasta raiz na fonte de artefactos. Pode especificar um caminho absoluto para o modelo e parâmetros, mas perde a capacidade de ver facilmente os seus lançamentos. A unidade de serviço depende do serviço.

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

Cada modelo deve incluir os recursos relacionados que pretende implementar num só passo. Por exemplo, uma unidade de serviço poderia ter um modelo que implanta todos os recursos para a frente do seu serviço.

Para mais informações, consulte a referência do [modelo serviceUnits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Modelo de rollout

O modelo de lançamento descreve os passos a tomar ao implementar o seu serviço. Especifica a topologia de serviço para utilizar e define a ordem para a implantação de unidades de serviço. Inclui uma fonte de artefacto para armazenar binários para a implantação. No seu modelo de lançamento, define a seguinte hierarquia:

* Fonte de artefacto
* Passo
* Rollout
  * Grupos de passos
    * Operações de implantação

A imagem seguinte mostra a hierarquia do modelo de lançamento:

![Hierarquia do lançamento aos degraus](./media/deployment-manager-overview/Rollout.png)

Cada lançamento pode ter muitos grupos de passos. Cada grupo de passos tem uma operação de implantação que aponta para uma unidade de serviço na topologia de serviço.

### <a name="artifact-source-for-binaries"></a>Fonte de artefactos para binários

No modelo de lançamento, cria-se uma fonte de artefacto para os binários que precisa de implementar para o serviço. Esta fonte de artefacto é semelhante à fonte de [artefactos para modelos,](#artifact-source-for-templates)exceto que contém os scripts, páginas web, código compilado ou outros ficheiros necessários pelo seu serviço.

### <a name="steps"></a>Passos

Pode definir um passo para executar antes ou depois da sua operação de implantação. Atualmente, apenas o `wait` passo e o passo 'healthCheck' estão disponíveis.

O passo de espera interrompe a implantação antes de continuar. Permite-lhe verificar se o seu serviço está a funcionar como esperado antes de implantar a próxima unidade de serviço. O exemplo que se segue mostra o formato geral de um passo de espera.

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

A duração da propriedade utiliza a [norma ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Durations) O exemplo anterior especifica uma espera de um minuto.

Para obter mais informações sobre a etapa de verificação de saúde, consulte Introduzir o lançamento da [integração em saúde para o Gestor](./deployment-manager-health-check.md) de Implantação e Tutorial do Azure: Utilize o exame de [saúde no Gestor](./deployment-manager-tutorial-health-check.md)de Destacamento saúde.

Para mais informações, consulte a referência do modelo de [passos](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Implementações

Para se certificar de que a fonte do artefacto está disponível, o lançamento depende dele. O lançamento define grupos de passos para cada unidade de serviço que é implantado. Pode definir ações a tomar antes ou depois da implantação. Por exemplo, pode especificar que a unidade de implementação aguarda após a implantação da unidade de serviço. Pode definir a ordem dos grupos de passos.

O objeto de identidade especifica a [identidade gerida atribuída](#identity-and-access) pelo utilizador que executa as ações de implementação.

O exemplo seguinte mostra o formato geral do lançamento.

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

Para mais informações, consulte a referência do modelo de [lançamento .](/azure/templates/Microsoft.DeploymentManager/rollouts)

## <a name="parameter-file"></a>Arquivo parâmetro

Crias dois ficheiros de parâmetros. Um ficheiro de parâmetro é utilizado ao implementar a topologia do serviço, e o outro é utilizado para a implementação do lançamento. Há alguns valores que precisa de ter a certeza de que são os mesmos em ambos os ficheiros de parâmetros.

## <a name="containerroot-variable"></a>contentorVariável raiz

Com implementações versístas, o caminho para os seus artefactos muda a cada nova versão. A primeira vez que fazes `https://<base-uri-blob-container>/binaries/1.0.0.0`uma implantação, o caminho pode ser. A segunda vez `https://<base-uri-blob-container>/binaries/1.0.0.1`pode ser. O Gestor de Implementação simplifica a obtenção `$containerRoot` do caminho raiz correto para a implementação atual utilizando a variável. Este valor muda a cada versão e não é conhecido antes da implantação.

Utilize `$containerRoot` a variável no ficheiro de parâmetropara modelo para implantar os recursos Azure. No momento de implantação, esta variável é substituída por valores reais do lançamento.

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

Reparem `artifactRoot` `sasUri` nas propriedades. A raiz do artefacto pode `binaries/1.0.0.0`ser definida para um valor como. O SAS URI é o URI do seu recipiente de armazenamento com um token SAS para acesso. O Gestor de Implementação constrói `$containerRoot` automaticamente o valor da variável. Combina esses valores no `<container>/<artifactRoot>`formato.

O seu modelo e ficheiro de parâmetros precisam de saber o caminho correto para obter os binários versídados. Por exemplo, para implementar ficheiros para uma aplicação web, crie o seguinte ficheiro de parâmetro com a variável $containerRoot. Deve usar duas pestanas`\\`para o caminho porque a primeira é um personagem de fuga.

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

Gere as implementações versonizadas criando novas pastas e passando essa raiz durante o lançamento. O caminho flui através do modelo que implanta os recursos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o Gestor de Destacamento. Dirija-se ao próximo artigo para aprender a implantar com o Gestor de Implementação.

> [!div class="nextstepaction"]
> [Tutorial: Use O Gestor de Implementação azure com modelos de Gestor de Recursos](./deployment-manager-tutorial.md)
>
> [Quickstart: Experimente o Gestor de Implantação azure em apenas alguns minutos](https://github.com/Azure-Samples/adm-quickstart)
