---
title: Escalar uma instância do Serviço Azure SignalR
description: Aprenda a escalar uma instância do Serviço Azure SignalR para adicionar ou reduzir a capacidade, através do portal Azure ou do Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 9/9/2020
ms.author: zhshang
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb01f2f96e5db4c94e759b114818360e6084255f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89595772"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Como escalar uma instância do Serviço Azure SignalR?
Este artigo mostra-lhe como escalar o seu caso de Serviço Azure SignalR. Há dois cenários para escalar, escalar e escalar.

* [Escala:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Obtenha mais unidades, ligações, mensagens e muito mais. Aumenta-se alterando o nível de preços de Free para Standard.
* [Escala :](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Aumentar o número de unidades SignalR. Pode aumentar até 100 unidades. Existem opções de unidade limitadas para selecionar para o dimensionamento: 1, 2, 5, 10, 20, 50 e 100 unidades para uma única instância do Serviço SignalR.

As regulações da balança demoram alguns minutos a aplicar-se. Em casos raros, pode levar cerca de 30 minutos a candidatar-se. Não exigem que altere o seu código ou reimplante a aplicação do servidor.

Para obter informações sobre os preços e capacidades de cada Serviço SignalR individual, consulte detalhes [de preços do serviço Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Alterando o serviço SignalR de nível **gratuito** para nível **Standard** ou vice-versa, o IP de serviço público será alterado e geralmente leva 30-60 minutos para propagar a mudança para servidores DNS em toda a internet. O seu serviço pode ser inacessível antes de o DNS ser atualizado. Geralmente não é recomendado mudar o seu nível de preços com demasiada frequência.


## <a name="scale-on-azure-portal"></a>Escala no portal Azure

1. No browser, abra o [portal do Azure](https://portal.azure.com).

2. Na sua página de Serviço SignalR, a partir do menu esquerdo, selecione **Scale**.
   
3. Escolha o seu nível de preços e, em seguida, clique em **Selecionar**. Desa esta hora a contagem de unidades para **o Nível Padrão.**
   
    ![Escala no Portal](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Clique em **Guardar**.

## <a name="scale-using-azure-cli"></a>Escala usando Azure CLI

Este script cria um novo recurso signalr Service de **Free** Tier e um novo grupo de recursos, e escala-o até **ao Standard** Tier. 

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

Para obter informações detalhadas, tais como mensagens incluídas e ligações para cada nível de preços, consulte [os detalhes dos preços do serviço SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

Para uma tabela de limites de serviço, quotas e restrições em cada nível, consulte [os limites do Serviço SignalR](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu sobre como escalar uma única instância do Serviço SignalR.

Vários pontos finais também são apoiados para cenários de escala, fragmentos e cross-region.

> [!div class="nextstepaction"]
> [serviço SignalR de escala com múltiplas instâncias](./signalr-howto-scale-multi-instances.md)
