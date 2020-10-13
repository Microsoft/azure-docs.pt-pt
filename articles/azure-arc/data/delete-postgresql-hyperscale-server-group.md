---
title: Eliminar um grupo de servidores de hiperescala pós-escala Azure
description: Eliminar um grupo de servidores de hiperescala pós-escala ativado por Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dcabe4b1520c66b8d5bfa398dc1248972587cd32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940801"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Eliminar um grupo de servidores de hiperescala pós-escala Azure

Este documento descreve os passos para eliminar um grupo de servidor da sua configuração Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>Eliminar o grupo de servidores

Como exemplo, consideremos que queremos eliminar a instância _pós-corrida da_ configuração abaixo:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

O formato geral do comando de eliminação é:
```console
azdata arc postgres server delete -n <server group name>
```
Para mais detalhes sobre o comando de eliminação, corra:
```console
azdata arc postgres server delete --help
```

### <a name="lets-delete-the-server-group-used-in-this-example"></a>Vamos eliminar o grupo de servidores utilizado neste exemplo:
```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Recuperar as alegações de volume persistentes de Kubernetes (PVCs)

A eliminação de um grupo de servidores não remove os seus [PVCs associados](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Esta ação é propositada. A intenção é ajudar o utilizador a aceder aos ficheiros de base de dados caso a eliminação da instância tenha sido acidental. A eliminação de PVCs não é obrigatória. No entanto, é recomendada. Se não recuperar esses PVCs, eventualmente acabará com erros, uma vez que o cluster do Kubernetes vai considerar que está a ficar sem espaço em disco. Para recuperar PVCs, execute os seguintes passos:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. Listar os PVCs para o grupo de servidores que eliminou
Para listar os PVCs, executar este comando:
```console
kubectl get pvc [-n <namespace name>]
```

Devolve a lista de PVSs, em particular os PVCs para o grupo de servidores que eliminou. Por exemplo:
```console
kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Existem 8 PVCs para este grupo de servidores.

### <a name="2-delete-each-of-the-pvcs"></a>2. Eliminar cada um dos PVCs
Elimine os dados e faça login PVCs para cada um dos nós de hiperescala postgreSQL (Coordenador e Trabalhadores) do grupo de servidor que eliminou.
O formato geral deste comando é: 
```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Por exemplo:
```console
kubectl delete pvc data-postgres01-0
kubectl delete pvc data-postgres01-1 
kubectl delete pvc data-postgres01-2
kubectl delete pvc data-postgres01-3
kubectl delete pvc logs-postgres01-0
kubectl delete pvc logs-postgres01-1
kubectl delete pvc logs-postgres01-2
kubectl delete pvc logs-postgres01-3
```

Cada um destes comandos kubectl confirmará a eliminação bem sucedida do PVC. Por exemplo:
```console
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>**NOTA** Como indicado, não eliminar os PVCs pode eventualmente obter o seu cluster Kubernetes numa situação em que irá lançar erros. Alguns destes erros podem incluir a impossição no seu cluster Kubernetes com azdata, uma vez que as cápsulas podem ser despejadas devido a este problema de armazenamento (comportamento normal de Kubernetes).
>
> Por exemplo, pode ver mensagens nos registos semelhantes a:  
    > Anotações: microsoft.com/ignore-pod-health: verdadeiro  
    > Estado: Falhado  
    > Razão: Despejado  
    > Mensagem: O nó era baixo em recursos: armazenamento efémero. O controlador de contentores estava a usar 16372Ki, que excede o seu pedido de 0.
    
## <a name="next-step"></a>Passo seguinte
Criar [Azure Arc ativado em hiperescala pósgresql](create-postgresql-hyperscale-server-group.md)
