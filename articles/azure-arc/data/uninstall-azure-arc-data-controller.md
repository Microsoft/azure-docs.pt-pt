---
title: Eliminar controlador de dados do Arco azul
description: Eliminar controlador de dados do Arco azul
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91660681"
---
# <a name="delete-azure-arc-data-controller"></a>Eliminar controlador de dados do Arco azul

O seguinte artigo descreve como eliminar um controlador de dados Azure Arc.

Antes de prosseguir, certifique-se de que todos os serviços de dados que foram criados no tratamento de dados são removidos da seguinte forma:

## <a name="log-in-to-the-data-controller"></a>Faça login no controlador de dados

Faça login no controlador de dados que pretende eliminar:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Lista & eliminar os serviços de dados existentes

Executar o seguinte comando para verificar se existem algumas instâncias geridas pela SQL criadas:

```
azdata arc sql mi list
```

Para cada sql gerido instância a partir da lista acima, executar o comando de eliminação da seguinte forma:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Da mesma forma, para verificar se existem instâncias de hiperescala pós-SQL, corra:

```
azdata arc postgres server list
```

E, para cada instância de hiperescala postgreSQL, executar o comando de eliminação da seguinte forma:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Eliminar controlador

Depois de todas as instâncias geridas pelo SQL e de instâncias de hiperescala postgresQL terem sido removidas, o controlador de dados pode ser eliminado da seguinte forma:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Remover SCCs (apenas Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Eliminar objetos de nível cluster

Além dos objetos com mira no espaço de nome, se também pretende eliminar os objetos de nível de cluster, como os CRDs, `clusterroles` e `clusterrolebindings` executar os seguintes comandos:

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Opcionalmente, elimine o espaço de nome do controlador de dados Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Passos seguintes

[Quais são os serviços de dados habilitados a Azure Arc?](overview.md)
