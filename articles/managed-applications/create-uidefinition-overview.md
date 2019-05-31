---
title: Compreender a interface do Usuário a criar aplicativos gerenciados de definição para o Azure | Documentos da Microsoft
description: Descreve como criar definições de IU para aplicações geridas do Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 3d0a6d97440404904c041369a4631fdd3fb618b4
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257554"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Criar a interface de utilizador do portal do Azure para a aplicação gerida
Este documento apresenta os principais conceitos do ficheiro Createuidefinition. O portal do Azure utiliza este ficheiro para gerar a interface do usuário para a criação de um aplicativo gerenciado.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

Um CreateUiDefinition sempre contém três propriedades: 

* Processador
* version
* parameters

Para aplicativos gerenciados, o manipulador deve ser sempre `Microsoft.Compute.MultiVm`, e é a versão mais recente suportada `0.1.2-preview`.

O esquema da propriedade parâmetros depende a combinação do processador especificado e a versão. Para aplicativos gerenciados, as propriedades suportadas são `basics`, `steps`, e `outputs`. As propriedades de noções básicas e passos de conter o _elementos_ - como caixas de texto e as listas pendentes - a ser apresentado no portal do Azure. A propriedade de saídas é usada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo de implementação Azure Resource Manager.

Incluindo `$schema` é recomendada, mas opcionais. Se for especificado, o valor para `version` tem de corresponder à versão contida o `$schema` URI.

Pode utilizar um editor de JSON para criar sua definição de interface do Usuário, ou pode utilizar a área de segurança de definição de interface do Usuário para criar e a definição de interface do Usuário de pré-visualização. Para obter mais informações sobre a área de segurança, consulte [testar sua interface do portal para aplicações geridas do Azure](test-createuidefinition.md).

## <a name="basics"></a>Noções básicas
A etapa de noções básicas sempre é a primeira etapa do assistente gerado quando o portal do Azure analisa o ficheiro. Além de exibir os elementos especificados no `basics`, o portal injeta elementos aos utilizadores escolher a subscrição, grupo de recursos e localização para a implementação. Em geral, os elementos que consultar os parâmetros de toda a implementação, como o nome das credenciais de cluster ou de administrador, devem ir neste passo.

Se o comportamento de um elemento depende da subscrição do utilizador, grupo de recursos ou localização, em seguida, esse elemento não é possível utilizar Noções básicas. Por exemplo, **Microsoft.Compute.SizeSelector** depende da subscrição do utilizador e a localização para determinar a lista de tamanhos disponíveis. Por conseguinte, **Microsoft.Compute.SizeSelector** só pode ser utilizada em passos. Em geral, apenas elementos no **Microsoft.Common** espaço de nomes pode ser utilizado nas noções básicas. Embora alguns elementos em outros namespaces (como **Microsoft.Compute.Credentials**) que não dependem de contexto do usuário, ainda são permitidas.

## <a name="steps"></a>Passos
A propriedade de passos pode conter zero ou mais passos adicionais para apresentar após Noções básicas, cada um deles contendo um ou mais elementos. Considere adicionar passos por função ou a camada de aplicação que está sendo implementada. Por exemplo, adicione um passo de entradas de dados para os nós principais e um passo para os nós de trabalho num cluster.

## <a name="outputs"></a>Saídas
O portal do Azure utiliza o `outputs` propriedade para mapear elementos `basics` e `steps` para os parâmetros do modelo de implementação Azure Resource Manager. As chaves nesse dictionary são os nomes dos parâmetros do modelo e os valores são propriedades dos objetos de saída dos elementos referenciados.

Para definir o nome de recurso de aplicação gerida, tem de incluir um valor chamado `applicationResourceName` na propriedade saídas. Se não definir este valor, o aplicativo atribui um GUID para o nome. Pode incluir uma caixa de texto na interface do usuário que solicita um nome do usuário.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Funções
Assim como as funções de modelo no Azure Resource Manager (tanto na sintaxe e a funcionalidade), CreateUiDefinition fornece funções para trabalhar com entradas e saídas e funcionalidades, tais como lógica condicional dos elementos.

## <a name="next-steps"></a>Passos Seguintes
O próprio ficheiro Createuidefinition tem um esquema simples. A profundidade real do mesmo é proveniente de todos os elementos suportados e as funções. Esses itens são descritos em maior detalhe em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um esquema JSON atual para createUiDefinition está disponível aqui: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Para um ficheiro de interface de utilizador de exemplo, consulte [Createuidefinition](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
