---
title: Colocação de um grupo de servidores de hiperescala PostgreSQL nos nosmos de cluster Kubernetes
description: Explica como os casos postgreSQL que formam um grupo de servidores de hiperescala PostgreSQL são colocados nos nosmos de cluster Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1fc768890e932d1f17ad111b4681b75721ae1e06
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148098"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure Arc habilitado colocação do grupo de servidores de hiperescala postgreSQL

Neste artigo estamos a dar um exemplo para ilustrar como os casos postgreSQL de Azure Arc ativados pelo grupo de servidores de hiperescala postgresQL são colocados nos nós físicos do cluster Kubernetes que os acolhe. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Configuração

Neste exemplo, estamos a usar um cluster Azure Kubernetes Service (AKS) que tem quatro nós físicos. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::

Listar os nóns físicos do cluster Kubernetes executando o comando:

```console
kubectl get nodes
```

O que mostra os quatro nóns físicos dentro do aglomerado de Kubernetes:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

A arquitetura pode ser representada como:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::

O cluster Kubernetes acolhe um Controlador de Dados Azure Arc e um grupo de servidores de hiperescala postgreSQL ativado. Este grupo de servidores é constituído por três instâncias PostgreSQL: um coordenador e dois trabalhadores.

Listar as cápsulas com o comando:

```console
kubectl get pods -n arc3
```
Que produz a seguinte saída:

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
Cada uma dessas cápsulas acolhe um caso Pós-SQL. Juntos formam o grupo de servidores pós-escala pós-escala do Arco Azure:

```output
Pod name        Role in the server group
postgres01-0  Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>Posicionamento
Vamos ver como Kubernetes coloca as cápsulas do grupo de servidores. Descreva cada cápsula e identifique em que nó físico do cluster Kubernetes estão colocados. Por exemplo, para o Coordenador, executar o seguinte comando:

```console
kubectl describe pod postgres01-0 -n arc3
```

Que produz a seguinte saída:

```output
Name:         postgres01-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

À medida que corremos este comando para cada uma das cápsulas, resumimos a colocação atual como:

| Função de grupo de servidor|Cápsula de grupo de servidor|Kubernetes nódoa física que hospeda a cápsula |
|--|--|--|
| Trabalhador|postgres01-1|aks-agentpool-42715708-vmss00002 |
| Trabalhador|postgres01-2|aks-agentpool-42715708-vmss00003 |

E note também, na descrição das cápsulas, os nomes dos recipientes que cada pod acolhe. Por exemplo, para o segundo trabalhador, executar o seguinte comando:

```console
kubectl describe pod postgres01-2 -n arc3
```

Que produz a seguinte saída:

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Cada cápsula que faz parte do grupo de servidores pós-escala pós-escala do Arco Azure, ativado por PostgreSQL, acolhe os seguintes três recipientes:

|Contentores|Descrição
|----|----|
|`Fluentbit` |Data * colecionador de registos: https://fluentbit.io/
|`Postgres`|Instância pós-SQL parte do arco Azure habilitado grupo de servidores de hiperescala PosgreSQL
|`Telegraf` |Colecionador de métricas: https://www.influxdata.com/time-series-platform/telegraf/

A arquitetura parece:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::

Significa que, neste ponto, cada instância PostgreSQL que constitui o grupo de servidores de hiperescala pós-escala do Arco Azure ativado é hospedado em hospedeiros físicos específicos dentro do recipiente Kubernetes. Esta é a melhor configuração para ajudar a obter o maior desempenho do Azure Arc ativado o grupo de servidores pós-escala PostgreSQL, uma vez que cada função (coordenador e trabalhadores) utiliza os recursos de cada nó físico. Esses recursos não são partilhados entre vários papéis postgreSQL.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Dimensionar O Arco Azure ativado a hiperescala pós-SQL

Agora, vamos escalar para adicionar um terceiro nó de trabalhador ao grupo de servidor e observar o que acontece. Criará um quarto caso PostgreSQL que será hospedado numa quarta cápsula.
Para escalar para fora executar o comando:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Que produz a seguinte saída:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Listar os grupos de servidores implantados no Controlador de Dados do Arco Azure e verificar se o grupo de servidores funciona agora com três trabalhadores. Execute o comando:

```console
azdata arc postgres server list
```

E observe que escalou de dois trabalhadores para três trabalhadores:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Como fizemos anteriormente, observe que o grupo de servidores agora usa um total de quatro cápsulas:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

E descreva a nova cápsula para identificar em qual dos nós físicos do cluster Kubernetes está hospedado.
Execute o comando:

```console
kubectl describe pod postgres01-3 -n arc3
```

Para identificar o nome do nó de alojamento:

