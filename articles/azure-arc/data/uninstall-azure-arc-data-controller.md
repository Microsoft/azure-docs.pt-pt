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
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939110"
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

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Opcionalmente, elimine o espaço de nome do controlador de dados Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Passos seguintes

[Quais são os serviços de dados habilitados a Azure Arc?](overview.md)