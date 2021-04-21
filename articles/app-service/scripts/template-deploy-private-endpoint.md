---
title: Use um modelo de Gestor de Recursos Azure para implementar um ponto final privado para uma aplicação web
description: Aprenda a usar o modelo ARM para implementar um ponto final privado para a sua aplicação web.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df71c1a92840ae0e7fa263e2ababcf5b3e059789
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832541"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Crie uma aplicação de Serviço de Aplicações e implemente um ponto final privado utilizando um modelo de Gestor de Recursos Azure

Neste arranque rápido, você usa um modelo Azure Resource Manager (ARM) para criar uma aplicação web e expô-la com um ponto final privado.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Pré-requisito

Precisa de uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Este modelo cria um ponto final privado para uma aplicação web Azure.

### <a name="review-the-template"></a>Rever o modelo

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Implementar o modelo

Aqui está como implementar o modelo do Gestor de Recursos Azure para Azure:

1. Para iniciar súm na Azure e abrir o modelo, selecione este link:  [Implementar para Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). O modelo cria a rede virtual, a aplicação web, o ponto final privado e a zona privada do DNS.
2. Selecione ou crie o seu grupo de recursos.
3. Insira o nome da sua aplicação web, plano de Serviço de Aplicações Azure e ponto final privado.
5. Leia a declaração sobre os termos e condições. Se concordar, **selecione concordo com os termos e condições acima**  >  **de Compra.** A colocação pode levar vários minutos para terminar.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o ponto final privado, elimine o grupo de recursos. Isto remove o ponto final privado e todos os recursos conexos.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

- Pode encontrar mais modelos de Gestor de Recursos Azure para aplicações web do Azure App Service nas amostras do [modelo ARM.](../samples-resource-manager-templates.md)
