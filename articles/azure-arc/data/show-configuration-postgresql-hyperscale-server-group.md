---
title: Mostrar a configuração de um grupo de servidores de hiperescala postgreSQL ativado por Arco
titleSuffix: Azure Arc enabled data services
description: Mostrar a configuração de um grupo de servidores de hiperescala postgreSQL ativado por Arco
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 716759fd6542cd473c236992ac88b69bfe5d0a66
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148019"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Mostrar a configuração de um grupo de servidores de hiperescala postgreSQL ativado por Arco

Este artigo explica como exibir a configuração do(s) grupo de servidores. Fá-lo antecipando algumas perguntas que pode estar a fazer a si mesmo e responde-as. Por vezes pode haver várias respostas válidas. Este artigo apresenta os mais comuns ou úteis. Agrucha essas questões por tema:

- de um ponto de vista Kubernetes
- a partir de um Azure Arc habilitado o ponto de vista dos serviços de dados

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Do ponto de vista de Kubernetes

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Quantas cápsulas são utilizadas pela Azure Arc ativada pela Hyperscale PostgreSQL?

Lista os recursos kubernetes do tipo Postgres. Execute o comando:

```console
kubectl get postgresqls [-n <namespace name>]
```

A saída deste comando mostra a lista de grupos de servidores criados. Para cada um, indica o número de cápsulas. Por exemplo:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Este exemplo mostra que são criados 2 grupos de servidores e cada um funciona em 3 cápsulas (1 coordenador + 2 trabalhadores). Isto significa que os grupos de servidores criados neste Controlador de Dados Azure Arc utilizam 6 cápsulas.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Que cápsulas são utilizadas por Azure Arc ativados por grupos de servidores de hiperescala PostgreSQL?

Executar:

```console
kubectl get pods [-n <namespace name>]
```

Isto devolve a lista de cápsulas. Verá as cápsulas utilizadas pelos grupos de servidor com base nos nomes que deu a esses grupos de servidores. Por exemplo:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

Neste exemplo, as seis cápsulas que acolhem os dois grupos de servidores que são criados são:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Que cápsula de grupo de servidor é usada para que função o grupo de servidores?

Qualquer nome de pod sufixado com `-0` representa um nó coordenador. Qualquer nome de nó sufixado `-x` por onde é 1 ou maior é nó de trabalhador. No exemplo acima:
- Os coordenadores `postgres01-0` são: `postgres02-0`
- Os trabalhadores são: `postgres01-2` `postgres01-2` , `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Qual é o estado das cápsulas?

Corra `kubectl get pods` e olhe para a coluna `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Que alegações de volume persistente (PVCs) estão a ser utilizadas? 

Para entender quais os PVCs utilizados, bem como os que são usados para dados, registos e backups, executar: 

```console
kubectl get pvc [-n <namespace name>]
```

Por predefinição, o prefixo do nome de um PVC indica a sua utilização:

- `backups-`...: é um PVC usado para backups
- `data-`...: é PVC usado para ficheiros de dados
- `logs-`...: é um PVC usado para registos de transações/ficheiros WAL

Por exemplo:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Do ponto de vista dos serviços de dados Azure Arc:

* Quantos grupos de servidores são criados num Controlador de Dados do Arco?
* Como se chamam?
* Quantos nós de trabalhadores usam?
* Que versão dos Postgres são executados?

Utilize qualquer um dos seguintes comandos.
- **Com kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Por exemplo, produz a saída abaixo, onde cada linha é a `servergroup` . A estrutura do nome no visor abaixo é formada como:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   A saída acima mostra 2 grupos de servidores que são da versão 12 do Postgres. Se a versão fosse Postgres 11, o nome do CRD seria postgresql-11.arcdata.microsoft.com em vez disso.

   Cada um deles funciona em 3 nós/vagens: 1 coordenador e 2 trabalhadores.

- **Com azdata:**

Execute o seguinte comando. A saída mostra informações semelhantes ao que o kubectl mostra:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Quanta memória e vCores estão a ser usados e que extensões foram criadas para um grupo?

Executar qualquer um dos seguintes comandos

**Com Kubectl:**

Use kubectl para descrever os recursos de Postgres. Para tal, precisa do seu tipo (nome do recurso Kubernetes (CRD) para a versão pósgres correspondente, como mostrado acima) e o nome do grupo de servidores.
O formato geral deste comando é:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Por exemplo:

```console
kubectl describe postgresql-12/postgres02
```

Estes comandos mostram a configuração do grupo de servidores:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Shards:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

Vamos chamar alguns pontos específicos de interesse na descrição do `servergroup` acima mostrado. O que nos diz sobre este grupo de servidores?

- É da versão 12 dos Postgres: 
   > Tipo:         `postgresql-12`
- Foi criado durante o mês de agosto de 2020:
   > Assinalação do tempo da criação:  `2020-08-31T21:01:07Z`
- Duas extensões postgres foram criadas neste grupo de servidores: `citus` e `pg_stat_statements`
   > Motor: Extensões:  `citus` Nome: Nome:  `pg_stat_statements`
- Usa dois nó de trabalhadores
   > Escala: Fragmentos:  `2`
- É garantido que utiliza 1 cpu/vCore e 512MB de Ram por nó. Utilizará mais de 4 cpu/vCores e 1024MB de memória:
   > Agendamento: Padrão: Recursos: Limites: Cpu: 4 Memória: 1024Mi Pedidos: Cpu: 1 Memória: 512Mi
 - Está disponível para consultas e não tem qualquer problema. Todos os nós estão a funcionar:
   > Estado: ... Cápsulas prontas: 3/3 Estado: Pronto

**Com azdata:**

O formato geral do comando é:

```console
azdata arc postgres server show -n <server group name>
```

Por exemplo:

```console
azdata arc postgres server show -n postgres02
```

Devolve a saída abaixo num formato e conteúdo muito semelhante ao devolvido pela kubectl.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "shards": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Passos seguintes
- [Leia sobre os conceitos de Azure Arc habilitado a hiperescala pósgresql](concepts-distributed-postgres-hyperscale.md)
- [Leia sobre como escalar (adicionar nós de trabalhador) um grupo de servidor](scale-out-postgresql-hyperscale-server-group.md)
- [Leia sobre como escalar para cima/para baixo (aumentar ou reduzir a memória e/ou vCores) um grupo de servidores](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Leia sobre a configuração do armazenamento](storage-configuration.md)
- [Leia como monitorizar uma instância de base de dados](monitor-grafana-kibana.md)
- [Utilize extensões PostgreSQL no seu grupo de servidores de hiperescala pós-escala do Arco Azure](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Configurar a segurança para o grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc](configure-security-postgres-hyperscale.md)
