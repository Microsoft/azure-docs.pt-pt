---
title: Dimensionar automaticamente os clusters do Azure HDInsight
description: Utilize a função Azure HDInsight Autoscale para clusters de escala Apache Hadoop automaticamente
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 730df91d922c4bd6187748654f8184cfb7dc6ea0
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612712"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Dimensionar automaticamente os clusters do Azure HDInsight

A funcionalidade autoescala gratuita da Azure HDInsight pode aumentar ou diminuir automaticamente o número de nós de trabalhadores no seu cluster com base em critérios previamente definidos. Você define um número mínimo e máximo de nós durante a criação do cluster, estabelece os critérios de escala usando um horário diurno ou métricas de desempenho específicas, e a plataforma HDInsight faz o resto.

## <a name="how-it-works"></a>Como funciona

A funcionalidade Autoscale utiliza dois tipos de condições para desencadear eventos de escala: limiares para várias métricas de desempenho do cluster (chamada *escala baseada em carga)* e gatilhos baseados no tempo (chamado *escalamento baseado no horário).* O dimensionamento à base de carga altera o número de nós no seu cluster, dentro de um intervalo que definiu, para garantir uma utilização ideal do CPU e minimizar o custo de funcionamento. A escala baseada no horário altera o número de nós no seu cluster com base em operações que associa a datas e horários específicos.

O vídeo que se segue fornece uma visão geral dos desafios que a Autoscale resolve e como pode ajudá-lo a controlar os custos com o HDInsight.


> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Escolha de escala baseada em carga ou em escalas baseadas em horários

Considere os seguintes fatores ao escolher um tipo de escala:

* Variação da carga: a carga do cluster segue um padrão consistente em momentos específicos, em dias específicos? Caso contrário, o agendamento baseado na carga é uma opção melhor.
* Requisitos de SLA: A escala automática é reativa em vez de preditiva. Haverá um atraso suficiente entre quando a carga começa a aumentar e quando o cluster precisa estar no seu tamanho-alvo? Se houver requisitos rigorosos de SLA e a carga for um padrão conhecido fixo, 'base de agenda' é uma opção melhor.

### <a name="cluster-metrics"></a>Métricas de cluster

A autoescala continuamente o cluster e recolhe as seguintes métricas:

|Métrica|Descrição|
|---|---|
|CPU total pendente|O número total de núcleos necessários para iniciar a execução de todos os contentores pendentes.|
|Memória Total Pendente|A memória total (em MB) necessária para iniciar a execução de todos os recipientes pendentes.|
|CpU total gratuito|A soma de todos os núcleos não reutilizados nos nós dos trabalhadores ativos.|
|Memória Total Livre|A soma da memória nãousada (em MB) nos nós ativos do trabalhador.|
|Memória usada por nó|A carga num nó de trabalhador. Um nó de trabalhador no qual é utilizado 10 GB de memória, é considerado sob mais carga do que um trabalhador com 2 GB de memória usada.|
|Número de Mestrados de Aplicação por Nó|O número de contentores Diretor de Aplicação (AM) que funcionam num nó de trabalhador. Um nó operário que acolhe dois contentores AM, é considerado mais importante do que um nó de trabalhador que alberga contentores am zero.|

As métricas acima são verificadas a cada 60 segundos. Pode configurar operações de escala para o seu cluster utilizando qualquer uma destas métricas.

### <a name="load-based-scale-conditions"></a>Condições de escala à base de carga

Quando forem detetadas as seguintes condições, a Autoscale emitirá um pedido de escala:

|Aumento vertical|Escala para baixo|
|---|---|
|O CPU total pendente é superior ao CPU total gratuito por mais de 3 minutos.|O CPU total pendente é inferior ao total de CPU gratuito por mais de 10 minutos.|
|A memória total pendente é maior do que a memória total gratuita por mais de 3 minutos.|A memória total pendente é inferior à memória total gratuita por mais de 10 minutos.|

Para a escala, a Autoscale emite um pedido de escala para adicionar o número necessário de nós. A escala baseia-se no número de novos nós de trabalhadores necessários para satisfazer os atuais requisitos de CPU e memória.

Para uma escala para baixo, a Autoscale emite um pedido para remover um certo número de nós. A escala baseia-se no número de recipientes AM por nó. E os atuais requisitos de CPU e memória. O serviço também deteta quais os nós que são candidatos à remoção com base na execução de empregos em curso. A operação de escala para baixo primeiro desativa os nós e, em seguida, remove-os do cluster.

