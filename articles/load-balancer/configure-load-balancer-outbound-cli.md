---
title: Configurar regras de saída e balanceamento de carga no balanceador de carga Standard com a CLI do Azure | Documentos da Microsoft
description: Este artigo mostra como configurar regras de saída e balanceamento de carga no balanceador de carga Standard com a CLI do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jpconnock
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: dd549005776656bb755b78fc0ca73d24edcc77bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973750"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Configurar o balanceamento de carga e regras de saída no balanceador de carga Standard com a CLI do Azure

Este início rápido mostra como configurar regras de saída no balanceador de carga Standard com a CLI do Azure.  

Quando tiver terminado, o recurso do Balanceador de carga contém dois front-ends e regras associadas a eles: um para entrada e outro para saída.  Cada front-end tem uma referência para um endereço IP público e esta utiliza de cenário um endereço IP público diferente para entrada e saída.   A regra de balanceamento de carga fornece balanceamento de carga entrada única e a regra de saída controla o NAT de saída fornecido para a VM.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos chamado *myresourcegroupoutbound* no *eualeste2* localização:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Criar a rede virtual
Criar uma rede virtual denominada *myvnetoutbound* com uma sub-rede denominada *mysubnetoutbound* no *myresourcegroupoutbound* usando [az rede vnet criar](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Criar endereço IP público de entrada 

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. Um Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Uso [criar a rede de az public-ip](https://docs.microsoft.com/cli/azure/network/public-ip#create) para criar um endereço IP público Standard com o nome *mypublicipinbound* na *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Criar endereço IP público de saída 

Criar um endereço IP padrão para a configuração de saída de front-end do Balanceador de carga [criar a rede de az public-ip](https://docs.microsoft.com/cli/azure/network/public-ip#create) com o nome *mypublicipoutbound* no *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```


## <a name="create-azure-load-balancer"></a>Criar Balanceador de carga do Azure

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
  - Um IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  - Tráfego de rede com balanceamento de um conjunto de back-end onde o IP de front-end envia a carga.
  - uma sonda de estado de funcionamento que determina o estado de funcionamento das instâncias de VM de back-end.
  - Uma entrada Balanceador de carga que define como o tráfego é distribuído pelas VMS.
  - Uma saída Balanceador de carga que define como o tráfego é distribuído das VMs.

### <a name="create-load-balancer"></a>Criar Balanceador de carga

Criar um balanceador de carga com a entrada através de endereços IP [criar az rede lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) com o nome *lb* que inclui uma configuração de IP de front-end entrada com o nome *myfrontendinbound*, um conjunto de back-end designado *bepool* que está associado com o endereço IP público *mypublicipinbound* que criou no passo anterior.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepool \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-frontend-ip"></a>Criar endereço IP de front-end saída
Criar a configuração de IP de saída de front-end do Balanceador de carga com [criar az rede lb frontend-ip](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) que inclui e o nome de configuração de IP de front-end saída *myfrontendoutbound* ou seja associados para o endereço IP público *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para assegurar que podem enviar o tráfego de rede. A instância da máquina virtual com verificações de sonda com falha é removida do balanceador de carga até ficar novamente online e uma verificação de sonda determinar que está em bom estado. Crie uma sonda de estado de funcionamento com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) para monitorizar o estado de funcionamento das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Criar regra de balanceamento de carga

Uma regra de Balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o conjunto de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de Balanceador de carga *myinboundlbrule* com [criar regra de lb de rede de az](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) para escutar a porta 80 no conjunto de front-end *myfrontendinbound* e enviar tráfego de rede com balanceamento de carga para o conjunto de endereços de back-end *bepool* também através da porta 80. 

>[!NOTE]
>Esta regra de balanceamento de carga desativa automática saída (S) NAT devido a esta regra com o-- parâmetro disable-saída snat. NAT de saída só é fornecido pela regra de saída.

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
--backend-pool-name bepool \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Criar regra de saída

Uma regra de saída define o IP público de front-end, representado pelo front-end *myfrontendoutbound*, que será utilizado para todo o tráfego de saída da NAT, bem como o conjunto de back-end para o qual esta regra se aplique.  Criar uma regra de saída *myoutboundrule* tradução de rede de saída de todas as máquinas virtuais (configurações de IP de NIC) na *bepool* conjunto back-end.  O comando abaixo também altera o tempo de limite de inatividade saído de 4 para 15 minutos e aloca 10000 SNAT portas em vez de 1024.  Revisão [regras de saída](https://aka.ms/lboutboundrules) para obter mais detalhes.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepool
```
## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou o Balanceador de carga Standard, configuradas regras de tráfego de Balanceador de carga de entrada, configuradas e a sonda de estado de funcionamento para as VMs no conjunto de back-end. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

