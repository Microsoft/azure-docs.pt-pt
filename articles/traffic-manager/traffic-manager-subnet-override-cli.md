---
title: Subnet Azure Traffic Manager sobreposição usando Azure CLI [ Microsoft Docs
description: Este artigo irá ajudá-lo a entender como a subposição da subnet do Gestor de Tráfego pode ser usada para substituir o método de encaminhamento de um perfil do Gestor de Tráfego para direcionar o tráfego para um ponto final baseado no endereço IP do utilizador final através da gama IP predefinida para mapeamentos de pontofinal.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938477"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Subnet do Gestor de Tráfego sobreposição usando O ClI Azure

A subposição da sub-rede do Gestor de Tráfego permite alterar o método de encaminhamento de um perfil.  A adição de uma sobreposição direcionará o tráfego com base no endereço IP do utilizador final com uma gama IP predefinida para mapeamento de ponto final. 

## <a name="how-subnet-override-works"></a>Como funciona a sobreposição da subnet

Quando as substituições da subnet são adicionadas a um perfil de gestor de tráfego, o Traffic Manager verificará primeiro se há uma subposição para o endereço IP do utilizador final. Se um for encontrado, a consulta de DNS do utilizador será direcionada para o ponto final correspondente.  Se não for encontrado um mapeamento, o Gestor de Tráfego recorrerá ao método original de encaminhamento do perfil. 

As gamas de endereços IP podem ser especificadas como gamas CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereços (por exemplo, 1.2.3.4-5.6.7.8). As gamas IP associadas a cada ponto final devem ser únicas a esse ponto final. Qualquer sobreposição de gamas IP entre diferentes pontos finais fará com que o perfil seja rejeitado pelo Gestor de Tráfego.

Existem dois tipos de perfis de encaminhamento que suportam sobreposições de sub-rede:

* **Geographic** - Se o Gestor de Tráfego encontrar uma sub-rede sobreposição para o endereço IP da consulta DNS, irá encaminhar a consulta para o ponto final qualquer que seja a saúde do ponto final.
* **Desempenho** - Se o Gestor de Tráfego encontrar uma subposição para o endereço IP da consulta DNS, só irá encaminhar o tráfego para o ponto final se for saudável.  O Gestor de Tráfego vai voltar ao heurístico de encaminhamento de desempenho se o ponto final de subposição não for saudável.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma sub-rede de gestor de tráfego

Para criar uma subposição de sub-rede do Gestor de Tráfego, pode utilizar o Azure CLI para adicionar as subredes para a sobreposição ao ponto final do Gestor de Tráfego.

## <a name="azure-cli"></a>CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Atualize o ponto final do Gestor de Tráfego com a sobreposição da sub-rede.
Utilize o Azure CLI para atualizar o seu ponto final com a [atualização do gestor de pontofinal](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)do gestor de tráfego da rede Az .

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

Pode remover as gamas de endereços IP executando a atualização de [ponto final do gestor de tráfego da rede Az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) com a opção **--remover.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre os métodos de [encaminhamento de tráfego](traffic-manager-routing-methods.md)do Gestor de Tráfego.

Conheça o [método de encaminhamento de tráfego subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)