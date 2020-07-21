---
title: Com este modelo de gestor de recursos Azure, poderá implementar o Ponto Final Privado para a Web App.
description: Saiba como usar o modelo ARM para implementar o Ponto Final Privado para a sua Aplicação Web
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535732"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Crie uma aplicação de Serviço de Aplicações e implemente o Private Endpoint usando o modelo de Gestor de Recursos Azure

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar uma Web App e expô-la com um ponto final privado.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Pré-requisito

Precisa de uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Este modelo cria um ponto final privado para uma Aplicação Web Azure.

### <a name="review-the-template"></a>Rever o modelo

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Implementar o modelo

Aqui está como implementar o modelo do Gestor de Recursos Azure para Azure:

1. Para iniciar súm na Azure e abrir o modelo, **selecione Implementar para Azure**. O modelo cria o VNet, a Web App, o Private Endpoint e a zona privada de DNS.

   [Implementar no Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos.
3. Digite o nome da sua Aplicação Web, Plano de Serviço de Aplicações, Ponto Final Privado.
5. Leia a declaração de termos e condições. Se concordar, selecione concordo com os termos e condições acima > Compra. A implantação pode demorar vários minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o ponto final privado, elimine o grupo de recursos. Isto remove o ponto final privado e todos os recursos conexos.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximos passos

- Modelos adicionais de Gestor de Recursos Azure para aplicações web do Azure App Service podem ser encontrados nas [amostras](../samples-resource-manager-templates.md)do modelo ARM .
