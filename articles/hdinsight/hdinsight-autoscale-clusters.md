---
title: Dimensionar automaticamente os clusters do HDInsight do Azure (pré-visualização)
description: Utilize a funcionalidade de dimensionamento automático do HDInsight para dimensionar automaticamente os clusters
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 6ec981164de0ff61b0e83d54255d046a1418ed96
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000110"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Dimensionar automaticamente os clusters do HDInsight do Azure (pré-visualização)

> [!Important]
> A funcionalidade de dimensionamento automático só funciona com clusters do Spark, Hive e o MapReduce criados após 8 de Maio de 2019. 

Funcionalidade de dimensionamento automático do Azure HDInsight cluster dimensiona-se automaticamente o número de nós de trabalho num cluster e reduzir verticalmente. Outros tipos de nós do cluster não podem ser dimensionados atualmente.  Durante a criação de um novo cluster do HDInsight, pode ser definido um número mínimo e máximo de nós de trabalho. Dimensionamento automático, em seguida, monitoriza os requisitos de recursos da carga de análise e aumenta o número de nós de trabalho ou para baixo. Não é sem custos adicionais para esta funcionalidade.

## <a name="cluster-compatibility"></a>Compatibilidade de cluster

A tabela seguinte descreve os tipos de cluster e versões que são compatíveis com a funcionalidade de dimensionamento automático.

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 sem ESP | Sim | Sim | Não | Não | Não | Não | Não |
| HDInsight 4.0 sem ESP | Sim | Sim | Não | Não | Não | Não | Não |
| HDInsight 3.6 com ESP | Sim | Sim | Não | Não | Não | Não | Não |
| HDInsight 3.6 com ESP | Sim | Sim | Não | Não | Não | Não | Não |

## <a name="how-it-works"></a>Como funciona

Pode escolher com base na carga dimensionamento ou com base na agenda de dimensionamento para o seu cluster do HDInsight. Dimensionamento com base em carga altera o número de nós do cluster, dentro de um intervalo que definir, para garantir a utilização de CPU ideal e minimizar os custos em execução.

Alterações de dimensionamento com base na agenda o número de nós do cluster com base nas condições que entrem em vigor em alturas específicas. Estas condições dimensionar o cluster para um número de nós pretendido.

### <a name="metrics-monitoring"></a>Métricas de monitorização

Dimensionamento automático continuamente monitoriza o cluster e recolhe as métricas seguintes:

* **Total pendente da CPU**: O número total de núcleos necessários para iniciar a execução de todos os contentores pendentes.
* **Total pendente memória**: A memória total (em MB), necessária para iniciar a execução de todos os pendentes contentores.
* **Total de CPU livre**: A soma de todos os núcleos não utilizados em nós de trabalho do Active Directory.
* **Total de memória livre**: A soma da memória não utilizada (em MB) em nós de trabalho do Active Directory.
* **Utilização de memória por nó**: A carga num nó de trabalho. Um nó de trabalho em que é utilizada a 10 GB de memória, é considerado sob carga mais do que uma função de trabalho com 2 GB de memória utilizada.
* **Número de modelos de estrutura mestres de aplicação por nó**: O número de contentores de aplicação Master (AM) em execução num nó de trabalho. Um nó de trabalho que está a alojar dois contentores de AM, é considerado mais importante do que um nó de trabalho que está a alojar contentores zero AM.

As métricas acima são verificadas a cada 60 segundos. Dimensionamento automático toma decisões de ampliação e redução verticais com base nessas métricas.

### <a name="load-based-cluster-scale-up"></a>Aumento vertical de cluster com base na carga

Quando forem detetadas as seguintes condições, dimensionamento automático irá emitir um pedido de aumento vertical:

* Total pendente da CPU é superior ao total de CPU gratuita por mais de 3 minutos.
* Total pendente memória é superior ao total de memória livre para mais de 3 minutos.

O serviço HDInsight calcula quantos novos nós de trabalho são necessários para satisfazer os requisitos de memória e CPU atual e, em seguida, emite um pedido de aumentar verticalmente para adicionar o número necessário de nós.

### <a name="load-based-cluster-scale-down"></a>Cluster com base na carga verticalmente

Quando forem detetadas as seguintes condições, o dimensionamento automático emitirá uma solicitação de redução vertical:

* Total pendente da CPU é inferior a total de CPU gratuita durante mais de 10 minutos.
* Total pendente memória é inferior a total de memória livre para mais de 10 minutos.

