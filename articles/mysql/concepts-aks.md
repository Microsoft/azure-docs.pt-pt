---
title: Ligar o serviço Kubernetes do Azure (AKS) com base de dados do Azure para MySQL
description: Saiba mais sobre como ligar o serviço Kubernetes do Azure com a base de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/28/2018
ms.openlocfilehash: d9f2e26a2bc89329ca9038c666c0d960289e2670
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790526"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Ligar o serviço Kubernetes do Azure e base de dados do Azure para MySQL

O Azure Kubernetes Service (AKS) fornece um cluster de Kubernetes gerido, que pode utilizar no Azure. Seguem-se algumas opções a considerar ao utilizar o AKS e base de dados do Azure para MySQL em conjunto para criar uma aplicação.


## <a name="accelerated-networking"></a>Redes aceleradas
Utilize o funcionamento de rede subjacentes as VMs aceleradas no cluster do AKS. Quando o funcionamento em rede acelerado está ativado numa VM, existe uma latência mais baixa, menor interferência e menor utilização da CPU na VM. Saber mais sobre como acelerada funciona de rede, as versões suportadas do sistema operacional e suportada para as instâncias de VM [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

A partir de Novembro de 2018, o AKS suporta redes aceleradas nessas instâncias VM suportadas. Funcionamento em rede acelerado é ativado por predefinição em novos clusters do AKS que usam essas VMs.

Pode confirmar se o cluster do AKS tenha o accelerated networking:
1. Aceda ao portal do Azure e selecione o cluster do AKS.
2. Selecione o separador de propriedades.
3. Copie o nome do **grupo de recursos de infraestrutura**.
4. Use a barra de pesquisa de portal para localizar e abrir o grupo de recursos de infraestrutura.
5. Selecione uma VM no grupo de recursos.
6. Vá para a VM **redes** separador.
7. Confirme se **Accelerated networking** está "ativado."

Ou através da CLI do Azure utilizando os dois comandos seguintes:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
O resultado será o grupo de recursos gerada que AKS cria que contém a interface de rede. Assumem o nome "nodeResourceGroup" e utilizá-la no comando seguinte. **EnableAcceleratedNetworking** qualquer um será VERDADEIRO ou FALSO:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Abrir o Open Service Broker para o Azure 
[Abra o Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) permite-lhe aprovisionar serviços do Azure diretamente a partir de Kubernetes ou Cloud Foundry. É uma [API do Mediador de serviço aberta](https://www.openservicebrokerapi.org/) implementação para o Azure.

Com o OSBA, pode criar uma base de dados do Azure para o servidor MySQL e vinculá-lo ao seu cluster do AKS com a linguagem nativa dos Kubernetes. Saiba mais sobre como utilizar OSBA e base de dados do Azure para MySQL em conjunto no [página do OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Passos Seguintes
- [Criar um cluster do Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
- Saiba como [instalar o WordPress a partir de um gráfico do Helm com OSBA e base de dados do Azure para MySQL](../aks/integrate-azure.md)
