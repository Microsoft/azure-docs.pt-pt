---
title: Visão geral - Automatizar a implementação para apps Azure Logic
description: Saiba mais sobre os modelos do Azure Resource Manager para automatizar a implementação de apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: a3d7386e976551d70fbbc08930b2ab5603aa5d50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269051"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Visão geral: Automatizar a implementação para apps Azure Logic utilizando modelos de Gestor de Recursos Azure

Quando estiver pronto para automatizar a criação e implementação da sua aplicação lógica, pode expandir a definição subjacente do fluxo de trabalho da sua aplicação lógica para um [modelo de Gestor de Recursos Azure.](../azure-resource-manager/management/overview.md) Este modelo define a infraestrutura, recursos, parâmetros e outras informações para o fornecimento e implementação da sua aplicação lógica. Ao definir parâmetros para valores que variam na implementação, também conhecido como *parametrizante*, é possível implementar repetidamente e de forma consistente aplicações lógicas com base em diferentes necessidades de implementação.

Por exemplo, se se implantar em ambientes para desenvolvimento, teste e produção, provavelmente utiliza diferentes cadeias de ligação para cada ambiente. Pode declarar parâmetros de modelo que aceitam diferentes cadeias de ligação e, em seguida, armazenar essas cordas num [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md)separados . Desta forma, pode alterar esses valores sem ter de atualizar e recolocar o modelo. Para cenários em que tenha valores de parâmetros sensíveis ou seguros, tais como palavras-passe e segredos, pode armazenar esses valores no [Cofre da Chave Azure](../azure-resource-manager/templates/key-vault-parameter.md) e ter os seus parâmetros a recuperar esses valores. No entanto, nestes cenários, seria reenviado para recuperar os valores atuais.

