---
title: Gerir registos para um cluster HDInsight - Azure HDInsight
description: Determine os tipos, tamanhos e políticas de retenção para ficheiros de registo de atividade sonantes da Atividade HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 8c3cbf4c18b32a94abfe95e77be768020b44fda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272308"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Gerir registos de um cluster do HDInsight

Um cluster HDInsight produz uma variedade de ficheiros de registo. Por exemplo, apache Hadoop e serviços relacionados, como Apache Spark, produzem registos de execução de emprego detalhados. A gestão de ficheiros de log faz parte da manutenção de um cluster HDInsight saudável. Também pode haver requisitos regulamentares para arquivar log.  Devido ao número e tamanho dos ficheiros de registo, otimizar o armazenamento de registos e arquivar ajuda na gestão de custos do serviço.

A gestão dos registos de cluster SDInsight inclui a retenção de informações sobre todos os aspetos do ambiente de cluster. Estas informações incluem todos os registos do Serviço Azure associados, configuração de cluster, informações de execução de emprego, quaisquer estados de erro e outros dados conforme necessário.

Os passos típicos na gestão de log sons HDInsight são:

* Passo 1: Determinar as políticas de retenção de registos
* Passo 2: Gerir os registos de configuração de versões de serviço de cluster
* Passo 3: Gerir ficheiros de registo de execução de trabalho de cluster
* Passo 4: Previsão de tamanhos e custos de armazenamento de volume de registo
* Passo 5: Determinar as políticas e processos de arquivo de registo

## <a name="step-1-determine-log-retention-policies"></a>Passo 1: Determinar as políticas de retenção de registos

O primeiro passo para a criação de uma estratégia de gestão de loglog hDInsight é recolher informações sobre cenários de negócio sintetizadores e requisitos de armazenamento de histórico de execução de emprego.

### <a name="cluster-details"></a>Detalhes do cluster

Os seguintes detalhes do cluster são úteis para ajudar a recolher informações na sua estratégia de gestão de registos. Reúna esta informação de todos os clusters HDInsight que criou numa determinada conta Azure.

* Nome do cluster
* Região de cluster e zona de disponibilidade de Azure
* Estado de aglomerado, incluindo detalhes da última mudança de estado
* Tipo e número de instâncias HDInsight especificadas para os nós de mestre, núcleo e tarefa