```output
Name:         postgres01-3
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

A colocação dos casos PostgreSQL nos nóns físicos do cluster é agora:

|Função de grupo de servidor|Cápsula de grupo de servidor|Kubernetes nódoa física que hospeda a cápsula
|-----|-----|-----
|Coordenador|postgres01-0|aks-agentpool-42715708-vmss000000
|Trabalhador|postgres01-1|aks-agentpool-42715708-vmss00002
|Trabalhador|postgres01-2|aks-agentpool-42715708-vmss00003
|Trabalhador|postgres01-3|aks-agentpool-42715708-vmss000000

E note que o casulo do novo trabalhador (postgres01-3) foi colocado no mesmo nó que o coordenador. 

A arquitetura parece:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Aglomerado de 4 nó AKS no portal Azure" do controlador que mantém um olho na disponibilidade do controlador de dados.|aks-agentpool-42715708-vmss000000
|logsdb-0|Esta é uma instância de Pesquisa Elástica que é usada para armazenar todos os registos recolhidos em todas as cápsulas de serviços de dados do Arco. Elasticsearch, recebe dados do `Fluentbit` recipiente de cada vagem|aks-agentpool-42715708-vmss00003
|logsui-5fzv5|Este é um caso kibana que se senta em cima da base de dados de Pesquisa Elástica para apresentar um GUI de análise de registo.|aks-agentpool-42715708-vmss00003
|métricasdb-0|Este é um caso do InfluxDB que é usado para armazenar todas as métricas recolhidas em todas as cápsulas de serviços de dados arc. InfluxDB, recebe dados do `Telegraf` recipiente de cada cápsula|aks-agentpool-42715708-vmss000000
|métricas-47d47|Este é um daemonset implantado em todos os nós Kubernetes no cluster para recolher métricas de nível de nó sobre os nós.|aks-agentpool-42715708-vmss00002
|métricas-864kj|Este é um daemonset implantado em todos os nós Kubernetes no cluster para recolher métricas de nível de nó sobre os nós.|aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf|Este é um daemonset implantado em todos os nós Kubernetes no cluster para recolher métricas de nível de nó sobre os nós.|aks-agentpool-42715708-vmss00003
|metricsdc-nxm4l|Este é um daemonset implantado em todos os nós Kubernetes no cluster para recolher métricas de nível de nó sobre os nós.|aks-agentpool-42715708-vmss000000
|metricsui-4fb7l|Este é um caso grafana que se senta em cima da base de dados do InfluxDB para apresentar um painel de monitorização GUI.|aks-agentpool-42715708-vmss00003
|mgmtproxy-4qppp|Esta é uma camada de procuração de aplicação web que fica em frente aos casos de Grafana e Kibana.|aks-agentpool-42715708-vmss00002

> \* O sufixo nos nomes das cápsulas variará em outras implementações. Além disso, estamos aqui apenas listando as cápsulas hospedadas dentro do espaço de nome Kubernetes do Controlador de Dados do Arco Azure.

A arquitetura parece:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::

Isto significa que os nós coordenadores (Pod 1) do Azure Arc ativado pelo grupo de servidores Postgres Hyperscale partilham os mesmos recursos físicos que o terceiro nó de trabalhador (Pod 4) do grupo de servidores. Isso é aceitável, uma vez que o nó coordenador está normalmente a utilizar muito poucos recursos em comparação com o que um nó do Trabalhador pode estar a usar. A partir daí, poderá inferir que deve escolher cuidadosamente:
- o tamanho do cluster Kubernetes e as características de cada um dos seus nós físicos (memória, vCore)
- o número de nós físicos dentro do cluster Kubernetes
- as aplicações ou cargas de trabalho que hospeda no cluster Kubernetes.

A implicação de hospedar demasiadas cargas de trabalho no cluster Kubernetes está a ser estrangulada pode acontecer para o grupo de servidores pós-escala pós-SQL ativado pelo Arco Azure. Se isso acontecer, não beneficiará tanto da sua capacidade de escalar horizontalmente. O desempenho que obtém do sistema não é apenas sobre a colocação ou as características físicas dos nós físicos ou do sistema de armazenamento. O desempenho que obtém também é sobre como configurar cada um dos recursos que executam dentro do cluster Kubernetes (incluindo a Hiperescala Pós-SQL ativada por Azure Arc), por exemplo, os pedidos e limites que definiu para a memória e vCore. A quantidade de carga de trabalho que pode hospedar num determinado cluster Kubernetes é relativa às características do cluster Kubernetes, à natureza das cargas de trabalho, ao número de utilizadores, à forma como as operações do cluster Kubernetes são feitas...

## <a name="scale-out-aks"></a>Escalone a AKS

Vamos demonstrar que a escala horizontal tanto o cluster AKS como o Azure Arc ativado pelo servidor de hiperescala PostgreSQL é uma forma de beneficiar ao máximo do alto desempenho do Azure Arc ativado pela Hiperescala PósgreSQL.
Vamos adicionar um quinto nó ao cluster AKS:

:::row:::
    :::column:::
        Antes
    :::column-end:::
    :::column:::
        Depois
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::
    :::column-end:::
:::row-end:::

A arquitetura parece:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::

Vamos ver que cápsulas do espaço de nome do Controlador de Dados do Arco estão hospedadas no novo nó físico AKS executando o comando:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

E vamos atualizar a representação da arquitetura do nosso sistema:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::

Podemos observar que o novo nó físico do cluster Kubernetes está hospedado apenas o casulo métrico que é necessário para os serviços de dados do Azure Arc. Note que, neste exemplo, estamos focados apenas no espaço de nome do Arc Data Controller, não estamos representando as outras cápsulas.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Escalar Azure Arc ativado hyperscale pósgresQL novamente

O quinto nó físico ainda não está a receber nenhuma carga de trabalho. À medida que escalamos o Arco Azure habilitado a Hiperescala PostgreSQL, a Kubernetes otimizará a colocação da nova cápsula PostgreSQL e não deve coocalizá-la em nós físicos que já estão a acolher mais cargas de trabalho. Executar o seguinte comando para escalar o Arco Azure ativado a Hiperescala PostgreSQL de 3 a 4 trabalhadores. No final da operação, o grupo de servidores será constituído e distribuído por cinco instâncias PostgreSQL, um coordenador e quatro trabalhadores.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Que produz a seguinte saída:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Listar os grupos de servidores implantados no Controlador de Dados e verificar se o grupo de servidor funciona agora com quatro trabalhadores:

```console
azdata arc postgres server list
```

E observe que escalou de três para quatro trabalhadores. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Como fizemos anteriormente, observe o grupo de servidores agora usa quatro cápsulas:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

A forma do grupo de servidores é agora:

|Função de grupo de servidor|Cápsula de grupo de servidor
|----|-----
|Coordenador|postgres01-0
|Trabalhador|postgres01-1
|Trabalhador|postgres01-2
|Trabalhador|postgres01-3
|Trabalhador|postgres01-4

Vamos descrever a cápsula postgres01-4 para identificar em que nó físico está hospedado:

```console
kubectl describe pod postgres01-4 -n arc3
```

E observe em que cápsulas corre:

|Função de grupo de servidor|Cápsula de grupo de servidor| Vagem
|----|-----|------
|Coordenador|postgres01-0|aks-agentpool-42715708-vmss000000
|Trabalhador|postgres01-1|aks-agentpool-42715708-vmss00002
|Trabalhador|postgres01-2|aks-agentpool-42715708-vmss00003
|Trabalhador|postgres01-3|aks-agentpool-42715708-vmss000000
|Trabalhador|postgres01-4|aks-agentpool-42715708-vmss00004

E a arquitetura parece:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Aglomerado de 4 nó AKS no portal Azure":::

Kubernetes marcou o novo pod PostgreSQL no nó físico menos carregado do cluster Kubernetes.

## <a name="summary"></a>Resumo

Para beneficiar ao máximo da escalabilidade e do desempenho do grupo de servidores Azure Arc ativado horizontalmente, deve evitar a contenção de recursos dentro do cluster Kubernetes:
- entre o grupo de servidores pós-escala pós-SQL do Arco Azure e outras cargas de trabalho hospedadas no mesmo cluster Kubernetes
- entre todas as instâncias PostgreSQL que constituem o Azure Arc ativado grupo de servidores de hiperescala postgreSQL

Pode conseguir isto de várias formas:
- Dimensionar tanto Kubernetes como Azure Arc ativado Postgres Hyperscale: considere escalar horizontalmente o cluster Kubernetes da mesma forma que você está escalando o grupo de servidores de hiperescala pós-escala ativado pelo Arco Azure. Adicione um nó físico ao cluster para cada trabalhador que adicionar ao grupo de servidor.
- Escala A azure Arc ativada Postgres Hyperscale sem escalonar Kubernetes: ao definir as restrições de recursos certas (pedido e limites na memória e vCore) nas cargas de trabalho hospedadas em Kubernetes (Azure Arc habilitado pós-escala pós-SQL incluído), você permitirá a colocação de cargas de trabalho em Kubernetes e reduzirá o risco de contenção de recursos. Você precisa ter certeza de que as características físicas dos nós físicos do cluster Kubernetes podem honrar os constrangimentos de recursos que você define. Também deve garantir que o equilíbrio permanece à medida que as cargas de trabalho evoluem ao longo do tempo ou à medida que mais cargas de trabalho são adicionadas no cluster Kubernetes.
- Utilize os mecanismos Kubernetes (seletor de vagem, afinidade, anti-afinidade) para influenciar a colocação das cápsulas.

## <a name="next-steps"></a>Passos seguintes

[Dimensione o seu Azure Arc ativado pelo grupo de servidores pós-escala PostgreSQL adicionando mais nóns de trabalhadores](scale-out-postgresql-hyperscale-server-group.md)
