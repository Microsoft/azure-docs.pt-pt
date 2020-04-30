---
title: Visão geral - Implementação de automatizar para Aplicações Lógicas Azure
description: Saiba sobre os modelos do Gestor de Recursos Azure para automatizar a implementação de aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 7a99038f41043b899886c7161f9b12c77c807c4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641820"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Visão geral: Implementação automática para Aplicações Lógicas Azure utilizando modelos de Gestor de Recursos Azure

Quando estiver pronto para automatizar a criação e implementação da sua aplicação lógica, pode expandir a definição de fluxo de trabalho subjacente da sua aplicação lógica para um [modelo de Gestor de Recursos Azure](../azure-resource-manager/management/overview.md). Este modelo define a infraestrutura, recursos, parâmetros e outras informações para o fornecimento e implementação da sua aplicação lógica. Ao definir parâmetros para valores que variam na implementação, também conhecido como *parametrização,* pode implementar repetida e consistentemente aplicações lógicas baseadas em diferentes necessidades de implementação.

Por exemplo, se se implantar em ambientes de desenvolvimento, teste e produção, provavelmente utiliza cordas de ligação diferentes para cada ambiente. Pode declarar parâmetros de modelo que aceitam diferentes cordas de ligação e, em seguida, armazenar essas cordas num ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md)separados . Dessa forma, pode alterar esses valores sem ter de atualizar e recolocar o modelo. Para cenários em que tem valores de parâmetros sensíveis ou devem ser protegidos, como palavras-passe e segredos, pode armazenar esses valores no [Cofre de Chaves Azure](../azure-resource-manager/templates/key-vault-parameter.md) e ter os seus parâmetros de ficheiro para recuperar esses valores. No entanto, nestes cenários, redistribuir-se-ia para recuperar os valores atuais.

