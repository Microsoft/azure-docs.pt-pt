---
title: Conectar-se ao serviço kubernetes do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba mais sobre como conectar o AKS (serviço kubernetes do Azure) com o banco de dados do Azure para PostgreSQL-servidor único
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769885"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Conectando o serviço kubernetes do Azure e o banco de dados do Azure para PostgreSQL-servidor único

O AKS (serviço kubernetes do Azure) fornece um cluster kubernetes gerenciado que você pode usar no Azure. Abaixo estão algumas opções a serem consideradas ao usar o AKS e o banco de dados do Azure para PostgreSQL em conjunto para criar um aplicativo.


## <a name="accelerated-networking"></a>Redes aceleradas
Use VMs subjacentes habilitadas para rede acelerada em seu cluster AKS. Quando a rede acelerada está habilitada em uma VM, há latência mais baixa, tremulação reduzida e utilização da CPU reduzida na VM. Saiba mais sobre como funciona a rede acelerada, as versões de sistema operacional com suporte e as instâncias de VM com suporte para [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

De novembro de 2018, o AKS dá suporte à rede acelerada nessas instâncias de VM com suporte. A rede acelerada é habilitada por padrão em novos clusters AKS que usam essas VMs.

Você pode confirmar se o cluster do AKS tem rede acelerada:
1. Vá para a portal do Azure e selecione o cluster AKS.
2. Selecione a guia Propriedades.
3. Copie o nome do **grupo de recursos de infraestrutura**.
4. Use a barra de pesquisa do portal para localizar e abrir o grupo de recursos de infraestrutura.
5. Selecione uma VM nesse grupo de recursos.
6. Vá para a guia **rede** da VM.
7. Confirme se a **rede acelerada** está ' habilitada '.

Ou por meio do CLI do Azure usando os dois comandos a seguir:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A saída será o grupo de recursos gerado que o AKS cria contendo a interface de rede. Pegue o nome "nodeResourceGroup" e use-o no próximo comando. **EnableAcceleratedNetworking** será true ou false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Abrir o Service Broker do Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) permite que você provisione os serviços do Azure diretamente do kubernetes ou do Cloud Foundry. É uma implementação de [API aberta Service Broker](https://www.openservicebrokerapi.org/) para o Azure.

Com o OSBA, você pode criar um servidor de banco de dados do Azure para PostgreSQL e associá-lo ao cluster AKS usando o idioma nativo do kubernetes. Saiba mais sobre como usar o OSBA e o banco de dados do Azure para PostgreSQL juntos na [página do GitHub do OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Pooling de conexão
Um pooler de conexões minimiza o custo e o tempo associados à criação e ao fechamento de novas conexões com o banco de dados. O pool é uma coleção de conexões que podem ser reutilizadas. 

Há vários pools de conexão que você pode usar com PostgreSQL. Um deles é [PgBouncer](https://pgbouncer.github.io/). No registro de contêiner da Microsoft, fornecemos um PgBouncer em contêiner leve que pode ser usado em um sidecar para pool de conexões do AKS para o banco de dados do Azure para PostgreSQL. Visite a [página do Hub do Docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) para saber como acessar e usar essa imagem. 


## <a name="next-steps"></a>Passos seguintes
-  [Criar um cluster do serviço kubernetes do Azure](../aks/kubernetes-walkthrough.md)
