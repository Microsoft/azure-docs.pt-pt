---
title: Solucionar problemas de um trabalho lento ou com falha no cluster HDInsight do Azure
description: Diagnostique e solucione problemas de um trabalho lento ou com falha em um cluster do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 69711f7ac20882617de175b1b90d8df4f2858c4d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498083"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Solucionar problemas de um trabalho lento ou com falha em um cluster HDInsight

Se um aplicativo processando dados em um cluster HDInsight estiver sendo executado lentamente ou falhando com um código de erro, você terá várias opções de solução de problemas. Se seus trabalhos estiverem demorando mais para serem executados do que o esperado ou se você estiver vendo tempos de resposta lentos em geral, poderá haver falhas no fluxo do cluster, como os serviços em que o cluster é executado. No entanto, a causa mais comum desses gargalos é o dimensionamento insuficiente. Ao criar um novo cluster HDInsight, selecione os tamanhos de [máquina virtual](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)apropriados.

Para diagnosticar um cluster lento ou com falha, reúna informações sobre todos os aspectos do ambiente, como serviços do Azure associados, configuração de cluster e informações de execução do trabalho. Um diagnóstico útil é tentar reproduzir o estado de erro em outro cluster.

* Etapa 1: coletar dados sobre o problema.
* Etapa 2: validar o ambiente de cluster HDInsight.
* Etapa 3: exibir a integridade do cluster.
* Etapa 4: examinar a pilha e as versões do ambiente.
* Etapa 5: examinar os arquivos de log do cluster.
* Etapa 6: verificar as definições de configuração.
* Etapa 7: reproduzir a falha em um cluster diferente.

## <a name="step-1-gather-data-about-the-issue"></a>Etapa 1: coletar dados sobre o problema

O HDInsight fornece muitas ferramentas que você pode usar para identificar e solucionar problemas com clusters. As etapas a seguir o orientarão nessas ferramentas e fornecerão sugestões para identificar o problema.

### <a name="identify-the-problem"></a>Identificar o problema

Para ajudar a identificar o problema, considere as seguintes perguntas:

* O que eu esperava acontecer? O que aconteceu em vez disso?
* Quanto tempo o processo leva para ser executado? Quanto tempo deveria ser executado?
* As minhas tarefas sempre são executadas lentamente neste cluster? Eles foram executados mais rapidamente em um cluster diferente?
* Quando esse problema ocorreu pela primeira vez? Com que frequência isso aconteceu desde?
* Alguma coisa foi alterada na minha configuração de cluster?

### <a name="cluster-details"></a>Detalhes do cluster

Informações importantes do cluster incluem:

