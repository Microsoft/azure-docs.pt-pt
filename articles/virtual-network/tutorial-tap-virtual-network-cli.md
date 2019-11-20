---
title: Criar, alterar ou excluir um toque de VNet-CLI do Azure
description: Saiba como criar, alterar ou excluir um toque de rede virtual usando o CLI do Azure.
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
ms.openlocfilehash: 05ce45a52db2b8a47223023ce31b5591b2b97c37
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185402"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Trabalhar com uma rede virtual toque usando o CLI do Azure

O TAP (ponto de acesso ao terminal) da rede virtual do Azure permite transmitir continuamente o tráfego de rede da máquina virtual para uma ferramenta de análise ou coletor de pacotes de rede. O coletor ou a ferramenta de análise é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/) . Para obter uma lista de soluções de parceiros que são validadas para trabalhar com o toque de rede virtual, consulte [soluções de parceiros](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Criar um recurso tocar na rede virtual

Leia [pré-requisitos](virtual-network-tap-overview.md#prerequisites) antes de criar um recurso tocar na rede virtual. Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/bash)ou executando a CLI (interface de linha de comando) do Azure do seu computador. O Azure Cloud Shell é um shell interativo gratuito, que não requer a instalação do CLI do Azure no seu computador. Você deve entrar no Azure com uma conta que tenha as [permissões](virtual-network-tap-overview.md#permissions)apropriadas. Este artigo requer o CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). O toque de rede virtual está disponível atualmente como uma extensão. Para instalar a extensão, você precisa executar `az extension add -n virtual-network-tap`. Se você estiver executando o CLI do Azure localmente, também precisará executar `az login` para criar uma conexão com o Azure.

1. Recupere a ID da sua assinatura em uma variável que é usada em uma etapa posterior:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Defina a ID da assinatura que será usada para criar um recurso de toque de rede virtual.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registre novamente a ID de assinatura que você usará para criar um recurso de toque de rede virtual. Se você receber um erro de registro ao criar um recurso TAP, execute o seguinte comando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Se o destino para o toque de rede virtual for o adaptador de rede na ferramenta de solução de virtualização de rede para coletor ou análise-

   - Recupere a configuração de IP da interface de rede da solução de virtualização de rede em uma variável usada em uma etapa posterior. A ID é o ponto de extremidade que agregará o tráfego de toque. O exemplo a seguir recupera a ID da configuração de IP *ipconfig1* para uma interface de rede chamada *mynetworkinterface*, em um grupo de recursos chamado *MyResource*Group:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Crie o toque de rede virtual na região westcentralus do Azure usando a ID da configuração de IP como o destino e uma propriedade de porta opcional. A porta especifica a porta de destino na configuração de IP da interface de rede em que o toque do tráfego será recebido:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Se o destino para o toque de rede virtual for um balanceador de carga interno do Azure:
  
   - Recupere a configuração de IP de front-end do balanceador de carga interno do Azure em uma variável que é usada em uma etapa posterior. A ID é o ponto de extremidade que agregará o tráfego de toque. O exemplo a seguir recupera a ID da configuração de IP de front-end *frontendipconfig1* para um balanceador de carga chamado *myInternalLoadBalancer*, em um grupo de recursos chamado *MyResource*Group:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - Crie o toque de rede virtual usando a ID da configuração de IP de front-end como o destino e uma propriedade de porta opcional. A porta especifica a porta de destino na configuração de IP de front-end em que o toque do tráfego será recebido:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Confirme a criação do toque da rede virtual:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Adicionar uma configuração do TAP a uma interface de rede

1. Recupere a ID de um recurso de toque de rede virtual existente. O exemplo a seguir recupera um toque de rede virtual chamado *myTap* em um grupo de recursos chamado *MyResource*Group:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Crie uma configuração do TAP na interface de rede da máquina virtual monitorada. O exemplo a seguir cria uma configuração de toque para uma interface de rede chamada *mynetworkinterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Confirme a criação da configuração de toque:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Excluir a configuração de toque em uma interface de rede

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Listar toques de rede virtual em uma assinatura

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Excluir um toque de rede virtual em um grupo de recursos

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
