---
title: Ligue ao Serviço Azure Kubernetes - Base de Dados Azure para MySQL
description: Saiba mais sobre a ligação do Serviço Azure Kubernetes com a Base de Dados Azure para o MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537283"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Serviço Azure Kubernetes e Base de Dados Azure para MySQL

O Azure Kubernetes Service (AKS) fornece um cluster kubernetes gerido que pode utilizar em Azure. Abaixo estão algumas opções a considerar ao usar a AKS e a Base de Dados Azure para o MySQL em conjunto para criar uma aplicação.


## <a name="accelerated-networking"></a>Redes aceleradas
Utilize VMs subjacentes acelerados ativados por rede no seu cluster AKS. Quando a rede acelerada é ativada num VM, há menor latência, nervosismo reduzido e diminuição da utilização do CPU no VM. Saiba mais sobre como funciona o networking acelerado, as versões de SO suportadas e os casos de VM suportados para [linux](../virtual-network/create-vm-accelerated-networking-cli.md).

A partir de novembro de 2018, a AKS apoia a aceleração da rede nos casos de VM suportados. A rede acelerada é ativada por padrão em novos clusters AKS que utilizam esses VMs.

Pode confirmar se o seu cluster AKS acelerou a rede:
1. Vá ao portal Azure e selecione o seu cluster AKS.
2. Selecione o separador Properties (Propriedades).
3. Copie o nome do Grupo de Recursos de **Infraestruturas.**
4. Utilize a barra de pesquisa do portal para localizar e abrir o grupo de recursos de infraestrutura.
5. Selecione um VM nesse grupo de recursos.
6. Vá ao separador de **rede** da VM.
7. Confirme se a **rede acelerada** está ativada.

Ou através do Azure CLI utilizando os seguintes dois comandos:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A saída será o grupo de recursos gerados que o AKS cria contendo a interface de rede. Pegue no nome "nodeResourceGroup" e use-o no comando seguinte. **EnableAcceleratedNetworking** será verdadeiro ou falso:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Abrir o Open Service Broker para o Azure 
[O Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) permite-lhe fornecer serviços Azure diretamente da Kubernetes ou Cloud Foundry. É uma implementação da [API](https://www.openservicebrokerapi.org/) de Corretor de Serviço aberto para o Azure.

Com a OSBA, pode criar uma Base de Dados Azure para o servidor MySQL e ligá-lo ao seu cluster AKS utilizando a língua nativa de Kubernetes. Saiba como utilizar a OSBA e a Base de Dados Azure para mySQL juntas na [página OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Passos seguintes
- [Criar um cluster de serviço Azure Kubernetes](../aks/kubernetes-walkthrough.md)
- Saiba como instalar o WordPress a partir de um gráfico helm utilizando a BASE de [Dados OSBA e Azure para mySQL](../aks/integrate-azure.md)