### <a name="cluster-compatibility"></a>Compatibilidade do cluster

> [!Important]
> A funcionalidade de Dimensionamento Automático do Azure HDInsight foi lançada para disponibilidade geral a 7 de novembro de 2019 para os clusters do Spark e do Hadoop com melhorias não disponíveis na versão de pré-visualização da funcionalidade. Se tiver criado um cluster do Spark antes de 7 de novembro de 2019 e quiser utilizar a funcionalidade de Dimensionamento Automático no cluster, o caminho recomendado será criar um novo cluster e ativar o Dimensionamento Automático no novo cluster.
>
> O Dimensionamento Automático para clusters do Interactive Query (LLAP) e do HBase ainda está em pré-visualização. O Dimensionamento Automático só está disponível nos clusters do Spark, do Hadoop, do Interactive Query e do HBase.

A tabela seguinte descreve os tipos e versões de cluster compatíveis com a função Autoscale.

| Versão | Spark | Hive | LLAP | HBase | Kafka | Tempestade | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 sem ESP | Yes | Yes | Yes | Sim* | No | No | No |
| HDInsight 4.0 sem ESP | Yes | Yes | Yes | Sim* | No | No | No |
| HDInsight 3.6 com ESP | Yes | Yes | Yes | Sim* | No | No | No |
| HDInsight 4.0 com ESP | Yes | Yes | Yes | Sim* | No | No | No |

\* Os clusters HBase só podem ser configurados para dimensionamento baseado em horários e não à base de carga.

## <a name="get-started"></a>Introdução

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Criar um cluster com autoscaling baseado em carga

Para ativar a funcionalidade Autoscale com escala à base de carga, complete os seguintes passos como parte do processo normal de criação do cluster:

