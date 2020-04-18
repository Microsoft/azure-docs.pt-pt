---
title: Escala automaticamente os clusters Azure HDInsight
description: Utilize a função De escala Automática Azure HDInsight para clusters de escala de Hadoop Apache automaticamente
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4f9b43b6f800bb47942ccc00fee0fac4536d2ec0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640586"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Escala automaticamente os clusters Azure HDInsight

> [!Important]
> A funcionalidade Azure HDInsight Autoscale foi lançada para disponibilidade geral a 7 de novembro de 2019 para os clusters Spark e Hadoop e incluiu melhorias não disponíveis na versão de pré-visualização da funcionalidade. Se criou um cluster Spark antes de 7 de novembro de 2019 e pretende utilizar a funcionalidade De Escala Automática no seu cluster, o caminho recomendado é criar um novo cluster e ativar a Escala Automática no novo cluster.
>
> A escala automática para clusters De Consulta Interativa (LLAP) e HBase ainda está em pré-visualização. A escala automática só está disponível nos clusters Spark, Hadoop, Interactive Query e HBase.

A função de cluster Autoscale da Azure HDInsight escala automaticamente o número de nós de trabalhador num cluster para cima e para baixo. Outros tipos de nós no cluster não podem ser escalados atualmente.  Durante a criação de um novo cluster HDInsight, pode ser definido um número mínimo e máximo de nós de trabalhadores. A escala automática monitoriza então os requisitos de recursos da carga de análise e escala o número de nós dos trabalhadores para cima ou para baixo. Não há nenhuma taxa adicional para esta funcionalidade.

## <a name="cluster-compatibility"></a>Compatibilidade do cluster

A tabela seguinte descreve os tipos de cluster e versões compatíveis com a função De escala Automática.

| Versão | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 sem ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 4.0 sem ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 3.6 com ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |
| HDInsight 4.0 com ESP | Sim | Sim | Sim | Sim* | Não | Não | Não |

\*Os clusters HBase só podem ser configurados para escalas baseadas em horários, não baseadas em carga.

## <a name="how-it-works"></a>Como funciona

Pode escolher a escala baseada na carga ou a escalação baseada em horários para o seu cluster HDInsight. A escala baseada em carga altera o número de nós no seu cluster, dentro de uma gama que definiu, para garantir uma utilização ótima do CPU e minimizar o custo de funcionamento.

A escala baseada na programação altera o número de nós no seu cluster com base em condições que têm efeito em momentos específicos. Estas condições escalam o cluster para um número pretendido de nós.

### <a name="metrics-monitoring"></a>Monitorização de métricas

A escala automática monitoriza continuamente o cluster e recolhe as seguintes métricas:

|Métrica|Descrição|
|---|---|
|CpU total pendente|O número total de núcleos necessários para iniciar a execução de todos os contentores pendentes.|
|Total de Memória Pendente|A memória total (em MB) necessária para iniciar a execução de todos os recipientes pendentes.|
|CpU total grátis|A soma de todos os núcleos não utilizados nos nós dos trabalhadores ativos.|
|Memória Total Livre|A soma da memória não utilizada (em MB) nos nós dos trabalhadores ativos.|
|Memória Usada por Nó|A carga num nó de trabalhador. Um nó de trabalhador em que é utilizado 10 GB de memória é considerado sob mais carga do que um trabalhador com 2 GB de memória usada.|
|Número de Mestrados de Aplicação por Nó|O número de contentores De Aplicação Master (AM) em funcionamento num nó de trabalhador. Um nó de trabalhador que alberga dois contentores AM, é considerado mais importante do que um nó de trabalhador que alberga zero contentores AM.|

As métricas acima são verificadas a cada 60 segundos. A escala automática toma decisões com base nestas métricas.

### <a name="load-based-scale-conditions"></a>Condições de escala baseadas em carga

Quando forem detetadas as seguintes condições, a Escala Automática emitirá um pedido de escala:

|Aumento vertical|Redução da escala|
|---|---|
|O CPU total pendente é maior do que o TOTAL de CPU gratuito por mais de 3 minutos.|O CPU total pendente é inferior ao total de CPU gratuito por mais de 10 minutos.|
|A memória total pendente é maior do que a memória total gratuita por mais de 3 minutos.|A memória total pendente é inferior a memória gratuita total por mais de 10 minutos.|

Para a escala, a Escala Automática emite um pedido de escala para adicionar o número necessário de nós. A escala baseia-se no número de novos nódosos operários necessários para satisfazer os atuais requisitos de CPU e memória.

