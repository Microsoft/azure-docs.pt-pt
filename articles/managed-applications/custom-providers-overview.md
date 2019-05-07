---
title: Descrição geral da pré-visualização do Azure fornecedores personalizados
description: Descreve os conceitos para a criação de um fornecedor de recursos personalizados com o Azure Resource Manager
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159859"
---
# <a name="azure-custom-providers-preview-overview"></a>Descrição geral de fornecedores de personalizado pré-visualização do Azure

Fornecedores de serviços do Azure personalizado, pode expandir o Azure para trabalhar com o seu serviço. Crie seu próprio fornecedor de recursos, incluindo tipos de recursos personalizadas e ações. O provedor personalizado é integrado com o Azure Resource Manager. Pode utilizar funcionalidades do Gestor de recursos, como implementações de modelo e o controlo de acesso baseado em funções, implantar e proteger o seu serviço.

Este artigo fornece uma visão geral dos fornecedores personalizados e as respetivas capacidades. A imagem seguinte mostra o fluxo de trabalho para recursos e as ações definidas num provedor personalizado.

![Descrição geral do fornecedor personalizado](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Fornecedores personalizados está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Definir o seu fornecedor personalizado

Iniciar, permitindo que precisa saber sobre o seu fornecedor personalizado do Azure Resource Manager. Implementar no Azure um recurso de fornecedor personalizado, que usa o tipo de recurso **Microsoft.CustomProviders/resourceProviders**. Esse recurso, vai definir os recursos e ações para o seu serviço.

Por exemplo, se o seu serviço tem um tipo de recurso com o nome **utilizadores**, incluem esse tipo de recurso na sua definição de fornecedor personalizado. Para cada tipo de recurso, fornecem um ponto final que oferece as operações REST (PUT, GET, DELETE) para esse tipo de recurso. O ponto final pode ser alojado em qualquer ambiente, e contém a lógica para a forma como o seu serviço processa operações no tipo de recurso.

Também pode definir ações personalizadas para o seu fornecedor de recursos. Ações representam operações POST. Utilize ações para operações como iniciar, parar ou reiniciar. Fornecer um ponto de extremidade que processa o pedido.

O exemplo seguinte mostra como definir um provedor personalizado com uma ação e um tipo de recurso.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

Para **routingType**, os valores aceites são `Proxy` e `Cache`. Proxy significa pedidos para o tipo de recurso ou ação são processadas pelo ponto final. A definição de cache só é suportada para tipos de recursos, não ações. Para especificar o cache, também tem de especificar o proxy. Cache significa que as respostas do ponto de extremidade são armazenadas para otimizar operações de leitura. Utilize a definição de cache torna mais fácil de implementar uma API consistente e em conformidade com outros serviços do Gestor de recursos.

## <a name="deploy-your-resource-types"></a>Implantar os seus tipos de recursos

Depois de definir o seu fornecedor personalizado, pode implantar seus tipos de recurso personalizado. O exemplo seguinte mostra o JSON que inclua no seu modelo para implementar o tipo de recurso para o seu fornecedor personalizado. Este tipo de recurso pode ser implementado no mesmo modelo com outros recursos do Azure.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Gerir o acesso

Utilizar o Azure [controlo de acesso baseado em funções](../role-based-access-control/overview.md) para gerir o acesso ao seu fornecedor de recursos. Pode atribuir [funções incorporadas](../role-based-access-control/built-in-roles.md) como proprietário, contribuinte ou leitor para os utilizadores. Em alternativa, pode definir [funções personalizadas](../role-based-access-control/custom-roles.md) que são específicas para as operações no seu fornecedor de recursos.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Vá para o artigo seguinte para criar um provedor personalizado.

> [!div class="nextstepaction"]
> [Tutorial: Criar fornecedor personalizado e implementar recursos personalizados](create-custom-provider.md)
