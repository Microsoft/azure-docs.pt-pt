---
title: Criar um grupo de segurança de rede (clássico) com a CLI 1.0 do Azure | Documentos da Microsoft
description: Saiba como criar e implementar um grupo de segurança de rede (clássico) com a CLI 1.0 do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5468801e56849498d712f51e71cfb31bf068398a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696623"
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Criar um grupo de segurança rede (clássico) com a CLI 1.0 do Azure
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [criar NSGs no modelo de implementação do Resource Manager](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Os seguintes comandos da CLI do Azure de exemplo esperam um ambiente simples já criado com base no cenário. Se quiser executar os comandos à medida que são apresentadas neste documento, primeiro crie o ambiente de teste pela [criar uma VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Crie um NSG para a sub-rede de front-end

1. Se nunca tiver utilizado a CLI do Azure, veja [instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
2. Mudar para o modo clássico:

    ```azurecli
    azure config mode asm
    ```   

3. Crie um NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Crie uma regra de segurança que permite o acesso à porta 3389 (RDP) a partir da internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Crie uma regra que permite o acesso para a porta 80 (HTTP) a partir da internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Associe o NSG à sub-rede de front-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Criar o NSG para sub-rede de back-end

1. Crie o NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Crie uma regra que permite o acesso a porta 1433 (SQL) a partir da sub-rede de front-end:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Crie uma regra que nega o acesso à internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Associe o NSG à sub-rede de back-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```