Pode obter a maior parte desta informação de alto nível utilizando o portal Azure.  Em alternativa, pode utilizar [o Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para obter informações sobre o seu cluster HDInsight( s):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Também pode utilizar o PowerShell para visualizar esta informação.  Para mais informações, consulte os [clusters Apache Manage Hadoop no HDInsight utilizando o Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Compreenda as cargas de trabalho que correm nos seus aglomerados

É importante compreender os tipos de carga de trabalho que estão a ser geridos no seu cluster HDInsight para conceber estratégias de registo apropriadas para cada tipo.

* As cargas de trabalho são experimentais (como desenvolvimento ou teste) ou qualidade de produção?
* Com que frequência funcionam as cargas de trabalho de qualidade de produção?
* Alguma das cargas de trabalho é intensiva em recursos e/ou a longo prazo?
* Alguma das cargas de trabalho utiliza um conjunto complexo de serviços Hadoop para os quais são produzidos vários tipos de troncos?
* Alguma das cargas de trabalho tem requisitos de linhagem de execução regulamentar associados?

### <a name="example-log-retention-patterns-and-practices"></a>Padrões e práticas de retenção de registos de exemplo

* Considere manter o rastreio da linhagem de dados adicionando um identificador a cada entrada de registo, ou através de outras técnicas. Isto permite-lhe rastrear a origem original dos dados e a operação, e seguir os dados através de cada fase para compreender a sua consistência e validade.

* Considere como pode recolher registos do cluster, ou de mais de um cluster, e colá-los para fins como auditoria, monitorização, planeamento e alerta. Pode utilizar uma solução personalizada para aceder e descarregar os ficheiros de registo regularmente e combiná-los e analisá-los para fornecer um ecrã de dashboard. Também pode adicionar capacidades adicionais para alertar para a deteção de segurança ou falha. Pode construir estes utilitários utilizando powerShell, os SDKs HDInsight ou código que aceda ao modelo de implementação clássico do Azure.

* Considere se uma solução ou serviço de monitorização seria um benefício útil. O Microsoft System Center fornece um pacote de [gestão HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Também pode utilizar ferramentas de terceiros como Apache Chukwa e Ganglia para recolher e centralizar registos. Muitas empresas oferecem serviços para monitorizar as soluções de big data baseadas em Hadoop, por exemplo: Centerity, Compuware APM, Sematext SPM e Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Passo 2: Gerir versões de serviço de cluster e ver registos

Um cluster típico do HDInsight utiliza vários serviços e pacotes de software de código aberto (tais como Apache HBase, Apache Spark, e assim por diante). Para algumas cargas de trabalho, como bioinformática, pode ser necessário manter o histórico de registos de configuração de serviço, além de registos de execução de emprego.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ver configurações de configuração de cluster com o Ambari UI

Apache Ambari simplifica a gestão, configuração e monitorização de um cluster HDInsight fornecendo uma UI web e uma API REST. Ambari está incluído em clusters HDInsight baseados em Linux. Selecione o painel do Painel de **Cluster** na página HDInsight do portal Azure para abrir a página de link **do Cluster Dashboards.**  Em seguida, selecione o painel de painel de painel de **cluster HDInsight** para abrir o Ambari UI.  Foi solicitado para as credenciais de login do seu cluster.

Para abrir uma lista de vistas de serviço, selecione o painel **Ambari Views** na página do portal Azure para HDInsight.  Esta lista varia, dependendo das bibliotecas que instalou.  Por exemplo, você pode ver YARN Queue Manager, Hive View e Tez View.  Selecione qualquer link de serviço para ver informações de configuração e serviço.  A página Ambari UI **Stack and Version** fornece informações sobre o histórico de configuração e versão de serviço dos serviços de cluster. Para navegar nesta secção do Ambari UI, selecione o menu **Admin** e, em seguida, **Pilhas e Versões**.  Selecione o separador **Versões** para ver informações sobre a versão do serviço.

![Apache Ambari administrador stack e versões](./media/hdinsight-log-management/ambari-stack-versions.png)

Utilizando o Ambari UI, pode descarregar a configuração para quaisquer (ou todos) serviços que executem um determinado anfitrião (ou nó) no cluster.  Selecione o menu **Anfitriões** e, em seguida, o link para o anfitrião de interesse. Na página do anfitrião, selecione o botão **Host Actions** e, em seguida, baixe **os Configs**do Cliente .

![Apache Ambari descarregue configs de cliente anfitrião](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Ver os registos de ação do script

[As ações](hdinsight-hadoop-customize-cluster-linux.md) do script HDInsight executam scripts num cluster, manualmente ou quando especificados. Por exemplo, as ações de script podem ser usadas para instalar software adicional no cluster ou para alterar as definições de configuração dos valores predefinidos. Os registos de ação do script podem fornecer informações sobre os erros ocorridos durante a configuração do cluster, bem como alterações de configuração que podem afetar o desempenho e disponibilidade do cluster.  Para ver o estado de uma ação de script, selecione o botão **de operações** no seu Ambari UI ou aceda aos registos de estado na conta de armazenamento predefinido. Os registos de `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`armazenamento estão disponíveis em .

### <a name="view-ambari-alerts-status-logs"></a>Ver Registos de estado de alerta de Ambari

Apache Ambari escreve alterações `ambari-alerts.log`de estado de alerta para . O caminho `/var/log/ambari-server/ambari-alerts.log`completo é. Para permitir a depuração do registo, altere uma propriedade em `/etc/ambari-server/conf/log4j.properties.` Change e, em seguida, entre em: `# Log alert state changes`

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Passo 3: Gerir os ficheiros de registo de execução de trabalho do cluster

O próximo passo é rever os ficheiros de registo de execução de emprego para os vários serviços.  Os serviços podem incluir Apache HBase, Apache Spark, e muitos outros. Um cluster Hadoop produz um grande número de troncos verbosos, de modo que determinar quais os troncos são úteis (e que não são) pode ser demorado.  Compreender o sistema de registo é importante para a gestão direcionada dos ficheiros de registo.  A imagem que se segue é um ficheiro de registo de exemplo.

![Saída de amostra de ficheiro de registo de exemplo HDInsight](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Aceda aos ficheiros de registo hadoop

O HDInsight armazena os seus ficheiros de registo tanto no sistema de ficheiros de cluster como no Armazenamento Azure. Pode examinar ficheiros de registo no cluster abrindo uma ligação [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) ao cluster e navegando no sistema de ficheiros, ou utilizando o portal Hadoop YARN Status no servidor de nódeame de cabeça remota. Pode examinar os ficheiros de registo no Armazenamento Azure utilizando qualquer uma das ferramentas que podem aceder e transferir dados do Armazenamento Azure. Exemplos são [AzCopy,](../storage/common/storage-use-azcopy.md) [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)e o Visual Studio Server Explorer. Também pode utilizar as bibliotecas PowerShell e Azure Storage Client, ou os SDKs Azure .NET, para aceder a dados no armazenamento de blob Azure.

Hadoop dirige o trabalho dos trabalhos como tentativas de tarefa em *vários* nós do cluster. O HDInsight pode iniciar tentativas de tarefa especulativas, terminando quaisquer outras tentativas de tarefa que não completem primeiro. Isto gera uma atividade significativa que é registada no controlador, stderr e ficheiros de log syslog no voo. Além disso, várias tentativas de tarefa estão a decorrer simultaneamente, mas um ficheiro de registo só pode apresentar resultados linearmente.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Registos HDInsight escritos para armazenamento De Blob Azure

Os clusters HDInsight são configurados para escrever registos de tarefas para uma conta de armazenamento Azure Blob para qualquer trabalho que seja submetido usando os cmdlets Azure PowerShell ou as APIs de submissão de trabalho .NET.  Se submeter os trabalhos através do SSH ao cluster, então a informação de registo de execução é armazenada nas Tabelas Azure, conforme discutido na secção anterior.

Além dos ficheiros de registo core gerados pelo HDInsight, serviços instalados como o YARN também geram ficheiros de registo de execução de emprego.  O número e o tipo de ficheiros de registo dependem dos serviços instalados.  Os serviços comuns são Apache HBase, Apache Spark, e assim por diante.  Investigue os ficheiros de execução de registo de trabalho de cada serviço para compreender os ficheiros de registo globais disponíveis no seu cluster.  Cada serviço tem os seus próprios métodos únicos de registo e locais para armazenar ficheiros de registo.  Como exemplo, os detalhes para aceder aos ficheiros de registo de serviço mais comuns (da Arn) são discutidos na secção seguinte.

### <a name="hdinsight-logs-generated-by-yarn"></a>Registos HDInsight gerados por ARN

O YARN agrega os registos em todos os recipientes num nó de trabalhador e armazena esses registos como um ficheiro de registo agregado por nó de trabalhador. Este registo é armazenado no sistema de ficheiros predefinido após o acabamento de uma aplicação. A sua aplicação pode utilizar centenas ou milhares de contentores, mas os registos para todos os recipientes que são executados num único nó de trabalhador esgotam-se sempre num único ficheiro. Só há um log por nó de trabalhador usado pela sua aplicação. A agregação de registoé ativada por padrão na versão 3.0 dos clusters HDInsight. Os registos agregados estão localizados no armazenamento predefinido do cluster.

```
/app-logs/<user>/logs/<applicationId>
```

Os registos agregados não são diretamente legíveis, uma vez que estão escritos num formato binário TFile indexado por recipiente. Utilize os registos do YARN ResourceManager ou ferramentas CLI para ver estes registos como texto simples para aplicações ou contentores de interesse.

#### <a name="yarn-cli-tools"></a>Ferramentas DE CLI de FIOS

Para utilizar as ferramentas YARN CLI, tem primeiro de se ligar ao cluster HDInsight utilizando o SSH. Especifique `<containerId>`as `<worker-node-address>` `<applicationId>`informações `<user-who-started-the-application>`e informações ao executar estes comandos. Pode ver os registos como texto simples com um dos seguintes comandos:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

O YARN ResourceManager UI funciona no nó da cabeça do cluster, e é acedido através da UI web ambari. Utilize os seguintes passos para visualizar os registos de ARN:

1. Num browser, navegue até `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME pelo nome do cluster do HDInsight.
2. A partir da lista de serviços à esquerda, selecione YARN.
3. A partir do dropdown quick Links, selecione um dos nós da cabeça do cluster e, em seguida, selecione **registos do ResourceManager**. É-lhe apresentada uma lista de links para registos de ARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Passo 4: Previsão de tamanhos e custos de armazenamento de volume de registo

Após completar os passos anteriores, tem uma compreensão dos tipos e volumes de ficheiros de registo que o seu cluster HDInsight está a produzir.

Em seguida, analise o volume de dados de registo em locais de armazenamento de registo sintetizadores durante um período de tempo. Por exemplo, pode analisar o volume e o crescimento ao longo de 30-60-90 períodos de dia.  Grave estas informações numa folha de cálculo ou use outras ferramentas como o Estúdio Visual, o Explorador de Armazenamento Azure ou a Power Query para o Excel. Para mais informações, consulte [os registos do Analyze HDInsight](hdinsight-debug-jobs.md).  

Agora tem informação suficiente para criar uma estratégia de gestão de registos para os registos chave.  Utilize a sua folha de cálculo (ou ferramenta de eleição) para prever tanto o crescimento do tamanho do registo como os custos do serviço de armazenamento de registo Saque Azure.  Considere também quaisquer requisitos de retenção de registos para o conjunto de registos que está a examinar.  Agora pode reprever os custos futuros de armazenamento de registo, depois de determinar quais os ficheiros de registo que podem ser eliminados (se houver) e quais os registos que devem ser mantidos e arquivados para armazenamento azure mais barato.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Passo 5: Determinar as políticas e processos de arquivo de registo

Depois de determinar quais os ficheiros de registo que podem ser eliminados, pode ajustar os parâmetros de registo em muitos serviços hadoop para eliminar automaticamente ficheiros de registo após um período de tempo especificado.

Para certos ficheiros de registo, pode utilizar uma abordagem de arquivamento de ficheiros de registo a preços mais baixos. Para registos de atividade do Gestor de Recursos Azure, pode explorar esta abordagem utilizando o portal Azure.  Configurar o arquivo dos registos do Gestor de Recursos selecionando o link **De Registo de Atividade** no portal Azure para a sua instância HDInsight.  No topo da página de pesquisa do Registo de Atividades, selecione o item do menu **Export** para abrir o painel de registo da **atividade de exportação.**  Preencha a subscrição, região, quer exporte para uma conta de armazenamento, e quantos dias para reter os registos. Neste mesmo painel, também pode indicar se deve exportar para um centro de eventos.

![Pré-visualização do registo da atividade de exportação do portal Azure](./media/hdinsight-log-management/hdi-export-log-files.png)

Em alternativa, pode script log arquivar com PowerShell.  Por exemplo, script PowerShell, consulte [registos de Automação Archive Azure para armazenamento de Blob Azure](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Aceder às métricas de armazenamento azure

O Armazenamento Azure pode ser configurado para registar operações de armazenamento e acesso. Pode utilizar estes registos muito detalhados para monitorização e planeamento de capacidades e para auditoria de pedidos de armazenamento. As informações registadas incluem detalhes de latência, permitindo-lhe monitorizar e afinar o desempenho das suas soluções.
Pode utilizar o .NET SDK para hadoop para examinar os ficheiros de registo gerados para o Armazenamento Azure que detém os dados para um cluster HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Controlar o tamanho e o número de índices de backup para ficheiros de registo antigos

Para controlar o tamanho e o número de ficheiros `RollingFileAppender`de registo retidos, detete as seguintes propriedades do :

* `maxFileSize`é o tamanho crítico do ficheiro, acima do qual o ficheiro é enrolado. O valor padrão é de 10 MB.
* `maxBackupIndex`especifica o número de ficheiros de backup a criar, padrão 1.

### <a name="other-log-management-techniques"></a>Outras técnicas de gestão de registos

Para evitar o escoamento do espaço do disco, pode utilizar algumas ferramentas de S, como [o logrotate](https://linux.die.net/man/8/logrotate) para gerir o manuseamento de ficheiros de registo. Pode configurar `logrotate` para executar diariamente, comprimindo ficheiros de registo e removendo os antigos. A sua abordagem depende dos seus requisitos, tais como por quanto tempo manter os ficheiros de registo nos nós locais.  

Também pode verificar se a exploração madeireira DEBUG está ativada para um ou mais serviços, o que aumenta consideravelmente o tamanho do registo de saída.  

Para recolher os registos de todos os nós para uma localização central, pode criar um fluxo de dados, como ingerir todas as entradas de registo em Solr.

## <a name="next-steps"></a>Passos seguintes

* [Prática de Monitorização e Exploração Madeudentária para HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Aceder aos registos de aplicações yARN apache hadoop em HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Como controlar o tamanho dos ficheiros de registo para vários componentes Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
