---
title: Gerenciar logs para um cluster HDInsight-Azure HDInsight
description: Determine os tipos, tamanhos e políticas de retenção para arquivos de log de atividades do HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: d4774dcc96e5f7639ca0b03bca992c9a3126230b
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623901"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Gerir registos de um cluster do HDInsight

HDInsight An cluster produz uma variedade de arquivos de log. Por exemplo, Apache Hadoop e serviços relacionados, como Apache Spark, produzem logs de execução de trabalho detalhados. O gerenciamento de arquivos de log faz parte da manutenção de um cluster HDInsight íntegro. Também pode haver requisitos regulatórios para o arquivamento de log.  Devido ao número e tamanho dos arquivos de log, otimizar o armazenamento e o arquivamento de log ajuda com o gerenciamento de custos de serviço.

O gerenciamento de logs do cluster HDInsight inclui reter informações sobre todos os aspectos do ambiente de cluster. Essas informações incluem todos os logs de serviço do Azure associados, configuração de cluster, informações de execução do trabalho, quaisquer Estados de erro e outros dados, conforme necessário.

As etapas típicas no gerenciamento de log do HDInsight são:

* Passo 1: Determinar políticas de retenção de log
* Passo 2: Gerenciar logs de configuração de versões do serviço de cluster
* Passo 3: Gerenciar arquivos de log de execução de trabalho do cluster
* Passo 4: Custos e tamanhos de armazenamento de volume de log de previsão
* Passo 5: Determinar políticas e processos de arquivo de log

## <a name="step-1-determine-log-retention-policies"></a>Passo 1: Determinar políticas de retenção de log

A primeira etapa na criação de uma estratégia de gerenciamento de log de cluster HDInsight é reunir informações sobre cenários de negócios e requisitos de armazenamento de histórico de execução de trabalhos.

### <a name="cluster-details"></a>Detalhes do cluster

Os detalhes do cluster a seguir são úteis para ajudar a reunir informações em sua estratégia de gerenciamento de log. Reúna essas informações de todos os clusters HDInsight que você criou em uma conta específica do Azure.

* Nome do cluster
* Região de cluster e zona de disponibilidade do Azure
* Estado do cluster, incluindo detalhes da última alteração de estado
* Tipo e número de instâncias do HDInsight especificado para os nós mestre, principal e tarefa

Você pode obter a maioria dessas informações de nível superior usando o portal do Azure.  Como alternativa, você pode usar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para obter informações sobre seus clusters HDInsight:

