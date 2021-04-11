---
title: Documentação de orientação sobre dimensionamento
description: Plano para a dimensão de uma implantação de Azure Arc habilitado serviços de dados.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3bbd778eabf150b734b04e004006dfeea2254ec4
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077487"
---
# <a name="sizing-guidance"></a>Orientação de Dimensionamento

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Visão geral da orientação de dimensionamento

Ao planear a implantação de serviços de dados do Azure Arc, deverá planear a quantidade correta de computação, memória e armazenamento que será necessária para executar o controlador de dados Azure Arc e para o número de exemplos geridos pelo SQL e grupos de servidores de hiperescala postgreSQL que irá implementar.  Uma vez que os serviços de dados ativados do Azure Arc são implantados em Kubernetes, você tem a flexibilidade de adicionar capacidade adicional ao seu cluster Kubernetes ao longo do tempo adicionando nós de computação adicionais ou armazenamento.  Este guia fornecerá orientações sobre os requisitos mínimos, bem como fornecerá orientações sobre os tamanhos recomendados para alguns requisitos comuns.

## <a name="general-sizing-requirements"></a>Requisitos gerais de dimensionamento

> [!NOTE]
> Se você não está familiarizado com os conceitos deste artigo, você pode ler mais sobre [a governação de recursos Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) e [notação de tamanho Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Os números de núcleos devem ser um valor inteiro superior ou igual a um.

Ao utilizar addata para implantação, os valores de memória devem ser especificados numa potência de dois números - isto é, utilizando os sufixos: Ki, Mi ou Gi.

Os valores-limite devem ser sempre maiores do que o valor de pedido, se especificado.

Os valores-limite para os núcleos são a métrica faturada em caso gerido pelo SQL e grupos de servidores de hiperescala postgreSQL.

## <a name="minimum-deployment-requirements"></a>Requisitos mínimos de implantação

Um tamanho mínimo Azure Arc habilitado a implementação de serviços de dados poderia ser considerado como o controlador de dados Azure Arc mais um SQL gerido instância mais um grupo de servidores de hiperescala PostgreSQL com dois nós de trabalhadores.  Para esta configuração, você precisa de pelo menos 16 GB de RAM e 4 núcleos de capacidade _disponível_ no seu cluster Kubernetes.  Deve certificar-se de que tem um tamanho mínimo de nó kubernetes de 8 GB de RAM e 4 núcleos e uma capacidade total de 16 GB de RAM disponível em todos os seus nós Kubernetes.  Por exemplo, pode ter 1 nó a 32 GB de RAM e 4 núcleos ou pode ter 2 nós com RAM de 16GB e 4 núcleos cada.

Consulte o artigo [de configuração de armazenamento](storage-configuration.md) para obter mais detalhes sobre o tamanho do armazenamento.

## <a name="data-controller-sizing-details"></a>Detalhes de dimensionamento do controlador de dados

O controlador de dados é uma coleção de cápsulas que são implantadas no seu cluster Kubernetes para fornecer uma API, o serviço de controlador, o bootstrapper, e as bases de dados de monitorização e dashboards.  Esta tabela descreve os valores padrão para pedidos e limites de memória e CPU.

|Nome do pod|Pedido cpu|Pedido de Memória|Limite do CPU|Limite de memória|Notas|
|---|---|---|---|---|---|
|**bootstrapper**|100m|100Mi|200m|200Mi||
|**Controlo**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10m|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**métricasdc**|100m|200Mi|200m|300Mi|Metricsdc é um daemonset que é criado em cada um dos nós Kubernetes no seu cluster.  Os números na tabela aqui são _por nó._ Se definir o allowNodeMetricsCollection = falso no seu ficheiro de perfil de implementação antes de criar o controlador de dados, o daemonset metricsdc não será criado.|
|**metricsui**|20m|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

Pode anular as definições predefinidos para o controlador e as cápsulas de controlo no seu ficheiro de perfil de implementação ou no ficheiro YAML do controlador de dados.  Exemplo:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Consulte o artigo [de configuração de armazenamento](storage-configuration.md) para obter mais detalhes sobre o tamanho do armazenamento.

## <a name="sql-managed-instance-sizing-details"></a>SQL geriu detalhes de dimensionamento de instâncias

Cada instância gerida pela SQL deve ter os seguintes pedidos mínimos de recursos:
- Memória: 2Gi
- Núcleos: 1

Cada cápsula de instância gerida sql que é criada tem três recipientes:

|Nome do contentor|Pedido cpu|Pedido de Memória|Limite do CPU|Limite de memória|Notas|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Não especificado|Não especificado|Os pedidos de recursos de recipiente fluentbit são _para além_ dos pedidos especificados para a instância gerida pelo SQL.|
|arc-sqlmi|Utilizador especificado ou não especificado.|Utilizador especificado ou não especificado.|Utilizador especificado ou não especificado.|Utilizador especificado ou não especificado.|
|recolhido|Não especificado|Não especificado|Não especificado|Não especificado|

O tamanho do volume predefinido para todos os volumes persistentes é 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Detalhes de dimensionamento do grupo de servidores de hiperescala postgreSQL

Cada nó do grupo de servidor de hiperescala PostgreSQL deve ter os seguintes pedidos mínimos de recursos:
- Memória: 256Mi
- Núcleos: 1

Cada coordenador do grupo de servidores de hiperescala PostgreSQL ou o grupo de trabalhadores criados tem três recipientes:

|Nome do contentor|Pedido cpu|Pedido de Memória|Limite do CPU|Limite de memória|Notas|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Não especificado|Não especificado|Os pedidos de recursos de recipiente fluentbit são _além_ dos pedidos especificados para os nós do grupo de servidores de hiperescala PostgreSQL.|
|postgres|Utilizador especificado ou não especificado.|Utilizador especificado ou 256Mi (predefinição).|Utilizador especificado ou não especificado.|Utilizador especificado ou não especificado.||
|telegrafo|Não especificado|Não especificado|Não especificado|Não especificado||

## <a name="cumulative-sizing"></a>Dimensionamento cumulativo

A dimensão global de um ambiente necessário para os serviços de dados habilitados a Azure Arc é principalmente uma função do número e tamanho das instâncias da base de dados que serão criadas.  A dimensão global pode ser difícil de prever com antecedência, sabendo que o número de casos irá crescer e diminuir e a quantidade de recursos necessários para cada instância da base de dados mudará.

O tamanho da linha de base para um dado ambiente de serviços de dados habilitado a Azure Arc é o tamanho do controlador de dados que requer 4 núcleos e 16 GB de RAM.  A partir daí pode adicionar em cima o total acumulado de núcleos e memória necessários para as instâncias da base de dados.  Para o sql gerido exemplo o número de cápsulas é igual ao número de instâncias geridas SQL que são criadas.  Para os grupos de servidores de hiperescala PostgreSQL, o número de cápsulas é equivalente ao número de nós de trabalhador mais um para o nó coordenador.  Por exemplo, se tiver um grupo de Servidor PostgreSQL com 3 nós de trabalhadores, o número total de cápsulas será de 4.

Além dos núcleos e memória que solicita para cada instância de base de dados, deve adicionar 250m de núcleos e 250Mi de RAM para os contentores do agente.

Segue-se um cálculo de tamanho de exemplo.

Requisitos:

- **"SQL1"**: 1 SQL gerido com 16 GB de RAM, 4 núcleos
- **"SQL2"**: 1 SQL gerido com 256 GB de RAM, 16 núcleos
- **"Postgres1"**: 1 Grupo de servidores de hiperescala postgreSQL com 4 trabalhadores a 12 GB de RAM, 4 núcleos

Cálculos de dimensionamento:

- O tamanho de "SQL1" é: 1 pod * ([16 Gi RAM, 4 núcleos] + [250Mi RAM, 250m cores]) para os agentes por vagem = 16,25 Gi RAM, 4,25 núcleos.
- O tamanho de "SQL2" é: 1 pod * ([256 Gi RAM, 16 núcleos] + + [250Mi RAM, 250m cores]) para os agentes por vagem = 256,25 Gi RAM, 16,25 núcleos.
- O tamanho total de SQL 1 e SQL 2 é: (16,25 GB + 256,25 Gi) = 272,5 GB de RAM, (4,25 núcleos + 16,25 núcleos) = 20,5 núcleos.

- O tamanho de "Postgres1" é: (4 cápsulas de trabalhador + 1 cápsula coordenador) * ([12 GB de RAM, 4 núcleos] + [250Mi RAM, 250m de núcleos]) para os agentes por vagem = 61,25 GB RAM, 21,25 núcleos.

- A capacidade total necessária para as instâncias da base de dados é: 272,5 GB RAM, 20,5 núcleos para SQL + 61,25 GB RAM, 21,25 núcleos para PósgreSQL Hyperscale = 333,75 GB RAM, 42,5 cores.

- A capacidade total necessária para as instâncias da base de dados mais o controlador de dados é: 333,75 GB de RAM, 42,5 núcleos para as instâncias da base de dados + 16 GB de RAM, 4 núcleos para o controlador de dados = 349,75 GB RAM, 46,5 núcleos.

Consulte o artigo [de configuração de armazenamento](storage-configuration.md) para obter mais detalhes sobre o tamanho do armazenamento.

## <a name="other-considerations"></a>Outras considerações

Tenha em mente que um dado de dados de caso de pedido de tamanho para núcleos ou RAM não pode exceder a capacidade disponível dos nós Kubernetes no cluster.  Por exemplo, se o maior nó Kubernetes que você tem no seu cluster Kubernetes é de 256 GB de RAM e 24 núcleos, você não será capaz de criar uma instância de base de dados com um pedido de 512 GB de RAM e 48 núcleos.  

É uma boa ideia manter pelo menos 25% da capacidade disponível nos nós kubernetes para permitir que kubernetes programassem eficientemente as cápsulas e permitissem uma escala elástica e um crescimento a longo prazo a pedido.  

Nos seus cálculos de dimensionamento, não se esqueça de adicionar os requisitos de recursos das cápsulas do sistema Kubernetes e quaisquer outras cargas de trabalho que possam estar a partilhar capacidade com o Azure Arc habilitado serviços de dados no mesmo cluster Kubernetes.

Para manter a alta disponibilidade durante a manutenção planeada e a continuidade de desastres, você deve planear que pelo menos um dos nós Kubernetes no seu cluster não esteja disponível em qualquer momento.  Kubernetes tentará reagendar as cápsulas que estavam a funcionar num dado nó que foi retirado para manutenção ou devido a uma falha.  Se não houver capacidade disponível nos restantes nós, essas cápsulas não serão reagendadas para criação até que haja novamente capacidade disponível.  Tenha muito cuidado com as grandes situações de base de dados.  Por exemplo, se houver apenas um nó Kubernetes grande o suficiente para satisfazer os requisitos de recursos de uma grande caixa de dados e esse nó falhar, então Kubernetes não será capaz de agendar essa pod de caixa de dados para outro nó Kubernetes.

Tenha em mente os [limites máximos para um tamanho de cluster Kubernetes.](https://kubernetes.io/docs/setup/best-practices/cluster-large/)

O seu administrador Kubernetes pode ter configurado [quotas de recursos](https://kubernetes.io/docs/concepts/policy/resource-quotas/) no seu espaço/projeto de nome.  Tenha isto em mente ao planear os tamanhos das suas instâncias de base de dados.
