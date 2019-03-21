---
title: Resolver problemas de um cluster de HDInsight lento ou a falhar - Azure HDInsight
description: Diagnosticar e resolver problemas de um cluster de HDInsight lento ou a falhar.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 0129a09383b59aa5d213ef7ff1c78f23588472a7
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295475"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Resolver problemas de um cluster do HDInsight lento ou a falhar

Se um cluster do HDInsight está a ser executadas lentamente ou falhar com um código de erro, tem várias opções de resolução de problemas. Se as tarefas estão a demorar mais tempo a executar o que o esperado, ou se está a ver tempos de resposta lento em geral, pode haver falhas a montante do seu cluster, como os serviços em que o cluster é executado. No entanto, a causa mais comum destes abrandamentos é insuficiente de dimensionamento. Quando cria um novo cluster do HDInsight, selecione o adequado [tamanhos de máquinas virtuais](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Para diagnosticar um cluster lento ou a falhar, recolha informações sobre todos os aspectos do ambiente, como serviços associados do Azure, configuração de cluster e informações de execução de tarefa. Um diagnóstico útil é tentar reproduzir o estado de erro no outro cluster.

* Passo 1: Recolha dados sobre o problema.
* Passo 2: Validam o ambiente de cluster do HDInsight.
* Passo 3: Ver o estado de funcionamento do seu cluster.
* Passo 4: Reveja a pilha de ambiente e versões.
* Passo 5: Examine os ficheiros de registo do cluster.
* Passo 6: Verifique as definições de configuração.
* Passo 7: Reproduza a falha num cluster diferente.

## <a name="step-1-gather-data-about-the-issue"></a>Passo 1: Recolher dados sobre o problema

HDInsight fornece várias ferramentas que pode utilizar para identificar e resolver problemas com os clusters. Os seguintes passos guiá-lo através destas ferramentas e fornecem sugestões para indicar o problema.

### <a name="identify-the-problem"></a>Identificar o problema

Para ajudar a identificar o problema, considere as seguintes perguntas:

* O que esperar acontecer? Em vez disso, o que aconteceu?
* O tempo que o processo demorou a execução? O intervalo de tempo deve ser ter executado?
* Tem minha tarefas sempre executado lentamente neste cluster? Eles executasse o mais rapidamente num cluster diferente?
* Quando esse problema pela primeira vez Ocorreu? A frequência com que ele aconteceu desde?
* Nada foi alterado na minha configuração de cluster?

### <a name="cluster-details"></a>Detalhes do cluster

Informações de cluster importantes incluem:

* Nome do cluster.
* Região de cluster – verificar a existência [interrupções de região](https://azure.microsoft.com/status/).
* Tipo de cluster do HDInsight e a versão.
* Tipo e número de instâncias do HDInsight especificado para os nós principais e de trabalho.

O portal do Azure pode fornecer estas informações:

![Portal do HDInsight Azure informações](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Também pode utilizar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Outra opção é usar o PowerShell. Para obter mais informações, consulte [Apache Hadoop gerir clusters no HDInsight com o Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Passo 2: Validam o ambiente de cluster do HDInsight

Cada cluster de HDInsight baseia-se em vários serviços do Azure e em software open-source, como o Apache HBase e o Apache Spark. Clusters do HDInsight também podem chamar outros serviços do Azure, tais como redes virtuais do Azure.  Uma falha do cluster pode ser causada por qualquer um dos serviços em execução no seu cluster, ou por um serviço externo.  Uma alteração de configuração do serviço de cluster também pode causar o falha do cluster.

### <a name="service-details"></a>Detalhes do serviço

* Verificar as versões de lançamento de biblioteca de código-fonte aberto.
* Verifique a existência [falhas no serviço Azure](https://azure.microsoft.com/status/).  
* Verificar a existência de limites de utilização do serviço do Azure. 
* Verifique a configuração de sub-rede de rede Virtual do Azure.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ver as definições de configuração de cluster com a IU do Ambari

Apache Ambari fornece gestão e monitorização de um cluster do HDInsight com uma IU da web e uma API de REST. Ambari está incluído nos clusters do HDInsight baseado em Linux. Selecione o **Dashboard de clusters** painel no portal do Azure HDInsight página.  Selecione o **dashboard de clusters do HDInsight** painel para abrir a IU do Ambari e introduza as credenciais de início de sessão do cluster.  

![IU do Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Para abrir uma lista de vistas de serviço, selecione **vistas Ambari** na página do portal do Azure.  Esta lista depende de quais bibliotecas são instaladas. Por exemplo, poderá ver o Gestor de filas do YARN, a vista do Hive e a exibição de Tez.  Selecione uma ligação de serviço para ver a configuração e informações de serviço.

#### <a name="check-for-azure-service-outages"></a>Verificação para as falhas de serviço do Azure

HDInsight depende de vários serviços do Azure. Ele é executado servidores virtuais no Azure HDInsight, arquivos de dados e scripts no armazenamento de Blobs do Azure ou no armazenamento do Azure Data Lake, e ficheiros de registo de índices no armazenamento de tabelas do Azure. As interrupções para estes serviços, embora seja raro, podem causar problemas no HDInsight. Se tiver inesperados abrandamentos ou falhas no seu cluster, verifique os [Azure Status Dashboard](https://azure.microsoft.com/status/). O estado de cada serviço é listado por região. Verifique o seu cluster região e também regiões para todos os serviços relacionados.

#### <a name="check-azure-service-usage-limits"></a>Verifique os limites de utilização do serviço do Azure

Se a lançar um cluster grande, ou se tiver iniciado o muitos clusters em simultâneo, um cluster pode falhar se tiver excedido um limite de serviço do Azure. Limites do serviço variam, dependendo da sua subscrição do Azure. Para obter mais informações, consulte [subscrição do Azure e limites do serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Pode solicitar que o Microsoft aumentar o número de recursos do HDInsight disponíveis (por exemplo, núcleos de VMS e instâncias de VM) com um [pedido de aumento de quota de núcleos do Resource Manager](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Verifique a versão de lançamento

Compare a versão do cluster com a versão mais recente do HDInsight. Cada versão do HDInsight inclui aprimoramentos como novos aplicativos, recursos, patches e correções de erros. O problema que está a afetar o seu cluster possam ter sido corrigido na versão de lançamento mais recente. Se possível, volte a executar o cluster recorrendo a versão mais recente do HDInsight e bibliotecas associadas, como o Apache HBase, Apache Spark e outros.

#### <a name="restart-your-cluster-services"></a>Reinicie os serviços de cluster

Se estiver a sofrer abrandamentos no seu cluster, considere reiniciar seus serviços através da IU do Ambari ou a CLI clássica do Azure. O cluster pode estar com erros transitórios e reiniciar é a forma mais rápida para estabilizar o seu ambiente e, possivelmente, melhorar o desempenho.

## <a name="step-3-view-your-clusters-health"></a>Passo 3: Ver estado de funcionamento do seu cluster

Clusters do HDInsight são compostos por diferentes tipos de nós em execução em instâncias de máquina virtual. Cada nó pode ser monitorizada de carência de recursos, problemas de conectividade de rede e outros problemas que podem abrandar o cluster. Cada cluster contém dois nós principais e a maioria dos tipos de cluster contêm uma combinação de trabalho e nós de extremidade. 

Para obter uma descrição de vários nós utiliza de cada tipo de cluster, consulte [configurar clusters no HDInsight com o Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

As secções seguintes descrevem como verificar o estado de funcionamento de cada nó e do cluster geral.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Obtém um instantâneo do Estado de funcionamento do cluster com o dashboard de interface do Usuário do Ambari

O [dashboard da IU do Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) fornece uma descrição geral do Estado de funcionamento do cluster, como tempo de atividade, memória, rede e a utilização da CPU, utilização de disco do HDFS e assim por diante. Utilize a secção de anfitriões do Ambari para ver os recursos num nível de anfitrião. Também pode parar e reiniciar serviços.

### <a name="check-your-webhcat-service"></a>Verifique o seu serviço de WebHCat

Um cenário comum de falha nas tarefas de Apache Hive, Apache Pig ou Apache Sqoop é uma falha com o [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (ou *Templeton*) service. WebHCat é uma interface REST para execução de tarefa remota, como o Hive, Pig, contexto e MapReduce. WebHCat se traduz os pedidos de envio do trabalho em aplicativos do Apache Hadoop YARN e retorna um status derivado do Estado da aplicação YARN.  As secções seguintes descrevem os códigos de estado de HTTP de WebHCat comuns.

#### <a name="badgateway-502-status-code"></a>BadGateway (código de estado 502)

Esta é uma mensagem genérica de nós de gateway e é o código de estado de falha mais comuns. Uma causa possível para que isso é o serviço de WebHCat sendo para baixo no nó principal do Active Directory. Para verificar essa possibilidade, utilize o comando CURL seguinte:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari apresenta um alerta que mostra os anfitriões em que o serviço de WebHCat está desativado. Pode tentar colocar o serviço de WebHCat cópia de segurança, reiniciando o serviço no respetivo anfitrião.

![Restart WebHCat Server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Se um servidor de WebHCat ainda não surgir, verifique o registo de operações para mensagens de falha. Para obter mais informações, consulte a `stderr` e `stdout` arquivos referenciados no nó.

#### <a name="webhcat-times-out"></a>WebHCat exceder o tempo limite

Um Gateway do HDInsight exceder o tempo limite as respostas que demoram mais de dois minutos, retornando `502 BadGateway`. Serviços YARN para Estados das tarefas de consulta de WebHCat e, se YARN demora mais de dois minutos a responder, que pode tempo limite do pedido.

Neste caso, reveja os seguintes registos no `/var/log/webhcat` diretório:

* **webhcat.log** é o registo log4j para os registos de escritas de servidor
* **webhcat console.log** é o stdout do servidor quando iniciado
* **consola de webhcat-error.log** é stderr de processo do servidor

> [!NOTE]  
> Cada `webhcat.log` será transferido diariamente, geração de ficheiros com o nome `webhcat.log.YYYY-MM-DD`. Selecione o ficheiro adequado para o intervalo de tempo que está a investigar.

As secções seguintes descrevem algumas causas possíveis para tempos limite de WebHCat.

##### <a name="webhcat-level-timeout"></a>Tempo limite de nível de WebHCat

Quando WebHCat está sob carga, com mais de 10 soquetes abertos, demora mais tempo a estabelecer novas ligações de socket, que podem resultar num tempo limite. Para listar as ligações de rede para e de WebHCat, utilize `netstat` no nó principal atual do Active Directory:

```bash
netstat | grep 30111
```

30111 é a porta de que escuta de WebHCat na. O número de soquetes abertos deve ser inferior a 10.

Se não existirem não existem soquetes abertos, o comando anterior não produz um resultado. Para verificar se Templeton está ativo e à escuta na porta 30111, utilize:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Tempo limite de nível de YARN

Templeton chama YARN para executar tarefas e a comunicação entre Templeton e YARN pode fazer com que um tempo limite.

Ao nível do YARN, existem dois tipos de tempos limite:

1. Submeter uma tarefa YARN pode demorar muito tempo suficiente para fazer com que um tempo limite.

    Se abrir o `/var/log/webhcat/webhcat.log` ficheiro de registo e procure "tarefa em fila", poderá ver várias entradas em que o tempo de execução é excessivamente longo (> 2000 ms), com entradas que mostra o aumento de tempos de espera.

    O tempo para as tarefas em fila continua a aumentar, porque a taxa a que novas tarefas são enviadas é maior do que a taxa a que os trabalhos antigos são concluídos. Assim que a memória YARN é 100% utilizado, o *joblauncher fila* já não pode empresta a capacidade dos *fila padrão*. Por conseguinte, não mais novas tarefas podem ser aceites para a fila de joblauncher. Esse comportamento pode fazer com que o tempo de espera para se tornar mais e mais tempo, causando um erro de tempo limite que é, normalmente, seguido de muitos outros.

    A imagem seguinte mostra a fila de joblauncher em % 714.4 usado em excesso. Isso é aceitável, desde que existe capacidade ainda livre na fila de predefinição emprestam. No entanto, quando o cluster seja totalmente utilizado e a memória YARN é atingiu o limite de 100%, novas tarefas têm de aguardar, que, eventualmente, faz com que o tempo limite.

    ![Fila de Joblauncher](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Existem duas formas de resolver este problema: Reduza a velocidade de novas tarefas a ser submetidas, ou aumentar a velocidade de consumo tarefas antigos, aumente verticalmente o cluster.

2. Processamento de YARN pode demorar muito tempo, que pode provocar tempos limite.

    * Liste todas as tarefas: Esta é uma chamada demorada. Esta chamada enumera os aplicativos a partir do ResourceManager do YARN e para cada aplicativo concluído, obtém o estado de JobHistoryServer o YARN. Com o número mais elevado de tarefas, essa chamada pode tempo limite.

    * Lista de tarefas com mais de sete dias: O JobHistoryServer de YARN do HDInsight está configurado para manter informações de tarefa concluída durante sete dias (`mapreduce.jobhistory.max-age-ms` valor). A tentar enumerar os resultados de tarefas eliminados num tempo limite.

Para diagnosticar esses problemas:

1. Determinar o intervalo de tempo UTC para resolver problemas
2. Selecionar o `webhcat.log` ficheiro (s)
3. Procure mensagens de erro e aviso durante esse período

#### <a name="other-webhcat-failures"></a>Outras falhas de WebHCat

1. Código de estado HTTP 500

    Na maioria dos casos em que o WebHCat devolve 500, a mensagem de erro contém detalhes sobre a falha. Caso contrário, examine `webhcat.log` para mensagens de aviso e erro.

2. Falhas de tarefas

    Pode haver casos em que as interações com WebHCat tenham êxito, mas as tarefas estão a falhar.

    Templeton recolhe o resultado da consola de tarefa como `stderr` em `statusdir`, que é muitas vezes útil para resolução de problemas. `stderr` contém o identificador da aplicação YARN da consulta real.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Passo 4: Reveja a pilha de ambiente e versões

IU do Ambari **Stack e a versão** página fornece informações sobre o histórico de versões do cluster services configuration e serviço.  Versões de biblioteca de serviço Hadoop incorretas podem ser uma causa da falha do cluster.  Na IU do Ambari, selecione o **administrador** menu e, em seguida **pilhas e versões**.  Selecione o **versões** separador na página para ver informações de versão do serviço:

![Pilha e versões](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Passo 5: Examinar os arquivos de log

Existem muitos tipos de registos que são gerados a partir dos vários serviços e componentes que compõem um cluster do HDInsight. [Ficheiros de registo de WebHCat](#check-your-webhcat-service) são descritos anteriormente. Existem vários outros útil arquivos de log que pode investigar para restringir a problemas com o seu cluster, conforme descrito nas seções a seguir.

* Clusters do HDInsight são compostas por vários nós, a maioria dos que têm como trabalho para executar os trabalhos submetidos. Tarefas são executadas em simultâneo, mas os ficheiros de registo podem apresentar apenas os resultados linearmente. HDInsight executa novas tarefas, outras pessoas que não são concluir primeiro a terminar. Todos os esta atividade é registada para o `stderr` e `syslog` ficheiros.

* Os ficheiros de registo de ação de script mostram erros ou alterações de configuração inesperado durante o processo de criação do seu cluster.

* Os registos de passo de Hadoop identificam as tarefas do Hadoop iniciadas como parte de um passo que contêm erros.

### <a name="check-the-script-action-logs"></a>Verifique os registos de ação de script

HDInsight [ações de script](hdinsight-hadoop-customize-cluster-linux.md) executar scripts no cluster manualmente ou quando especificado. Por exemplo, ações de script podem ser utilizadas para instalar software adicional no cluster ou para alterar as definições de configuração os valores predefinidos. A verificar os registos de ação de script, pode fornecer informações sobre os erros que ocorreram durante a configuração do cluster e a configuração.  Pode ver o estado de uma ação de script, selecionando o **ops** botão na IU do Ambari, ou ao aceder aos registos da conta de armazenamento predefinida.

Os registos de ação de script residem no `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` diretório.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Ver registos de HDInsight com ligações rápidas do Ambari

IU do Ambari HDInsight inclui diversas **ligações rápidas** secções.  Para acessar os links de registo para um determinado serviço no seu cluster do HDInsight, abra a IU do Ambari para o seu cluster, em seguida, selecione a ligação de serviço a partir da lista à esquerda. Selecione o **ligações rápidas** lista pendente, em seguida, o HDInsight nó de interesse e, em seguida, selecione a ligação para o seu registo associado.

Por exemplo, para registos do HDFS:

![Ligações de Ambari rápido para ficheiros de registo](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Ver ficheiros de registo gerado pelo Hadoop

Um cluster do HDInsight gera registos que são escritos no armazenamento de Blobs do Azure e de tabelas do Azure. YARN cria seus próprios registos de execução. Para obter mais informações, consulte [gerir os registos para um cluster do HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Reveja os registos da área dinâmica

Capturas de área dinâmica para dados contém um instantâneo de memória do aplicativo, incluindo os valores das variáveis nessa altura, que são úteis para diagnosticar problemas que ocorrem no tempo de execução. Para obter mais informações, consulte [registos da área dinâmica de ativação para os serviços do Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Passo 6: Verifique as definições de configuração

HDInsight clusters estão pré-configuradas com configurações padrão para serviços relacionados, como Hadoop, Hive, HBase e assim por diante. Dependendo do tipo de cluster, a configuração do hardware, o número de nós, os tipos de tarefas estiver a executar e os dados estiver a trabalhar com (e como esses dados está a ser processados), poderá ter de otimizar sua configuração.

Para obter instruções detalhadas sobre a otimização de configurações de desempenho para a maioria dos cenários, consulte [otimizar as configurações de cluster com o Apache Ambari](hdinsight-changing-configs-via-ambari.md). Ao utilizar o Spark, consulte [tarefas de otimizar o Apache Spark para um desempenho](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Passo 7: Reproduzir a falha num cluster diferente

Para ajudar a diagnosticar a origem de um erro de cluster, inicie um novo cluster com a mesma configuração e, em seguida, volte a submeter passos da tarefa falhada individualmente. Verificar os resultados de cada passo antes de processar a próxima Sílaba. Este método dá-lhe a oportunidade de corrigir e volte a executar um único passo com falhas. Esse método também tem a vantagem de apenas uma vez a carregar os dados de entrada.

1. Crie um novo cluster de teste com a mesma configuração do cluster com falhas.
2. Submeta o primeiro passo de tarefa para o cluster de teste.
3. Quando o passo conclui o processamento, verifique se existem erros nos ficheiros de registo de passo. Ligar ao nó de principal do cluster de teste e ver os ficheiros de registo existe. Os ficheiros de registo do passo apenas são apresentados depois do passo for executado durante algum tempo, estiver concluída, ou falha.
4. Se a primeira etapa foi concluída com êxito, execute o passo seguinte. Se ocorreram erros, investigue o erro nos ficheiros de registo. Caso tenha sido um erro no seu código, fazer a correção e volte a executar o passo.
5. Continue até que todos os passos executados sem erros.
6. Quando tiver concluído a depuração do cluster de teste, eliminá-lo.

## <a name="next-steps"></a>Passos Seguintes

* [Manage HDInsight clusters by using the Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)
* [Analisar registos do HDInsight](hdinsight-debug-jobs.md)
* [Sessão de aplicação do acesso Apache Hadoop YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Ativar capturas de área dinâmica para dados dos serviços de Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Problemas conhecidos para o cluster do Apache Spark no HDInsight](hdinsight-apache-spark-known-issues.md)
