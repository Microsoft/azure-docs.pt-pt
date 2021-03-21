---
title: Resolução de problemas num trabalho lento ou falhado no cluster Azure HDInsight
description: Diagnosticar e resolver problemas num aglomerado Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: eae5b5e1430f4e9bf1db62a4413e3b7abe3744cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699322"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Resolver problemas de um trabalho lento ou com falhas num cluster HDInsight

Se um dado de processamento de aplicação num cluster HDInsight estiver a funcionar lentamente ou a falhar com um código de erro, tem várias opções de resolução de problemas. Se os seus empregos estão a demorar mais tempo do que o esperado, ou se está a assistir a tempos de resposta lentos em geral, pode haver falhas a montante do seu cluster, como os serviços em que o cluster funciona. No entanto, a causa mais comum destes abrandamentos é a escalação insuficiente. Quando criar um novo cluster HDInsight, selecione os [tamanhos de máquinas virtuais apropriados](hdinsight-supported-node-configuration.md).

Para diagnosticar um cluster lento ou em falha, recolha informações sobre todos os aspetos do ambiente, tais como serviços azure associados, configuração de cluster e informação de execução de emprego. Um diagnóstico útil é tentar reproduzir o estado de erro em outro cluster.

* Passo 1: Recolher dados sobre o assunto.
* Passo 2: Validar o ambiente de cluster HDInsight.
* Passo 3: Veja a saúde do seu aglomerado.
* Passo 4: Rever a pilha de ambiente e as versões.
* Passo 5: Examinar os ficheiros de registo de cluster.
* Passo 6: Verifique as definições de configuração.
* Passo 7: Reproduzir a falha num cluster diferente.

## <a name="step-1-gather-data-about-the-issue"></a>Passo 1: Recolher dados sobre a questão

O HDInsight fornece muitas ferramentas que pode usar para identificar e resolver problemas com clusters. Os seguintes passos guiam-no através destas ferramentas e fornecem sugestões para identificar o problema.

### <a name="identify-the-problem"></a>Identificar o problema

Para ajudar a identificar o problema, considere as seguintes questões:

* O que esperava que acontecesse? O que aconteceu em vez disso?
* Quanto tempo demorou o processo a decorrer? Quanto tempo deveria ter durada?
* As minhas tarefas são sempre lentas neste aglomerado? Correram mais rápido num aglomerado diferente?
* Quando é que este problema ocorreu pela primeira vez? Quantas vezes aconteceu desde então?
* Mudou alguma coisa na minha configuração de agrupamento?

### <a name="cluster-details"></a>Detalhes do cluster

Informações importantes do cluster incluem:

