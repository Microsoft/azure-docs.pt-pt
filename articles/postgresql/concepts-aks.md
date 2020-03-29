---
title: Ligue ao Serviço Azure Kubernetes - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba mais sobre a ligação do Serviço Azure Kubernetes (AKS) com a Base de Dados Azure para PostgreSQL - Servidor Único
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769885"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Ligação do Serviço Azure Kubernetes e base de dados Azure para PostgreSQL - Servidor Único

O Azure Kubernetes Service (AKS) fornece um cluster kubernetes gerido que pode utilizar em Azure. Abaixo estão algumas opções a considerar ao utilizar a AKS e a Base de Dados Azure para postgreSQL em conjunto para criar uma aplicação.


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

Com a OSBA, pode criar uma Base de Dados Azure para servidor PostgreSQL e ligá-lo ao seu cluster AKS utilizando a língua nativa de Kubernetes. Saiba como utilizar a OSBA e a Base de Dados Azure para PostgreSQL juntos na [página OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Pooling de conexão
Um pooler de ligação minimiza o custo e o tempo associados à criação e fecho de novas ligações à base de dados. A piscina é uma coleção de conexões que podem ser reutilizadas. 

Existem vários poolers de ligação que pode usar com postgreSQL. Um deles é [o PgBouncer.](https://pgbouncer.github.io/) No Registo de Contentores da Microsoft, fornecemos um PgBouncer recipiente leve que pode ser usado num sidecar para ligar ligações de AKS a Azure Database para PostgreSQL. Visite a página do [docker hub](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) para aprender a aceder e usar esta imagem. 


## <a name="next-steps"></a>Passos seguintes
-  [Criar um cluster de serviço Azure Kubernetes](../aks/kubernetes-walkthrough.md)
