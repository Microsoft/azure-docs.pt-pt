---
title: Dimensionar automaticamente os clusters do Azure HDInsight
description: Usar o recurso de autoescala do Azure HDInsight para Apache Hadoop automaticamente os clusters de escala
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 5a8e641c8a1b29d657fe8b0eabf7657ab5973516
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666040"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Dimensionar automaticamente os clusters do Azure HDInsight

> [!Important]
> O recurso de dimensionamento automático só funciona para clusters Spark, Hive, LLAP e HBase criados depois de 8 de maio de 2019. 

O recurso de dimensionamento automático do cluster do Azure HDInsight dimensiona automaticamente o número de nós de trabalho em um cluster para cima e para baixo. Outros tipos de nós no cluster não podem ser dimensionados no momento.  Durante a criação de um novo cluster HDInsight, um número mínimo e máximo de nós de trabalho podem ser definidos. O dimensionamento automático monitora os requisitos de recursos da carga de análise e dimensiona o número de nós de trabalho para cima ou para baixo. Não há encargos adicionais para esse recurso.

## <a name="cluster-compatibility"></a>Compatibilidade de cluster

A tabela a seguir descreve os tipos de cluster e as versões que são compatíveis com o recurso de dimensionamento automático.

| Versão | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 sem ESP | Sim apenas 2,3| Sim | Não | Não | Não | Não | Não |
| HDInsight 4,0 sem ESP | Sim | Sim | Não | Não | Não | Não | Não |
| HDInsight 3,6 com ESP | Sim apenas 2,3 | Sim | Não | Não | Não | Não | Não |
| HDInsight 4,0 com ESP | Sim | Sim | Não | Não | Não | Não | Não |

## <a name="how-it-works"></a>Como funciona

Você pode escolher o dimensionamento baseado em carga ou o dimensionamento baseado em agendamento para o cluster HDInsight. O dimensionamento baseado em carga altera o número de nós no cluster, dentro de um intervalo definido, para garantir a utilização ideal da CPU e minimizar o custo de execução.

O dimensionamento baseado em agendamento altera o número de nós no cluster com base nas condições que entram em vigor em horários específicos. Essas condições dimensionam o cluster para um número de nós desejado.

### <a name="metrics-monitoring"></a>Monitoramento de métricas

O dimensionamento automático monitora continuamente o cluster e coleta as seguintes métricas:

* **Total de CPU pendente**: o número total de núcleos necessários para iniciar a execução de todos os contêineres pendentes.
* **Memória total pendente**: a memória total (em MB) necessária para iniciar a execução de todos os contêineres pendentes.
* **Total de CPU livre**: a soma de todos os núcleos não utilizados nos nós de trabalho ativos.
* **Memória livre total**: a soma da memória não utilizada (em MB) nos nós de trabalho ativos.
* **Memória usada por nó**: a carga em um nó de trabalho. Um nó de trabalho no qual são usados 10 GB de memória, é considerado sob mais carga do que um trabalhador com 2 GB de memória usada.
* **Número de mestres de aplicativo por nó**: o número de contêineres do aplicativo mestre (AM) em execução em um nó de trabalho. Um nó de trabalho que está hospedando dois contêineres AM é considerado mais importante do que um nó de trabalho que está hospedando zero contêineres AM.

As métricas acima são verificadas a cada 60 segundos. O dimensionamento automático faz decisões de redução e redução com base nessas métricas.

### <a name="load-based-cluster-scale-up"></a>Expansão de cluster com base em carga

Quando as condições a seguir forem detectadas, o dimensionamento automático emitirá uma solicitação de expansão:

* A CPU pendente total é maior que a CPU total livre por mais de 3 minutos.
* A memória total pendente é maior que a memória livre total por mais de 3 minutos.

O serviço HDInsight calcula quantos novos nós de trabalho são necessários para atender aos requisitos atuais de CPU e memória e, em seguida, emite uma solicitação de expansão para adicionar o número necessário de nós.

### <a name="load-based-cluster-scale-down"></a>Redução do cluster baseado em carga

Quando as seguintes condições forem detectadas, o dimensionamento automático emitirá uma solicitação de redução horizontal:

* A CPU pendente total é menor que a CPU total livre por mais de 10 minutos.
* A memória total pendente é menor que a memória livre total por mais de 10 minutos.

Com base no número de contêineres AM por nó e nos requisitos atuais de CPU e memória, o dimensionamento automático emite uma solicitação para remover um determinado número de nós. O serviço também detecta quais nós são candidatos para remoção com base na execução do trabalho atual. A operação de redução vertical primeiro encerra os nós e, em seguida, remove-os do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Criar um cluster com dimensionamento automático baseado em carga

Para usar o dimensionamento automático em um cluster, a opção **Habilitar dimensionamento automático** deve ser habilitada quando o cluster é criado. 

