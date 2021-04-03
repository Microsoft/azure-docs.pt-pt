---
title: Delete Azure Arc habilitado SQL Gestdited Instance
description: Delete Azure Arc habilitado SQL Gestdited Instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90940849"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Delete Azure Arc habilitado SQL Gestdited Instance
Este artigo descreve como pode eliminar um Arco Azure ativado sql GestdIted Instance.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Ver Arco Azure existente habilitado a SQL Gestão de Instâncias
Para ver as ocorrências geridas pela SQL, executar o seguinte comando:

```console
azdata arc sql mi list
```

A saída deve ser algo assim:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Apagar um Arco Azure ativado SQL Gestão De Instância
Para eliminar uma SqL Managed Instance, executar o seguinte comando:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

A saída deve ser algo assim:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Recuperar as alegações de volume persistentes de Kubernetes (PVCs)

A eliminação de uma instância gerida sql não remove os seus [PVCs associados](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Esta ação é propositada. A intenção é ajudar o utilizador a aceder aos ficheiros de base de dados caso a eliminação da instância tenha sido acidental. A eliminação de PVCs não é obrigatória. No entanto, é recomendada. Se não recuperar estes PVCs, acabará por ficar com erros, uma vez que o seu cluster Kubernetes sairá do espaço do disco. Para recuperar PVCs, execute os seguintes passos:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. Listar os PVCs para o grupo de servidores que eliminou
Para listar os PVCs, executar o seguinte comando:
```console
kubectl get pvc
```

No exemplo abaixo, note os PVCs para as Ocorrências Geridas SQL que apagou.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. Eliminar cada um dos PVCs
Elimine os dados e faça login PVCs para cada uma das Instâncias Geridas SQL que eliminou.
O formato geral deste comando é: 
```console
kubectl delete pvc <name of pvc>
```

Por exemplo:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Cada um destes comandos kubectl confirmará a eliminação bem sucedida do PVC. Por exemplo:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Como indicado, não eliminar os PVCs pode eventualmente obter o seu cluster Kubernetes numa situação em que irá lançar erros. Alguns destes erros podem incluir a impossição no seu cluster Kubernetes com azdata, uma vez que as cápsulas podem ser despejadas devido a este problema de armazenamento (comportamento normal de Kubernetes).
>
> Por exemplo, pode ver mensagens nos registos semelhantes a:  
> - Anotações: microsoft.com/ignore-pod-health: verdadeiro  
> - Estado: Falhado  
> - Razão: Despejado  
> - Mensagem: O nó era baixo em recursos: armazenamento efémero. O controlador de contentores estava a usar 16372Ki, que excede o seu pedido de 0.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [funcionalidades e capacidades do Arco Azure habilitado para a SQL Gestão de Instância](managed-instance-features.md)

[Comece por criar um Controlador de Dados](create-data-controller.md)

Já criou um Controlador de Dados? [Criar um Arco Azure habilitado sql instância gerida](create-sql-managed-instance.md)