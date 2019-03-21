---
title: Criar, alterar ou eliminar uma rede virtual TAP - CLI do Azure | Documentos da Microsoft
description: Saiba como criar, alterar ou eliminar uma rede virtual com a CLI do Azure de TOQUE.
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
ms.openlocfilehash: 3d95a9ea555cceda82530eb5c487eeb993c1a678
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188565"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Trabalhar com uma rede virtual TOQUE com a CLI do Azure

TOQUE (ponto de acesso de Terminal) de rede virtual do Azure permite-lhe para o stream continuamente o tráfego de rede de máquina virtual para uma ferramenta de recoletor ou análise da pacotes de rede. A ferramenta de recoletor ou análise é fornecida por um [aplicação de rede virtual](https://azure.microsoft.com/solutions/network-appliances/) parceiro. Para obter uma lista das soluções de parceiros são validadas para trabalhar com o teste de rede virtual, consulte [soluções de parceiros](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Criar uma rede virtual do recurso de TOQUE

Leia [pré-requisitos](virtual-network-tap-overview.md#prerequisites) antes de criar uma rede virtual do recurso de TOQUE. Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/bash), ou executando a interface de linha de comandos do Azure (CLI) do seu computador. O Azure Cloud Shell é um shell interativo gratuito, que não necessita de instalar a CLI do Azure no seu computador. Tem de iniciar sessão Azure com uma conta que tenha o adequado [permissões](virtual-network-tap-overview.md#permissions). Este artigo requer a versão 2.0.46 CLI do Azure ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). TESTE de rede virtual está atualmente disponível como uma extensão. Para instalar a extensão tem de executar `az extension add -n virtual-network-tap`. Se estiver a executar a CLI do Azure localmente, terá também de executar `az login` para criar uma ligação com o Azure.

1. Obter o ID da sua subscrição numa variável que é utilizada num passo posterior:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Defina o id de subscrição que irá utilizar para criar uma rede virtual do recurso de TOQUE.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Volte a registar o ID de subscrição que irá utilizar para criar uma rede virtual do recurso de TOQUE. Se obtiver um erro de registo quando cria um recurso de TOQUE, execute o seguinte comando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Se o destino para a rede virtual TOQUE é a interface de rede a aplicação de rede virtual para o recoletor ou a ferramenta de análise-

   - Obter a configuração de IP da interface de rede a aplicação de rede virtual numa variável que é utilizada num passo posterior. O ID é o ponto final que vai irá agregar o tráfego de TOQUE. O exemplo seguinte obtém o ID do *ipconfig1* configuração de IP para uma interface de rede com o nome *myNetworkInterface*, num grupo de recursos com o nome *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Crie o teste de rede virtual na região de westcentralus do azure com o ID da configuração do IP como o destino e uma propriedade de porta opcional. A porta Especifica a porta de destino na configuração de IP de interface de rede em que o tráfego de TOQUE será recebido:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Se o destino para a rede virtual TOQUE de um balanceador de carga interno do azure:
  
   - Obter a configuração de IP de front-end de Balanceador de carga interno do Azure para uma variável que é utilizada num passo posterior. O ID é o ponto final que vai irá agregar o tráfego de TOQUE. O exemplo seguinte obtém o ID do *frontendipconfig1* configuração de IP de front-end de um balanceador de carga com o nome *myInternalLoadBalancer*, num grupo de recursos com o nome  *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - Crie a rede virtual com o ID de configuração de IP Front-end como o destino e uma propriedade de porta opcional de TOQUE. A porta Especifica a porta de destino na configuração de IP de front-end onde o tráfego de TOQUE será recebido:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Confirme a criação da rede virtual TOQUE:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Adicionar uma configuração de TOQUE para uma interface de rede

1. Obter o ID de uma rede virtual existente recursos de TOQUE. O exemplo seguinte obtém uma rede virtual com o nome de TOQUE *myTap* num grupo de recursos com o nome *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Crie uma configuração de TOQUE na interface de rede da máquina virtual monitorizada. O exemplo seguinte cria uma configuração de TOQUE para uma interface de rede com o nome *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Confirme a criação da configuração do TOQUE:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Eliminar a configuração de TOQUE numa interface de rede

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Rede virtual da lista toca numa subscrição

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Eliminar uma rede virtual TOQUE num grupo de recursos

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
