---
title: Ligue-se ao Serviço Azure Kubernetes - Base de Dados Azure para o MySQL
description: Saiba como ligar o Serviço Azure Kubernetes com a Base de Dados Azure para o MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 712bf702ba355ec3b2ca6184c33da8489f8a178e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86519869"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Ligação do Serviço Azure Kubernetes e base de dados Azure para o MySQL

O Azure Kubernetes Service (AKS) fornece um cluster kubernetes gerido que pode usar no Azure. Abaixo estão algumas opções a considerar ao usar a AKS e a Azure Database para o MySQL juntos para criar uma aplicação.


## <a name="accelerated-networking"></a>Redes aceleradas
Utilize VMs subjacentes acelerados ativados em rede no seu cluster AKS. Quando a rede acelerada é ativada num VM, há uma menor latência, nervosismo reduzido e menor utilização do CPU no VM. Saiba mais sobre como funciona a rede acelerada, as versões de SISTEMA suportadas e exemplos de VM suportados para [o Linux.](../virtual-network/create-vm-accelerated-networking-cli.md)

A partir de novembro de 2018, a AKS suporta a rede acelerada nos casos de VM suportados. A rede acelerada é ativada por padrão em novos clusters AKS que utilizam esses VMs.

Pode confirmar se o seu cluster AKS acelerou a rede:
1. Vá ao portal Azure e selecione o seu cluster AKS.
2. Selecione o separador Properties (Propriedades).
3. Copie o nome do Grupo de **Recursos de Infraestrutura.**
4. Utilize a barra de pesquisa do portal para localizar e abrir o grupo de recursos de infraestrutura.
5. Selecione um VM nesse grupo de recursos.
6. Vá ao separador **de rede** do VM.
7. Confirme se **a rede acelerada** está 'Activada'.

Ou através do CLI Azure utilizando os seguintes dois comandos:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A saída será o grupo de recursos gerado que a AKS cria contendo a interface de rede. Pegue no nome "nodeResourceGroup" e use-o no comando seguinte. **EnableAcceleratedNetworking** será verdadeiro ou falso:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="next-steps"></a>Passos seguintes
- [Criar um cluster do Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
- Saiba como instalar o [WordPress a partir de um gráfico helm usando a BASE de Dados OSBA e Azure para o MySQL](../aks/integrate-azure.md)
