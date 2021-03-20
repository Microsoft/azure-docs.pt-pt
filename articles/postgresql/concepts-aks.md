---
title: Ligue-se ao Serviço Azure Kubernetes - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como ligar o Serviço Azure Kubernetes (AKS) com a Base de Dados Azure para PostgreSQL - Servidor Único
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b7da2fcc1310f03f894e048089658f25be3a149
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708854"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Ligação do Serviço Azure Kubernetes e base de dados Azure para PostgreSQL - Servidor Único

O Azure Kubernetes Service (AKS) fornece um cluster kubernetes gerido que pode usar no Azure. Abaixo estão algumas opções a considerar ao usar a AKS e a Azure Database para postgreSQL juntos para criar uma aplicação.


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


## <a name="connection-pooling"></a>Agrupamento de conexão
Um pooler de ligação minimiza o custo e o tempo associados à criação e encerramento de novas ligações à base de dados. A piscina é uma coleção de conexões que podem ser reutilizadas. 

Existem vários poolers de conexão que pode usar com PostgreSQL. Um deles é [PgBouncer.](https://pgbouncer.github.io/) No Registo de Contentores da Microsoft, fornecemos um PgBouncer leve e contentorizado que pode ser usado num sidecar para juntar ligações de AKS a Azure Database para PostgreSQL. Visite a página do [estivador](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) para aprender a aceder e usar esta imagem. 


## <a name="next-steps"></a>Passos seguintes
-  [Criar um cluster do Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
