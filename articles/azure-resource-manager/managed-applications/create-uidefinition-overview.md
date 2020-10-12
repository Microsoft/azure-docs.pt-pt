---
title: CreateUiDefinition.jsno arquivo para painel de portal
description: Descreve como criar definições de interface de utilizador para o portal Azure. Utilizado na definição de Aplicações Geridas Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 327fa1d7eb73d8e65bb4f81c1dff0fe2bec2913b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89319577"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json para experiência de criação de aplicação gerida do Azure

Este documento introduz os conceitos fundamentais do **createUiDefinition.jsarquivados.** O portal Azure utiliza este ficheiro para definir a interface do utilizador ao criar uma aplicação gerida.

O modelo é o seguinte

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

A `CreateUiDefinition` contém sempre três propriedades:

* manipulador
* versão
* parâmetros

O manipulador deve ser sempre `Microsoft.Azure.CreateUIDef` , e a versão mais recente suportada é `0.1.2-preview` .

O esquema da propriedade dos parâmetros depende da combinação do manipulador especificado e da versão. Para aplicações geridas, as propriedades suportadas `config` `basics` são, `steps` e `outputs` . Só se usa `config` quando é necessário anular o comportamento predefinido do `basics` passo. As propriedades básicas e de passos contêm os [elementos](create-uidefinition-elements.md) - como caixas de texto e dropdowns - a serem exibidos no portal Azure. A propriedade outputs é usada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo do Gestor de Recursos Azure.

Incluindo `$schema` é recomendado, mas opcional. Se especificado, o valor para `version` deve corresponder à versão dentro do `$schema` URI.

Pode utilizar um editor JSON para criar o seu createUiDefinition e, em seguida, testá-lo na [createUiDefinition Sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) para pré-visualizar. Para obter mais informações sobre a caixa de areia, consulte [testar a interface do seu portal para aplicações geridas azure.](test-createuidefinition.md)

## <a name="config"></a>Configurar

A `config` propriedade é opcional. Use-o para anular o comportamento predefinido do passo básico, ou para definir a sua interface como um assistente passo a passo. Se `config` for usado, é a primeira propriedade do **createUiDefinition.jsna** secção do `parameters` arquivo. O exemplo a seguir mostra as propriedades disponíveis.

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

### <a name="wizard"></a>Feiticeiro

A `isWizard` propriedade permite-lhe exigir validação bem sucedida de cada passo antes de avançar para o passo seguinte. Quando a `isWizard` propriedade não é especificada, o padrão é **falso,** e a validação passo a passo não é necessária.

Quando `isWizard` estiver ativado, definido como **verdadeiro,** o separador **Básicos** está disponível e todos os outros separadores estão desativados. Quando o botão **Seguinte** é selecionado, o ícone do separador indica se a validação de um separador passou ou falhou. Depois de concluídos os campos necessários e validado, o botão **Seguinte** permite a navegação para o separador seguinte. Quando todos os separadores passam a validação, pode ir à página **'Rever e Criar' e** selecionar o botão **Criar** para iniciar a implementação.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="Assistente de separador":::

### <a name="override-basics"></a>Sobrepor-se ao básico

O básico config permite-lhe personalizar o passo básico.

Para `description` , forneça uma cadeia ativada por marcação que descreva o seu recurso. O formato multi-linha e as ligações são suportadas.

Os `subscription` `resourceGroup` elementos e elementos permitem especificar validações adicionais. A sintaxe para especificar validações é idêntica à validação personalizada para [caixa de texto](microsoft-common-textbox.md). Também pode especificar `permission` validações no grupo de subscrição ou recursos.  

O controlo de subscrição aceita uma lista de espaços de nome do fornecedor de recursos. Por exemplo, pode especificar **Microsoft.Compute**. Mostra uma mensagem de erro quando o utilizador seleciona uma subscrição que não suporta o fornecedor de recursos. O erro ocorre quando o fornecedor de recursos não está registado nessa subscrição, e o utilizador não tem permissão para registar o fornecedor de recursos.  

O controlo do grupo de recursos tem uma opção para `allowExisting` . Quando `true` , os utilizadores podem selecionar grupos de recursos que já possuem recursos. Esta bandeira é mais aplicável aos modelos de solução, onde o comportamento padrão determina que os utilizadores devem selecionar um grupo de recursos novo ou vazio. Na maioria dos outros cenários, especificar esta propriedade não é necessário.  

Para `location` , especificar as propriedades para o controlo de localização que deseja anular. Quaisquer propriedades não ultrapassadas são definidas para os seus valores padrão. `resourceTypes` aceita uma série de cordas que contenham nomes de tipo de recurso totalmente qualificados. As opções de localização são restritas apenas a regiões que suportam os tipos de recursos.  `allowedValues`   aceita uma variedade de cordas da região. Apenas essas regiões aparecem no recuo.Pode definir os dois `allowedValues`    `resourceTypes` e. O resultado é a intersecção de ambas as listas. Por último, a `visible` propriedade pode ser usada para desativar condicional ou completamente a localização.  

## <a name="basics"></a>Noções básicas

O **passo Basics** é o primeiro passo gerado quando o portal Azure analisa o ficheiro. Por predefinição, o passo básico permite que os utilizadores escolham a subscrição, o grupo de recursos e a localização para implementação.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Assistente de separador":::

Pode adicionar mais elementos nesta secção. Quando possível, adicione elementos que consultam parâmetros de implantação, como o nome de um cluster ou credenciais de administrador.

O exemplo a seguir mostra uma caixa de texto que foi adicionada aos elementos predefinidos.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>Passos

A propriedade passos contém zero ou mais passos adicionais para exibir após o básico. Cada passo contém um ou mais elementos. Considere adicionar passos por função ou nível da aplicação que está a ser implementada. Por exemplo, adicione um passo para entradas de nó mestre, e um passo para os nós operários em um cluster.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Saídas

O portal Azure utiliza a `outputs` propriedade para mapear elementos de `basics` e para os `steps` parâmetros do modelo de implementação do Gestor de Recursos Azure. As teclas deste dicionário são os nomes dos parâmetros do modelo, e os valores são propriedades dos objetos de saída dos elementos referenciados.

Para definir o nome do recurso de aplicação gerido, deve incluir um valor nomeado `applicationResourceName` na propriedade outputs. Se não definir este valor, a aplicação atribui um GUID para o nome. Pode incluir uma caixa de texto na interface do utilizador que solicita um nome ao utilizador.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Tipos de recurso

Para filtrar os locais disponíveis apenas para os locais que suportam os tipos de recursos a implementar, fornecer uma matriz dos tipos de recursos. Se fornecer mais de um tipo de recurso, apenas os locais que suportam todos os tipos de recursos são devolvidos. Esta propriedade é opcional.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Funções

A CreateUiDefinition fornece [funções](create-uidefinition-functions.md) para trabalhar com entradas e saídas de elementos, e funcionalidades como condicionalidades. Estas funções são semelhantes tanto na sintaxe como na funcionalidade das funções do modelo do Gestor de Recursos Azure.

## <a name="next-steps"></a>Passos seguintes

O createUiDefinition.jsno ficheiro em si tem um esquema simples. A verdadeira profundidade vem de todos os elementos e funções suportados. Estes itens são descritos com maior detalhe em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um esquema JSON atual para criar Adefinition está disponível aqui: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Para um ficheiro de interface de utilizador, consulte [createUiDefinition.jsligado .](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)