Com base no número de contentores de AM por nó e os requisitos de memória e CPU atual, o dimensionamento automático emite um pedido para remover um determinado número de nós. O serviço também Deteta que nós são candidatos para a remoção com base na execução da tarefa atual. A escala de operação de redução decommissions primeiro os nós e, em seguida, remove-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Criar um cluster com o dimensionamento automático baseado em carga

Para ativar a funcionalidade de dimensionamento automático com o dimensionamento com base na carga, conclua os passos seguintes como parte do processo de criação do normal cluster:

1. Selecione **personalizado (tamanho, definições, aplicações)** vez **criação rápida**.
1. No **personalizada** passo 5 (**tamanho do Cluster**), verifique o **dimensionamento automático do nó de trabalho** caixa de verificação.
1. Selecione a opção **com base na carga** sob **tipo de dimensionamento automático**.
1. Introduza os valores pretendidos para as seguintes propriedades:  

    * Inicial **nós de número de trabalho**.  
    * **Mínimo** número de nós de trabalho.  
    * **Máximo** número de nós de trabalho.  

    ![Ativar a opção de dimensionamento automático com base na carga de nó de trabalho](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

O número inicial de nós de trabalho deve estar entre o mínimo e máximo, inclusive. Este valor define o tamanho inicial do cluster quando for criado. O número mínimo de nós de trabalho tem de ser maior que zero.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Criar um cluster com o dimensionamento automático com base na agenda

Para ativar a funcionalidade de dimensionamento automático com o dimensionamento com base na agenda, conclua os passos seguintes como parte do processo de criação do normal cluster:

1. Selecione **personalizado (tamanho, definições, aplicações)** vez **criação rápida**.
1. No **personalizada** passo 5 (**tamanho do Cluster**), verifique o **dimensionamento automático do nó de trabalho** caixa de verificação.
1. Introduza o **nós de número de trabalho**, que controla o limite para aumentar verticalmente o cluster.
1. Selecione a opção **baseadas na agenda** sob **tipo de dimensionamento automático**.
1. Clique em **configurar** para abrir o **configuração de dimensionamento automático** janela.
1. Selecione o seu fuso horário e, em seguida, clique em **+ adicionar condição**
1. Selecione os dias da semana em que a nova condição deve ser aplicada a.
1. Edite o tempo que a condição deve levar em vigor e o número de nós que o cluster deve ser dimensionado para.
1. Adicione mais condições se for necessário.

    ![Ativar a opção de dimensionamento automático com base na agenda do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

O número de nós tem de ser entre 1 e o número de nós de trabalho que introduziu antes de adicionar condições.

### <a name="final-creation-steps"></a>Passos de criação final

Para dimensionar com base na carga tanto com base na agenda, selecione o tipo VM para nós de trabalho ao clicar em **velikost Pracovního uzlu** e **avançar o tamanho do nó**. Depois de escolher o tipo VM para cada tipo de nó, pode ver o intervalo de custo estimado para o cluster inteiro. Ajuste os tipos VM para se ajustar ao seu orçamento.

![Ativar a opção de dimensionamento automático com base na agenda do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

A sua subscrição tem uma quota de capacidade para cada região. O número total de núcleos de seus nós principais, combinados com o número máximo de nós de trabalho não pode exceder a quota da capacidade. No entanto, esta quota é um limite não restritivo; Pode sempre criar um pedido de suporte para facilmente aumentá-la.

> [!Note]  
> Se ultrapassar o limite de quota de núcleos total, receberá uma mensagem de erro dizendo "máxima de nós excedeu os núcleos disponíveis nesta região, escolha outra região ou contacte o suporte para aumentar a quota."

Para obter mais informações sobre a criação de clusters do HDInsight com o portal do Azure, consulte [baseado em Linux criar clusters no HDInsight com o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo do Resource Manager

#### <a name="load-based-autoscaling"></a>Dimensionamento automático baseado em carga

Pode criar um cluster do HDInsight com o dimensionamento automático com base na carga um modelo Azure Resource Manager, ao adicionar um `autoscale` nó para o `computeProfile`  >  `workernode` seção com as propriedades `minInstanceCount` e `maxInstanceCount` como mostrado no fragmento json abaixo.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Para obter mais informações sobre a criação de clusters com modelos do Resource Manager, consulte [Apache Hadoop criar clusters no HDInsight utilizando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Dimensionamento automático com base na agenda

Pode criar um cluster do HDInsight com o dimensionamento automático com base na agenda de um modelo Azure Resource Manager, ao adicionar um `autoscale` nó para o `computeProfile`  >  `workernode` secção. O `autoscale` nó contém um `recurrence` que tem um `timezone` e `schedule` que descreve quando a alteração terá lugar.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Ativar e desativar o dimensionamento automático para um cluster em execução

Para ativar o dimensionamento automático num cluster em execução, selecione **tamanho do Cluster** sob **definições**. Em seguida, clique em **ativar o dimensionamento automático**. Selecione o tipo de dimensionamento automático que pretende e introduza as opções de dimensionamento com base na carga ou baseadas na agenda. Por fim, clique em **guardar**.

![Ativar a opção de dimensionamento automático com base na agenda do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="best-practices"></a>Melhores práticas

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Escolher o dimensionamento com base na carga ou baseadas na agenda

Antes de tomar uma decisão modo no qual escolher, considere os seguintes fatores:

* Variação de carga: faz a carga do cluster seguem um padrão consistente em horários específicos, em dias específicos. Caso contrário, agendamento de carga com base é uma opção melhor.
* Requisitos de SLA: O dimensionamento de dimensionamento automático é reativo, em vez de previsão. Haverá um atraso suficiente entre quando a carga começa a aumentar e quando o cluster tem de estar no seu tamanho de destino? Se existirem rigorosos requisitos de SLA e a carga é um padrão conhecido fixo, 'programação com base' é uma opção melhor.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considere a latência de dimensionamento de cópia de segurança ou reduzir verticalmente operações

Pode demorar 10 a 20 minutos para uma operação de dimensionamento concluir. Quando configurar uma agenda personalizada, planeie esse atraso. Por exemplo, o se tiver o tamanho do cluster ser 20 às 9:00, defina o acionador de agenda para um momento anterior como 8 às 11:30, para que a operação de dimensionamento for concluída, 9:00.

### <a name="preparation-for-scaling-down"></a>Preparação para reduzir verticalmente

Durante o processo no dimensionamento do cluster, o dimensionamento automático irá desativar os nós de acordo com o tamanho de destino. Se existir estiver executando tarefas em nós, o dimensionamento automático irá esperar até que as tarefas são concluídas. Uma vez que cada nó de trabalho também serve uma função no HDFS, os dados temporários irão ser mudou para os nós restantes. Portanto, deve verificar se existe espaço suficiente em nós restantes para alojar todos os dados temporários. 

As tarefas em execução irão continuar a executar e concluir. As tarefas pendentes irão esperar para ser agendado as normal com menos nós de trabalho disponíveis.

## <a name="monitoring"></a>Monitorização

### <a name="cluster-status"></a>Estado do cluster

O estado do cluster listado no portal do Azure pode ajudá-lo a monitorizar atividades de dimensionamento automático.

![Ativar a opção de dimensionamento automático com base na carga de nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todas as mensagens de estado de cluster que poderá ver são explicadas na lista abaixo.

| Estado do cluster | Explicação |
|---|---|
| Em Execução | O cluster está a funcionar normalmente. Todas as atividades de dimensionamento automático anteriores foram concluídas com êxito. |
| A atualizar  | A configuração de dimensionamento automático do cluster está a ser atualizada.  |
| Configuração do HDInsight  | Um cluster de aumentar ou reduzir verticalmente a operação está em curso.  |
| Erro de atualização  | HDInsight detetou problemas durante a atualização de configuração de dimensionamento automático. Os clientes podem optar por repita a atualização ou desativar o dimensionamento automático.  |
| Erro  | Algo está errado com o cluster e não é utilizável. Eliminar este cluster e criar um novo.  |

Para ver o número atual de nós no seu cluster, vá para o **tamanho do Cluster** do gráfico no **descrição geral** página para o seu cluster, ou clique em **tamanho do Cluster** em  **Definições**.

### <a name="operation-history"></a>Histórico de operação

Pode ver o histórico de ampliação e redução vertical de cluster como parte das métricas de cluster. Também pode listar todas as ações de dimensionamento ao longo do dia anterior, semana ou outro período de tempo.

Selecione **métricas** sob **monitorização**. Em seguida, clique em **adicionar métrica** e **número de trabalhadores Active** do **métrica** na caixa pendente. Clique no botão no canto superior direito para alterar o intervalo de tempo.

![Ativar a opção de dimensionamento automático com base na agenda do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Passos Seguintes

* Leia sobre as melhores práticas para dimensionar clusters manualmente em [melhores práticas de dimensionamento](hdinsight-scaling-best-practices.md)
