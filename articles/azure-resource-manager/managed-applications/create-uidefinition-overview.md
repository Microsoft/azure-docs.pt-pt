---
title: Ficheiro CreateUiDefinition.json para painel de portal
description: Descreve como criar definições de interface de utilizador para o portal Azure. Utilizado na definição de Aplicações Geridas Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294897"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json para experiência de criação de aplicação gerida do Azure

Este documento introduz os conceitos fundamentais do ficheiro **createUiDefinition.json** que o portal Azure utiliza para definir a interface do utilizador ao criar uma aplicação gerida.

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

A CreateUiDefinition contém sempre três propriedades: 

* manipulador
* versão
* parâmetros

O manipulador deve `Microsoft.Azure.CreateUIDef`ser sempre , e `0.1.2-preview`a versão mais recente suportada é .

O esquema da propriedade dos parâmetros depende da combinação do manipulador e versão especificado. Para aplicações geridas, as `basics`propriedades `steps`suportadas são, e `outputs`. As propriedades básicas e passos contêm os [elementos](create-uidefinition-elements.md) - como caixas de texto e dropdowns - a serem exibidos no portal Azure. A propriedade de saídas é usada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo de implementação do Gestor de Recursos Azure.

Incluir `$schema` é recomendado, mas opcional. Se especificado, o `version` valor para deve `$schema` coincidir com a versão dentro do URI.

Pode utilizar um editor JSON para criar a sua createUiDefinition e depois testá-la na [createUiDefinition Sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) para pré-visualizar. Para mais informações sobre a caixa de areia, consulte [Testar a interface do portal para aplicações geridas](test-createuidefinition.md)pelo Azure .

## <a name="basics"></a>Noções básicas

O básico é o primeiro passo gerado quando o portal Azure analisa o ficheiro. Além de exibir os elementos `basics`especificados, o portal injeta elementos para os utilizadores escolherem a subscrição, o grupo de recursos e a localização para a implementação. Quando possível, os elementos que consultam parâmetros de implantação em toda a escala, como o nome de um cluster ou credenciais de administrador, devem ir neste passo.

## <a name="steps"></a>Passos

A propriedade de passos pode conter zero ou mais passos adicionais para exibir após o básico, cada um dos quais contém um ou mais elementos. Considere adicionar passos por função ou nível da aplicação que está a ser implementada. Por exemplo, adicione um passo para as inputs do nó mestre, e um passo para os nós dos trabalhadores em um cluster.

## <a name="outputs"></a>Saídas

O portal Azure `outputs` utiliza a propriedade `basics` `steps` para mapear elementos de e para os parâmetros do modelo de implantação do Gestor de Recursos Azure. As chaves deste dicionário são os nomes dos parâmetros do modelo, e os valores são propriedades dos objetos de saída dos elementos referenciados.

Para definir o nome de recurso de aplicação gerido, deve incluir um valor nomeado `applicationResourceName` na propriedade de saídas. Se não definir este valor, a aplicação atribui um GUID para o nome. Pode incluir uma caixa de texto na interface do utilizador que solicita um nome ao utilizador.

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

Para filtrar os locais disponíveis apenas para os locais que suportam os tipos de recursos a implementar, fornecer uma variedade dos tipos de recursos. Se fornecer mais do que um tipo de recurso, apenas as localizações que suportam todos os tipos de recursos são devolvidas. Esta propriedade é opcional.

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

A CreateUiDefinition fornece [funções](create-uidefinition-functions.md) para trabalhar com as inputações e saídas dos elementos, e características tais como condicionalismos. Estas funções são semelhantes tanto na sintaxe como na funcionalidade para as funções do modelo do Gestor de Recursos Azure.

## <a name="next-steps"></a>Passos seguintes

O ficheiro createUiDefinition.json em si tem um esquema simples. A verdadeira profundidade vem de todos os elementos e funções suportados. Estes itens são descritos com maior detalhe em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um atual esquema JSON para criar UiDefinição está disponível aqui: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Para um exemplo, ficheiro de interface de utilizador, consulte [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