* Nome do cluster.
* Região de cluster - verifique as [interrupções na região.](https://azure.microsoft.com/status/)
* Tipo e versão de cluster HDInsight.
* Tipo e número de casos HDInsight especificados para os nós da cabeça e do trabalhador.

O portal Azure pode fornecer esta informação:

![Informação do portal HDInsight Azure](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Também pode utilizar [O Azure CLI:](/cli/azure/)

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Outra opção é usar o PowerShell. Para obter mais informações, consulte  [gerir os clusters Apache Hadoop em HDInsight com a Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Passo 2: Validar o ambiente de cluster HDInsight

Cada cluster HDInsight conta com vários serviços Azure, e em software de código aberto como Apache HBase e Apache Spark. Os clusters HDInsight também podem recorrer a outros serviços Azure, como redes virtuais Azure.  Uma falha de cluster pode ser causada por qualquer um dos serviços de execução no seu cluster, ou por um serviço externo.  Uma alteração de configuração de serviço de cluster também pode fazer com que o cluster falhe.

### <a name="service-details"></a>Detalhes do serviço

* Verifique as versões de lançamento da biblioteca de código aberto.
* Verifique se há [paragens de serviço da Azure](https://azure.microsoft.com/status/).  
* Verifique os limites de utilização do Serviço Azure. 
* Verifique a configuração da sub-rede Azure Virtual Network.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ver configurações de configuração do cluster com a UI Ambari

Apache Ambari fornece gestão e monitorização de um cluster HDInsight com uma UI web e uma API REST. Ambari está incluído em clusters HDInsight baseados em Linux. Selecione o **painel de painel de instrumentos** do cluster na página HDInsight do portal Azure.  Selecione o painel **de painel de painel de cluster HDInsight** para abrir o UI Ambari e introduza as credenciais de login do cluster.  

![Visão geral do painel de ambari de Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Para abrir uma lista de visualizações de serviços, selecione **Ambari Views** na página do portal Azure.  Esta lista depende de quais bibliotecas estão instaladas. Por exemplo, pode ver YARN Queue Manager, Hive View e Tez View.  Selecione um link de serviço para ver as informações de configuração e serviço.

#### <a name="check-for-azure-service-outages"></a>Verificar interrupções de serviço do Azure

A HDInsight conta com vários serviços Azure. Executa servidores virtuais no Azure HDInsight, armazena dados e scripts no armazenamento de Azure Blob ou no Azure Data Lake Storage, e indexa ficheiros de registo no armazenamento da Tabela Azure. As perturbações nestes serviços, embora raras, podem causar problemas no HDInsight. Se tiver abrandamentos ou falhas inesperadas no seu cluster, verifique o Painel de Estado do [Azure](https://azure.microsoft.com/status/). O estado de cada serviço é listado por região. Consulte a região do seu cluster e também as regiões para quaisquer serviços relacionados.

#### <a name="check-azure-service-usage-limits"></a>Verifique os limites de utilização do serviço Azure

Se estiver a lançar um grande cluster, ou tiver lançado muitos clusters simultaneamente, um cluster pode falhar se tiver excedido um limite de serviço Azure. Os limites de serviço variam, dependendo da subscrição do Azure. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
Pode solicitar que a Microsoft aumente o número de recursos HDInsight disponíveis (tais como núcleos VM e instâncias VM) com um pedido de [aumento de quota de base do Gestor de Recursos](../azure-portal/supportability/resource-manager-core-quotas-request.md).

#### <a name="check-the-release-version"></a>Verifique a versão de lançamento

Compare a versão do cluster com a versão mais recente do HDInsight. Cada versão HDInsight inclui melhorias como novas aplicações, funcionalidades, patches e correções de bugs. O problema que está a afetar o seu cluster pode ter sido corrigido na versão mais recente. Se possível, reexame o seu cluster utilizando a versão mais recente do HDInsight e bibliotecas associadas, tais como Apache HBase, Apache Spark, entre outros.

#### <a name="restart-your-cluster-services"></a>Reinicie os seus serviços de cluster

Se estiver a sofrer abrandamentos no seu cluster, considere reiniciar os seus serviços através do Ambari UI ou do Azure Classic CLI. O cluster pode estar a sofrer erros transitórios, e reiniciar é a forma mais rápida de estabilizar o seu ambiente e possivelmente melhorar o desempenho.

## <a name="step-3-view-your-clusters-health"></a>Passo 3: Veja a saúde do seu cluster

Os clusters HDInsight são compostos por diferentes tipos de nós que estão a funcionar em casos de máquinas virtuais. Cada nó pode ser monitorizado para a fome de recursos, problemas de conectividade da rede e outros problemas que podem abrandar o cluster. Cada aglomerado contém dois nós de cabeça, e a maioria dos tipos de cluster contêm uma combinação de nós de trabalhador e borda. 

Para obter uma descrição dos vários nós que cada tipo de cluster utiliza, consulte [Configurar clusters em HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

As secções seguintes descrevem como verificar a saúde de cada nó e do aglomerado geral.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Obtenha uma foto da saúde do cluster usando o painel de instrumentos da UI Ambari

O [dashboard Ambari UI](#view-cluster-configuration-settings-with-the-ambari-ui) `https://<clustername>.azurehdinsight.net` () fornece uma visão geral da saúde do cluster, tais como o uptime, a memória, a utilização da rede e do CPU, o uso do disco HDFS, etc. Utilize a secção hosts de Ambari para ver os recursos a um nível de anfitrião. Também pode parar e reiniciar os serviços.

### <a name="check-your-webhcat-service"></a>Verifique o seu serviço WebHCat

Um cenário comum para os empregos de Apache Hive, Apache Pig ou Apache Sqoop falhando é uma falha com o serviço [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (ou *Templeton).* WebHCat é uma interface REST para execução remota de trabalho, como Hive, Pig, Scoop e MapReduce. O WebHCat traduz os pedidos de submissão de emprego em aplicações apache Hadoop YARN e devolve um estado derivado do estado da aplicação YARN.  As seguintes secções descrevem códigos de estado comuns do WebHCat HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (código de estado 502)

Este código é uma mensagem genérica dos nós de gateway, e é o código de estado de falha mais comum. Uma das causas possíveis para isso é o serviço WebHCat estar no nó de cabeça ativo. Para verificar esta possibilidade, utilize o seguinte comando CURL:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari apresenta um alerta mostrando os anfitriões em que o serviço WebHCat está em baixo. Pode tentar repor o serviço WebHCat reiniciando o serviço no seu anfitrião.

![Apache Ambari reiniciar servidor WebHCat](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Se um servidor WebHCat ainda não aparecer, verifique o registo de operações para obter mensagens de falha. Para obter informações mais detalhadas, verifique os `stderr` ficheiros e `stdout` ficheiros referenciados no nó.

#### <a name="webhcat-times-out"></a>Tempos de WebHCat

Um Gateway HDInsight times out respostas que demoram mais de dois minutos, voltando `502 BadGateway` . WebHCat consulta os serviços YARN para o status do trabalho, e se YARN demorar mais de dois minutos a responder, esse pedido pode sair.

Neste caso, reveja os seguintes registos no `/var/log/webhcat` diretório:

* **webhcat.log** é o log4j log para o qual o servidor escreve registos
* **webhcat-consola.log** é o stdout do servidor quando começou
* **webhcat-console-erro.log** é o stderr do processo do servidor

> [!NOTE]  
> Cada um `webhcat.log` é enrolado diariamente, gerando ficheiros chamados `webhcat.log.YYYY-MM-DD` . Selecione o ficheiro apropriado para o intervalo de tempo que está a investigar.

As seguintes secções descrevem algumas causas possíveis para os intervalos do WebHCat.

##### <a name="webhcat-level-timeout"></a>Tempo limite de nível WebHCat

Quando o WebHCat está sob carga, com mais de 10 tomadas abertas, demora mais tempo a estabelecer novas ligações de tomadas, o que pode resultar num intervalo. Para listar as ligações de rede de e para o WebHCat, utilize `netstat`  no cabeçano ativo atual:

```bash
netstat | grep 30111
```

30111 é a porta que o WebHCat ouve. O número de tomadas abertas deve ser inferior a 10.

Se não houver tomadas abertas, o comando anterior não produz um resultado. Para verificar se Templeton está de pé e a ouvir na porta 30111, use:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Tempo limite de nível YARN

Templeton chama YARN para gerir empregos, e a comunicação entre Templeton e YARN pode causar um tempo limite.

Ao nível do YARN, existem dois tipos de intervalos:

1. Submeter um trabalho de YARN pode demorar o suficiente para causar um tempo limite.

    Se abrir o `/var/log/webhcat/webhcat.log` ficheiro de registo e procurar "trabalho na fila", poderá ver várias entradas em que o tempo de execução é excessivamente longo (>2000 ms), com as entradas a mostrarem um aumento dos tempos de espera.

    O tempo para os postos de trabalho em fila continua a aumentar, porque a taxa a que são apresentados novos postos de trabalho é superior à taxa a que os antigos postos de trabalho estão concluídos. Uma vez que a memória YARN é 100% utilizada, a *fila do joblauncher* já não pode pedir capacidade emprestada da *fila predefinida*. Por conseguinte, não podem ser aceites mais novos postos de trabalho na fila do emprego. Este comportamento pode fazer com que o tempo de espera se torne cada vez mais longo, causando um erro de tempo limite que é geralmente seguido por muitos outros.

    A imagem a seguir mostra a fila de joblauncher em 714,4% sobreuso. Isto é aceitável desde que ainda haja capacidade livre na fila padrão para pedir emprestado. No entanto, quando o cluster é totalmente utilizado e a memória yarn está a 100% da capacidade, novos empregos devem esperar, o que eventualmente causa intervalos de tempo.

    ![Vista de fila do lançador HDInsight Job](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Há duas formas de resolver esta questão: ou reduzir a rapidez dos novos postos de trabalho que estão a ser apresentados, ou aumentar a velocidade de consumo dos empregos antigos, aumentando o cluster.

2. O processamento de FIOS pode demorar muito tempo, o que pode causar intervalos de tempo.

    * Enumerar todos os trabalhos: esta é uma chamada demorada. Esta chamada enumera as aplicações do YarN ResourceManager, e para cada aplicação concluída, obtém o estatuto do YARN JobHistoryServer. Com um maior número de empregos, esta chamada pode esgotar-se.

    * Trabalhos com mais de sete dias: O HDInsight YARN JobHistoryServer está configurado para reter informações de trabalho completas durante sete dias `mapreduce.jobhistory.max-age-ms` (valor). Tentar enumerar empregos purgados resulta num intervalo.

Para diagnosticar estas questões:

1. Determine o intervalo de tempo UTC para resolução de problemas
2. Selecione os `webhcat.log` ficheiros apropriados
3. Procure mensagens WARN e ERROR durante esse tempo

#### <a name="other-webhcat-failures"></a>Outras falhas do WebHCat

1. Código de estado HTTP 500

    Na maioria dos casos em que o WebHCat devolve 500, a mensagem de erro contém detalhes sobre a falha. Caso contrário, procure `webhcat.log` mensagens WARN e ERROR.

2. Falhas de emprego

    Pode haver casos em que as interações com o WebHCat são bem sucedidas, mas os trabalhos estão a falhar.

    Templeton recolhe a saída da consola de trabalho como `stderr` em , o que é `statusdir` frequentemente útil para a resolução de problemas. `stderr` contém o identificador de aplicação YARN da consulta real.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Passo 4: Rever a pilha de ambiente e versões

A página Ambari UI **Stack and Version** fornece informações sobre a configuração dos serviços de cluster e o histórico da versão de serviço.  As versões incorretas da biblioteca de serviços Hadoop podem ser uma causa de falha do cluster.  Na UI Ambari, selecione o menu **Admin** e, em seguida,  **Stacks e Versões**.  Selecione o separador **Versões** na página para ver as informações da versão de serviço:

![Pilha e versões Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Passo 5: Examinar os ficheiros de registo

Existem muitos tipos de registos que são gerados a partir dos muitos serviços e componentes que compõem um cluster HDInsight. [Os ficheiros de registo webHCat](#check-your-webhcat-service) são descritos anteriormente. Existem vários outros ficheiros de registo úteis que pode investigar para reduzir problemas com o seu cluster, conforme descrito nas seguintes secções.

* Os clusters HDInsight consistem em vários nós, a maioria dos quais são incumbidos de executar empregos submetidos. Os trabalhos são executados simultaneamente, mas os ficheiros de registo só podem exibir resultados linearmente. O HDInsight executa novas tarefas, terminando outras que não conseguem completar primeiro. Toda esta atividade está registada nos `stderr` `syslog` ficheiros e ficheiros.

* Os ficheiros de registo de ação do script mostram erros ou alterações inesperadas de configuração durante o processo de criação do seu cluster.

* Os registos de passos hadoop identificam os empregos da Hadoop lançados como parte de um passo contendo erros.

### <a name="check-the-script-action-logs"></a>Verifique os registos de ação do script

As [ações de script](hdinsight-hadoop-customize-cluster-linux.md) HDInsight executam scripts no cluster manualmente ou quando especificados. Por exemplo, as ações de script podem ser usadas para instalar software adicional no cluster ou para alterar as definições de configuração a partir dos valores padrão. A verificação dos registos de ação do script pode fornecer informações sobre os erros ocorridos durante a configuração e configuração do cluster.  Pode visualizar o estado de uma ação de script selecionando o botão **de operações** no UI Ambari ou acedendo aos registos a partir da conta de armazenamento predefinido.

Os registos de ação do script residem no `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` diretório.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Ver registos HDInsight usando Links Rápidos Ambari

O HDInsight Ambari UI inclui uma série de secções de **Links Rápidos.**  Para aceder aos links de registo de um determinado serviço no seu cluster HDInsight, abra o UI Ambari para o seu cluster e, em seguida, selecione o link de serviço da lista à esquerda. Selecione o dropdown **de Links Rápidos,** em seguida, o nó de interesse HDInsight e, em seguida, selecione o link para o seu registo associado.

Por exemplo, para registos HDFS:

![Ligações rápidas Ambari para registar ficheiros](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Ver ficheiros de registo gerados por Hadoop

Um cluster HDInsight gera troncos que são escritos para as mesas Azure e armazenamento Azure Blob. O YARN cria os seus próprios registos de execução. Para obter mais informações, consulte [Gerir registos para um cluster HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Rever depósitos de pilhas

Os depósitos de pilhas contêm uma imagem da memória da aplicação, incluindo os valores das variáveis da época, que são úteis para diagnosticar problemas que ocorrem em tempo de execução. Para obter mais informações, consulte [Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight baseado em Linux.](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="step-6-check-configuration-settings"></a>Passo 6: Verificar definições de configuração

Os clusters HDInsight são pré-configurados com definições padrão para serviços relacionados, tais como Hadoop, Hive, HBase, e assim por diante. Dependendo do tipo de cluster, da sua configuração de hardware, do seu número de nós, dos tipos de empregos que está a executar e dos dados com os que está a trabalhar (e de como esses dados estão a ser tratados), poderá ser necessário otimizar a sua configuração.

Para obter instruções detalhadas sobre a otimização das configurações de desempenho para a maioria dos cenários, consulte [configurações de cluster otimizar com Apache Ambari](hdinsight-changing-configs-via-ambari.md). Ao utilizar o Spark, consulte [os trabalhos da Otimize Apache Spark para desempenho.](spark/apache-spark-perf.md) 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Passo 7: Reproduzir a falha num cluster diferente

Para ajudar a diagnosticar a origem de um erro de cluster, inicie um novo cluster com a mesma configuração e, em seguida, reenvia os passos do trabalho falhado um a um. Verifique os resultados de cada passo antes de processar o próximo. Este método dá-lhe a oportunidade de corrigir e repetir um único passo falhado. Este método também tem a vantagem de carregar apenas os seus dados de entrada uma vez.

1. Crie um novo cluster de teste com a mesma configuração que o cluster falhado.
2. Submeta o primeiro passo de trabalho para o cluster de teste.
3. Quando o passo terminar o processamento, verifique se há erros nos ficheiros de registo de etapas. Ligue-se ao nó principal do cluster de teste e veja os ficheiros de registo lá. Os ficheiros de registo de passo só aparecem depois de o passo ser executado durante algum tempo, termina ou falha.
4. Se o primeiro passo for bem sucedido, corra o passo seguinte. Se houver erros, investigue o erro nos ficheiros de registo. Se foi um erro no seu código, faça a correção e reencaque o passo.
5. Continue até que todos os passos corram sem erros.
6. Quando terminar de depurar o cluster de teste, elimine-o.

## <a name="next-steps"></a>Passos seguintes

* [Manage HDInsight clusters by using the Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)
* [Analisar registos HDInsight](./hdinsight-troubleshoot-guide.md)
* [Aceder a sinal de aplicação Apache Hadoop YARN em HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Problemas conhecidos para o aglomerado de faíscas Apache em HDInsight](./spark/apache-spark-known-issues.md)