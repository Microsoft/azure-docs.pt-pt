---
title: Configurar regras de equilíbrio e saída de carga utilizando O Azure CLI
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar as regras de equilíbrio e saída de carga num Balanceador de Carga Padrão utilizando o Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 2b326c6c58b4685b6e73d0f9a641a2f90807d705
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84803754"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Configurar o balanceamento de carga e as regras de saída no Balanceador de Carga Standard com a CLI do Azure

Este quickstart mostra-lhe como configurar as regras de saída no Balanceador de Carga Padrão usando O Azure CLI.  

Quando terminar, o recurso Load Balancer contém dois frontends e regras associadas: um para entrada e outro para saída.  Cada frontend tem uma referência a um endereço IP público e este cenário usa um endereço IP público diferente para entrada versus saída.   A regra de equilíbrio de carga proporciona apenas o equilíbrio da carga de entrada e a regra de saída controla o NAT de saída previsto para o VM.  Este quickstart utiliza duas piscinas de backend separadas, uma para entrada e outra para saída, para ilustrar a capacidade e permitir flexibilidade para este cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.28 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado *myresourcegroupoutbound* na localização *eastus2:*

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Criar a rede virtual
Crie uma rede virtual chamada *myvnetoutbound* com uma sub-rede chamada *mysubnetoutbound* no *myresourcegroupoutbound* usando [a rede az vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Criar endereço IP público de entrada 

Para aceder à sua aplicação Web na Internet, precisa de um endereço IP público para o balanceador de carga. Um Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Utilize [a rede az public-ip criar](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um endereço IP público standard chamado *mypublicipinbound* in *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Criar endereço IP público de saída 

Crie um endereço IP standard para a configuração frontal do Balancer de Carga utilizando [a criação de ip público da rede Az](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Criar um Balanceador de Carga do Azure

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:
  - Um IP frontal que recebe o tráfego de rede de entrada no equilibrador de carga.
  - Uma piscina de backend onde o IP frontal envia o tráfego de rede equilibrado de carga.
  - Uma piscina de backend para conectividade de saída. 
  - Uma sonda de saúde que determina a saúde dos casos de VM backend.
  - Uma regra de entrada do balançador de carga que define como o tráfego é distribuído para os VMs.
  - Uma regra de saída do equilibrador de carga que define como o tráfego é distribuído a partir dos VMs.

### <a name="create-load-balancer"></a>Criar balanceador de carga

Crie um Balanceador de Carga com o endereço IP de entrada utilizando [a rede LB de rede az, que](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) inclui uma configuração IP frontal de entrada e uma rede *de* pool de backend que está associada ao endereço IP público *mypublicipinbound* que criou no passo anterior. *lb*

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

Crie um conjunto de endereços de backend adicional para definir a conectividade de saída para um conjunto de VMs com [rede az lb address-pool criar](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) com o nome *bepooloutbound*.  A criação de uma piscina separada de saída proporciona a máxima flexibilidade, mas pode omitir este passo e utilizar apenas a entrada *de entrada.*

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Criar IP frontend de saída
Crie a configuração IP frontend de saída para o Balanceador de Carga com [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) que inclui e outbound configuração IP de frontend nomeado *myfrontendoutbound* que está associado ao endereço IP público *mypublicipoutbound*

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

Uma regra do balançador de carga define a configuração IP frontal para o tráfego de entrada e a piscina de backend para receber o tráfego, juntamente com a porta de origem e destino necessária. Crie uma regra de equilíbrio de carga *myinboundlbrule* com [regra lb de rede az para](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) ouvir a porta 80 na piscina frontal *myfrontendinbound* e enviar tráfego de rede equilibrado de carga para a *piscina* de endereços backend também usando a porta 80. 

>[!NOTE]
>Esta regra de equilíbrio de carga desativa a saída automática (S)NAT como resultado desta regra com o parâmetro --desactivação-out-snat. O NAT de saída só é fornecido pela regra de saída.

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

Uma regra de saída define o FRONTend public IP, representado pela linha *frontal my frontendoutout,* que será usada para todo o tráfego NAT de saída, bem como para a piscina de backend a que esta regra se aplica.  Crie uma regra de saída *myoutboundrule* para tradução de rede de saída de todas as máquinas virtuais (configurações NIC IP) na piscina de backend *bepool.*  O comando abaixo também altera o tempo limite de saída de 4 para 15 minutos e atribui 10000 portas SNAT em vez de 1024.  Reveja [as regras de saída](https://aka.ms/lboutboundrules) para mais detalhes.

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

Se não quiser utilizar uma piscina separada, pode alterar o argumento do pool de endereços no comando anterior para especificar a *entrada de bepoolinbound.*  Recomendamos a utilização de piscinas separadas para flexibilidade e legibilidade da configuração resultante.

Neste ponto, você pode proceder com a adição dos seus VM's à piscina de backend *bepoolinbound* __e__ *bepooloutbound,* atualizando a configuração IP dos respetivos recursos NIC usando [a az rede nic ip-config endereço-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Próximos passos
Neste artigo, criou o Standard Load Balancer, configurado tanto as regras de tráfego do balanceador de entrada, configuradas e sonda de saúde para os VMs na piscina de backend. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
