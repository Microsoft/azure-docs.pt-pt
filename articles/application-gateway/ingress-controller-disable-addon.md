---
title: Desativar e reativar o complemento do controlador de entrada de gateway de aplicação para o cluster de serviços Azure Kubernetes
description: Este artigo fornece informações sobre como desativar e reativar o addon AGIC para o seu cluster AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807942"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>Desative e reative o addon AGIC para o seu cluster AKS
O Controlador de Entrada de Gateway (AGIC) implantado como um addon AKS permite-lhe ativar e desativar o addon com uma linha em Azure CLI. O ciclo de vida do Gateway de aplicação difere quando desativar o addon AGIC, dependendo se o Gateway de Aplicação foi criado pelo addon AGIC, ou se foi implantado separadamente do addon AGIC. Pode executar o mesmo comando para voltar a ativar o addon AGIC se alguma vez o desativar, ou para ativar o addon AGIC utilizando um cluster AKS existente e Gateway de aplicação.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>Desativar o addon AGIC com o Gateway de aplicação associado 
Se o addon AGIC implementou automaticamente o Gateway de Aplicação para si quando configurar tudo pela primeira vez, então desativar o add-on AGIC irá, por defeito, eliminar o Gateway de Aplicação com base em alguns critérios. Existem dois critérios que o addon AGIC procura para determinar se deve eliminar o Gateway de Aplicação associado quando o desativar:
- É o Gateway de Aplicação que o addon AGIC está associado ao grupo de recursos de nó MC_*? 
- O Gateway de Aplicação a que o add-on AGIC está associado tem a etiqueta "created-by: ingress-appgw"? A etiqueta é utilizada pela AGIC para determinar se o Gateway de Aplicação foi implantado ou não pelo add-on. 

Se ambos os critérios forem cumpridos, então o addon AGIC eliminará o Gateway de Aplicação que criou quando o add-on estiver desativado; no entanto, não apagará o IP público ou a sub-rede em que o Gateway de Aplicação foi implantado com/dentro. Se os primeiros critérios não forem cumpridos, então não importará se o Gateway de Aplicação tem a etiqueta "created-by: ingress-appgw" - desativar o add-on não apagará o Gateway de Aplicação. Da mesma forma, se os segundos critérios não forem cumpridos, ou seja, o Gateway de Aplicação não tem essa etiqueta, então desativar o add-on não apagará o Gateway de Aplicação no grupo de recursos de nó MC_*. 

> [!TIP] 
> Se não quiser que o Gateway de Aplicação seja eliminado ao desativar o addon, mas cumpre ambos os critérios, então remova a etiqueta "created-by: ingress-appgw" para evitar que o addon elimine o gateway de aplicação. 

Para desativar o addon AGIC, executar o seguinte comando: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>Ativar o addon AGIC sobre o gateway de aplicações existente e o cluster AKS
Se alguma vez desativar o addon AGIC e precisar de voltar a ativar o add-on, ou quiser ativar o addon utilizando um cluster de Gateway de Aplicação e AKS existente, em seguida, executar o seguinte comando:

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais detalhes sobre como permitir o addon AGIC utilizando um cluster de gateway de aplicação e AKS existente, consulte [a implementação do add-on aGIC .](tutorial-ingress-controller-add-on-existing.md)