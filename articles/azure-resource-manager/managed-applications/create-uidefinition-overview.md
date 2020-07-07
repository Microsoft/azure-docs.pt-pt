---
title: CreateUiDefinition.jsno arquivo para painel de portal
description: Descreve como criar definições de interface de utilizador para o portal Azure. Utilizado na definição de Aplicações Geridas Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80294897"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json para experiência de criação de aplicação gerida do Azure

Este documento introduz os conceitos fundamentais do **createUiDefinition.jsem** ficheiro que o portal Azure utiliza para definir a interface do utilizador ao criar uma aplicação gerida.

O modelo é o seguinte

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

Um CreateUiDefinition contém sempre três propriedades: 

* manipulador
* versão
* parâmetros

O manipulador deve ser sempre `Microsoft.Azure.CreateUIDef` , e a versão mais recente suportada é `0.1.2-preview` .

O esquema da propriedade dos parâmetros depende da combinação do manipulador especificado e da versão. Para aplicações geridas, as propriedades suportadas são `basics` `steps` , e `outputs` . As propriedades básicas e de passos contêm os [elementos](create-uidefinition-elements.md) - como caixas de texto e dropdowns - a serem exibidos no portal Azure. A propriedade outputs é usada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo de implementação do Gestor de Recursos Azure.

Incluindo `$schema` é recomendado, mas opcional. Se especificado, o valor para `version` deve corresponder à versão dentro do `$schema` URI.

Pode utilizar um editor JSON para criar o seu createUiDefinition e, em seguida, testá-lo na [createUiDefinition Sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) para pré-visualizar. Para obter mais informações sobre a caixa de areia, consulte [testar a interface do seu portal para aplicações geridas azure.](test-createuidefinition.md)

## <a name="basics"></a>Noções básicas

O básico é o primeiro passo gerado quando o portal Azure analisa o ficheiro. Além de apresentar os elementos especificados `basics` em , o portal injeta elementos para que os utilizadores escolham a subscrição, o grupo de recursos e a localização para a implementação. Quando possível, os elementos que consultam parâmetros de implantação, como o nome de um cluster ou credenciais de administrador, devem ir neste passo.

## <a name="steps"></a>Passos

A propriedade dos passos pode conter zero ou mais passos adicionais para exibir após o básico, cada um dos quais contém um ou mais elementos. Considere adicionar passos por função ou nível da aplicação que está a ser implementada. Por exemplo, adicione um passo para entradas de nó mestre, e um passo para os nós operários em um cluster.

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