```azurecli
    az hdinsight list --resource-group <ResourceGroup>
    az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Você também pode usar o PowerShell para exibir essas informações.  Para obter mais informações, consulte [Apache Manage Hadoop clusters no HDInsight usando Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Entender as cargas de trabalho em execução em seus clusters

É importante entender os tipos de carga de trabalho em execução em seus clusters HDInsight para criar estratégias de registro em log apropriadas para cada tipo.

* As cargas de trabalho são experimentais (como desenvolvimento ou teste) ou qualidade de produção?
* Com que frequência as cargas de trabalho de qualidade de produção normalmente são executadas?
* Alguma das cargas de trabalho consome muitos recursos e/ou execução longa?
* Qualquer uma das cargas de trabalho usa um conjunto complexo de serviços do Hadoop para os quais vários tipos de logs são produzidos?
* Alguma das cargas de trabalho tem requisitos de linhagem de execução regulatória associados?

### <a name="example-log-retention-patterns-and-practices"></a>Padrões e práticas de retenção de log de exemplo

* Considere manter o controle de linhagem de dados adicionando um identificador a cada entrada de log ou por outras técnicas. Isso permite que você rastreie de volta a fonte original dos dados e da operação e siga os dados em cada estágio para entender sua consistência e validade.

* Considere como você pode coletar logs do cluster ou de mais de um cluster e agrupá-los para fins como auditoria, monitoramento, planejamento e alertas. Você pode usar uma solução personalizada para acessar e baixar os arquivos de log regularmente e combiná-los e analisá-los para fornecer uma exibição de painel. Você também pode adicionar recursos adicionais para alertas de segurança ou detecção de falha. Você pode criar esses utilitários usando o PowerShell, os SDKs do HDInsight ou o código que acessa o modelo de implantação clássico do Azure.

* Considere se uma solução ou um serviço de monitoramento seria um benefício útil. O Microsoft System Center fornece um [pacote de gerenciamento do HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Você também pode usar ferramentas de terceiros, como Apache Chukwa e Ganglia, para coletar e centralizar logs. Muitas empresas oferecem serviços para monitorar soluções de Big Data baseadas em Hadoop, por exemplo: Centerity, Compuware APM, Sematext SPM e Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Passo 2: Gerenciar versões do serviço de cluster e exibir logs de ação de script

Um cluster HDInsight típico usa vários serviços e pacotes de software de código aberto (como o Apache HBase, Apache Spark e assim por diante). Para algumas cargas de trabalho, como bioinformática, talvez seja necessário manter o histórico do log de configuração do serviço além dos logs de execução do trabalho.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Exibir definições de configuração de cluster com a interface do usuário do amAmbari

O Apache Ambari simplifica o gerenciamento, a configuração e o monitoramento de um cluster HDInsight, fornecendo uma interface do usuário da Web e uma API REST. O Ambari está incluído em clusters HDInsight baseados em Linux. Selecione o painel **painel do cluster** na página portal do Azure HDInsight para abrir a página de link painéis do **cluster** .  Em seguida, selecione o painel de **painel do cluster HDInsight** para abrir a interface do usuário do amAmbari.  Suas credenciais de logon do cluster serão solicitadas.

Para abrir uma lista de exibições de serviço, selecione o painel **modos de exibição Ambari** na página portal do Azure para o HDInsight.  Essa lista varia, dependendo de quais bibliotecas você instalou.  Por exemplo, você pode ver YARN Queue Manager, Hive View e tez View.  Selecione qualquer link de serviço para ver as informações de configuração e de serviço.  A página **pilha e versão** da interface do usuário do Ambari fornece informações sobre o histórico de versão de serviço e configuração de serviços de cluster. Para navegar até esta seção da interface do usuário do amAmbari, selecione o menu **admin** e, em seguida, **pilhas e versões**.  Selecione a guia **versões** para ver as informações de versão do serviço.

![Pilha e versões](./media/hdinsight-log-management/stack-versions.png)

Usando a interface do usuário do amAmbari, você pode baixar a configuração para qualquer serviço (ou todos) em execução em um host (ou nó) específico no cluster.  Selecione o menu hosts e, em seguida, o link para o host de interesse. Na página desse host, selecione o botão **ações do host** e, em seguida, **Baixe as configurações do cliente**. 

![Configurações do cliente host](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Exibir os logs de ação de script

As [ações de script](hdinsight-hadoop-customize-cluster-linux.md) do HDInsight executam scripts em um cluster, seja manualmente ou quando especificado. Por exemplo, as ações de script podem ser usadas para instalar software adicional no cluster ou alterar definições de configuração dos valores padrão. Os logs de ação de script podem fornecer informações sobre erros que ocorreram durante a instalação do cluster e também alterações de definições de configuração que podem afetar o desempenho e a disponibilidade do cluster.  Para ver o status de uma ação de script, selecione o botão **Ops** na sua interface do usuário do Ambari ou acesse os logs de status na conta de armazenamento padrão. Os logs de armazenamento estão disponíveis `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`em.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Passo 3: Gerenciar os arquivos de log de execução do trabalho do cluster

A próxima etapa é examinar os arquivos de log de execução do trabalho para os vários serviços.  Os serviços podem incluir o Apache HBase, o Apache Spark e muitos outros. Um cluster Hadoop produz um grande número de logs detalhados, portanto, determinar quais logs são úteis (e quais não são) pode ser demorado.  Compreender o sistema de registro em log é importante para o gerenciamento direcionado de arquivos de log.  Este é um exemplo de arquivo de log.

![Exemplo de arquivo de log do HDInsight](./media/hdinsight-log-management/logs.png)

### <a name="access-the-hadoop-log-files"></a>Acessar os arquivos de log do Hadoop

O HDInsight armazena seus arquivos de log no sistema de arquivos de cluster e no armazenamento do Azure. Você pode examinar os arquivos de log no cluster abrindo uma conexão [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) para o cluster e navegando no sistema de arquivos ou usando o portal de status do Hadoop yarn no servidor do nó de cabeçalho remoto. Você pode examinar os arquivos de log no armazenamento do Azure usando qualquer uma das ferramentas que podem acessar e baixar dados do armazenamento do Azure. Os exemplos são [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)e o Visual Studio Gerenciador de servidores. Você também pode usar o PowerShell e as bibliotecas de cliente de armazenamento do Azure, ou os SDKs do .NET do Azure, para acessar dados no armazenamento de BLOBs do Azure.

O Hadoop executa o trabalho dos trabalhos como *tentativas de tarefas* em vários nós no cluster. O HDInsight pode iniciar tentativas de tarefa especulativa, encerrando as outras tentativas de tarefa que não são concluídas primeiro. Isso gera uma atividade significativa que é registrada nos arquivos de log do controlador, stderr e syslog imediatamente. Além disso, várias tentativas de tarefas são executadas simultaneamente, mas um arquivo de log só pode exibir resultados linearmente.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Logs do HDInsight gravados no armazenamento de BLOBs do Azure

Os clusters HDInsight são configurados para gravar logs de tarefas em uma conta de armazenamento de BLOBs do Azure para qualquer trabalho enviado usando os cmdlets Azure PowerShell ou as APIs de envio de trabalho do .NET.  Se você enviar trabalhos por meio de SSH para o cluster, as informações de log de execução serão armazenadas nas tabelas do Azure, conforme discutido na seção anterior.

Além dos principais arquivos de log gerados pelo HDInsight, os serviços instalados, como o YARN, também geram arquivos de log de execução do trabalho.  O número e o tipo de arquivos de log dependem dos serviços instalados.  Os serviços comuns são Apache HBase, Apache Spark e assim por diante.  Investigue os arquivos de execução de log de trabalho de cada serviço para entender os arquivos de log gerais disponíveis no cluster.  Cada serviço tem seus próprios métodos exclusivos de registro em log e locais para armazenar arquivos de log.  Por exemplo, os detalhes para acessar os arquivos de log de serviço mais comuns (de YARN) são discutidos na seção a seguir.

### <a name="hdinsight-logs-generated-by-yarn"></a>Logs do HDInsight gerados pelo YARN

O YARN agrega logs em todos os contêineres em um nó de trabalho e armazena esses logs como um arquivo de log agregado por nó de trabalho. Esse log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. Seu aplicativo pode usar centenas ou milhares de contêineres, mas os logs para todos os contêineres que são executados em um único nó de trabalho são sempre agregados a um único arquivo. Há apenas um log por nó de trabalho usado pelo seu aplicativo. A agregação de log é habilitada por padrão nos clusters HDInsight versão 3,0 e posteriores. Os logs agregados estão localizados no armazenamento padrão para o cluster.

```
    /app-logs/<user>/logs/<applicationId>