1. No **separador Configuração + preços,** selecione a caixa **de verificação de escala automática Enable.**
1. Selecione **a base de carga** sob o tipo De escala **Automática**.
1. Introduza os valores pretendidos para as seguintes propriedades:  

    * Número inicial **de nós** para **nóiro.**
    * **Número** min de nós operários.
    * **Número máximo** de nós de trabalhadores.

    ![Permitir a autoescala de carga baseada no nó do trabalhador](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

O número inicial de nós de trabalhador deve descer entre o mínimo e o máximo, inclusive. Este valor define o tamanho inicial do cluster quando é criado. O número mínimo de nós de trabalhador deve ser fixado para três ou mais. Escalar o seu cluster para menos de três nós pode resultar em ficar preso em modo de segurança devido a uma replicação de ficheiros insuficiente.  Para obter mais informações, consulte [Ficar preso no modo de segurança.](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Criar um cluster com autoscaling baseado em horários

Para ativar a funcionalidade Autoscale com escalamento baseado no horário, complete os seguintes passos como parte do processo normal de criação de cluster:

1. No **separador Configuração + preços,** verifique a caixa **de verificação de escala automática Enable.**
1. Introduza o **número de nós** para o nó do **trabalhador,** que controla o limite para a ampliação do cluster.
1. Selecione a opção **Baseia-se em** horários com base no **tipo Autoscale**.
1. Selecione **Configurar** para abrir a janela **de configuração de escala automática.**
1. Selecione o seu timezone e, em seguida, clique **em + Adicionar condição**
1. Selecione os dias da semana a que a nova condição deve ser aplicável.
1. Editar o tempo em que a condição deve entrar em vigor e o número de nós a que o cluster deve ser dimensionado.
1. Adicione mais condições, se necessário.

    ![Permitir a criação baseada no horário do nó operário](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

O número de nós deve estar entre 3 e o número máximo de nós de trabalhador que inseriu antes de adicionar condições.

### <a name="final-creation-steps"></a>Etapas finais de criação

Selecione o tipo VM para nós de trabalhador selecionando um VM da lista de drop-down sob o **tamanho do nó**. Depois de escolher o tipo VM para cada tipo de nó, pode ver a gama de custos estimada para todo o cluster. Ajuste os tipos de VM para se adaptar ao seu orçamento.

![Ativar o tamanho do nó de escala automática baseado no nó do nó do nó do nó do nó de marcação automática](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

A sua subscrição tem uma quota de capacidade para cada região. O número total de núcleos dos seus nós de cabeça e os nós máximos dos trabalhadores não podem exceder a quota de capacidade. No entanto, esta quota é um limite suave; você sempre pode criar um bilhete de apoio para fazê-lo aumentado facilmente.

> [!Note]  
> Se exceder o limite total de quota de base, receberá uma mensagem de erro dizendo que "o nó máximo excedeu os núcleos disponíveis nesta região, por favor escolha outra região ou contacte o suporte para aumentar a quota."

Para obter mais informações sobre a criação de clusters HDInsight utilizando o portal Azure, consulte [criar clusters baseados em Linux em HDInsight utilizando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Crie um cluster com um modelo de Gestor de Recursos

#### <a name="load-based-autoscaling"></a>Autoscalagem baseada em carga

Pode criar um cluster HDInsight com autoscaling baseado em carga um modelo de Gestor de Recursos Azure, adicionando um `autoscale` nó à secção com as propriedades e como mostrado no `computeProfile`  >  `workernode` `minInstanceCount` `maxInstanceCount` snippet json abaixo. Para obter um modelo completo de gestor de recursos, consulte [o modelo Quickstart: Implementar o Cluster de Faíscas com autoescala](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased)de carga ativada .

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

#### <a name="schedule-based-autoscaling"></a>Autoscalagem baseada em horários

Pode criar um cluster HDInsight com um modelo de Gestor de Recursos Azure baseado em horários, adicionando um `autoscale` nó à `computeProfile`  >  `workernode` secção. O `autoscale` nó contém um que tem um e que descreve quando a mudança `recurrence` `timezone` `schedule` ocorrerá. Para obter um modelo completo de gestor de recursos, consulte [Implementar o Cluster de Faíscas com autoescalação baseada em horários Ativado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

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
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Ativar e desativar a Autoescala para um cluster de corrida

#### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para ativar a autoescalação num cluster de execução, selecione **o tamanho do Cluster** em **Definições**. Em seguida, **selecione Ativar automaticamente**. Selecione o tipo de Autoscale que deseja e introduza as opções para dimensionamento baseado em carga ou agendamento. Por fim, **selecione Save**.

![Ativar o cluster de corrida de escala automática baseado em horários de trabalhador](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Utilizar a API REST

Para ativar ou desativar a Autoescalação num cluster de funcionamento utilizando a API REST, faça um pedido DE POST para o ponto final de Autoescala:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Utilize os parâmetros adequados na carga útil do pedido. A carga json abaixo poderia ser usada para ativar a Autoscale. Utilize a carga útil `{autoscale: null}` para desativar a Autoscale.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Consulte a secção anterior sobre [a automatização da escala automática baseada na carga](#load-based-autoscaling) para obter uma descrição completa de todos os parâmetros de carga útil.

## <a name="monitoring-autoscale-activities"></a>Monitorização das atividades de autoescala

### <a name="cluster-status"></a>Estado do cluster

O estado do cluster listado no portal Azure pode ajudá-lo a monitorizar as atividades de Autoscale.

![Permitir o estado do cluster de carga auto-escala baseado no nó do trabalhador](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todas as mensagens de estado do cluster que pode ver são explicadas na lista abaixo.

| Estado do cluster | Descrição |
|---|---|
| Em Execução | O aglomerado está a funcionar normalmente. Todas as atividades anteriores da Autoscale foram concluídas com sucesso. |
| Atualização  | A configuração de escala automática do cluster está a ser atualizada.  |
| Configuração HDInsight  | Está em curso uma operação de escala de cluster para cima ou para baixo.  |
| Erro de Atualização  | O HDInsight reuniu problemas durante a atualização de configuração autoescala. Os clientes podem optar por voltar a tentar a atualização ou desativar a autoescala.  |
| Erro  | Há algo de errado com o aglomerado, e não é utilizável. Elimine este cluster e crie um novo.  |

Para ver o número atual de nós no seu cluster, vá ao gráfico de tamanho do **Cluster** na página **'Vista Geral'** para o seu cluster. Ou selecione **o tamanho do cluster** em **Definições**.

### <a name="operation-history"></a>História da operação

Pode ver a escala de cluster e a história de escala como parte das métricas do cluster. Também pode listar todas as ações de escala ao longo do último dia, semana ou outro período de tempo.

Selecione **métricas** em **monitorização**. Em seguida, **selecione Adicionar métrica** e **número de trabalhadores ativos** a partir da caixa de entrega **métrica.** Selecione o botão no canto superior direito para alterar o intervalo de tempo.

![Ativar a métrica de autoescala baseada em horários dos trabalhadores](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="other-considerations"></a>Outras considerações

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considere a latência de operações de escala para cima ou para baixo

Pode levar 10 a 20 minutos para uma operação de escalonamento ser concluída. Ao configurar um horário personalizado, planeie este atraso. Por exemplo, se precisar do tamanho do cluster para ser 20 às 9:00 da manhã, defina o gatilho do horário para uma hora anterior, como 8:30 AM, de modo que a operação de escala esteja concluída até às 9:00 da manhã.

### <a name="preparation-for-scaling-down"></a>Preparação para escalonamento

Durante o processo de dimensionamento do cluster, a Autoscale irá desativar os nós para atingir o tamanho do alvo. Se as tarefas estiverem a ser executadas nesses nós, a Autoscale aguardará até que as tarefas estejam concluídas. Uma vez que cada nó de trabalhador também desempenha um papel no HDFS, os dados temporários serão transferidos para os restantes nós. Então deve certificar-se de que há espaço suficiente nos nós restantes para hospedar todos os dados temporários.

Os trabalhos de corrida continuarão. Os postos de trabalho pendentes aguardarão o agendamento com menos nós de trabalhadores disponíveis.

### <a name="minimum-cluster-size"></a>Tamanho mínimo do cluster

Não reduza o seu aglomerado para menos de três nós. Escalar o seu cluster para menos de três nós pode resultar em ficar preso em modo de segurança devido a uma replicação de ficheiros insuficiente.  Para obter mais informações, consulte [Ficar preso no modo de segurança.](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)

### <a name="llap-daemons-count"></a>Contagem de Daemons LLAP

No caso de agrupamentos LLAP habilitados para autoescala, o evento de escala automática para cima/para baixo também escala o número de daemons LLAP para o número de nós de trabalhadores ativos. Mas esta mudança no número de daemões não persiste na **num_llap_nodes** config em Ambari. Se os serviços da Hive forem reiniciados manualmente, então o número de daemons LLAP será reposto de acordo com o config em Ambari.

Vamos levar o cenário abaixo:
1. Um cluster habilitado para a autoescala LLAP é criado com 3 nós de trabalhadores e a autoescala baseada em carga é ativada com nós mínimos de trabalhador como 3 e nós máximos de trabalhador como 10.
2. Os daemons LLAP contam config de acordo com a configuração LLAP e Ambari é 3, uma vez que o cluster foi criado com 3 nós de trabalhadores.
3. Em seguida, uma autoescalação é acionada devido à carga no cluster, o cluster é agora dimensionado para 10 nós.
4. A verificação de autoescalação em execução a intervalos regulares nota que a contagem de daemons LLAP é 3, mas o número de nó de trabalhador ativo é de 10, o processo de autoescalação irá agora aumentar a contagem de daemon LLAP para 10, mas esta alteração não persiste no Ambari Config - num_llap_nodes.
5. A autoescala está agora desativada.
6. O cluster conta agora com 10 nós operários e 10 daemons LLAP.
7. O serviço LLAP é reiniciado manualmente.
8. Durante o reinício, verifica o num_llap_nodes config na configuração LLAP e nota o valor como 3, pelo que gira 3 instâncias de daemons, mas o número de nóiros é de 10. Há agora um desfasamento entre os dois.

Quando isto acontece, precisamos de alterar manualmente a **configuração num_llap_node (Número de nós(s) para a execução do daemon Hive LLAP) em advanced hive-interactive-env** para corresponder à contagem atual do nó do trabalhador ativo.

**Nota**

Eventos de autoescala não alteram as **consultas concurrentes totais totais da** Hive em Ambari. Isto significa que o Serviço Interativo Hive Server 2 **pode lidar apenas com o número de consultas simultâneas em qualquer momento, mesmo que a contagem de daemons LLAP seja dimensionada para cima e para baixo com base na carga/horário**. A recomendação geral é definir este config para o cenário de utilização máxima para evitar a intervenção manual. No entanto, deve-se estar ciente de que **a fixação de um valor elevado para o máximo total de consultas simultâneas config pode falhar o reinício do serviço hive Server 2 Interactive se o número mínimo de nós de trabalhadores não conseguir acomodar o número determinado de Tez Ams (igual ao Total Máximo Total De consultas conig)**

## <a name="next-steps"></a>Passos seguintes

Leia sobre as diretrizes para dimensionamento manualmente em [diretrizes de escala](hdinsight-scaling-best-practices.md)