Para habilitar o recurso de dimensionamento automático com dimensionamento baseado em carga, conclua as seguintes etapas como parte do processo normal de criação do cluster:

1. Na guia **configuração + preços** , marque a caixa de seleção **Habilitar dimensionamento automático** .
1. Selecione **baseado em carga** em **tipo de dimensionamento automático**.
1. Insira os valores desejados para as seguintes propriedades:  

    * **Número inicial de nós** para o **nó de trabalho**.
    * Número **mínimo** de nós de trabalho.
    * Número **máximo** de nós de trabalho.

    ![Habilitar dimensionamento automático baseado em carga de nó de trabalho](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

O número inicial de nós de trabalho deve estar entre o mínimo e o máximo, inclusive. Esse valor define o tamanho inicial do cluster quando ele é criado. O número mínimo de nós de trabalho deve ser definido como três ou mais. . Dimensionar o cluster para menos de três nós pode fazer com que ele fique preso no modo de segurança devido à replicação de arquivo insuficiente. Veja [como ficar preso no modo de segurança]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) para obter mais informações.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Criar um cluster com dimensionamento automático baseado em agendamento

Para habilitar o recurso de dimensionamento automático com dimensionamento baseado em agendamento, conclua as seguintes etapas como parte do processo normal de criação do cluster:

1. Na guia **configuração + preços** , marque a caixa de seleção **Habilitar dimensionamento automático** .
1. Insira o **número de nós** para o **nó de trabalho**, que controla o limite para escalar verticalmente o cluster.
1. Selecione a opção **baseado em agendamento** em **tipo de dimensionamento automático**.
1. Clique em **Configurar** para abrir a janela **configuração de dimensionamento automático** .
1. Selecione seu fuso horário e clique em **+ Adicionar condição**
1. Selecione os dias da semana aos quais a nova condição deve ser aplicada.
1. Edite a hora em que a condição deve entrar em vigor e o número de nós para o qual o cluster deve ser dimensionado.
1. Adicione mais condições, se necessário.

    ![Habilitar a criação baseada em agendamento de nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

O número de nós deve estar entre 3 e o número máximo de nós de trabalho que você inseriu antes de adicionar condições.

### <a name="final-creation-steps"></a>Etapas de criação final

Para o dimensionamento baseado em carga e em agendamento, selecione o tipo de VM para nós de trabalho selecionando uma VM na lista suspensa em **tamanho do nó**. Depois de escolher o tipo de VM para cada tipo de nó, você poderá ver o intervalo de custo estimado para todo o cluster. Ajuste os tipos de VM para se ajustarem ao seu orçamento.

![Habilitar o tamanho do nó de dimensionamento automático baseado na agenda do nó de trabalho](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Sua assinatura tem uma cota de capacidade para cada região. O número total de núcleos dos nós de cabeçalho combinados com o número máximo de nós de trabalho não pode exceder a cota de capacidade. No entanto, essa cota é um limite flexível; Você sempre pode criar um tíquete de suporte para que ele seja aumentado com facilidade.

> [!Note]  
> Se você exceder o limite de cota de núcleo total, receberá uma mensagem de erro informando ' o nó máximo excedeu os núcleos disponíveis nesta região, escolha outra região ou entre em contato com o suporte para aumentar a cota. '

Para obter mais informações sobre a criação do cluster HDInsight usando o portal do Azure, consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo do Resource Manager

#### <a name="load-based-autoscaling"></a>Dimensionamento automático baseado em carga

Você pode criar um cluster HDInsight com dimensionamento automático baseado em carga um modelo de Azure Resource Manager, adicionando um nó `autoscale` à seção `computeProfile` > `workernode` com as propriedades `minInstanceCount` e `maxInstanceCount`, conforme mostrado no trecho de JSON abaixo.

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

Para obter mais informações sobre como criar clusters com modelos do Resource Manager, consulte [Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Dimensionamento automático baseado em agendamento

Você pode criar um cluster HDInsight com dimensionamento automático baseado em agendamento um modelo de Azure Resource Manager, adicionando um nó `autoscale` à seção `computeProfile` > `workernode`. O nó `autoscale` contém uma `recurrence` que tem um `timezone` e `schedule` que descreve quando ocorrerá a alteração.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Habilitar e desabilitar o dimensionamento automático para um cluster em execução

#### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para habilitar o dimensionamento automático em um cluster em execução, selecione **tamanho do cluster** em **configurações**. Em seguida, clique em **Habilitar dimensionamento automático**. Selecione o tipo de dimensionamento automático desejado e insira as opções para o dimensionamento baseado em carga ou em agendamento. Por fim, clique em **salvar**.

![Habilitar dimensionamento automático baseado em agenda de nó de trabalho executando cluster](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>Utilizar a API REST

Para habilitar ou desabilitar o dimensionamento automático em um cluster em execução usando a API REST, faça uma solicitação POST para o ponto de extremidade de dimensionamento automático, conforme mostrado no trecho de código abaixo:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Use os parâmetros apropriados na carga de solicitação. A carga JSON abaixo pode ser usada para habilitar o dimensionamento automático. Use o `{autoscale: null}` de carga para desabilitar o dimensionamento automático.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Consulte a seção anterior sobre como [habilitar o dimensionamento automático baseado em carga](#load-based-autoscaling) para obter uma descrição completa de todos os parâmetros de carga.

## <a name="best-practices"></a>Melhores práticas

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Escolhendo o dimensionamento baseado em carga ou em agendamento

Considere os seguintes fatores antes de tomar uma decisão sobre qual modo escolher:

* Habilite o dimensionamento automático durante a criação do cluster.
* O número mínimo de nós deve ser pelo menos três.
* Variação de carga: a carga do cluster segue um padrão consistente em horários específicos, em dias específicos. Caso contrário, o agendamento baseado em carga é uma opção melhor.
* Requisitos de SLA: o dimensionamento automático é reativo em vez de preditiva. Haverá um atraso suficiente entre o momento em que a carga começa a aumentar e quando o cluster precisa estar em seu tamanho de destino? Se houver requisitos estritos de SLA e a carga for um padrão conhecido fixo, a ' agenda baseada em ' será uma opção melhor.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considerar a latência de operações de escala ou redução vertical

Pode levar de 10 a 20 minutos para que uma operação de dimensionamento seja concluída. Ao configurar um agendamento personalizado, planeje esse atraso. Por exemplo, se você precisar que o tamanho do cluster seja 20 às 9:00 AM, defina o gatilho de agendamento como um tempo anterior, como 8:30, para que a operação de dimensionamento seja concluída pela 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Preparação para reduzir verticalmente

Durante o processo de redução do dimensionamento do cluster, o dimensionamento automático encerrará os nós para atender ao tamanho do destino. Se houver tarefas em execução nesses nós, o dimensionamento automático aguardará até que as tarefas sejam concluídas. Como cada nó de trabalho também serve uma função no HDFS, os dados temporários serão deslocados para os nós restantes. Portanto, você deve verificar se há espaço suficiente nos nós restantes para hospedar todos os dados temporários.

Os trabalhos em execução continuarão a ser executados e concluídos. Os trabalhos pendentes aguardarão para serem agendados normalmente com menos nós de trabalho disponíveis.

### <a name="minimum-cluster-size"></a>Tamanho mínimo do cluster

Não dimensione o cluster para menos de três nós. Dimensionar o cluster para menos de três nós pode fazer com que ele fique preso no modo de segurança devido à replicação de arquivo insuficiente. Veja [como ficar preso no modo de segurança]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) para obter mais informações.

## <a name="monitoring"></a>Monitorização

### <a name="cluster-status"></a>Estado do cluster

O status do cluster listado no portal do Azure pode ajudá-lo a monitorar as atividades de dimensionamento automático.

![Habilitar o status do cluster de dimensionamento automático baseado em carga do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todas as mensagens de status do cluster que você pode ver são explicadas na lista abaixo.

| Estado do cluster | Explicação |
|---|---|
| A executar | O cluster está funcionando normalmente. Todas as atividades de dimensionamento automático anteriores foram concluídas com êxito. |
| Atualizada  | A configuração de autoescala do cluster está sendo atualizada.  |
| Configuração do HDInsight  | Uma operação de expansão ou redução do cluster está em andamento.  |
| Erro de atualização  | O HDInsight encontrou problemas durante a atualização de configuração de dimensionamento automático. Os clientes podem optar por repetir a atualização ou desabilitar o dimensionamento automático.  |
| Erro  | Algo está errado com o cluster e não é utilizável. Exclua este cluster e crie um novo.  |

Para exibir o número atual de nós no cluster, acesse o gráfico **de tamanho do cluster** na página **visão geral** do cluster ou clique em **tamanho do cluster** em **configurações**.

### <a name="operation-history"></a>Histórico de operação

Você pode exibir o histórico de expansão e redução do cluster como parte das métricas do cluster. Você também pode listar todas as ações de dimensionamento no dia anterior, semana ou outro período de tempo.

Selecione **métricas** em **monitoramento**. Em seguida, clique em **Adicionar métrica** e **número de operadores ativos** na caixa suspensa de **métrica** . Clique no botão no canto superior direito para alterar o intervalo de tempo.

![Habilitar métrica de dimensionamento automático baseado em agenda de nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Passos seguintes

* Leia sobre as práticas recomendadas para dimensionar clusters manualmente em [práticas recomendadas de dimensionamento](hdinsight-scaling-best-practices.md)
