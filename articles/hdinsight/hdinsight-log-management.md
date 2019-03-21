---
title: Gerir registos de um cluster do HDInsight - Azure HDInsight
description: Determine os tipos, tamanhos e as políticas de retenção para ficheiros de registo de atividade do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 0f0a22ea4a24a82cb4acf7a3b20a743ee7425c72
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294914"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Gerir registos de um cluster do HDInsight

Um cluster do HDInsight produz uma variedade de ficheiros de registo. Por exemplo, o Apache Hadoop e serviços relacionados, como o Apache Spark, produzem registos de execução de tarefa detalhadas. Gestão de ficheiros de registo é parte da manutenção de um cluster do HDInsight em bom estado. Também pode ser exigências normativas de arquivamento de registo.  Devido ao número e tamanho dos ficheiros de registo, Otimizando o armazenamento de registo e arquivamento de ajuda com o serviço de gestão de custos.

Gestão de registos de cluster do HDInsight inclui a reter informações sobre todos os aspectos do ambiente de cluster. Estas informações incluem todos os respectivos registos de serviço do Azure, configuração de cluster, informações de execução de tarefa, qualquer Estados de erro e outros dados conforme necessário.

As etapas típicas na gestão de registos do HDInsight são:

* Passo 1: Determinar as políticas de retenção do registo
* Passo 2: Gerir registos de configuração de versões de serviço de cluster
* Passo 3: Gerir ficheiros de registo de execução de tarefa de cluster
* Passo 4: Previsão de tamanhos de armazenamento de volume de registo e os custos
* Passo 5: Determinar as políticas de arquivo de log e de processos

## <a name="step-1-determine-log-retention-policies"></a>Passo 1: Determinar as políticas de retenção do registo

É a primeira etapa na criação de uma estratégia de gestão de registo de cluster do HDInsight recolher informações sobre cenários de negócios e requisitos de armazenamento de histórico de execução de tarefa.

### <a name="cluster-details"></a>Detalhes do cluster

Os seguintes detalhes de cluster são úteis para ajudar a recolher informações na sua estratégia de gestão do registo. Recolha estas informações de todos os clusters do HDInsight que criou uma determinada conta do Azure.

* Nome do cluster
* Região de cluster e zona de disponibilidade do Azure
* Estado do cluster, incluindo detalhes da última alteração de estado
* Tipo e número de instâncias do HDInsight especificado para o principal, núcleos e nós de tarefa

Pode aproveitar essas informações de nível superior do portal do Azure.  Em alternativa, pode utilizar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para obter informações sobre os clusters do HDInsight:

```azurecli
    az hdinsight list --resource-group <ResourceGroup>
    az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Também pode utilizar o PowerShell para ver estas informações.  Para obter mais informações, consulte [Apache Hadoop para gerir clusters no HDInsight com o Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Compreender as cargas de trabalho em execução nos seus clusters

É importante compreender os tipos de carga de trabalho em execução nos seus clusters do HDInsight para o design apropriado de estratégias para cada tipo de registo.

* São as cargas de trabalho experimental (por exemplo, desenvolvimento ou teste) ou de qualidade de produção?
* A frequência com que são as cargas de trabalho de qualidade de produção normalmente executados?
* São qualquer uma das cargas de trabalho com muitos recursos e/ou de longa execução?
* Qualquer uma das cargas de trabalho a utilizar um conjunto complexo de serviços do Hadoop para o qual são produzidos vários tipos de registos?
* Qualquer uma das cargas de trabalho associou requisitos de linhagem de execução regulamentares?

### <a name="example-log-retention-patterns-and-practices"></a>Padrões de retenção do registo de exemplo e práticas

* Considere a linhagem de dados de controlo, adicionando um identificador para cada entrada de log ou por meio de outras técnicas de manutenção. Isto permite-lhe a origem dos dados e a operação de efectuar o rastreio e siga os dados por meio de cada fase para compreender a sua consistência e a validade.

* Considere como pode recolher registos do cluster, ou a partir de mais de um cluster e agrupá-los para fins de auditoria, monitorização, planeamento e os alertas. Poderá utilizar uma solução personalizada para aceder e transferir os ficheiros de registo numa base regular e combinar e analisá-los para fornecer uma exibição de dashboard. Também pode adicionar capacidades adicionais para os alertas de segurança ou a deteção de falhas. Pode criar estes utilitários com o PowerShell, os SDKs do HDInsight ou o código que acede ao modelo de implementação clássica do Azure.

* Considere se uma solução de monitorização ou serviço seria uma vantagem útil. O Microsoft System Center fornece uma [pacote de gestão do HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Também pode utilizar ferramentas de terceiros, como Apache Chukwa e Ganglia para recolher e centralizar os registos. Muitas empresas oferecem serviços para monitorizar soluções de macrodados baseada no Hadoop, por exemplo: Centerity, Compuware APM, Sematext SPM e Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Passo 2: Gerir versões de serviço de cluster e ver os registos de ação de Script

Um cluster de HDInsight típico utiliza vários pacotes de software open-source (como o Apache HBase, Apache Spark e assim por diante) e os serviços. Para algumas cargas de trabalho, como bioinformatics, poderá ser necessário para manter o histórico de registos de configuração de serviço, além de registos de execução de tarefa.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ver as definições de configuração de cluster com a IU do Ambari

Apache Ambari simplifica o gerenciamento, configuração e monitorização de um cluster do HDInsight, fornecendo uma web interface do Usuário e uma API REST. Ambari está incluído nos clusters do HDInsight baseado em Linux. Selecione o **Dashboard de clusters** painel no portal do Azure HDInsight página para abrir o **Dashboards de clusters** página de ligação.  Em seguida, selecione o **dashboard de clusters do HDInsight** painel para abrir a IU do Ambari.  Deverá informar suas credenciais de início de sessão do cluster.

Para abrir uma lista de vistas de serviço, selecione o **vistas Ambari** painel na página do portal do Azure para HDInsight.  Esta lista varia consoante as bibliotecas de que instalou.  Por exemplo, poderá ver o Gestor de filas do YARN, a vista do Hive e a exibição de Tez.  Selecione qualquer ligação de serviço para ver a configuração e informações de serviço.  IU do Ambari **Stack e a versão** página fornece informações sobre a configuração dos serviços de cluster e o histórico de versões de serviço. Para navegar para esta secção da IU do Ambari, selecione o **administrador** menu e, em seguida **pilhas e versões**.  Selecione o **versões** guia para ver informações de versão do serviço.

![Pilha e versões](./media/hdinsight-log-management/stack-versions.png)

Com a IU do Ambari, pode transferir a configuração para os serviços de qualquer (ou todos) em execução num anfitrião específico (ou nós) no cluster.  Selecione o **anfitriões** menu, em seguida, a ligação para o anfitrião de interesse. Na página de nesse anfitrião, selecione o **ações do anfitrião** botão e, em seguida **configurações de cliente transferir**. 

![Configurações de cliente do anfitrião](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Ver os registos de ação de script

HDInsight [ações de script](hdinsight-hadoop-customize-cluster-linux.md) executar scripts num cluster, manualmente ou quando especificado. Por exemplo, ações de script podem ser utilizadas para instalar software adicional no cluster ou para alterar as definições de configuração os valores predefinidos. Registos de ação de script podem fornecer informações sobre os erros que ocorreram durante a configuração do cluster bem como alterações de definições de configuração que podem afetar o desempenho do cluster e a disponibilidade.  Para ver o estado de uma ação de script, selecione o **ops** botão na sua IU do Ambari ou acesso o estado de registos na conta de armazenamento predefinida. Os registos de armazenamento estão disponíveis em `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Passo 3: Gerir os ficheiros de registo de execução de tarefa de cluster

A próxima etapa é examinar os ficheiros de registo de execução de tarefa para vários serviços.  Os serviços podem incluir o Apache HBase, Apache Spark e muitos outros. Um cluster do Hadoop produz um grande número de registos verbosos, para que determinar os registos que são úteis (e que não são) pode ser demorado.  Compreender o sistema de registo é importante para a gestão de destino de ficheiros de registo.  Segue-se um ficheiro de registo de exemplo.