Esta visão geral descreve os atributos num modelo de Gestor de Recursos que inclui uma definição lógica de fluxo de trabalho de aplicações. Tanto o modelo como a definição de fluxo de trabalho utilizam a sintaxe JSON, mas existem algumas diferenças porque a definição de fluxo de trabalho também segue o [esquema de linguagem de definição de fluxo](../logic-apps/logic-apps-workflow-definition-language.md)de trabalho. Por exemplo, as expressões de modelo e as expressões de definição de fluxo de trabalho diferem na forma como [referenciam os parâmetros](#parameter-references) e os valores que podem aceitar.

> [!TIP]
> Para obter uma forma mais fácil de obter um modelo de aplicação lógica parametrizada válido que esteja maioritariamente pronto para ser implantado, use o Visual Studio (edição comunitária gratuita ou maior) e as Ferramentas de Aplicações Lógicas Azure para o Estúdio Visual. Em seguida, pode [criar a sua aplicação lógica no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou encontrar e descarregar uma [aplicação lógica existente do Azure para o Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)
>
> Ou, pode criar modelos de aplicativos lógicos utilizando [o Azure PowerShell com o módulo LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

A aplicação lógica de exemplo neste tópico usa um [gatilho do Office 365 Outlook](/connectors/office365/) que dispara quando um novo e-mail chega e uma [ação de Armazenamento Azure Blob](/connectors/azureblob/) que cria uma bolha para o corpo de e-mail e envia essa bolha para um recipiente de armazenamento Azure. Os exemplos também mostram como parametrizar valores que variam na implementação.

Para obter mais informações sobre os modelos do Gestor de Recursos, consulte estes tópicos:

* [Estrutura e sintaxe do gestor de recursos Azure](../azure-resource-manager/templates/template-syntax.md)
* [Melhores práticas do modelo do Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [Desenvolver modelo do Azure Resource Manager para consistência da cloud](../azure-resource-manager/templates/templates-cloud-consistency.md)

Para informações de recursos de modelo específicas de apps lógicas, contas de integração, artefactos de conta de integração e ambientes de serviços de integração, consulte [os tipos de recursos microsoft.logic](/azure/templates/microsoft.logic/allversions).

Para modelos de aplicativos de lógica de amostra, consulte estes exemplos:

* [Modelo completo](#full-example-template) que é usado para os exemplos deste tópico
* [Modelo de aplicativo de lógica quickstart de amostra](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) no GitHub

Para as aplicações lógicas REST API, comece com a visão geral da [AZure Logic Apps REST API](/rest/api/logic).

<a name="template-structure"></a>

## <a name="template-structure"></a>Estrutura do modelo

No nível superior, um modelo de Gestor de Recursos segue esta estrutura, que é totalmente descrita na estrutura do modelo do [Gestor de Recursos Azure e](../azure-resource-manager/templates/template-syntax.md) no tópico de sintaxe:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Para um modelo de aplicação lógica, você trabalha principalmente com estes objetos de modelo:

| Atributo | Descrição |
|-----------|-------------|
| `parameters` | Declara os parâmetros do modelo para aceitar os [valores](../azure-resource-manager/templates/template-syntax.md#parameters) a utilizar ao criar e personalizar recursos para implantação em Azure. Por exemplo, estes parâmetros aceitam os valores para o nome e localização da sua aplicação lógica, ligações e outros recursos necessários para a implementação. Pode armazenar estes valores de parâmetros num [ficheiro de parâmetros](#template-parameter-files), que é descrito mais tarde neste tópico. Para mais detalhes [gerais, consulte Parâmetros - Estrutura do modelo do Gestor de Recursos e sintaxe](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Define os [recursos](../azure-resource-manager/templates/template-syntax.md#resources) para criar ou atualizar e implementar para um grupo de recursos Azure, como a sua aplicação lógica, conexões, contas de armazenamento Azure, e assim por diante. Para mais detalhes gerais, consulte [Recursos - Estrutura do modelo do Gestor de Recursos e sintaxe.](../azure-resource-manager/templates/template-syntax.md#resources) |
||||

O seu modelo de aplicativo lógica utiliza este formato de nome de ficheiro:

**<*lógica-app-nome*>.jsem**

> [!IMPORTANT]
> A sintaxe do modelo é sensível ao caso, por isso certifique-se de que utiliza um invólucro consistente. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parâmetros do modelo

Um modelo de aplicação lógica tem `parameters` vários objetos que existem em diferentes níveis e desempenham diferentes funções. Por exemplo, no nível superior, pode declarar [parâmetros de modelo](../azure-resource-manager/templates/template-syntax.md#parameters) para que os valores aceitem e utilizem na implementação ao criar e implantar recursos em Azure, por exemplo:

* O seu aplicativo de lógica
* Ligações que a sua lógica utiliza para aceder a outros serviços e sistemas através de [conectores geridos](../connectors/apis-list.md)
* Outros recursos que a sua aplicação lógica precisa para implantação

  Por exemplo, se a sua aplicação lógica utilizar uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para cenários business-to-business (B2B), o objeto de topo do modelo `parameters` declara o parâmetro que aceita o ID de recurso para essa conta de integração.

Aqui está a estrutura geral e a sintaxe para uma definição de parâmetro, que é totalmente descrito por [Parâmetros - Estrutura do modelo de gestor de recursos e sintaxe](../azure-resource-manager/templates/template-syntax.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Este exemplo mostra apenas os parâmetros do modelo para os valores utilizados para criar e implantar estes recursos em Azure:

* Nome e localização para a sua aplicação lógica
* ID para usar para uma conta de integração que está ligada à app lógica

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Com exceção dos parâmetros que lidam com valores sensíveis ou que devem ser protegidos, como nomes de utilizador, palavras-passe e segredos, todos estes parâmetros incluem `defaultValue` atributos, embora em alguns casos, os valores padrão sejam valores vazios. Os valores de implantação a utilizar para estes parâmetros do modelo são fornecidos pelo [ficheiro de parâmetros](#template-parameter-files) da amostra descrito mais tarde neste tópico.

Para obter mais informações sobre a fixação dos parâmetros do modelo, consulte estes tópicos:

* [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Melhorar a segurança dos parâmetros do modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Passe valores de parâmetro garantidos com cofre de chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

Outros objetos de modelo frequentemente referenciam parâmetros do modelo para que possam usar os valores que passam através dos parâmetros do modelo, por exemplo:

* O [objeto de recursos do](#template-resources)seu modelo , descrito mais tarde neste tópico, define cada recurso em Azure que pretende criar e implementar, como a definição de recursos da sua [aplicação lógica.](#logic-app-resource-definition) Estes recursos usam frequentemente valores de parâmetros de modelo, como o nome da sua aplicação lógica e informações de localização e conexão.

* A um nível mais profundo na definição de recursos da sua aplicação lógica, o objeto de parâmetros da sua [definição de fluxo de trabalho](#workflow-definition-parameters) declara parâmetros para os valores a utilizar no tempo de funcionamento da sua aplicação lógica. Por exemplo, pode declarar parâmetros de definição de fluxo de trabalho para o nome de utilizador e senha que um gatilho HTTP utiliza para autenticação. Para especificar os valores para parâmetros de definição de fluxo de trabalho, utilize o `parameters` objeto que está *fora* da definição de fluxo de trabalho, mas ainda *dentro* da definição de recursos da sua aplicação lógica. Neste `parameters` objeto externo, pode referenciar os parâmetros do modelo previamente declarados, que podem aceitar valores na implementação de um ficheiro de parâmetros.

Ao referenciar parâmetros, as expressões e funções do modelo utilizam diferentes sintaxes e comportam-se de forma diferente das expressões e funções de definição de fluxo de trabalho. Para obter mais informações sobre estas diferenças, consulte [referências aos parâmetros](#parameter-references) mais tarde neste tópico.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Boas práticas - parâmetros de modelo

Aqui estão algumas boas práticas para definir parâmetros:

* Declare parâmetros apenas para valores que variem, com base nas suas necessidades de implantação. Não declare parâmetros para valores que permaneçam iguais em diferentes requisitos de implementação.

* Inclua o `defaultValue` atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores sensíveis ou devem ser seguros. Utilize sempre parâmetros seguros para nomes de utilizador, palavras-passe e segredos. Para ocultar ou proteger valores de parâmetros sensíveis, siga as orientações nestes tópicos:

  * [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Melhorar a segurança dos parâmetros do modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Passe valores de parâmetro garantidos com cofre de chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

* Para diferenciar os nomes dos parâmetros do modelo dos nomes dos parâmetros de definição de fluxo de trabalho, pode utilizar nomes de parâmetros descritivos, por exemplo: `TemplateFabrikamPassword`

Para obter mais boas práticas do modelo, consulte [as melhores práticas para os parâmetros do modelo.](../azure-resource-manager/templates/template-best-practices.md#parameters)

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Arquivo de parâmetros de modelo

Para fornecer os valores dos parâmetros do modelo, guarde esses valores num [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md). Desta forma, pode utilizar diferentes ficheiros de parâmetros com base nas suas necessidades de implantação. Aqui está o formato de nome de ficheiro para usar:

* Nome do ficheiro do modelo de aplicativo lógica: ** < *logic-app-name*>.json**
* Nome do ficheiro de parâmetros: ** < *logic-app-name*>.parameters.json**

Aqui está a estrutura dentro do arquivo de parâmetros, que inclui uma referência de cofre chave para [passar um valor de parâmetro seguro com Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Este ficheiro de parâmetros de exemplo especifica os valores dos parâmetros do modelo declarados anteriormente neste tópico:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Recursos do modelo

O seu modelo tem um `resources` objeto, que é um conjunto que contém definições para cada recurso para criar e implementar no Azure, como a definição de recursos da sua [aplicação lógica,](#logic-app-resource-definition) [definições de recursos de conexão,](#connection-resource-definitions)e quaisquer outros recursos que a sua aplicação lógica precise para a implementação.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Os modelos podem incluir definições de recursos para várias aplicações lógicas, por isso certifique-se de que todos os recursos da sua aplicação lógica especificam o mesmo grupo de recursos Azure. Quando implementa o modelo para um grupo de recursos Azure utilizando o Visual Studio, é-lhe solicitado qual a aplicação lógica que pretende abrir. Além disso, o seu projeto de grupo de recursos Azure pode conter mais do que um modelo, por isso certifique-se de que seleciona o ficheiro de parâmetros corretos quando solicitado.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Ver definições de recursos

Para rever as definições de recursos para todos os recursos de um grupo de recursos Azure, [baixe a sua aplicação lógica do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), que é a forma mais fácil de criar um modelo de aplicação lógica parametrizada válido que esteja maioritariamente pronto para ser implantado.

Para obter informações gerais sobre os recursos do modelo e seus atributos, consulte estes tópicos:

* [Recursos - Estrutura do modelo do Gestor de Recursos e sintaxe](../azure-resource-manager/templates/template-syntax.md#resources)
* [Melhores práticas para recursos de modelo](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definição de recurso de aplicativo lógico

A [definição](/azure/templates/microsoft.logic/workflows) de recursos de fluxo de trabalho da sua aplicação lógica num modelo começa com o `properties` objeto, que inclui esta informação:

* O estado da sua aplicação lógica na implementação
* O ID para qualquer conta de integração usada pela sua aplicação lógica
* A definição de fluxo de trabalho da sua aplicação lógica
* Um `parameters` objeto que define os valores a utilizar no tempo de execução
* Outras informações de recursos sobre a sua aplicação lógica, como nome, tipo, localização, e assim por diante

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Aqui estão os atributos específicos da definição de recursos de aplicação lógica:

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| `state` | Sim | String | O estado da sua aplicação lógica em implementação onde `Enabled` significa que a sua aplicação lógica está ao vivo e significa que a sua `Disabled` aplicação lógica está inativa. Por exemplo, se não estiver pronto para a sua aplicação lógica entrar em direto mas quiser implementar uma versão de projeto, pode usar a `Disabled` opção. |
| `integrationAccount` | Não | Objeto | Se a sua aplicação lógica utilizar uma conta de integração, que armazena artefactos para cenários business-to-business (B2B), este objeto inclui o `id` atributo, que especifica o ID para a conta de integração. |
| `definition` | Sim | Objeto | A definição subjacente do fluxo de trabalho da sua aplicação lógica, que é o mesmo objeto que aparece na visão de código e está totalmente descrito na referência de Schema para o tópico [de Linguagem de Definição de Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-definition-language.md) Nesta definição de fluxo de trabalho, o `parameters` objeto declara parâmetros para os valores a utilizar no tempo de execução da aplicação lógica. Para obter mais informações, consulte [a definição e os parâmetros do Fluxo de Trabalho.](#workflow-definition-parameters) <p><p>Para ver os atributos na definição de fluxo de trabalho da sua aplicação lógica, mude de "design view" para "code view" no portal Azure ou Visual Studio, ou utilizando uma ferramenta como [o Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | Não | Objeto | Os [valores do parâmetro de definição de fluxo de trabalho](#workflow-definition-parameters) para usar no tempo de execução da aplicação lógica. As definições de parâmetros para estes valores aparecem dentro [do objeto de parâmetros da definição de fluxo de trabalho.](#workflow-definition-parameters) Além disso, se a sua aplicação lógica utilizar [conectores geridos](../connectors/apis-list.md) para aceder a outros serviços e sistemas, este objeto inclui um `$connections` objeto que define os valores de ligação a utilizar no tempo de execução. |
| `accessControl` | Não | Objeto | Para especificar atributos de segurança para a sua aplicação lógica, como restringir o acesso IP a gatilhos de pedido ou executar entradas e saídas de histórico. Para obter mais informações, consulte [acesso seguro a aplicações lógicas.](../logic-apps/logic-apps-securing-a-logic-app.md) |
||||

Para obter mais informações sobre definições de recursos para estes objetos de Aplicações Lógicas, consulte [os tipos de recursos microsoft.logic](/azure/templates/microsoft.logic/allversions):

* [Definição de recursos de fluxo de trabalho](/azure/templates/microsoft.logic/workflows)
* [Definição de recursos de ambiente de serviço de integração](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [Ambiente de serviço de integração gerido definição de recursos API](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [Definição de recursos de conta de integração](/azure/templates/microsoft.logic/integrationaccounts)

* Artefactos de conta de integração:

  * [Definição de recursos do acordo](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [Definição de recursos de montagem](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [Definição de recursos de configuração de lote](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [Definição de recursos de certificado](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [Definição de recursos de mapa](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [Definição de recursos de parceiro](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [Definição de recursos de esquema](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [Definição de recursos de sessão](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definição e parâmetros do fluxo de trabalho

A definição de fluxo de trabalho da sua aplicação lógica aparece no `definition` objeto, que aparece no objeto dentro da `properties` definição de recursos da sua aplicação lógica. Este `definition` objeto é o mesmo objeto que aparece na vista de código e é totalmente descrito na referência de Schema para o tópico [de linguagem de definição de fluxo de trabalho.](../logic-apps/logic-apps-workflow-definition-language.md) A definição de fluxo de trabalho inclui um objeto de declaração interior `parameters` onde pode definir novos parâmetros ou editar os parâmetros existentes para os valores que são utilizados pela definição de fluxo de trabalho em tempo de execução. Em seguida, pode referenciar estes parâmetros dentro do gatilho ou ações no seu fluxo de trabalho. Por predefinição, este `parameters` objeto está vazio a menos que a sua aplicação lógica crie ligações a outros serviços e sistemas através de [conectores geridos.](../connectors/apis-list.md)

Para definir os valores para parâmetros de definição de fluxo de trabalho, use o `parameters` objeto que está *fora* da definição de fluxo de trabalho, mas ainda *dentro* da definição de recursos da sua aplicação lógica. Neste `parameters` objeto externo, pode então referenciar os parâmetros do modelo previamente declarados, que podem aceitar valores na implementação de um ficheiro de parâmetros.

> [!TIP]
>
> Como uma boa prática, não faça referência direta aos parâmetros do modelo, que são avaliados na implementação, a partir de dentro da definição de fluxo de trabalho. Em vez disso, declare um parâmetro de definição de fluxo de trabalho, que pode então definir no `parameters` objeto que está *fora* da definição de fluxo de trabalho, mas ainda *dentro* da definição de recursos da sua aplicação lógica. Para obter mais informações, consulte [referências aos parâmetros.](#parameter-references)

Esta sintaxe mostra onde pode declarar parâmetros nos níveis de definição do modelo e do fluxo de trabalho, juntamente com o local onde pode definir esses valores de parâmetros, referindo os parâmetros de definição do modelo e do fluxo de trabalho:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parâmetros de definição de fluxo de trabalho seguros

Para um parâmetro de definição de fluxo de trabalho que lida com informações sensíveis, palavras-passe, chaves de acesso ou segredos em tempo de execução, declare ou edite o parâmetro para utilizar o `securestring` tipo ou `secureobject` parâmetro. Pode fazer referência a este parâmetro em toda e dentro da definição de fluxo de trabalho. No nível superior do modelo, declare um parâmetro que tenha o mesmo tipo para lidar com esta informação na implementação.

Para definir o valor para o parâmetro de definição de fluxo de trabalho, use o `parameters` objeto que está *fora* da definição de fluxo de trabalho, mas ainda *dentro* da definição de recurso de aplicação lógica para referenciar o parâmetro do modelo. Finalmente, para passar o valor para o seu parâmetro de modelo na implementação, guarde esse valor no [Cofre da Chave Azure](../azure-resource-manager/templates/key-vault-parameter.md) e refira o cofre de chaves no ficheiro de [parâmetros](#template-parameter-files) que é usado pelo seu modelo na implementação.

Este modelo de exemplo mostra como pode completar estas tarefas definindo parâmetros seguros quando necessário para que possa armazenar os seus valores no Cofre da Chave Azure:

* Declare parâmetros seguros para os valores utilizados para autenticar o acesso.
* Utilize estes valores tanto nos níveis de definição do modelo como do fluxo de trabalho.
* Forneça estes valores utilizando um ficheiro de parâmetros.

**Modelo**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Arquivo de parâmetros**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Boas práticas - parâmetros de definição de fluxo de trabalho

Para garantir que o Designer de Aplicações Lógicas pode mostrar corretamente os parâmetros de definição do fluxo de trabalho, siga estas boas práticas:

* Inclua o `defaultValue` atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores sensíveis ou devem ser seguros.

* Utilize sempre parâmetros seguros para nomes de utilizador, palavras-passe e segredos. Para ocultar ou proteger valores de parâmetros sensíveis, siga as orientações nestes tópicos:

  * [Recomendações de segurança para parâmetros de ação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Recomendações de segurança para parâmetros nas definições de fluxo de trabalho](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Passe valores de parâmetro seguro com cofre de chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

Para obter mais informações sobre os parâmetros de definição do fluxo de trabalho, consulte [Parâmetros - Linguagem de Definição de Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-definition-language.md#parameters)

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definições de recursos de ligação

Quando a sua aplicação lógica cria e utiliza ligações a outros serviços e sistemas utilizando [conectores geridos,](../connectors/apis-list.md)o objeto do seu modelo contém as definições de `resources` recursos para essas ligações. Embora crie ligações a partir de uma aplicação lógica, as ligações são recursos Azure separados com as suas próprias definições de recursos. Para rever estas definições de recursos de conexão, [baixe a sua aplicação lógica do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), que é a forma mais fácil de criar um modelo de aplicação lógica parametrizada válido que esteja maioritariamente pronto para ser implantado.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

As definições de recursos de ligação referem os parâmetros de nível superior do modelo para os seus valores, o que significa que pode fornecer estes valores na implementação utilizando um ficheiro de parâmetros. Certifique-se de que as ligações utilizam o mesmo grupo de recursos Azure e a localização da sua aplicação lógica.

Aqui está uma definição de recurso de exemplo para uma ligação do Office 365 Outlook e os parâmetros do modelo correspondentes:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

A definição de recursos da sua aplicação lógica também funciona com definições de recursos de conexão destas formas:

* Dentro da definição de fluxo de trabalho, o `parameters` objeto declara um parâmetro para os `$connections` valores de ligação a utilizar no tempo de execução da aplicação lógica. Além disso, o gatilho ou ação que cria uma ligação utiliza os valores correspondentes que passam por este `$connections` parâmetro.

* *Fora da* definição de fluxo de trabalho, mas ainda *dentro* da definição de recursos da sua aplicação lógica, outro objeto define `parameters` os valores a utilizar no tempo de execução para o `$connections` parâmetro, referindo os parâmetros do modelo correspondentes. Estes valores utilizam expressões de modelo para recursos de referência que armazenam de forma segura os metadados para as ligações na sua aplicação lógica.

  Por exemplo, os metadados podem incluir cordas de ligação e fichas de acesso, que pode armazenar no [Cofre da Chave Azure.](../azure-resource-manager/templates/key-vault-parameter.md) Para passar esses valores para os parâmetros do seu modelo, refere o cofre de chaves no ficheiro de [parâmetros](#template-parameter-files) que é usado pelo seu modelo na implementação. Para obter mais informações sobre diferenças nos parâmetros de referência, consulte [Referências a parâmetros](#parameter-references) mais tarde neste tópico.

  Quando abre a definição de fluxo de trabalho da sua aplicação lógica na visualização de código através do portal Azure ou Do Estúdio Visual, o `$connections` objeto aparece fora da definição de fluxo de trabalho, mas ao mesmo nível. Esta encomenda na vista de código facilita a referência a estes parâmetros quando atualiza manualmente a definição de fluxo de trabalho:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* A definição de recursos da sua aplicação lógica tem um `dependsOn` objeto que especifica as dependências das ligações utilizadas pela sua aplicação lógica.

Cada ligação que cria tem um nome único em Azure. Quando cria múltiplas ligações ao mesmo serviço ou sistema, cada nome de ligação é anexado a um número, que aumenta com cada nova ligação criada, por exemplo, `office365` , e assim por `office365-1` diante.

Este exemplo mostra as interações entre a definição de recursos da sua aplicação lógica e uma definição de recurso de ligação para o Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parâmetros de ligação seguros

Para um parâmetro de ligação que lida com informações sensíveis, palavras-passe, teclas de acesso ou segredos, a definição de recursos da ligação inclui um `parameterValues` objeto que especifica estes valores no formato de par de valor-nome. Para ocultar esta informação, pode declarar ou editar os parâmetros do modelo para estes valores utilizando os `securestring` tipos ou `secureobject` parâmetros. Em seguida, pode armazenar essa informação no [Cofre da Chave Azure](../azure-resource-manager/templates/key-vault-parameter.md). Para passar esses valores para os parâmetros do seu modelo, refere o cofre de chaves no ficheiro de [parâmetros](#template-parameter-files) que é usado pelo seu modelo na implementação.

Aqui está um exemplo que fornece o nome da conta e a chave de acesso para uma ligação de armazenamento Azure Blob:

**Arquivo de parâmetros**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Modelo**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Autenticar ligações

Após a implementação, a sua aplicação lógica funciona de ponta a ponta com parâmetros válidos. No entanto, deve ainda autorizar quaisquer ligações OAuth para gerar fichas de acesso válidas para [autenticar as suas credenciais](../active-directory/develop/authentication-vs-authorization.md). Para mais informações, consulte [as ligações autoriteiras](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Algumas ligações suportam a utilização de um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md) Azure Ative (Azure AD) para autorizar ligações para uma aplicação lógica [registada em Azure AD](../active-directory/develop/quickstart-register-app.md). Por exemplo, esta definição de recurso de ligação Azure Data Lake mostra como referenciar os parâmetros do modelo que lidam com as informações do principal do serviço e como o modelo declara estes parâmetros:

**Definição de recursos de conexão**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Atributo | Descrição |
|-----------|-------------|
| `token:clientId` | A aplicação ou iD do cliente associado ao seu principal de serviço |
| `token:clientSecret` | O valor-chave associado ao seu principal serviço |
| `token:TenantId` | A iD do diretório para o seu inquilino Azure AD |
| `token:grantType` | O tipo de subvenção solicitado, que deve ser `client_credentials` . Para obter mais informações, consulte [a plataforma de identidade da Microsoft e o fluxo de credenciais de cliente OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definições de parâmetros de modelo**

O objeto de nível superior do modelo `parameters` declara estes parâmetros para a ligação de exemplo:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Para obter mais informações sobre o trabalho com os principais serviços, consulte estes tópicos:

* [Criar um principal de serviço utilizando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md)
* [Criar um principal de serviço Azure utilizando a Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
* [Criar um principal de serviço com um certificado utilizando a Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Referências aos parâmetros

Para parâmetros de modelo de referência, você pode usar expressões de modelo com [funções de modelo](../azure-resource-manager/templates/template-functions.md), que são avaliadas na implementação. As expressões do modelo usam suportes quadrados **([]**

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Para referenciar os parâmetros de definição de fluxo de [trabalho, utiliza-se expressões e funções linguísticas de definição de fluxo de trabalho,](../logic-apps/workflow-definition-language-functions-reference.md)que são avaliadas em tempo de execução. Pode notar que algumas funções de modelo e funções de definição de fluxo de trabalho têm o mesmo nome. As expressões de definição de fluxo de trabalho começam com o símbolo **@** "at" ():

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Pode passar os valores dos parâmetros do modelo para a definição de fluxo de trabalho para a sua aplicação lógica para utilizar no tempo de execução. No entanto, evite usar parâmetros de modelo, expressões e sintaxe na definição de fluxo de trabalho porque o Designer de Aplicações Lógicas não suporta elementos de modelo. Além disso, a sintaxe do modelo e as expressões podem complicar o seu código devido a diferenças quando as expressões são avaliadas.

Em vez disso, siga estes passos gerais para declarar e referenciar os parâmetros de definição de fluxo de trabalho para utilizar em tempo de execução, declarar e referenciar os modelos a utilizar na implementação, e especificar os valores a transmitir na implementação utilizando um ficheiro de parâmetros. Para obter mais detalhes, consulte a definição de [fluxo de trabalho e parâmetros](#workflow-definition-parameters) mais cedo neste tópico.

1. Crie o seu modelo e declare os parâmetros do modelo para que os valores aceitem e utilizem na implementação.

1. Na definição de fluxo de trabalho, declare os parâmetros para que os valores aceitem e utilizem em tempo de execução. Em seguida, pode referenciar estes valores ao longo e dentro da definição de fluxo de trabalho.

1. No `parameters` objeto que está *fora* da definição de fluxo de trabalho mas ainda *dentro* da definição de recursos da sua aplicação lógica, desenrique os valores para os parâmetros de definição do seu fluxo de trabalho, referindo os parâmetros do modelo correspondentes. Desta forma, pode passar os valores dos parâmetros do modelo nos parâmetros de definição do fluxo de trabalho.

1. No ficheiro de parâmetros, especifique os valores para o seu modelo utilizar na implementação.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Modelo de exemplo completo

Aqui está o modelo de amostra parametrizada que é usado pelos exemplos deste tópico:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar modelos de aplicação lógica](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
