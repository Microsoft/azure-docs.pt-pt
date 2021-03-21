---
title: Sub-rede Azure Traffic Manager sobrepõe-se usando Azure CLI | Microsoft Docs
description: Este artigo irá ajudá-lo a entender como a sub-rede do Gestor de Tráfego pode ser usada para substituir o método de encaminhamento de um perfil de Gestor de Tráfego para direcionar o tráfego para um ponto final baseado no endereço IP do utilizador final através da gama IP predefinida para mapeamentos de ponto final.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 2e289728c7fde9b98256d079d45067aba1d4d805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211333"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Sub-rede do Gestor de Tráfego sobrepõe-se usando Azure CLI

A substituição da sub-rede do Traffic Manager permite alterar o método de encaminhamento de um perfil.  A adição de uma sobreposição irá direcionar o tráfego com base no endereço IP do utilizador final com uma gama IP predefinida para o mapeamento do ponto final. 

## <a name="how-subnet-override-works"></a>Como funciona a sobreposição da sub-rede

Quando as sobreposições de sub-rede são adicionadas a um perfil de gestor de tráfego, o Traffic Manager verificará primeiro se existe uma substituição da sub-rede para o endereço IP do utilizador final. Se um for encontrado, a consulta de DNS do utilizador será direcionada para o ponto final correspondente.  Se não for encontrado um mapeamento, o Gestor de Tráfego recorrerá ao método de encaminhamento original do perfil. 

Os intervalos de endereços IP podem ser especificados como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereço (por exemplo, 1.2.3.4-5.6.7.8). As gamas IP associadas a cada ponto final devem ser únicas a esse ponto final. Qualquer sobreposição de gamas IP entre diferentes pontos finais fará com que o perfil seja rejeitado pelo Gestor de Tráfego.

Existem dois tipos de perfis de encaminhamento que suportam substituições de sub-rede:

* **Geographic** - Se o Traffic Manager encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, irá encaminhar a consulta para o ponto final, seja qual for a saúde do ponto final.
* **Desempenho** - Se o Traffic Manager encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, só irá encaminhar o tráfego para o ponto final se for saudável.  O Gestor de Tráfego vai recuar para o encaminhamento de desempenho heurístico se o ponto final de substituição da sub-rede não for saudável.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma sub-rede do Traffic Manager

Para criar uma sub-rede do Traffic Manager, pode utilizar o CLI do Azure para adicionar as sub-redes para a substituição do ponto final do Traffic Manager.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Atualize o ponto final do Gestor de Tráfego com a sobreposição da sub-rede.
Utilize o Azure CLI para atualizar o seu ponto final com [a atualização do ponto final do gestor de tráfego da rede Az](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

```azurecli-interactive
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

Pode remover as gamas de endereços IP executando a atualização do [ponto final do gestor de tráfego da rede Az](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) com a opção de **remoção.**

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [os métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager .

Conheça o [método de encaminhamento de tráfego sub-rede](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)