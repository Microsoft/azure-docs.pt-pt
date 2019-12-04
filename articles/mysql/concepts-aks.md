---
title: Conectar-se ao serviço kubernetes do Azure-banco de dados do Azure para MySQL
description: Saiba mais sobre como conectar o serviço kubernetes do Azure com o banco de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 71b266231b7ed3012e5ea7f65fe9487eeb5fb358
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773616"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Conectando o serviço kubernetes do Azure e o banco de dados do Azure para MySQL

O AKS (serviço kubernetes do Azure) fornece um cluster kubernetes gerenciado que você pode usar no Azure. Abaixo estão algumas opções a serem consideradas ao usar o AKS e o banco de dados do Azure para MySQL juntos para criar um aplicativo.


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

Com o OSBA, você pode criar um servidor de banco de dados do Azure para MySQL e associá-lo ao cluster AKS usando o idioma nativo do kubernetes. Saiba mais sobre como usar o OSBA e o banco de dados do Azure para MySQL juntos na [página do GitHub do OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Passos seguintes
- [Criar um cluster do serviço kubernetes do Azure](../aks/kubernetes-walkthrough.md)
- Saiba como [instalar o WordPress de um gráfico do Helm usando o OSBA e o banco de dados do Azure para MySQL](../aks/integrate-azure.md)
