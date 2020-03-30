---
title: Escala uma instância de Serviço De Sinalização Azure
description: Aprenda a dimensionar uma instância de Serviço De Sinalização Azure para adicionar ou reduzir a capacidade, através do portal Azure ou do Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659292"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Como escalar uma instância de Serviço De Sinalização Azure?
Este artigo mostra-lhe como escalar a sua instância de Serviço De Sinalizador Azure. Há dois cenários para escalar, escalar e escalar.

* [Escala para cima](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais unidades, ligações, mensagens e muito mais. Escala-se alterando o nível de preços de Free para Standard.
* [Escala para fora](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumente o número de unidades SignalR. Pode saquear até 100 unidades.

As definições de escala demoram alguns minutos a aplicar. Em casos raros, pode levar cerca de 30 minutos para se candidatar. Não exigem que altere o seu código ou reimplemente a sua aplicação de servidor.

Para obter informações sobre os preços e capacidades do Serviço SignalR individual, consulte os detalhes de preços do [serviço De sinalização Azure](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Alterando o Serviço signalr de nível **gratuito** para nível **Standard** ou vice-versa, o IP de serviço público será alterado e geralmente leva 30-60 minutos para propagar a alteração para servidores DNS em toda a internet. O seu serviço pode ser inacessível antes que o DNS seja atualizado. Geralmente não é recomendado alterar o seu nível de preços com demasiada frequência.


## <a name="scale-on-azure-portal"></a>Escala no portal Azure

1. No browser, abra o [portal do Azure](https://portal.azure.com).

2. Na sua página de Serviço SignalR, a partir do menu esquerdo, **selecione Escala**.
   
3. Escolha o seu nível de preços e, em seguida, clique em **Selecionar**. Desloque a contagem de unidades para **o Standard** Tier.
   
    ![Escala no Portal](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Clique em **Guardar**.

## <a name="scale-using-azure-cli"></a>Escala usando Azure CLI

Este script cria um novo recurso signalR service da **Free** Tier e um novo grupo de recursos, e escala-o até ao **Standard** Tier. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Anote o nome real gerado para o novo grupo de recursos. Irá utilizar esse nome de grupo de recursos quando quiser eliminar todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Comparar níveis de preços

Para obter informações detalhadas, tais como mensagens e ligações incluídas para cada nível de preços, consulte os detalhes de [preços do serviço SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

Para uma tabela de limites de serviço, quotas e restrições em cada nível, consulte os limites do [Serviço SignalR](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a escalar a instância de serviço de sinalização única.

Vários pontos finais também são apoiados para cenários de escala, sharding e região transversal.

> [!div class="nextstepaction"]
> [serviço de sinalização de escala com múltiplas instâncias](./signalr-howto-scale-multi-instances.md)