![Exemplo de ficheiro de registo do HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Aceder aos ficheiros de registo do Hadoop

HDInsight armazena seus arquivos de log no sistema de arquivos de cluster e no armazenamento do Azure. Pode examinar os ficheiros de registo do cluster ao abrir um [SSH](/hdinsight-hadoop-linux-use-ssh-unix.md) ligação ao cluster e o sistema de ficheiros de navegação ou ao utilizar o portal de estado de YARN do Hadoop no servidor remoto de nó principal. Pode examinar os ficheiros de registo no armazenamento do Azure através de qualquer uma das ferramentas que podem acessar e transferir dados do armazenamento do Azure. Os exemplos são [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)e o Visual Studio Server Explorer. Também pode utilizar os SDKs do .NET do Azure e as bibliotecas de cliente de armazenamento do Azure ou do PowerShell para aceder aos dados no armazenamento de Blobs do Azure.

Executa o trabalho de tarefas como o Hadoop *tentativas de tarefas* em vários nós no cluster. HDInsight pode iniciar a tarefa especulativa tentativas, quaisquer outras tentativas de tarefas que não forem concluídas pela primeira vez a terminar. Isso gera atividade significativa que é registada para o controlador, stderr e syslog log arquivos no momento. Além disso, várias tentativas de tarefas em execução simultânea, mas um ficheiro de registo pode apresentar apenas os resultados linearmente.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Registos do HDInsight escritos no armazenamento de Blobs do Azure

Clusters do HDInsight são configurados para escrever registos de tarefas para uma conta de armazenamento de Blobs do Azure para qualquer tarefa que é enviada através de cmdlets do Azure PowerShell ou a submissão da tarefa de .NET APIs.  Se submeter tarefas através de SSH para o cluster, em seguida, as informações de registo de execução são armazenadas nas tabelas do Azure conforme discutido na seção anterior.

Além dos ficheiros de registo principal gerados pelo HDInsight, serviços instalados, como o YARN também gerar ficheiros de registo de execução de tarefa.  O número e tipo de ficheiros de registo depende dos serviços instalados.  Serviços comuns são o Apache HBase, Apache Spark e assim por diante.  Investigue os ficheiros de execução do registo de tarefa para cada serviço compreender que o Registro em log global de ficheiros disponível no seu cluster.  Cada serviço tem seus próprios métodos exclusivos de Registro em log e locais para o armazenamento de ficheiros de registo.  Por exemplo, os detalhes para aceder aos ficheiros de registo de serviço mais comuns (a partir do YARN) são discutidas na seção a seguir.

### <a name="hdinsight-logs-generated-by-yarn"></a>Registos de HDInsight gerados pelo YARN

YARN agrega registos em todos os contentores num nó de trabalho e armazena esses registos como um ficheiro de registo de agregados por nó de trabalho. Esse registo é armazenado no sistema de ficheiros predefinido após a conclusão de uma aplicação. Seu aplicativo pode usar centenas ou milhares de contentores, mas os registos para todos os contentores que são executados num nó único do worker sempre são agregados para um único arquivo. Há apenas um registo por nó de trabalho utilizado pela sua aplicação. Agregação de registo está ativada por predefinição em clusters de HDInsight versão 3.0 e superior. Registos agregados estão localizados no armazenamento de predefinido para o cluster.

```
    /app-logs/<user>/logs/<applicationId>
```

Os registos agregados não são diretamente legíveis, como elas são gravadas num formato binário de TFile indexado por contêiner. Utilize os registos do YARN Resource Manager ou ferramentas CLI para ver estes registos como texto simples para aplicações ou contentores de interesse.

#### <a name="yarn-cli-tools"></a>Ferramentas de CLI do YARN

Para utilizar as ferramentas de CLI do YARN, primeiro tem de ligar para o cluster do HDInsight através de SSH. Especifique a `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, e `<worker-node-address>` informações ao executar estes comandos. Pode ver os registos como texto simples com um dos seguintes comandos:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>IU de ResourceManager do YARN

IU de ResourceManager do YARN é executado no nó principal do cluster e é acedido através da IU web do Ambari. Utilize os seguintes passos para ver os registos do YARN:

1. Num browser, navegue para `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME com o nome do cluster do HDInsight.
2. Na lista de serviços no lado esquerdo, selecione YARN.
3. Na lista pendente Links rápidos, selecione um de nós principais do cluster e, em seguida, selecione **registos de ResourceManager**. É apresentada uma lista de ligações para os registos YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Passo 4: Previsão de tamanhos de armazenamento de volume de registo e os custos

Depois de concluir os passos anteriores, tem um conhecimento dos tipos e volumes de ficheiros de registo que estão produzindo seus clusters do HDInsight.

Em seguida, analise o volume de dados de registo em localizações de armazenamento de chave de registo durante um período de tempo. Por exemplo, pode analisar volume e crescimento mais de 30 a 60-90 períodos de dia.  Registe estas informações numa folha de cálculo ou utilizar outras ferramentas, como o Visual Studio, o Explorador de armazenamento do Azure ou o Power Query para Excel. Para obter mais informações, consulte [HDInsight analisar registos](hdinsight-debug-jobs.md).  

Agora tem informação suficiente para criar uma estratégia de gestão de registo para os registos de chave.  Use sua folha de cálculo (ou ferramenta de escolha) para prever os dois crescimento de tamanho do registo e registar os custos de serviço do Azure de armazenamento no futuro.  Considere também os requisitos de retenção de registo para o conjunto de registos que está examinando.  Agora pode reforecast log futuros custos de armazenamento, depois de determinar quais arquivos de log podem ser eliminados (se aplicável) e os registos que devem ser mantidos e arquivados para o armazenamento do Azure mais barato.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Passo 5: Determinar as políticas de arquivo de log e de processos

Depois de determinar quais arquivos de log podem ser eliminados, pode ajustar os parâmetros de registo em muitos serviços do Hadoop para eliminar automaticamente os ficheiros de registo após um período de tempo especificado.

Para determinados ficheiros de registo, pode utilizar um ficheiro de registo com preços inferiores abordagem de arquivamento. Para os registos de atividade do Azure Resource Manager, pode explorar esta abordagem com o portal do Azure.  Configurar o arquivamento dos registos do ARM, selecionando o **registo de atividades**"ligação no portal do Azure para a sua instância de HDInsight.  No topo da página de pesquisa de registo de atividades, selecione o **exportar** item de menu para abrir o **exportar registo de atividades** painel.  Preencha a subscrição, região, se pretende exportar para uma conta de armazenamento e número de dias a manter os registos. No mesmo neste painel, também pode indicar se pretende exportar para um hub de eventos. 

![Exportar ficheiros de registo](./media/hdinsight-log-management/archive.png)

Em alternativa, pode criar scripts arquivamento de registo com o PowerShell.  Para obter um exemplo de script do PowerShell, consulte [registos de automatização do arquivo do Azure para armazenamento de Blobs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Aceder a métricas de armazenamento do Azure

O armazenamento do Azure pode ser configurado para operações de armazenamento de registo e acesso. Pode utilizar estes registos muito detalhados para a capacidade de monitorização e planeamento e para pedidos ao armazenamento de auditoria. As informações com sessão iniciada incluem detalhes de latência, permitindo-lhe monitorizar e otimizar o desempenho das suas soluções.
Pode utilizar o SDK do .NET para o Hadoop para examinar os arquivos de log gerados para o armazenamento do Azure que contém os dados para um cluster do HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Controlar o tamanho e número de índices de cópia de segurança para ficheiros de registo antigos

Para controlar o tamanho e número de ficheiros de registo guardadas, defina as seguintes propriedades do `RollingFileAppender`:

* `maxFileSize` é o tamanho crítico de arquivo, acima, que o ficheiro é implementado. O valor predefinido é de 10 MB.
* `maxBackupIndex` Especifica o número de ficheiros de cópia de segurança a ser criada por predefinição 1.

### <a name="other-log-management-techniques"></a>Outras técnicas de gerenciamento de log

Para evitar a execução sem espaço em disco, pode utilizar algumas ferramentas de sistema operacional, como [logrotate](https://linux.die.net/man/8/logrotate) para gerir a manipulação de ficheiros de registo. Pode configurar `logrotate` para executar diariamente, a compressão iniciar ficheiros e remover antigos. Sua abordagem depende dos requisitos, tais como o período de tempo para manter o logfiles em nós locais.  

Também pode verificar se o registo de depuração está ativado para um ou mais serviços, que aumenta significativamente o tamanho do registo de saída.  

Para recolher os registos de todos os nós para um local central, pode criar um fluxo de dados, tais como ingestão todas as entradas de registo para o Solr.

## <a name="next-steps"></a>Passos Seguintes

* [Monitorização e registo prática para HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Sessão de aplicação do acesso Apache Hadoop YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Como controlar o tamanho dos ficheiros de registo para vários componentes do Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
