---
title: 'Tutorial: Criar um equilibrador de carga cross-region usando Azure CLI'
titleSuffix: Azure Load Balancer
description: Começa com este tutorial implantando um Azure Load Balancer cross-region usando O Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791918"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Tutorial: Criar um balanceador de carga azure cross-region usando Azure CLI

Um equilibrador de carga transversal garante que um serviço está disponível globalmente em várias regiões de Azure. Se uma região falhar, o tráfego é encaminhado para o próximo e mais próximo e saudável equilibrador regional de carga.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar balanceador de carga entre regiões.
> * Crie uma regra de balanceador de carga.
> * Crie uma piscina de backend contendo dois equilibradores de carga regionais.
> * Teste o equilibrador de carga.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Dois  balançadores de carga sku Azure standard com piscinas de backend implantadas em duas regiões Azure diferentes.
    - Para obter informações sobre a criação de um equilibrador de carga padrão regional e máquinas virtuais para piscinas de backend, consulte [Quickstart: Criar um equilibrador de carga público para carregar VMs de equilíbrio utilizando O Azure CLI](quickstart-load-balancer-standard-public-cli.md).
        - Apeia o nome dos equilibradores de carga e das máquinas virtuais de cada região com um **-R1** e **-R2**. 
- Azure CLI instalado localmente ou Azure Cloud Shell.

Se optar por instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Inscreva-se no Azure CLI:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Criar balanceador de carga entre regiões

Nesta secção, você vai criar um equilibrador de carga transversal, endereço IP público e regra de equilíbrio de carga.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [a criação de grupo az:](/cli/azure/group#az_group_create)

* Denominado **myResourceGroupLB-CR**.
* Na **localização oeste.**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Criar um equilibrador de carga transversal com [a criação de rede az cross-region-lb:](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create)

* Denominado **myLoadBalancer-CR**.
* Uma piscina frontal chamada **myFrontEnd-CR.**
* Uma piscina de backend chamada **myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra do balançador de carga define:

* Configuração IP frontend para o tráfego de entrada.
* O backend IP pool para receber o tráfego.
* A fonte necessária e o porto de destino. 

Crie uma regra do balançador de carga com [a regra de cruzamento de regiões-lb da rede az criar:](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create)

* Nomeado **myHTTPRule-CR**
* Ouvir no **Porto 80** na piscina frontal **myFrontEnd-CR**.
* Envio de tráfego de rede equilibrado de carga para a piscina de endereço de backend **myBackEndPool-CR** usando **a Porta 80**. 
* Protocolo **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Criar conjunto de back-end

Nesta secção, você adicionará dois equilibradores de carga padrão regionais ao pool de backend do equilibrador de carga cross-region.

> [!IMPORTANT]
> Para completar estes passos, certifique-se de que dois equilibradores de carga regionais com pools de backend foram implantados na sua subscrição.  Para obter mais informações, consulte **[Quickstart: Criar um equilibrador de carga pública para carregar VMs de equilíbrio utilizando O Azure CLI](quickstart-load-balancer-standard-public-cli.md)**.

### <a name="add-the-regional-frontends-to-load-balancer"></a>Adicione os frontends regionais ao equilibrador de carga

Nesta secção, você colocará as identidades de recursos de dois balançadores de carga regionais em variáveis.  Em seguida, você usará as variáveis para adicionar os frontends ao conjunto de endereços de backend do equilibrador de carga cross-region.

Recupere os IDs de recursos com [o show frontend-ip da rede Az](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show)lb .

Utilize [o endereço de piscina de endereços cross-region-lb](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) da rede az para adicionar os frontends que colocou em variáveis no pool de backend do equilibrador de carga de região cruzada:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta secção, você vai testar o equilibrador de carga cross-region. Você vai ligar ao endereço IP público em um navegador web.  Você vai parar as máquinas virtuais em uma das piscinas regionais de backend balancer de carga e observar o failover.

1. Para obter o endereço IP público do esquilibrador de carga, utilize [o programa público-ip da rede Az:](/cli/azure/network/public-ip#az_network_public_ip_show)

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

3. Pare as máquinas virtuais na piscina de backend de um dos esquilibradores de carga regionais.

4. Refresque o navegador web e observe o failover da ligação ao outro equilibrador regional de carga.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize o comando de eliminação do [grupo AZ](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o equilibrador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

* Criou um equilibrador de carga entre regiões.
* Criou uma regra de equilíbrio de carga.
* Adicionou equilibradores de carga regionais ao pool de backend do equilibrador de carga entre regiões.
* Testei o equilibrador de carga.

Avance para o próximo artigo para aprender a...
> [!div class="nextstepaction"]
> [Balancear carga de VMs por zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