* Nome do cluster.
* Região do cluster-Verifique se há [interrupções de região](https://azure.microsoft.com/status/).
* Tipo e versão do cluster HDInsight.
* Tipo e número de instâncias do HDInsight especificadas para os nós de cabeçalho e de trabalho.

O portal do Azure pode fornecer essas informações:

![Informações de portal do Azure do HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Você também pode usar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Outra opção é usar o PowerShell. Para obter mais informações, consulte [gerenciar clusters Apache Hadoop no HDInsight com Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Etapa 2: validar o ambiente de cluster HDInsight

Cada cluster HDInsight depende de vários serviços do Azure e de software livre, como o Apache HBase e o Apache Spark. Os clusters HDInsight também podem chamar outros serviços do Azure, como redes virtuais do Azure.  Uma falha de cluster pode ser causada por qualquer um dos serviços em execução no cluster ou por um serviço externo.  Uma alteração de configuração do serviço de cluster também pode causar falha no cluster.

### <a name="service-details"></a>Detalhes do serviço

* Verifique as versões de lançamento da biblioteca de código aberto.
* Verifique se há [interrupções de serviço do Azure](https://azure.microsoft.com/status/).  
* Verifique os limites de uso do serviço do Azure. 
* Verifique a configuração de sub-rede da rede virtual do Azure.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Exibir definições de configuração de cluster com a interface do usuário do amAmbari

O Apache Ambari fornece gerenciamento e monitoramento de um cluster HDInsight com uma interface do usuário da Web e uma API REST. O Ambari está incluído em clusters HDInsight baseados em Linux. Selecione o painel **painel do cluster** na página portal do Azure HDInsight.  Selecione o painel de **painel do cluster HDInsight** para abrir a interface do usuário do amAmbari e insira as credenciais de logon do cluster.  

![Visão geral do painel do Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Para abrir uma lista de exibições de serviço, selecione **Ambari exibições** na página portal do Azure.  Essa lista depende de quais bibliotecas estão instaladas. Por exemplo, você pode ver YARN Queue Manager, Hive View e tez View.  Selecione um link de serviço para ver as informações de configuração e de serviço.

#### <a name="check-for-azure-service-outages"></a>Verificar se há interrupções de serviço do Azure

O HDInsight depende de vários serviços do Azure. Ele executa servidores virtuais no Azure HDInsight, armazena dados e scripts no armazenamento de BLOBs do Azure ou Azure Data Lake Storage e indexa arquivos de log no armazenamento de tabelas do Azure. As interrupções nesses serviços, embora raras, podem causar problemas no HDInsight. Se você tiver lentidão ou falhas inesperadas em seu cluster, verifique o [painel de status do Azure](https://azure.microsoft.com/status/). O status de cada serviço é listado por região. Verifique a região do cluster e também as regiões para quaisquer serviços relacionados.

#### <a name="check-azure-service-usage-limits"></a>Verificar os limites de uso do serviço do Azure

Se você estiver iniciando um cluster grande ou tiver iniciado vários clusters simultaneamente, um cluster poderá falhar se você tiver excedido um limite de serviço do Azure. Os limites de serviço variam de acordo com sua assinatura do Azure. Para obter mais informações, veja [Subscrição do Azure e limites, quotas e restrições do serviço](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Você pode solicitar que a Microsoft aumente o número de recursos do HDInsight disponíveis (como os núcleos de VM e instâncias de VM) com uma [solicitação de aumento de cota de núcleo do Resource Manager](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Verificar a versão de lançamento

Compare a versão do cluster com a versão mais recente do HDInsight. Cada versão do HDInsight inclui aprimoramentos, como novos aplicativos, recursos, patches e correções de bugs. O problema que está afetando seu cluster pode ter sido corrigido na versão de lançamento mais recente. Se possível, execute novamente o cluster usando a versão mais recente do HDInsight e bibliotecas associadas, como Apache HBase, Apache Spark e outros.

#### <a name="restart-your-cluster-services"></a>Reinicie os serviços de cluster

Se você estiver experimentando lentidão no cluster, considere reiniciar seus serviços por meio da interface do usuário do amAmbari ou da CLI clássica do Azure. O cluster pode estar apresentando erros transitórios e a reinicialização é a maneira mais rápida de estabilizar seu ambiente e, possivelmente, melhorar o desempenho.

## <a name="step-3-view-your-clusters-health"></a>Etapa 3: exibir a integridade do cluster

Os clusters HDInsight são compostos de diferentes tipos de nós em execução em instâncias de máquina virtual. Cada nó pode ser monitorado para consumo de recursos, problemas de conectividade de rede e outros problemas que podem retardar o cluster. Cada cluster contém dois nós de cabeçalho e a maioria dos tipos de cluster contém uma combinação de nós de trabalho e de borda. 

Para obter uma descrição dos vários nós que cada tipo de cluster usa, consulte [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md).

As seções a seguir descrevem como verificar a integridade de cada nó e do cluster geral.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Obter um instantâneo da integridade do cluster usando o painel de interface do usuário do amAmbari

O [painel de interface do usuário do Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) fornece uma visão geral da integridade do cluster, como tempo de atividade, memória, rede e uso da CPU, uso de disco do HDFS e assim por diante. Use a seção hosts de Ambari para exibir recursos em um nível de host. Você também pode parar e reiniciar os serviços do.

### <a name="check-your-webhcat-service"></a>Verifique o serviço WebHCat

Um cenário comum para trabalhos Apache Hive, Apache Pig ou Apache Sqoop falha é uma falha com o serviço [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (ou *Templeton*). WebHCat é uma interface REST para execução de trabalho remota, como Hive, Pig, contexto e MapReduce. O WebHCat converte as solicitações de envio de trabalho em aplicativos Apache Hadoop YARN e retorna um status derivado do status do aplicativo YARN.  As seções a seguir descrevem códigos de status HTTP WebHCat comuns.

#### <a name="badgateway-502-status-code"></a>BadGateway (código de status 502)

Esse código é uma mensagem genérica de nós de gateway e são os códigos de status de falha mais comuns. Uma causa possível para isso é o serviço WebHCat estar inoperante no nó de cabeçalho ativo. Para verificar essa possibilidade, use o seguinte comando de ONDULAção:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari exibe um alerta mostrando os hosts nos quais o serviço WebHCat está inoperante. Você pode tentar colocar o serviço WebHCat novamente reiniciando o serviço em seu host.

![Servidor do WebHCat de reinicialização do Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Se um servidor WebHCat ainda não vier, verifique o log de operações em busca de mensagens de falha. Para obter informações mais detalhadas, verifique os arquivos de `stderr` e `stdout` referenciados no nó.

#### <a name="webhcat-times-out"></a>WebHCat tempo limite excedido

O gateway de HDInsight An expira as respostas que levam mais de dois minutos, retornando `502 BadGateway`. WebHCat consulta os status do YARN dos serviços de trabalho e, se YARN levar mais de dois minutos para responder, essa solicitação poderá atingir o tempo limite.

Nesse caso, examine os seguintes logs no diretório `/var/log/webhcat`:

* **webhcat. log** é o log de Log4J no qual o servidor grava logs
* **webhcat-console. log** é o stdout do servidor quando iniciado
* **webhcat-console-Error. log** é o stderr do processo do servidor

> [!NOTE]  
> Cada `webhcat.log` é transferida sobre o diário, gerando arquivos nomeados `webhcat.log.YYYY-MM-DD`. Selecione o arquivo apropriado para o intervalo de tempo que você está investigando.

As seções a seguir descrevem algumas causas possíveis para tempos limite do WebHCat.

##### <a name="webhcat-level-timeout"></a>Tempo limite de nível de WebHCat

Quando WebHCat está sob carga, com mais de 10 soquetes abertos, leva mais tempo para estabelecer novas conexões de soquete, o que pode resultar em um tempo limite. Para listar as conexões de rede de e para WebHCat, use `netstat` no cabeçalho ativo atual:

```bash
netstat | grep 30111
```

30111 é a porta WebHCat escuta. O número de soquetes abertos deve ser inferior a 10.

Se não houver nenhum Soquete aberto, o comando anterior não produzirá um resultado. Para verificar se o Templeton está ativo e ouvindo na porta 30111, use:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Tempo limite de nível de YARN

Templeton chama YARN para executar trabalhos e a comunicação entre Templeton e YARN pode causar um tempo limite.

No nível de YARN, há dois tipos de tempo limite:

1. O envio de um trabalho YARN pode levar muito tempo para causar um tempo limite.

    Se você abrir o arquivo de log `/var/log/webhcat/webhcat.log` e procurar "trabalho em fila", poderá ver várias entradas em que o tempo de execução é excessivamente longo (> 2000 MS), com entradas mostrando tempos de espera crescentes.

    O tempo para os trabalhos em fila continua a aumentar porque a taxa na qual novos trabalhos são enviados é maior do que a taxa na qual os trabalhos antigos são concluídos. Depois que a memória YARN for 100% usada, a *fila joblauncher* não poderá mais emprestar a capacidade da *fila padrão*. Portanto, nenhum novo trabalho pode ser aceito na fila joblauncher. Esse comportamento pode fazer com que o tempo de espera se torne mais longo e mais longo, causando um erro de tempo limite que geralmente é seguido por muitos outros.

    A imagem a seguir mostra a fila joblauncher em 714,4% usada. Isso é aceitável desde que ainda haja capacidade livre na fila padrão para a qual fazer o empréstimo. No entanto, quando o cluster é totalmente utilizado e a memória YARN está a 100% de capacidade, novos trabalhos devem aguardar, o que eventualmente causa tempos limite.

    ![Exibição de fila do iniciador de trabalho do HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Há duas maneiras de resolver esse problema: Reduza a velocidade de envio de novos trabalhos ou aumente a velocidade de consumo de trabalhos antigos ao escalar verticalmente o cluster.

2. O processamento de YARN pode levar muito tempo, o que pode causar tempos limite.

    * Listar todos os trabalhos: essa é uma chamada demorada. Essa chamada enumera os aplicativos do ResourceManager YARN e, para cada aplicativo concluído, obtém o status de YARN JobHistoryServer. Com números mais altos de trabalhos, essa chamada pode atingir o tempo limite.

    * Listar trabalhos com mais de sete dias: o JobHistoryServer HDInsight YARN está configurado para reter informações de trabalho concluídas por sete dias (`mapreduce.jobhistory.max-age-ms` valor). A tentativa de enumerar os trabalhos limpos resulta em um tempo limite.

Para diagnosticar esses problemas:

1. Determinar o intervalo de tempo UTC para solucionar problemas
2. Selecionar os arquivos de `webhcat.log` apropriados
3. Procurar mensagens de aviso e de erro durante esse tempo

#### <a name="other-webhcat-failures"></a>Outras falhas de WebHCat

1. Código de status HTTP 500

    Na maioria dos casos em que WebHCat retorna 500, a mensagem de erro contém detalhes sobre a falha. Caso contrário, procure `webhcat.log` mensagens de aviso e de erro.

2. Falhas de trabalho

    Pode haver casos em que as interações com WebHCat são bem-sucedidas, mas os trabalhos estão falhando.

    O Templeton coleta a saída do console de trabalho como `stderr` em `statusdir`, que é geralmente útil para solução de problemas. `stderr` contém o identificador do aplicativo YARN da consulta real.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Etapa 4: examinar a pilha e as versões do ambiente

A página **pilha e versão** da interface do usuário do Ambari fornece informações sobre a configuração de serviços de cluster e o histórico de versão do serviço.  As versões incorretas da biblioteca de serviço do Hadoop podem ser uma causa da falha do cluster.  Na interface do usuário do amAmbari, selecione o menu **admin** e, em seguida, **pilhas e versões**.  Selecione a guia **versões** na página para ver as informações de versão do serviço:

![Pilha e versões do Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Etapa 5: examinar os arquivos de log

Há muitos tipos de logs que são gerados a partir de vários serviços e componentes que compõem um cluster HDInsight. [Os arquivos de log do WebHCat](#check-your-webhcat-service) são descritos anteriormente. Há vários outros arquivos de log úteis que você pode investigar para restringir problemas com o cluster, conforme descrito nas seções a seguir.

* Os clusters HDInsight consistem em vários nós, a maioria dos quais são tarefas para executar trabalhos enviados. Os trabalhos são executados simultaneamente, mas os arquivos de log podem exibir apenas os resultados linearmente. O HDInsight executa novas tarefas, encerrando outras que falham ao concluir primeiro. Todas essas atividades são registradas nos arquivos `stderr` e `syslog`.

* Os arquivos de log de ação de script mostram erros ou alterações de configuração inesperadas durante o processo de criação do cluster.

* Os logs de etapa do Hadoop identificam os trabalhos do Hadoop iniciados como parte de uma etapa que contém erros.

### <a name="check-the-script-action-logs"></a>Verificar os logs de ação de script

As [ações de script](hdinsight-hadoop-customize-cluster-linux.md) do HDInsight executam scripts no cluster manualmente ou quando especificadas. Por exemplo, as ações de script podem ser usadas para instalar software adicional no cluster ou alterar definições de configuração dos valores padrão. A verificação dos logs de ação de script pode fornecer informações sobre erros ocorridos durante a instalação e configuração do cluster.  Você pode exibir o status de uma ação de script selecionando o botão **Ops** na interface do usuário do amAmbari ou acessando os logs da conta de armazenamento padrão.

Os logs de ação de script residem no diretório `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Exibir logs do HDInsight usando links rápidos do Ambari

A interface do usuário do HDInsight Ambari inclui uma série de seções de **links rápidos** .  Para acessar os links de log para um serviço específico em seu cluster HDInsight, abra a interface do usuário do amAmbari para o cluster e, em seguida, selecione o link do serviço na lista à esquerda. Selecione a lista suspensa **links rápidos** , o nó do HDInsight de interesse e, em seguida, selecione o link para seu log associado.

Por exemplo, para logs do HDFS:

![Ambari links rápidos para arquivos de log](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Exibir arquivos de log gerados pelo Hadoop

HDInsight An cluster gera logs que são gravados nas tabelas do Azure e no armazenamento de BLOBs do Azure. O YARN cria seus próprios logs de execução. Para obter mais informações, consulte [gerenciar logs para um cluster HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Examinar despejos de heap

Os despejos de heap contêm um instantâneo da memória do aplicativo, incluindo os valores das variáveis nesse momento, que são úteis para diagnosticar problemas que ocorrem em tempo de execução. Para obter mais informações, consulte [habilitar despejos de heap para serviços de Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Etapa 6: verificar as definições de configuração

Os clusters HDInsight são pré-configurados com configurações padrão para serviços relacionados, como Hadoop, Hive, HBase e assim por diante. Dependendo do tipo de cluster, sua configuração de hardware, seu número de nós, os tipos de trabalhos que você está executando e os dados com os quais você está trabalhando (e como esses dados estão sendo processados), talvez seja necessário otimizar sua configuração.

Para obter instruções detalhadas sobre como otimizar as configurações de desempenho para a maioria dos cenários, consulte [otimizar as configurações de cluster com o Apache Ambari](hdinsight-changing-configs-via-ambari.md). Ao usar o Spark, consulte [otimizar trabalhos de Apache Spark para desempenho](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Etapa 7: reproduzir a falha em um cluster diferente

Para ajudar a diagnosticar a origem de um erro de cluster, inicie um novo cluster com a mesma configuração e reenvie as etapas do trabalho com falha um a um. Verifique os resultados de cada etapa antes de processar o próximo. Esse método oferece a oportunidade de corrigir e executar novamente uma única etapa com falha. Esse método também tem a vantagem de carregar apenas os dados de entrada uma vez.

1. Crie um novo cluster de teste com a mesma configuração que o cluster com falha.
2. Envie a primeira etapa de trabalho para o cluster de teste.
3. Quando a etapa concluir o processamento, verifique se há erros na etapa arquivos de log. Conecte-se ao nó mestre do cluster de teste e exiba os arquivos de log ali. A etapa arquivos de log só aparecem depois que a etapa é executada por algum tempo, termina ou falha.
4. Se a primeira etapa for bem-sucedida, execute a próxima etapa. Se houver erros, investigue o erro nos arquivos de log. Se fosse um erro em seu código, faça a correção e execute a etapa novamente.
5. Continue até que todas as etapas sejam executadas sem erros.
6. Quando terminar de depurar o cluster de teste, exclua-o.

## <a name="next-steps"></a>Passos seguintes

* [Manage HDInsight clusters by using the Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)
* [Analisar logs do HDInsight](hdinsight-debug-jobs.md)
* [Acessar Apache Hadoop entrada do aplicativo YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Habilitar despejos de heap para serviços de Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Problemas conhecidos do cluster Apache Spark no HDInsight](hdinsight-apache-spark-known-issues.md)
