---
title: Configure regras de equilíbrio de carga e saída utilizando o Azure CLI
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar regras de equilíbrio de carga e saída num Balancer de Carga Padrão utilizando o ClI Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 7230b0c2b80137b068bbeacf43ab2133491a69b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225475"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Configurar o balanceamento de carga e as regras de saída no Balanceador de Carga Standard com a CLI do Azure

Este quickstart mostra-lhe como configurar as regras de saída no Balancer de Carga Padrão utilizando o Azure CLI.  

Quando terminar, o recurso Load Balancer contém duas extremidades frontais e regras associadas a eles: uma para entrada e outra para saída.  Cada frontend tem uma referência a um endereço IP público e este cenário usa um endereço IP público diferente para entrada versus saída.   A regra de equilíbrio da carga fornece apenas o equilíbrio da carga de entrada e a regra de saída controla o NAT de saída previsto para o VM.  Este quickstart utiliza duas piscinas de backend separadas, uma para entrada e outra para saída, para ilustrar a capacidade e permitir flexibilidade para este cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado *myresourcegroupoutbound* no local *eastus2:*

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Criar a rede virtual
Criar uma rede virtual chamada *myvnetoutbound* com uma subnet chamada *mysubnetoutbound* no *myresourcegroupoutbound* usando a criação de [rede az vnet](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Criar endereço IP público de entrada 

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. Um Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Use a [rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um endereço IP público padrão chamado *mypublicipinbound* in *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Criar endereço IP público de saída 

Crie um endereço IP padrão para a configuração de saída frontal do Balancer de Carga utilizando a criação de ip [público-ip](https://docs.microsoft.com/cli/azure/network/public-ip)da rede Az .

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Criar um Balanceador de Carga do Azure

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
  - Um IP frontal que recebe o tráfego de rede de entrada no equilibrador de carga.
  - Uma piscina de backend onde o IP frontend envia o tráfego de rede equilibrado de carga.
  - Uma piscina de backend para conectividade de saída. 
  - Uma sonda de saúde que determina a saúde dos casos de VM de backend.
  - Uma regra de entrada do equilibrador de carga que define como o tráfego é distribuído para os VMs.
  - Uma regra de saída do equilibrador de carga que define como o tráfego é distribuído a partir dos VMs.

### <a name="create-load-balancer"></a>Criar balanceadores de carga

Crie um Balancer de Carga com o endereço IP de entrada utilizando a [az network lb criar](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) *lb* que inclui uma configuração IP frontal de entrada e uma piscina de backend *bepoolinbound* que está associada com o endereço IP público *mypublicipinbound* que você criou no passo anterior.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Criar piscina de saída

Crie um conjunto adicional de endereços backend para definir a conectividade de saída para um conjunto de VMs com [az rede lb endereço-pool criar](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) com o nome *bepooloutbound*.  A criação de uma piscina separada de saída proporciona a máxima flexibilidade, mas você pode omitir este passo e apenas usar a entrada de *entrada* também.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Criar IP frontend de saída
Crie a configuração IP frontal de saída para o Balancer de carga com [az rede lb frontend-ip criação](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) que inclui e configuração IP frontal de saída chamada *myfrontendoutbound* que está associada ao endereço IP público *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Criar sonda de saúde

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Crie uma sonda de estado de funcionamento com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorizar o estado de funcionamento das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Criar regra de equilíbrio de carga

Uma regra do equilíbrio de carga define a configuração IP frontal para o tráfego de entrada e a piscina de backend para receber o tráfego, juntamente com a fonte e a porta de destino necessárias. Crie uma regra de equilíbrio de carga *myinboundlbrule* com a regra lb [da rede az criar](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para ouvir a porta 80 na piscina frontal *myfrontendinbound* e enviar tráfego de rede equilibrado para a *piscina* de endereços de backend também usando a porta 80. 

>[!NOTE]
>Esta regra de equilíbrio de carga desativa a saída automática (S)NAT em resultado desta regra com o parâmetro --desactivação-snat. O NAT de saída só é fornecido pela regra de saída.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Criar regra de saída

Uma regra de saída define o IP público frontend, representado pela frontend *myfrontendoutbound*, que será usada para todo o tráfego nat de saída, bem como para a piscina de backend a que esta regra se aplica.  Crie uma *regra* de saída para a tradução de rede de saída de todas as máquinas virtuais (configurações IP NIC) em piscina de backend *bepool.*  O comando abaixo também altera o tempo de saída de 4 para 15 minutos e atribui 10000 portas SNAT em vez de 1024.  Reveja [as regras de saída](https://aka.ms/lboutboundrules) para mais detalhes.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Se não quiser utilizar uma piscina separada de saída, pode alterar o argumento da piscina de endereços no comando anterior para especificar a sua *ligação.*  Recomendamos que utilize piscinas separadas para flexibilidade e legibilidade da configuração resultante.

Neste ponto, pode proceder à adição dos seus VM's à piscina de backend *bepoolinbound* __e__ *bepooloutbound* atualizando a configuração IP dos respetivos recursos NIC utilizando a [az network nic ip-config address-pool](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou o Standard Load Balancer, configurado as regras de tráfego de balanceadores de carga de entrada, configurado e sonda de saúde para os VMs na piscina de backend. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