Para reduzir a escala, a Escala Automática emite um pedido para remover um certo número de nós. A redução da escala baseia-se no número de recipientes AM por nó. E os atuais requisitos de CPU e memória. O serviço também deteta quais os nódosos candidatos à remoção com base na execução atual do emprego. A operação de redução da escala primeiro desativa os nós e, em seguida, retira-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Criar um cluster com autoscalcificação baseado em carga

Para ativar a função de escala automática com escala à base de carga, complete os seguintes passos como parte do processo normal de criação do cluster:

1. No separador **de preços Configuração +,** selecione a caixa de verificação **enable autoscale.**
1. Selecione **baseado em carga sob** o tipo de escala **automática**.
1. Introduza os valores pretendidos para as seguintes propriedades:  

    * Número inicial **de nós** para o **nó operário.**
    * **Número min** de nós operários.
    * **Número máximo** de nós operários.

    ![Ativar a escala automática baseada em carga do nó do trabalhador](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

O número inicial de nós dos trabalhadores deve cair entre o mínimo e o máximo, inclusive. Este valor define o tamanho inicial do cluster quando é criado. O número mínimo de nós dos trabalhadores deve ser fixado em três ou mais. Escalar o seu cluster para menos de três nós pode resultar em ficar preso em modo de segurança devido à replicação insuficiente do ficheiro.  Para mais informações, consulte [Ficar preso em modo de segurança](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Criar um cluster com autoscalcificação baseado em horários

Para ativar a função De escala Automática com escala baseada no horário, complete os seguintes passos como parte do processo normal de criação do cluster:

1. No separador **de preços Configuração +,** verifique a caixa de verificação **enable autoscale.**
1. Introduza o **número de nós** para o nó **operário,** que controla o limite para a escala ção do cluster.
1. Selecione a opção **Baseada no horário sob** o tipo de escala **automática**.
1. **Selecione Configurar** para abrir a janela de configuração de **escala automática.**
1. Selecione o seu fuso horário e, em seguida, clique **em + Adicionar condição**
1. Selecione os dias da semana a que a nova condição deve aplicar-se.
1. Editar o tempo que a condição deve produzir efeito e o número de nós para os os dois a que o cluster deve ser escalado.
1. Adicione mais condições se necessário.

    ![Ativar a criação baseada no horário do nó dos trabalhadores](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

O número de nós deve estar entre 3 e o número máximo de nós de trabalhador que inseriu antes de adicionar condições.

### <a name="final-creation-steps"></a>Passos finais da criação

Selecione o tipo VM para os nódosos dos trabalhadores selecionando um VM da lista de abandono em tamanho do **nó**. Depois de escolher o tipo VM para cada tipo de nó, pode ver a gama de custos estimada para todo o cluster. Ajuste os tipos vM para se adaptar ao seu orçamento.

![Ativar o tamanho do nó de escala automática baseado no nó do nó do nó do nó do nó do nó do nó do nó do nó](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

A sua subscrição tem uma quota de capacidade para cada região. O número total de núcleos dos seus nós de cabeça e os nós máximos dos trabalhadores não podem exceder a quota de capacidade. No entanto, esta quota é um limite suave; você pode sempre criar um bilhete de apoio para o aumentar facilmente.

> [!Note]  
> Se exceder o limite total de quota-núcleo, receberá uma mensagem de erro dizendo que "o nó máximo excedeu os núcleos disponíveis nesta região, por favor escolha outra região ou contacte o suporte para aumentar a quota".

Para obter mais informações sobre a criação de clusterS HDInsight utilizando o portal Azure, consulte [Create Linux clusters em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo de Gestor de Recursos

#### <a name="load-based-autoscaling"></a>Autoscalcificação baseada em carga

Pode criar um cluster HDInsight com autoscalcificação baseado em carga `autoscale` num modelo `computeProfile`  >  `workernode` de Gestor `minInstanceCount` de Recursos Azure, adicionando um nó à secção com as propriedades e `maxInstanceCount` como mostrado no snippet json abaixo.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
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

#### <a name="schedule-based-autoscaling"></a>Autoscalcificação baseada em horários

Pode criar um cluster HDInsight com um modelo de Gestor de Recursos `autoscale` Azure baseado `computeProfile`  >  `workernode` em horários, adicionando um nó à secção. O `autoscale` nó contém `recurrence` um `timezone` que `schedule` tem um e que descreve quando a mudança vai ocorrer.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Ativar e desativar a escala automática para um cluster de execução

#### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para ativar a escala automática num cluster de execução, selecione **o tamanho do cluster** em **definições**. Em seguida, selecione **Ativar a escala automática**. Selecione o tipo de escala Automática que deseja e introduza as opções para escalas baseadas em carga ou para agenda. Finalmente, selecione **Guardar**.

![Ativar o cluster de funcionamento de escala automática baseada em horário sinuoso do nó do trabalhador](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Utilizar a API REST

Para ativar ou desativar a escala automática num cluster de funcionamento utilizando a API REST, faça um pedido POST para o ponto final de escala automática:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Utilize os parâmetros adequados na carga útil do pedido. A carga útil json abaixo poderia ser usada para ativar a escala automática. Utilize a `{autoscale: null}` carga útil para desativar a escala automática.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Consulte a secção anterior sobre [a escala automática baseada em carga](#load-based-autoscaling) para obter uma descrição completa de todos os parâmetros de carga útil.

## <a name="guidelines"></a>Diretrizes

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Escolha a escala baseada em carga ou em horário

Considere os seguintes fatores antes de tomar uma decisão sobre qual o modo a escolher:

* Ativar a escala automática durante a criação do cluster.
* O número mínimo de nós deve ser pelo menos três.
* Variação da carga: a carga do cluster segue um padrão consistente em momentos específicos, em dias específicos. Caso contrário, o agendamento baseado em carga é uma opção melhor.
* Requisitos de SLA: A escala automática é reativa em vez de preditiva. Haverá um atraso suficiente entre quando a carga começa a aumentar e quando o cluster precisa de estar no seu tamanho-alvo? Se existem requisitos sla rigorosos e a carga for um padrão conhecido fixo, "baseado em horários" é uma opção melhor.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considere a latência da escala para cima ou para baixo operações

Pode levar 10 a 20 minutos para que uma operação de escalação esteja concluída. Ao configurar um horário personalizado, planeje este atraso. Por exemplo, se precisar do tamanho do cluster para ser 20 às 9:00, detete o gatilho para um horário mais precoce, como 8:30 AM, de modo a que a operação de escala esteja concluída até às 9:00 da manhã.

### <a name="preparation-for-scaling-down"></a>Preparação para a escala

Durante o processo de escala de cluster, a Escala Automática irá desativar os nós para atingir o tamanho do alvo. Se as tarefas estiverem a decorrer nesses nós, a Escala Automática aguardará até que as tarefas estejam concluídas. Uma vez que cada nó de trabalhador também desempenha um papel no HDFS, os dados temporários serão transferidos para os restantes nós. Então deve certificar-se de que há espaço suficiente nos restantes nós para alojar todos os dados temporários.

Os trabalhos de corrida continuarão. Os postos de trabalho pendentes vão esperar pelo agendamento com menos nós de trabalhadordisponível.

### <a name="minimum-cluster-size"></a>Tamanho mínimo do cluster

Não reduza o seu agrupamento para menos de três nós. Escalar o seu cluster para menos de três nós pode resultar em ficar preso em modo de segurança devido à replicação insuficiente do ficheiro.  Para mais informações, consulte [Ficar preso em modo de segurança](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="monitoring"></a>Monitorização

### <a name="cluster-status"></a>Estado do cluster

O estado do cluster listado no portal Azure pode ajudá-lo a monitorizar as atividades de escala automática.

![Ativar o estado de cluster de autoescala baseado em autoescala do nó do trabalhador](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todas as mensagens de estado do cluster que pode ver estão explicadas na lista abaixo.

| Estado do cluster | Descrição |
|---|---|
| A executar | O cluster está a funcionar normalmente. Todas as atividades anteriores da Autoscale foram concluídas com sucesso. |
| Atualização  | A configuração de escala automática do cluster está a ser atualizada.  |
| Configuração HDInsight  | Está em curso uma escala de cluster ou uma redução da operação.  |
| Atualizar erro  | O HDInsight encontrou problemas durante a atualização de configuração de escala automática. Os clientes podem optar por voltar a tentar a atualização ou desativar a escala automática.  |
| Erro  | Há algo de errado com o aglomerado, e não é utilizável. Elimine este cluster e crie um novo.  |

Para ver o número atual de nós no seu cluster, vá ao gráfico de **tamanho do Cluster** na página de visão **geral** para o seu cluster. Ou selecione **o tamanho do Cluster** em **Definições**.

### <a name="operation-history"></a>História da operação

Pode ver a história de escala de cluster e escala como parte das métricas do cluster. Também pode enumerar todas as ações de escala ao longo do último dia, semana ou outro período de tempo.

Selecione **Métricas** sob **monitorização**. Em seguida, **selecione Adicionar métrica** e Número de **Trabalhadores Ativos** da caixa de dropdown **Métrica.** Selecione o botão na parte superior direita para alterar o intervalo de tempo.

![Ativar a métrica de escala automática baseada no horário do nó do trabalhador](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Passos seguintes

Leia sobre as diretrizes para escalonar clusters manualmente nas [diretrizes](hdinsight-scaling-best-practices.md) de escala