```

Os logs agregados não são diretamente legíveis, pois são gravados em um formato binário TFile indexado pelo contêiner. Use os logs ResourceManager do YARN ou as ferramentas da CLI para exibir esses logs como texto sem formatação para aplicativos ou contêineres de interesse.

#### <a name="yarn-cli-tools"></a>Ferramentas da CLI do YARN

Para usar as ferramentas da CLI do YARN, primeiro você deve se conectar ao cluster HDInsight usando SSH. Especifique as `<applicationId>` `<user-who-started-the-application>`informações, `<containerId>`, e ao executar esses comandos. `<worker-node-address>` Você pode exibir os logs como texto sem formatação com um dos seguintes comandos:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>Interface do usuário do ResourceManager YARN

A interface do usuário do ResourceManager YARN é executada no nó principal do cluster e é acessada por meio da interface do usuário da Web do amAmbari. Use as etapas a seguir para exibir os logs do YARN:

1. Em um navegador da Web, navegue `https://CLUSTERNAME.azurehdinsight.net`até. Substitua CLUSTERname pelo nome do seu cluster HDInsight.
2. Na lista de serviços à esquerda, selecione YARN.
3. Na lista suspensa links rápidos, selecione um dos nós de cabeçalho do cluster e, em seguida, selecione **logs de ResourceManager**. Você verá uma lista de links para logs do YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Passo 4: Custos e tamanhos de armazenamento de volume de log de previsão

