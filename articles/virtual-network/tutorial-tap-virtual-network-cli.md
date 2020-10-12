---
title: Criar, alterar ou apagar um VNet TAP - Azure CLI
description: Saiba como criar, alterar ou eliminar uma rede virtual TAP utilizando o Azure CLI.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87488844"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Trabalhar com uma rede virtual TAP utilizando o Azure CLI

A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da rede de máquinas virtuais para um coletor de pacotes de rede ou uma ferramenta de análise. A ferramenta de coletor ou de análise é fornecida por um parceiro [de aparelho virtual de rede.](https://azure.microsoft.com/solutions/network-appliances/) Para obter uma lista de soluções parceiras validadas para trabalhar com a rede virtual TAP, consulte [soluções parceiras.](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) 

## <a name="create-a-virtual-network-tap-resource"></a>Criar um recurso TAP de rede virtual

Leia [os pré-requisitos](virtual-network-tap-overview.md#prerequisites) antes de criar um recurso TAP de rede virtual. Pode executar os comandos que seguem no [Azure Cloud Shell,](https://shell.azure.com/bash)ou executando a interface de linha de comando Azure (CLI) a partir do seu computador. O Azure Cloud Shell é uma concha interativa gratuita, que não requer a instalação do CLI Azure no seu computador. Tem de iniciar seduca no Azure com uma conta que tenha as [permissões](virtual-network-tap-overview.md#permissions)adequadas. Este artigo requer a versão Azure CLI 2.0.46 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). A rede virtual TAP encontra-se atualmente disponível como extensão. Para instalar a extensão é necessário executar `az extension add -n virtual-network-tap` . Se estiver a executar o Azure CLI localmente, também precisa de correr `az login` para criar uma ligação com o Azure.

1. Recupere o ID da sua subscrição numa variável que é usada num passo posterior:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Desaprova o id de subscrição que utilizará para criar um recurso TAP de rede virtual.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Re-registrar o ID de subscrição que utilizará para criar um recurso TAP de rede virtual. Se tiver um erro de registo quando criar um recurso TAP, execute o seguinte comando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Se o destino para a rede virtual TAP for a interface de rede no aparelho virtual de rede para o colecionador ou ferramenta de análise -

   - Recupere a configuração IP da interface de rede do aparelho virtual da rede numa variável que é usada num passo posterior. O ID é o ponto final que vai agregar o tráfego da TAP. O exemplo a seguir recupera o ID da configuração *IP ipconfig1* para uma interface de rede chamada *myNetworkInterface,* num grupo de recursos chamado *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Crie a rede virtual TAP na região de Westcentralus azure utilizando o ID da configuração IP como destino e uma propriedade portuária opcional. A porta especifica a porta de destino na configuração IP da interface de rede onde o tráfego TAP será recebido:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Se o destino para a rede virtual TAP for um equilibrador interno de carga azul:
  
   - Recupere a configuração IP frontal do balançador interno de carga Azure numa variável que é usada num passo posterior. O ID é o ponto final que vai agregar o tráfego da TAP. O exemplo a seguir recupera o ID da configuração IP frontal *de frontendipconfig1* para um balanceador de carga chamado *myInternalLoadBalancer,* num grupo de recursos chamado *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Crie a rede virtual TAP utilizando o ID da configuração IP frontend como destino e uma propriedade portuária opcional. A porta especifica a porta de destino na configuração IP frontal onde o tráfego TAP será recebido :  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Confirmar a criação da rede virtual TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Adicione uma configuração TAP a uma interface de rede

1. Recupere o ID de um recurso tap de rede virtual existente. O exemplo a seguir recupera uma rede virtual chamada *myTap* num grupo de recursos chamado *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Criar uma configuração TAP na interface de rede da máquina virtual monitorizada. O exemplo a seguir cria uma configuração TAP para uma interface de rede chamada *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Confirmar a criação da configuração TAP:

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

## <a name="list-virtual-network-taps-in-a-subscription"></a>Liste TAPs de rede virtual em uma subscrição

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Eliminar uma rede virtual TAP num grupo de recursos

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
