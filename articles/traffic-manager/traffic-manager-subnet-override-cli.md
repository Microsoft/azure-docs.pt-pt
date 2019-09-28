---
title: Substituição de sub-rede do Gerenciador de tráfego do Azure usando CLI do Azure | Microsoft Docs
description: Este artigo o ajudará a entender como a substituição de sub-rede do Gerenciador de tráfego pode ser usada para substituir o método de roteamento de um perfil do Gerenciador de tráfego para direcionar o tráfego para um ponto de extremidade com base no endereço IP do usuário final por meio do intervalo de IP predefinido para mapeamentos de ponto de extremidade.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: d285db22ef92128e025a677ea7f86d623dfe130c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351173"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Substituição de sub-rede do Gerenciador de tráfego usando CLI do Azure

A substituição de sub-rede do Gerenciador de tráfego permite alterar o método de roteamento de um perfil.  A adição de uma substituição direcionará o tráfego com base no endereço IP do usuário final com um intervalo de IP predefinido para o mapeamento de ponto de extremidade. 

## <a name="how-subnet-override-works"></a>Como a substituição de sub-rede funciona

Quando as substituições de sub-rede são adicionadas a um perfil do Gerenciador de tráfego, o Gerenciador de tráfego primeiro verificará se há uma substituição de sub-rede para o endereço IP do usuário final. Se um for encontrado, a consulta DNS do usuário será direcionada para o ponto de extremidade correspondente.  Se um mapeamento não for encontrado, o Gerenciador de tráfego fará o fallback para o método de roteamento original do perfil. 

Os intervalos de endereços IP podem ser especificados como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereços (por exemplo, 1.2.3.4-5.6.7.8). Os intervalos de IP associados a cada ponto de extremidade devem ser exclusivos para esse ponto de extremidade. Qualquer sobreposição de intervalos de IP entre diferentes pontos de extremidade fará com que o perfil seja rejeitado pelo Gerenciador de tráfego.

Há dois tipos de perfis de roteamento que dão suporte a substituições de sub-rede:

* **Geográfico** – se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele roteará a consulta para o ponto de extremidade, independentemente da integridade do ponto de extremidade.
* **Desempenho** – se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele roteará apenas o tráfego para o ponto de extremidade se ele estiver íntegro.  O Gerenciador de tráfego fará o fallback para o heurística de roteamento de desempenho se o ponto de extremidade de substituição de sub-rede não estiver íntegro.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma substituição de sub-rede do Gerenciador de tráfego

Para criar uma substituição de sub-rede do Gerenciador de tráfego, você pode usar CLI do Azure para adicionar as sub-redes para a substituição para o ponto de extremidade do Gerenciador de tráfego.

## <a name="azure-cli"></a>CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Atualize o ponto de extremidade do Gerenciador de tráfego com a substituição de sub-rede.
Use CLI do Azure para atualizar seu ponto de extremidade com [AZ Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

Você pode remover os intervalos de endereços IP executando a [atualização de ponto de extremidade AZ Network Traffic-Manager](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) com a opção **--Remove** .

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager.

Saiba mais sobre o [método de roteamento de tráfego de sub-rede](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)