Depois de concluir as etapas anteriores, você compreenderá os tipos e volumes de arquivos de log que seus clusters HDInsight estão produzindo.

Em seguida, analise o volume de dados de log em locais de armazenamento de log de chaves durante um período de tempo. Por exemplo, você pode analisar o volume e o crescimento em intervalos de 30-60-90 dias.  Registre essas informações em uma planilha ou use outras ferramentas, como o Visual Studio, o Gerenciador de Armazenamento do Azure ou o Power Query para Excel. Para obter mais informações, consulte [analisar logs do HDInsight](hdinsight-debug-jobs.md).  

Agora você tem informações suficientes para criar uma estratégia de gerenciamento de log para os logs de chave.  Use sua planilha (ou ferramenta de escolha) para prever o crescimento do tamanho do log e o armazenamento de log que os custos do serviço do Azure avançando.  Considere também quaisquer requisitos de retenção de log para o conjunto de logs que você está examinando.  Agora você pode reprever os custos de armazenamento de log futuros, depois de determinar quais arquivos de log podem ser excluídos (se houver) e quais logs devem ser retidos e arquivados em um armazenamento do Azure menos dispendioso.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Passo 5: Determinar políticas e processos de arquivo de log

Depois de determinar quais arquivos de log podem ser excluídos, você pode ajustar os parâmetros de log em muitos serviços do Hadoop para excluir automaticamente os arquivos de log após um período de tempo especificado.

Para determinados arquivos de log, você pode usar uma abordagem de arquivamento de arquivo de log de menor preço. Para Azure Resource Manager logs de atividades, você pode explorar essa abordagem usando o portal do Azure.  Configure o arquivamento dos logs do ARM selecionando o link **log de atividades**no portal do Azure para sua instância do HDInsight.  Na parte superior da página de pesquisa do log de atividades, selecione o item de menu **Exportar** para abrir o painel **Exportar log de atividades** .  Preencha a assinatura, a região, se deseja exportar para uma conta de armazenamento e quantos dias para manter os logs. Nesse mesmo painel, você também pode indicar se deseja exportar para um hub de eventos. 

![Exportar arquivos de log](./media/hdinsight-log-management/archive.png)

Como alternativa, você pode fazer script de arquivamento de log com o PowerShell.  Para obter um exemplo de script do PowerShell, consulte [arquivar logs de automação do Azure no armazenamento de BLOBs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Acessando as métricas do armazenamento do Azure

O armazenamento do Azure pode ser configurado para registrar operações de armazenamento e acesso. Você pode usar esses logs muito detalhados para monitoramento e planejamento de capacidade e para auditar solicitações de armazenamento. As informações registradas incluem detalhes de latência, permitindo que você monitore e ajuste o desempenho de suas soluções.
Você pode usar o SDK do .NET para Hadoop para examinar os arquivos de log gerados para o armazenamento do Azure que contém os dados de um cluster HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Controlar o tamanho e o número de índices de backup para arquivos de log antigos

Para controlar o tamanho e o número de arquivos de log retidos, defina as seguintes `RollingFileAppender`Propriedades do:

* `maxFileSize`é o tamanho crítico do arquivo, acima do qual o arquivo é revertido. O valor padrão é 10 MB.
* `maxBackupIndex`Especifica o número de arquivos de backup a serem criados, padrão 1.

### <a name="other-log-management-techniques"></a>Outras técnicas de gerenciamento de log

Para evitar a indisponibilidade de espaço em disco, você pode usar algumas ferramentas do sistema operacional, como o [logrotate](https://linux.die.net/man/8/logrotate) , para gerenciar a manipulação de arquivos de log. Você pode configurar `logrotate` o para ser executado diariamente, compactando arquivos de log e removendo os antigos. Sua abordagem depende de seus requisitos, como por quanto tempo manter os arquivos de log em nós locais.  

Você também pode verificar se o log de depuração está habilitado para um ou mais serviços, o que aumenta muito o tamanho do log de saída.  

Para coletar os logs de todos os nós para um local central, você pode criar um fluxo de dados, como ingerir todas as entradas de log em Solr.

## <a name="next-steps"></a>Passos Seguintes

* [Monitoramento e prática de registro em log para o HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Acessar Apache Hadoop logs de aplicativo do YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Como controlar o tamanho dos arquivos de log para vários componentes do Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
