---
title: Problemas de resolução de um trabalho lento ou falhado no cluster Azure HDInsight
description: Diagnosticar e resolver problemas num trabalho lento ou falhado num cluster Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895324"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Resolver problemas de um trabalho lento ou com falhas num cluster HDInsight

Se um tratamento de aplicação de dados num cluster HDInsight estiver a funcionar lentamente ou a falhar com um código de erro, tem várias opções de resolução de problemas. Se os seus empregos estiverem a demorar mais tempo do que o esperado, ou se estiver a ver tempos de resposta lentos em geral, pode haver falhas a montante do seu cluster, como os serviços em que o cluster funciona. No entanto, a causa mais comum destes abrandamentos é a escalação insuficiente. Quando criar um novo cluster HDInsight, selecione os tamanhos de [máquina virtual apropriados](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Para diagnosticar um cluster lento ou em falência, recolha informações sobre todos os aspetos do ambiente, tais como serviços azure associados, configuração de cluster e informações de execução de emprego. Um diagnóstico útil é tentar reproduzir o estado de erro em outro cluster.

* Passo 1: Recolher dados sobre o assunto.
* Passo 2: Validar o ambiente de cluster HDInsight.
* Passo 3: Veja a saúde do seu cluster.
* Passo 4: Reveja a pilha de ambiente e as versões.
* Passo 5: Examine os ficheiros de registo do cluster.
* Passo 6: Verifique as definições de configuração.
* Passo 7: Reproduzir a falha num cluster diferente.

## <a name="step-1-gather-data-about-the-issue"></a>Passo 1: Recolher dados sobre o problema

O HDInsight fornece muitas ferramentas que pode usar para identificar e resolver problemas com clusters. Os seguintes passos guiam-no através destas ferramentas e fornecem sugestões para identificar o problema.

### <a name="identify-the-problem"></a>Identificar o problema

Para ajudar a identificar o problema, considere as seguintes questões:

* O que esperava que acontecesse? O que aconteceu em vez disso?
* Quanto tempo demorou o processo a decorrer? Quanto tempo deveria ter durado?
* As minhas tarefas sempre correram lentamente neste aglomerado? Correram mais depressa num aglomerado diferente?
* Quando é que este problema ocorreu pela primeira vez? Quantas vezes aconteceu desde então?
* Mudou alguma coisa na minha configuração de cluster?

### <a name="cluster-details"></a>Detalhes do cluster

Informações importantes sobre o cluster incluem:

* Nome do aglomerado.
* Região de aglomerado - verifique se há [interrupções na região.](https://azure.microsoft.com/status/)
* Tipo e versão do cluster HDInsight.
* Tipo e número de instâncias HDInsight especificadas para os nós da cabeça e do trabalhador.

O portal Azure pode fornecer esta informação:

![Informação do portal HDInsight Azure](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Também pode utilizar [o Azure CLI:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Outra opção é usar o PowerShell. Para mais informações, consulte [Os clusters DeHadoop Apache em HDInsight com A PowerShell Azure](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Passo 2: Validar o ambiente de cluster HDInsight

Cada cluster HDInsight depende de vários serviços Azure, e de software de código aberto, como Apache HBase e Apache Spark. Os clusters HDInsight também podem recorrer a outros serviços Azure, como redes virtuais Azure.  Uma falha de cluster pode ser causada por qualquer um dos serviços de funcionamento no seu cluster, ou por um serviço externo.  Uma alteração de configuração do serviço de cluster também pode fazer com que o cluster falhe.

### <a name="service-details"></a>Detalhes do serviço

* Verifique as versões de lançamento da biblioteca de código aberto.
* Verifique se o [Serviço Azure Para a paragem](https://azure.microsoft.com/status/).  
* Verifique os limites de utilização do Serviço Azure. 
* Verifique a configuração da subnet azure Virtual Network.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ver configurações de configuração de cluster com o Ambari UI

A Apache Ambari fornece gestão e monitorização de um cluster HDInsight com uma UI web e uma API REST. Ambari está incluído em clusters HDInsight baseados em Linux. Selecione o painel do Painel de **Cluster** na página HDInsight do portal Azure.  Selecione o painel de painel de **instrumentos de cluster HDInsight** para abrir o Ambari UI e introduza as credenciais de login do cluster.  

![Visão geral do painel apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Para abrir uma lista de vistas de serviço, selecione **Ambari Views** na página do portal Azure.  Esta lista depende das bibliotecas instaladas. Por exemplo, você pode ver YARN Queue Manager, Hive View e Tez View.  Selecione um link de serviço para ver informações de configuração e serviço.

#### <a name="check-for-azure-service-outages"></a>Verificar interrupções de serviço do Azure

O HDInsight conta com vários serviços Azure. Executa servidores virtuais no Azure HDInsight, armazena dados e scripts no armazenamento do Azure Blob ou no Armazenamento do Lago De Dados Azure, e indexa ficheiros de registo no armazenamento da Tabela Azure. As perturbações nestes serviços, embora raras, podem causar problemas no HDInsight. Se tiver abrandamentos ou falhas inesperadas no seu cluster, verifique o Painel de [Estado Do Estado Do Azure](https://azure.microsoft.com/status/). O estado de cada serviço é listado por região. Verifique a região do seu cluster e também as regiões para quaisquer serviços relacionados.

#### <a name="check-azure-service-usage-limits"></a>Verifique os limites de utilização do serviço Azure

Se estiver a lançar um grande cluster, ou tiver lançado muitos clusters simultaneamente, um cluster pode falhar se tiver ultrapassado um limite de serviço Azure. Os limites de serviço variam, dependendo da subscrição do Azure. Para obter mais informações, veja [Subscrição do Azure e limites de serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
Pode solicitar que a Microsoft aumente o número de recursos HDInsight disponíveis (como núcleos VM e instâncias VM) com um pedido de aumento de [quota do Gestor](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)de Recursos .

#### <a name="check-the-release-version"></a>Verifique a versão de lançamento

Compare a versão do cluster com a mais recente versão HDInsight. Cada versão HDInsight inclui melhorias como novas aplicações, funcionalidades, patches e correções de bugs. O problema que está a afetar o seu cluster pode ter sido corrigido na versão de lançamento mais recente. Se possível, regere o seu cluster utilizando a versão mais recente do HDInsight e bibliotecas associadas, tais como Apache HBase, Apache Spark, entre outros.

#### <a name="restart-your-cluster-services"></a>Reinicie os seus serviços de cluster

Se estiver a sofrer abrandamentos no seu cluster, considere reiniciar os seus serviços através do Ambari UI ou do Azure Classic CLI. O cluster pode estar a sofrer erros transitórios, e reiniciar é a forma mais rápida de estabilizar o seu ambiente e possivelmente melhorar o desempenho.

## <a name="step-3-view-your-clusters-health"></a>Passo 3: Veja a saúde do seu cluster

Os clusters HDInsight são compostos por diferentes tipos de nós em instâncias de máquinas virtuais. Cada nó pode ser monitorizado para a fome de recursos, problemas de conectividade da rede e outros problemas que podem abrandar o cluster. Cada cluster contém dois nós de cabeça, e a maioria dos tipos de cluster contém uma combinação de nódeos de trabalho e borda. 

Para uma descrição dos vários nós que cada tipo de cluster utiliza, consulte [Conjunto de clusters em HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

As seguintes secções descrevem como verificar a saúde de cada nó e do aglomerado geral.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Obtenha uma foto da saúde do cluster usando o painel ambari UI

O [painel ambari UI](#view-cluster-configuration-settings-with-the-ambari-ui) `https://<clustername>.azurehdinsight.net`() fornece uma visão geral da saúde do cluster, tais como o tempo de uptime, memória, uso da rede e CPU, utilização do disco HDFS, e assim por diante. Utilize a secção anfitriã de Ambari para visualizar os recursos a nível de hospedeiro. Também pode parar e reiniciar os serviços.

### <a name="check-your-webhcat-service"></a>Verifique o seu serviço WebHCat

Um cenário comum para a Apache Hive, Apache Pig ou Apache Sqoop jobs failing é uma falha com o serviço [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (ou *Templeton).* WebHCat é uma interface REST para execução remota de trabalho, tais como Hive, Pig, Scoop e MapReduce. WebHCat traduz os pedidos de submissão de emprego em aplicações YARN Apache Hadoop, e devolve um estado derivado do estado de aplicação yARN.  As seguintes secções descrevem códigos de estado comuns do WebHCat HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (código de estado 502)

Este código é uma mensagem genérica dos nós de gateway, e é o código de estado de falha mais comum. Uma das causas possíveis para isso é que o serviço WebHCat está no nó da cabeça ativa. Para verificar esta possibilidade, utilize o seguinte comando CURL:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari apresenta um alerta mostrando os anfitriões em que o serviço WebHCat está em baixo. Pode tentar repor o serviço WebHCat reiniciando o serviço no seu anfitrião.

![Apache Ambari Reiniciar WebHCat Server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Se um servidor WebHCat ainda não aparecer, verifique o registo de operações para obter mensagens de falha. Para obter informações `stderr` mais `stdout` detalhadas, verifique os ficheiros e ficheiros referenciados no nó.

#### <a name="webhcat-times-out"></a>WebHCat vezes fora

Um HDInsight Gateway vezes para fora respostas que demoram mais de dois minutos, retornando `502 BadGateway`. WebHCat consulta serviços de YARN para estatutode emprego, e se o YARN demorar mais de dois minutos para responder, esse pedido pode esgotar-se.

Neste caso, reveja os seguintes registos no `/var/log/webhcat` diretório:

* **webhcat.log** é o log log4j para o qual o servidor escreve registos
* **webhcat-console.log** é o stdout do servidor quando iniciado
* **webhcat-consola-error.log** é o stderr do processo do servidor

> [!NOTE]  
> Cada `webhcat.log` um é enrolado diariamente, `webhcat.log.YYYY-MM-DD`gerando ficheiros chamados . Selecione o ficheiro apropriado para o intervalo de tempo que está a investigar.

As seguintes secções descrevem algumas possíveis causas para os intervalos do WebHCat.

##### <a name="webhcat-level-timeout"></a>Intervalo de nível WebHCat

Quando o WebHCat está sob carga, com mais de 10 tomadas abertas, demora mais tempo a estabelecer novas ligações de tomadas, o que pode resultar num intervalo. Para listar as ligações de rede `netstat` de e para webHCat, utilize no atual headnode ativo:

```bash
netstat | grep 30111
```

30111 é a porta webHCat escuta. O número de tomadas abertas deve ser inferior a 10.

Se não houver tomadas abertas, o comando anterior não produz um resultado. Para verificar se Templeton está de pé e a ouvir no porto 30111, use:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Tempo de tempo livre para o nível do ARN

Templeton chama a ARN para executar trabalhos, e a comunicação entre Templeton e YARN pode causar um intervalo.

Ao nível do ARN, existem dois tipos de intervalos:

1. Submeter um trabalho de ARN pode demorar o suficiente para causar um intervalo.

    Se abrir `/var/log/webhcat/webhcat.log` o ficheiro de registo e procurar por "trabalho em fila", poderá ver várias entradas onde o tempo de execução é excessivamente longo (>2000 ms), com entradas que mostram cada vez mais tempo de espera.

    O tempo para os postos de trabalho em fila continua a aumentar, porque a taxa a que são apresentados novos postos de trabalho é superior à taxa de conclusão dos antigos postos de trabalho. Uma vez que a memória YARN é 100% utilizada, a fila do lançador de *emprego* já não pode pedir capacidade emprestada da *fila padrão*. Por conseguinte, não podem ser aceites mais novos postos de trabalho na fila do lançador de empregos. Este comportamento pode fazer com que o tempo de espera se torne mais longo e mais longo, causando um erro de tempo que é geralmente seguido por muitos outros.

    A imagem seguinte mostra a fila do lançador de emprego soa em 714,4% sobreutilizada. Isto é aceitável, desde que ainda haja capacidade gratuita na fila padrão para pedir emprestado. No entanto, quando o cluster é totalmente utilizado e a memória do ARN está a 100% da capacidade, novos empregos devem esperar, o que acaba por causar tempo.

    ![Vista de fila de lançador de job HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Há duas formas de resolver esta questão: ou reduzir a velocidade de novos postos de trabalho a serem apresentados, ou aumentar a velocidade de consumo de empregos antigos, aumentando o aglomerado.

2. O processamento de ARN pode demorar muito tempo, o que pode causar intervalos.

    * Lista de todos os empregos: Esta é uma chamada morosa. Esta chamada enumera as aplicações do YARN ResourceManager, e para cada aplicação concluída, obtém o estado do YARN JobHistoryServer. Com um maior número de empregos, esta chamada pode esgotar-se.

    * Lista de empregos com mais de sete dias: O HDInsight YARN JobHistoryServer está configurado para manter informações completas de emprego por sete dias (valor).`mapreduce.jobhistory.max-age-ms` Tentar enumerar os empregos purgados resulta num intervalo.

Para diagnosticar estas questões:

1. Determine o intervalo de tempo UTC para resolução de problemas
2. Selecione `webhcat.log` os ficheiros apropriados
3. Procure mensagens WARN e ERROR durante esse tempo

#### <a name="other-webhcat-failures"></a>Outras falhas do WebHCat

1. Código de estado HTTP 500

    Na maioria dos casos em que o WebHCat devolve 500, a mensagem de erro contém detalhes sobre a falha. Caso contrário, `webhcat.log` procure mensagens WARN e ERROR.

2. Falhas no emprego

    Pode haver casos em que as interações com o WebHCat são bem sucedidas, mas os trabalhos estão a falhar.

    Templeton recolhe a saída `stderr` da `statusdir`consola de trabalho como em , o que é muitas vezes útil para resolução de problemas. `stderr`contém o identificador de aplicação YARN da consulta real.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Passo 4: Rever a pilha de ambiente e versões

A página Ambari UI **Stack and Version** fornece informações sobre a configuração de serviços de cluster e o histórico de versão de serviço.  Versões incorretas da biblioteca de serviços Hadoop podem ser uma causa de falha de cluster.  No Ambari UI, selecione o menu **Desine** e, em seguida, **Pilhas e Versões**.  Selecione o separador **Versões** na página para ver informações sobre a versão do serviço:

![Pilhas e versões Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Passo 5: Examinar os ficheiros de registo

Existem muitos tipos de registos que são gerados a partir de muitos serviços e componentes que compõem um cluster HDInsight. [Os ficheiros de registo WebHCat](#check-your-webhcat-service) são descritos anteriormente. Existem vários outros ficheiros de registo úteis que pode investigar para reduzir os problemas com o seu cluster, conforme descrito nas seguintes secções.

* Os clusters HDInsight consistem em vários nós, a maioria dos quais são encarregados de executar trabalhos submetidos. Os trabalhos funcionam simultaneamente, mas os ficheiros de registo só podem apresentar resultados linearmente. O HDInsight executa novas tarefas, terminando outras que não conseguem completar primeiro. Toda esta atividade está `stderr` `syslog` registada nos ficheiros e ficheiros.

* Os ficheiros de registo de ação do script mostram erros ou alterações de configuração inesperadas durante o processo de criação do seu cluster.

* Os registos de passo hadoop identificam os postos de trabalho hadoop lançados como parte de um passo que contém erros.

### <a name="check-the-script-action-logs"></a>Verifique os registos de ação do script

[As ações](hdinsight-hadoop-customize-cluster-linux.md) de script HDInsight executam scripts no cluster manualmente ou quando especificadas. Por exemplo, as ações de script podem ser usadas para instalar software adicional no cluster ou para alterar as definições de configuração dos valores predefinidos. Verificar os registos de ação do script pode fornecer informações sobre os erros ocorridos durante a configuração e configuração do cluster.  Pode visualizar o estado de uma ação de script selecionando o botão **de operações** no UI Ambari ou acedendo aos registos a partir da conta de armazenamento predefinida.

Os registos de ação `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` do guião residem no diretório.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Ver registos HDInsight usando links rápidos Ambari

O HDInsight Ambari UI inclui uma série de secções de **Ligações Rápidas.**  Para aceder aos links de registo de um determinado serviço no seu cluster HDInsight, abra o Ambari UI para o seu cluster e, em seguida, selecione o link de serviço da lista à esquerda. Selecione o dropdown **quick Links,** em seguida, o nó de interesse HDInsight e, em seguida, selecione o link para o seu registo associado.

Por exemplo, para registos HDFS:

![Links rápidos ambari para ficheiros de registo](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Ver ficheiros de registo gerados por Hadoop

Um cluster HDInsight gera registos que são escritos para tabelas Azure e armazenamento Azure Blob. O YARN cria os seus próprios registos de execução. Para obter mais informações, consulte [Gerir registos para um cluster HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Rever depósitos de heap

As lixeiras contêm uma imagem instantânea da memória da aplicação, incluindo os valores das variáveis na época, que são úteis para diagnosticar problemas que ocorrem no tempo de execução. Para obter mais informações, consulte [a Enable heap dumps para os serviços Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Passo 6: Verificar as definições de configuração

Os clusters HDInsight são pré-configurados com configurações predefinidas para serviços relacionados, tais como Hadoop, Hive, HBase, e assim por diante. Dependendo do tipo de cluster, da sua configuração de hardware, do número de nós, dos tipos de trabalhos com que está a executar e dos dados com os que está a trabalhar (e como esses dados estão a ser processados), poderá ter de otimizar a sua configuração.

Para obter instruções detalhadas sobre a otimização das configurações de desempenho para a maioria dos cenários, consulte configurações de [cluster otimizadas com Apache Ambari](hdinsight-changing-configs-via-ambari.md). Ao utilizar a Spark, consulte [os trabalhos de Otimize Apache Spark para o desempenho.](spark/apache-spark-perf.md) 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Passo 7: Reproduzir a falha num cluster diferente

Para ajudar a diagnosticar a origem de um erro de cluster, inicie um novo cluster com a mesma configuração e, em seguida, reenvie os passos do trabalho falhado um a um. Verifique os resultados de cada passo antes de processar o próximo. Este método dá-lhe a oportunidade de corrigir e reexecutar um único passo falhado. Este método também tem a vantagem de carregar apenas os seus dados de entrada uma vez.

1. Crie um novo cluster de teste com a mesma configuração que o cluster falhado.
2. Submeta o primeiro passo de trabalho para o cluster de teste.
3. Quando o passo concluir o processamento, verifique se há erros nos ficheiros de registo de passos. Ligue-se ao nó principal do cluster de teste e veja os ficheiros de registo lá. Os ficheiros de registo de passo só aparecem após o passo funcionar durante algum tempo, termina ou falha.
4. Se o primeiro passo for bem sucedido, corra o próximo passo. Se houver erros, investigue o erro nos ficheiros de registo. Se foi um erro no seu código, faça a correção e refaça o passo.
5. Continue até que todos os passos corram sem erros.
6. Quando terminar de depurar o cluster de teste, elimine-o.

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters do HDInsight através da IU da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analisar registos HDInsight](hdinsight-debug-jobs.md)
* [Aceda ao sinal de aplicação YARN Apache Hadoop em HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Questões conhecidas para o cluster Apache Spark no HDInsight](hdinsight-apache-spark-known-issues.md)
