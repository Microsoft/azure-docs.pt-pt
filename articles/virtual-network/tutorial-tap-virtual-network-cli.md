---
title: Criar, alterar ou eliminar um VNet TAP - Azure CLI
description: Aprenda a criar, alterar ou eliminar uma rede virtual TAP utilizando o Azure CLI.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 56288a65dc9e5b12a12393965b9670e394146181
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234954"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Trabalhar com uma rede virtual TAP utilizando o Azure CLI

A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da sua rede virtual de máquinas para uma ferramenta de coleção de pacotes de rede ou de análise. A ferramenta de coleção ou análise é fornecida por um parceiro de aparelho virtual de [rede.](https://azure.microsoft.com/solutions/network-appliances/) Para obter uma lista de soluções parceiras validadas para trabalhar com a rede virtual TAP, consulte [soluções parceiras.](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) 

## <a name="create-a-virtual-network-tap-resource"></a>Criar uma rede virtual de recursos TAP

Leia [os pré-requisitos](virtual-network-tap-overview.md#prerequisites) antes de criar um recurso TAP de rede virtual. Pode executar os comandos que se seguem na [Membrana Nuvem Azure,](https://shell.azure.com/bash)ou executando a interface de linha de comando Azure (CLI) a partir do seu computador. O Azure Cloud Shell é uma concha interativa gratuita, que não requer a instalação do Azure CLI no seu computador. Deve inscrever-se no Azure com uma conta que tenha as [permissões](virtual-network-tap-overview.md#permissions)adequadas. Este artigo requer a versão Azure CLI 2.0.46 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). A rede virtual TAP encontra-se atualmente disponível como uma extensão. Para instalar a extensão `az extension add -n virtual-network-tap`que precisa de executar. Se estiver a executar o Azure CLI `az login` localmente, também precisa de correr para criar uma ligação com o Azure.

1. Recupere a identificação da sua subscrição numa variável que é usada num passo posterior:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Detete o id de subscrição que utilizará para criar um recurso TAP de rede virtual.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Re-registe o ID de subscrição que utilizará para criar um recurso TAP de rede virtual. Se tiver um erro de registo quando criar um recurso TAP, execute o seguinte comando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Se o destino da rede virtual TAP for a interface de rede no aparelho virtual da rede para colecionador ou ferramenta de análise -

   - Recupere a configuração IP da interface de rede virtual do aparelho da rede numa variável que é utilizada num passo posterior. O ID é o ponto final que irá agregar o tráfego da TAP. O exemplo seguinte recupera o ID da configuração *IP ipconfig1 para* uma interface de rede chamada *myNetworkInterface,* num grupo de recursos chamado *myResourceGroup:*

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Criar a rede virtual TAP na região azul-oeste utilizando o ID da configuração IP como destino e uma propriedade portuária opcional. A porta especifica a porta de destino na configuração IP da interface de rede onde o tráfego DAA será recebido:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Se o destino da rede virtual TAP for um equilibrador de carga interna azul:
  
   - Recupere a configuração IP frontal do equilibrador de carga interna Azure numa variável que é usada num passo posterior. O ID é o ponto final que irá agregar o tráfego da TAP. O exemplo seguinte recupera o ID da configuração IP frontal frontal *frontendipconfig1* para um balancer de carga chamado *myInternalLoadBalancer,* num grupo de recursos chamado *myResourceGroup:*

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Criar a rede virtual TAP utilizando o ID da configuração IP frontend como destino e uma propriedade portuária opcional. A porta especifica a porta de destino na configuração IP frontal onde o tráfego da TAP será recebido:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Confirme a criação da rede virtual TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Adicione uma configuração TAP a uma interface de rede

1. Recupere a identificação de um recurso TAP da rede virtual existente. O exemplo seguinte recupera uma rede virtual tap chamada *myTap* num grupo de recursos chamado *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Criar uma configuração TAP na interface de rede da máquina virtual monitorizada. O exemplo seguinte cria uma configuração TAP para uma interface de rede chamada *myNetworkInterface:*

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Confirme a criação da configuração TAP:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Eliminar a configuração TAP numa interface de rede

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Liste tAPs de rede virtual numa subscrição

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Eliminar uma rede virtual TAP num grupo de recursos

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