Esta visão geral descreve os atributos num modelo de Gestor de Recursos que inclui uma definição lógica de fluxo de fluxo de aplicações. Tanto o modelo como a definição de fluxo de trabalho utilizam a sintaxe JSON, mas existem algumas diferenças porque a definição de fluxo de trabalho também segue o esquema de linguagem de definição de fluxo de [trabalho.](../logic-apps/logic-apps-workflow-definition-language.md) Por exemplo, as expressões de modelo e as expressões de definição de fluxo de trabalho diferem na forma como [referenciam parâmetros](#parameter-references) e nos valores que podem aceitar.

> [!TIP]
> Para obter um modelo de aplicação lógica paramétrica válido que esteja maioritariamente pronto para ser implantado, use o Visual Studio (edição comunitária gratuita ou maior) e as Ferramentas de Aplicações lógicas Azure para o Estúdio Visual. Em seguida, pode [criar a sua aplicação lógica no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou encontrar e descarregar uma [aplicação lógica existente do Azure para o Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)
>
> Ou, pode criar modelos de aplicações lógicas utilizando [o Azure PowerShell com o módulo LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

A aplicação lógica de exemplo neste tópico usa um [gatilho do Office 365 Outlook](/connectors/office365/) que dispara quando um novo e-mail chega e uma ação de Armazenamento [Azure Blob](/connectors/azureblob/) que cria uma bolha para o corpo de e-mail e carrega essa bolha para um recipiente de armazenamento Azure. Os exemplos também mostram como parametrizar valores que variam na implantação.

Para obter mais informações sobre os modelos do Gestor de Recursos, consulte estes tópicos:

* [Estrutura do modelo do Gestor de Recursos Azure e sintaxe](../azure-resource-manager/templates/template-syntax.md)
* [Melhores práticas do modelo do Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [Desenvolver modelo do Azure Resource Manager para consistência da cloud](../azure-resource-manager/templates/templates-cloud-consistency.md)

Para modelos de aplicativos de lógica de amostra, consulte estes exemplos:

* [Modelo completo](#full-example-template) que é usado para os exemplos deste tópico
* [Modelo de aplicativo de lógica quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) de amostra no GitHub

Para informações de recursos de modelo específicas para aplicações lógicas, contas de integração e artefactos de conta de integração, consulte os tipos de [recursos Microsoft.Logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Estrutura do modelo

Ao nível superior, um modelo de Gestor de Recursos segue esta estrutura, que é totalmente descrita na estrutura do modelo do Gestor de Recursos Azure e no tópico de [sintaxe:](../azure-resource-manager/templates/template-syntax.md)

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
| `parameters` | Declara os [parâmetros](../azure-resource-manager/templates/template-syntax.md#parameters) do modelo para aceitar os valores a utilizar ao criar e personalizar recursos para implantação em Azure. Por exemplo, estes parâmetros aceitam os valores para o nome e localização da sua aplicação lógica, conexões e outros recursos necessários para a implementação. Pode armazenar estes valores de parâmetronum ficheiro de [parâmetros,](#template-parameter-files)que é descrito mais tarde neste tópico. Para mais detalhes gerais, consulte Parâmetros - Estrutura de modelo de gestor de [recursos e sintaxe.](../azure-resource-manager/templates/template-syntax.md#parameters) |
| `resources` | Define os [recursos](../azure-resource-manager/templates/template-syntax.md#resources) para criar ou atualizar e implementar para um grupo de recursos Azure, como a sua aplicação lógica, conexões, contas de armazenamento Azure, e assim por diante. Para mais detalhes gerais, consulte Recursos - Estrutura de [modelo de Gestor de Recursos e sintaxe.](../azure-resource-manager/templates/template-syntax.md#resources) |
||||

O modelo de aplicação lógica utiliza este formato de nome de ficheiro:

**<*lógica-nome de aplicação*>.json**

> [!IMPORTANT]
> A sintaxe do modelo é sensível ao caso, por isso certifique-se de que utiliza invólucroconsistente. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parâmetros do modelo

Um modelo de `parameters` aplicação lógica tem vários objetos que existem em diferentes níveis e desempenham diferentes funções. Por exemplo, ao nível superior, pode declarar [parâmetros](../azure-resource-manager/templates/template-syntax.md#parameters) de modelo para os valores aceitarem e utilizarem na implementação ao criar e implantar recursos em Azure, por exemplo:

* A sua aplicação lógica
* Conexões que a sua lógica utiliza para aceder a outros serviços e sistemas através [de conectores geridos](../connectors/apis-list.md)
* Outros recursos que a sua aplicação lógica precisa para a implantação

  Por exemplo, se a sua aplicação lógica usa uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para cenários `parameters` negócio-a-negócio (B2B), o objeto de nível superior do modelo declara o parâmetro que aceita o ID de recursos para essa conta de integração.

Aqui está a estrutura geral e a sintaxe para uma definição de parâmetro, que é totalmente descrita por Parâmetros - Estrutura de [modelo de Gestor de Recursos e sintaxe:](../azure-resource-manager/templates/template-syntax.md#parameters)

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
* ID para usar para uma conta de integração que está ligada à aplicação lógica

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

Com exceção dos parâmetros que manuseam valores sensíveis ou devem ser protegidos, `defaultValue` tais como nomes de utilizador, palavras-passe e segredos, todos estes parâmetros incluem atributos, embora em alguns casos, os valores predefinidos sejam valores vazios. Os valores de implantação a utilizar para estes parâmetros de modelo são fornecidos pelo ficheiro de [parâmetros](#template-parameter-files) da amostra descrito mais tarde neste tópico.

Para obter mais informações sobre a fixação de parâmetros de modelo, consulte estes tópicos:

* [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Melhorar a segurança dos parâmetros do modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Passe valores de parâmetro seguro com cofre de chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

Outros objetos de modelo muitas vezes referenciam parâmetros de modelo para que possam usar os valores que passam através de parâmetros de modelo, por exemplo:

* O objeto de recursos do seu [modelo,](#template-resources)descrito mais tarde neste tópico, define cada recurso em Azure que pretende criar e implementar, como a definição de recursos da sua [aplicação lógica.](#logic-app-resource-definition) Estes recursos usam frequentemente valores de parâmetros de modelo, como o nome e a localização da sua aplicação lógica e informações de conexão.

* A um nível mais profundo na definição de recursos da sua aplicação lógica, o [objeto de parâmetros da definição de fluxo](#workflow-definition-parameters) de trabalho declara parâmetros para os valores a utilizar no tempo de execução da sua aplicação lógica. Por exemplo, pode declarar parâmetros de definição de fluxo de trabalho para o nome de utilizador e palavra-passe que um gatilho HTTP utiliza para autenticação. Para especificar os valores dos parâmetros `parameters` de definição de fluxo de trabalho, utilize o objeto que está *fora* da definição de fluxo de trabalho, mas ainda *dentro* da definição de recursos da sua aplicação lógica. Neste objeto `parameters` exterior, pode fazer referência aos parâmetros de modelo previamente declarados, que podem aceitar valores na implementação a partir de um ficheiro de parâmetros.

Quando se referem parâmetros, expressões e funções de modelo usam diferentes sintaxe e comportam-se de forma diferente das expressões e funções da definição de fluxo de trabalho. Para obter mais informações sobre estas diferenças, consulte [referências aos parâmetros](#parameter-references) mais tarde neste tópico.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Boas práticas - parâmetros de modelo

Aqui estão algumas boas práticas para definir parâmetros:

* Declare parâmetros apenas para valores que variam, com base nas suas necessidades de implantação. Não declare parâmetros para valores que permaneçam os mesmos em diferentes requisitos de implementação.

* Incluir `defaultValue` o atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores sensíveis ou devem ser protegidos. Utilize sempre parâmetros seguros para nomes de utilizadores, senhas e segredos. Para ocultar ou proteger valores sensíveis dos parâmetros, siga a orientação nestes tópicos:

  * [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Melhorar a segurança dos parâmetros do modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Passe valores de parâmetro seguro com cofre de chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

* Para diferenciar os nomes dos parâmetros de parâmetros de modelo dos nomes dos parâmetros de definição de fluxo de trabalho, pode utilizar nomes de parâmetros descritivos do modelo, por exemplo:`TemplateFabrikamPassword`

Para mais práticas de modelo, consulte [as melhores práticas para parâmetros](../azure-resource-manager/templates/template-best-practices.md#parameters)de modelo .

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Arquivo de parâmetros de modelo

Para fornecer os valores para parâmetros de modelo, guarde esses valores num ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md). Dessa forma, pode utilizar diferentes ficheiros de parâmetros com base nas suas necessidades de implantação. Aqui está o formato de nome de ficheiro a utilizar:

* Nome de ficheiro de modelo de aplicativo lógico: ** < *nome de aplicação lógica*>.json**
* Nome de ficheiro de parâmetros: ** < *lógica-nome de aplicação*>.parameters.json**

Aqui está a estrutura dentro do ficheiro de parâmetros, que inclui uma referência chave do cofre para [passar um valor de parâmetro seguro com cofre de chave Azure:](../azure-resource-manager/templates/key-vault-parameter.md)

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

O seu `resources` modelo tem um objeto, que é uma matriz que contém definições para cada recurso criar e implementar no Azure, como a definição de recursos da sua [aplicação lógica,](#logic-app-resource-definition)quaisquer definições de recursos de [ligação,](#connection-resource-definitions)e quaisquer outros recursos que a sua aplicação lógica precise para a implementação.

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
> Os modelos podem incluir definições de recursos para aplicações lógicas múltiplas, por isso certifique-se de que todos os recursos da sua lógica especificam o mesmo grupo de recursos Azure. Quando implementa o modelo para um grupo de recursos Azure utilizando o Visual Studio, é solicitado para que aplicação lógica pretende abrir. Além disso, o seu projeto de grupo de recursos Azure pode conter mais de um modelo, por isso certifique-se de que seleciona o ficheiro de parâmetros correto quando solicitado.

Para obter informações gerais sobre os recursos do modelo e os seus atributos, consulte estes tópicos:

* [Recursos - Estrutura de modelo de gestor de recursos e sintaxe](../azure-resource-manager/templates/template-syntax.md#resources)
* [Boas práticas para recursos de modelo](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definição de recursos de aplicações lógicas

A definição de recursos da `properties` sua aplicação lógica começa com o objeto, que inclui esta informação:

* O estado da sua aplicação lógica em implementação
* O ID para qualquer conta de integração usada pela sua aplicação lógica
* Definição de fluxo de trabalho da sua aplicação lógica
* Um `parameters` objeto que define os valores a usar no tempo de execução
* Outras informações sobre o seu aplicativo lógico, como nome, tipo, localização, e assim por diante

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

Aqui estão os atributos que são específicos da definição de recursos de aplicações lógicas:

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| `state` | Sim | String | O estado da sua aplicação lógica na implementação, onde `Enabled` significa que a sua aplicação lógica está ao vivo e `Disabled` significa que a sua aplicação lógica está inativa. Por exemplo, se não estiver pronto para a sua aplicação lógica entrar em `Disabled` direto, mas quiser implementar uma versão de rascunho, pode utilizar a opção. |
| `integrationAccount` | Não | Objeto | Se a sua aplicação lógica utilizar uma conta de integração, que armazena artefactos `id` para cenários de negócios a empresas (B2B), este objeto inclui o atributo, que especifica o ID para a conta de integração. |
| `definition` | Sim | Objeto | A definição subjacente à definição de fluxo de trabalho da sua aplicação lógica, que é o mesmo objeto que aparece na vista de código e é totalmente descrita na [referência de Schema para](../logic-apps/logic-apps-workflow-definition-language.md) o tópico de Linguagem de Definição de Fluxo de Trabalho. Nesta definição de `parameters` fluxo de trabalho, o objeto declara parâmetros para os valores a utilizar no tempo de execução da aplicação lógica. Para obter mais informações, consulte a [definição de Fluxo de Trabalho e os parâmetros](#workflow-definition-parameters). <p><p>Para ver os atributos na definição de fluxo de trabalho da sua aplicação lógica, mude de "vista de design" para "visão de código" no portal Azure ou Visual Studio, ou utilizando uma ferramenta como [o Azure Resource Explorer.](https://resources.azure.com) |
| `parameters` | Não | Objeto | Os valores de [parâmetro de definição](#workflow-definition-parameters) de fluxo de trabalho para usar no tempo de execução da aplicação lógica. As definições de parâmetros para estes valores aparecem dentro [do objeto de parâmetros da definição de fluxo de trabalho](#workflow-definition-parameters). Além disso, se a sua aplicação lógica utilizar [conectores geridos](../connectors/apis-list.md) para aceder a outros serviços e sistemas, este objeto inclui um `$connections` objeto que define os valores de ligação a utilizar no tempo de execução. |
| `accessControl` | Não | Objeto | Para especificar atributos de segurança para a sua aplicação lógica, como restringir o acesso IP a gatilhos de pedido ou executar inputs e saídas de histórico. Para mais informações, consulte [O acesso seguro a aplicações lógicas.](../logic-apps/logic-apps-securing-a-logic-app.md) |
||||

Para informações de recursos de modelo específicas para aplicações lógicas, contas de integração e artefactos de conta de integração, consulte os tipos de [recursos Microsoft.Logic](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definição e parâmetros de fluxo de trabalho

A definição de fluxo de trabalho `definition` da sua aplicação `properties` lógica aparece no objeto, que aparece no objeto dentro da definição de recursos da sua aplicação lógica. Este `definition` objeto é o mesmo objeto que aparece na vista de código e é totalmente descrito na [referência de Schema para](../logic-apps/logic-apps-workflow-definition-language.md) o tópico de linguagem de definição de fluxo de trabalho. A definição de `parameters` fluxo de trabalho inclui um objeto de declaração interior onde pode definir parâmetros novos ou editar os parâmetros existentes para os valores que são utilizados pela sua definição de fluxo de trabalho no prazo de funcionamento. Pode então fazer referência a estes parâmetros dentro do gatilho ou ações no seu fluxo de trabalho. Por padrão, `parameters` este objeto está vazio a menos que a sua aplicação lógica crie ligações a outros serviços e sistemas através de [conectores geridos](../connectors/apis-list.md).

Para definir os valores para os `parameters` parâmetros de definição de fluxo de trabalho, utilize o objeto que está *fora* da definição de fluxo de trabalho, mas ainda *dentro* da definição de recursos da sua aplicação lógica. Neste objeto `parameters` exterior, pode então fazer referência aos parâmetros do modelo previamente declarados, que podem aceitar valores na implementação a partir de um ficheiro de parâmetros.

> [!TIP]
>
> Como uma boa prática, não referencia diretamente parâmetros de modelo, que são avaliados na implementação, a partir de dentro da definição de fluxo de trabalho. Em vez disso, declare um parâmetro de definição `parameters` de fluxo de trabalho, que pode então definir no objeto que está *fora* da definição de fluxo de trabalho, mas ainda *dentro* da definição de recursos da sua aplicação lógica. Para mais informações, consulte [Referências aos parâmetros](#parameter-references).

Esta sintaxe mostra onde pode declarar parâmetros tanto nos níveis de definição de modelo como de definição de fluxo de trabalho, juntamente com os quais pode definir esses valores de parâmetro, referenciando os parâmetros de definição de modelo e de fluxo de trabalho:

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

### <a name="secure-workflow-definition-parameters"></a>Parâmetros seguros de definição de fluxo de trabalho

Para um parâmetro de definição de fluxo de trabalho que lida com informações sensíveis, senhas, chaves de acesso ou segredos no tempo de execução, declare ou edite o parâmetro para utilizar o `securestring` tipo ou `secureobject` parâmetro. Pode fazer referência a este parâmetro em toda a sua definição de fluxo de trabalho. No nível superior do modelo, declare um parâmetro que tenha o mesmo tipo para lidar com esta informação na implementação.

Para definir o valor para o parâmetro `parameters` de definição de fluxo de trabalho, use o objeto que está *fora da* definição de fluxo de trabalho, mas ainda *dentro da* definição de recursos de aplicação lógica para fazer referência ao parâmetro do modelo. Finalmente, para passar o valor para o seu parâmetro de modelo na implementação, guarde esse valor no [Cofre de Chaves Azure](../azure-resource-manager/templates/key-vault-parameter.md) e referência ao cofre chave no ficheiro de [parâmetros](#template-parameter-files) que é usado pelo seu modelo na implantação.

Este modelo de exemplo mostra como pode completar estas tarefas definindo parâmetros seguros quando necessário para que possa armazenar os seus valores no Cofre chave Azure:

* Declare parâmetros seguros para os valores utilizados para autenticar o acesso.
* Utilize estes valores tanto nos níveis de definição de modelo como de definição de fluxo de trabalho.
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

Para garantir que o Logic App Designer pode mostrar corretamente parâmetros de definição de fluxo de trabalho, siga estas boas práticas:

* Incluir `defaultValue` o atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores sensíveis ou devem ser protegidos.

* Utilize sempre parâmetros seguros para nomes de utilizadores, senhas e segredos. Para ocultar ou proteger valores sensíveis dos parâmetros, siga a orientação nestes tópicos:

  * [Recomendações de segurança para parâmetros de ação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Recomendações de segurança para parâmetros nas definições de fluxo de trabalho](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Passe valores de parâmetro seguro com cofre de chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)

Para obter mais informações sobre os parâmetros de definição de fluxo de trabalho, consulte [Parâmetros - Linguagem definição](../logic-apps/logic-apps-workflow-definition-language.md#parameters)de fluxo de trabalho .

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definições de recursos de ligação

Quando a sua aplicação lógica cria e utiliza ligações a outros serviços `resources` e sistemautilizando [conectores geridos,](../connectors/apis-list.md)o objeto do seu modelo contém as definições de recursos para essas ligações.

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

As definições de recursos de ligação referem os parâmetros de nível superior do modelo para os seus valores, o que significa que pode fornecer estes valores na implementação utilizando um ficheiro de parâmetros. Certifique-se de que as ligações utilizam o mesmo grupo de recursos Azure e localização como a sua aplicação lógica.

Aqui está uma definição de recurso exemplo para uma ligação do Office 365 Outlook e os parâmetros de modelo correspondentes:

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

A definição de recursos da sua aplicação lógica também funciona com definições de recursos de conexão desta forma:

* Dentro da definição `parameters` de fluxo `$connections` de trabalho, o objeto declara um parâmetro para os valores de ligação a utilizar no tempo de execução da aplicação lógica. Além disso, o gatilho ou ação que cria `$connections` uma ligação utiliza os valores correspondentes que passam por este parâmetro.

* *Fora da* definição de fluxo de trabalho, `parameters` mas ainda *dentro* da definição de recursos da sua aplicação lógica, outro objeto define os valores a utilizar no tempo de funcionamento para o `$connections` parâmetro, referenciando os parâmetros do modelo correspondentes. Estes valores utilizam expressões de modelo para recursos de referência que armazenam de forma segura os metadados para as ligações na sua aplicação lógica.

  Por exemplo, os metadados podem incluir cordas de ligação e fichas de acesso, que pode armazenar no [Cofre de Chaves Azure](../azure-resource-manager/templates/key-vault-parameter.md). Para passar esses valores aos parâmetros do seu modelo, você refere esse cofre chave no ficheiro de [parâmetros](#template-parameter-files) que é usado pelo seu modelo na implantação. Para obter mais informações sobre diferenças na referência [aos parâmetros, consulte referências aos parâmetros](#parameter-references) mais tarde neste tópico.

  Ao abrir a definição de fluxo de trabalho da sua aplicação `$connections` lógica em vista de código através do portal Azure ou do Estúdio Visual, o objeto aparece fora da sua definição de fluxo de trabalho, mas ao mesmo nível. Esta encomenda em vista de código facilita a referência a estes parâmetros quando atualiza manualmente a definição de fluxo de trabalho:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* A definição de recursos `dependsOn` da sua aplicação lógica tem um objeto que especifica as dependências das ligações utilizadas pela sua aplicação lógica.

Cada ligação que cria tem um nome único em Azure. Quando cria múltiplas ligações ao mesmo serviço ou sistema, cada nome de ligação é anexado `office365`com `office365-1`um número, que aumenta com cada nova ligação criada, por exemplo, e assim por diante.

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

Para um parâmetro de ligação que lida com informações sensíveis, palavras-passe, chaves de acesso ou segredos, a definição de recursos da ligação inclui um `parameterValues` objeto que especifica estes valores no formato de par de nomes. Para ocultar esta informação, pode declarar ou editar os `securestring` parâmetros do modelo para estes valores utilizando os tipos ou `secureobject` parâmetros. Em seguida, pode armazenar essa informação no [Cofre de Chaves Azure.](../azure-resource-manager/templates/key-vault-parameter.md) Para passar esses valores aos parâmetros do seu modelo, você refere esse cofre chave no ficheiro de [parâmetros](#template-parameter-files) que é usado pelo seu modelo na implantação.

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

### <a name="authenticate-connections"></a>Ligações autenticadas

Após a implementação, a sua aplicação lógica funciona de ponta a ponta com parâmetros válidos. No entanto, deve ainda autorizar quaisquer ligações OAuth para gerar fichas de acesso válidas para [autenticar as suas credenciais](../active-directory/develop/authentication-scenarios.md). Para mais informações, consulte [Autorizar as ligações OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Algumas ligações suportam a [utilização](../active-directory/develop/app-objects-and-service-principals.md) de um serviço de diretório Ativo Azure (Azure AD) para autorizar ligações para uma aplicação lógica [registada no Azure AD](../active-directory/develop/quickstart-register-app.md). Por exemplo, esta definição de recurso de conexão Azure Data Lake mostra como referenciar os parâmetros do modelo que lidam com as informações do diretor de serviço e como o modelo declara estes parâmetros:

**Definição de recurso de ligação**

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
| `token:clientId` | A aplicação ou ID do cliente associado ao seu diretor de serviço |
| `token:clientSecret` | O valor-chave associado ao seu diretor de serviço |
| `token:TenantId` | A identificação do diretório para o seu inquilino da AD Azure |
| `token:grantType` | O tipo de subvenção `client_credentials`solicitado, que deve ser . Para mais informações, consulte a plataforma de identidade da Microsoft e o fluxo de credenciais de [clientes OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definições de parâmetro de modelo**

O objeto de alto `parameters` nível do modelo declara estes parâmetros para a ligação por exemplo:

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

Para obter mais informações sobre o trabalho com os diretores de serviço, consulte estes tópicos:

* [Criar um serviço principal utilizando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md)
* [Crie um serviço Azure principal utilizando o Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Criar um diretor de serviço com um certificado utilizando o Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Referências aos parâmetros

Para os parâmetros do modelo de referência, pode utilizar expressões de modelo com funções de [modelo,](../azure-resource-manager/templates/template-functions.md)que são avaliadas na implementação. Expressões de modelo usam suportes quadrados **([]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Para referência aos parâmetros de definição de fluxo de trabalho, utiliza [expressões e funções linguísticas](../logic-apps/workflow-definition-language-functions-reference.md)de definição de fluxo de trabalho, que são avaliadas no prazo de funcionamento. Pode notar que algumas funções de modelo e funções de definição de fluxo de trabalho têm o mesmo nome. As expressões de definição de fluxo**@** de trabalho começam com o símbolo "at" ():

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Pode passar os valores dos parâmetros do modelo para a definição de fluxo de trabalho para a sua aplicação lógica usar no tempo de execução. No entanto, evite utilizar parâmetros de modelo, expressões e sintaxe na sua definição de fluxo de trabalho porque o Logic App Designer não suporta elementos de modelo. Além disso, a sintaxe do modelo e expressões podem complicar o seu código devido a diferenças no momento em que as expressões são avaliadas.

Em vez disso, siga estes passos gerais para declarar e referenciar os parâmetros de definição de fluxo de trabalho a utilizar no tempo de execução, declarar e referenciar os parâmetros do modelo a utilizar na implementação e especificar os valores a transmitir na implementação utilizando um ficheiro de parâmetros. Para mais detalhes, consulte a secção de [definição de Fluxo de Trabalho e parâmetros](#workflow-definition-parameters) mais cedo neste tópico.

1. Crie o seu modelo e declare os parâmetros do modelo para que os valores aceitem e utilizem na implementação.

1. Na sua definição de fluxo de trabalho, declare os parâmetros para que os valores aceitem e utilizem no tempo de funcionamento. Pode então referenciar estes valores ao longo e dentro da sua definição de fluxo de trabalho.

1. No `parameters` objeto que está *fora* da definição de fluxo de trabalho mas ainda *dentro* da definição de recursos da sua aplicação lógica, defina os valores para os parâmetros de definição de fluxo de trabalho, referindo os parâmetros de modelo correspondentes. Dessa forma, pode passar os valores dos parâmetros do modelo nos parâmetros da definição de fluxo de trabalho.

1. No ficheiro de parâmetros, especifique os valores para o seu modelo utilizar na implementação.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Modelo de exemplo completo

Aqui está o modelo de amostra parametrizado que é usado pelos exemplos deste tópico:

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
