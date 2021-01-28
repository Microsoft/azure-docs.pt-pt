---
title: Gerir registos para um cluster HDInsight - Azure HDInsight
description: Determine os tipos, tamanhos e políticas de retenção para ficheiros de registo de atividades HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 0a6e837284917129bb56c6230e68927b79e95dac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945279"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Gerir registos de um cluster do HDInsight

Um cluster HDInsight produz uma variedade de ficheiros de registo. Por exemplo, Apache Hadoop e serviços relacionados, como Apache Spark, produzem registos de execução de emprego detalhados. A gestão de ficheiros de log faz parte da manutenção de um cluster HDInsight saudável. Também pode haver requisitos regulamentares para o arquivamento de registos.  Devido ao número e tamanho dos ficheiros de registo, otimizar o armazenamento de registos e arquivar ajuda na gestão dos custos de serviço.

A gestão de registos de clusters HDInsight inclui a retenção de informações sobre todos os aspetos do ambiente de cluster. Esta informação inclui todos os registos associados do Serviço Azure, configuração do cluster, informações de execução de emprego, quaisquer estados de erro e outros dados necessários.

Os passos típicos na gestão do registo HDInsight são:

* Passo 1: Determinar políticas de retenção de registos
* Passo 2: Gerir registos de configuração de versões de serviço de cluster
* Passo 3: Gerir ficheiros de registo de execução de trabalho de cluster
* Passo 4: Previsão de tamanhos e custos de armazenamento de volume de registo
* Passo 5: Determinar políticas e processos de arquivo de registos

## <a name="step-1-determine-log-retention-policies"></a>Passo 1: Determinar políticas de retenção de registos

O primeiro passo para a criação de uma estratégia de gestão de registos de cluster HDInsight é recolher informações sobre cenários de negócio e requisitos de armazenamento de histórico de execução de emprego.

### <a name="cluster-details"></a>Detalhes do cluster

Os seguintes detalhes do cluster são úteis para ajudar a recolher informações na sua estratégia de gestão de registos. Recolha esta informação de todos os clusters HDInsight que criou numa conta Azure em particular.

* Nome do cluster
* Região de cluster e zona de disponibilidade de Azure
* Estado do cluster, incluindo detalhes da última mudança de estado
* Tipo e número de instâncias HDInsight especificadas para os nós de mestre, núcleo e tarefa

Você pode obter a maior parte desta informação de alto nível usando o portal Azure.  Em alternativa, pode utilizar [o Azure CLI](/cli/azure/) para obter informações sobre o seu cluster HDInsight:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Também pode utilizar o PowerShell para visualizar estas informações.  Para obter mais informações, consulte [os clusters Apache Manage Hadoop em HDInsight utilizando a Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Compreenda as cargas de trabalho que correm nos seus clusters

É importante entender os tipos de carga de trabalho que estão a decorrer no seu cluster HDInsight para desenhar estratégias de registo apropriados para cada tipo.

* As cargas de trabalho são experimentais (como desenvolvimento ou teste) ou de qualidade de produção?
* Com que frequência as cargas de trabalho de qualidade de produção normalmente funcionam?
* Alguma das cargas de trabalho é intensiva em recursos e/ou longa duração?
* Alguma das cargas de trabalho utiliza um conjunto complexo de serviços Hadoop para os quais são produzidos vários tipos de registos?
* Alguma das cargas de trabalho tem requisitos de linhagem de execução regulamentar associados?

### <a name="example-log-retention-patterns-and-practices"></a>Exemplo de padrões e práticas de retenção de registos

* Considere manter o rastreio da linhagem de dados adicionando um identificador a cada entrada de registo, ou através de outras técnicas. Isto permite-lhe rastrear a origem original dos dados e da operação, e seguir os dados através de cada fase para entender a sua consistência e validade.

* Considere como pode recolher registos do cluster, ou de mais de um cluster, e colecioná-los para fins como auditoria, monitorização, planeamento e alerta. Pode utilizar uma solução personalizada para aceder e descarregar regularmente os ficheiros de registo e combiná-los e analisá-los para fornecer um ecrã de painel de instrumentos. Também pode adicionar capacidades adicionais para alertar para a deteção de segurança ou falha. Pode construir estes utilitários utilizando PowerShell, os HDInsight SDKs ou código que acede ao modelo de implementação clássico do Azure.

* Considere se uma solução ou serviço de monitorização seria um benefício útil. O Microsoft System Center fornece um pacote de [gestão HDInsight](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259). Também pode usar ferramentas de terceiros como Apache Chukwa e Ganglia para recolher e centralizar registos. Muitas empresas oferecem serviços para monitorizar soluções de big data baseadas em Hadoop, por exemplo: Centerity, Compuware APM, Sematext SPM e Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Passo 2: Gerir versões de serviço de cluster e visualizar registos

Um cluster típico de HDInsight utiliza vários serviços e pacotes de software de código aberto (como Apache HBase, Apache Spark, e assim por diante). Para algumas cargas de trabalho, como bioinformática, pode ser necessário manter o histórico de registos de configuração de serviço, além de registos de execução de emprego.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ver configurações de configuração do cluster com a UI Ambari

Apache Ambari simplifica a gestão, configuração e monitorização de um cluster HDInsight fornecendo uma UI web e uma API REST. Ambari está incluído em clusters HDInsight baseados em Linux. Selecione o painel **de painel do cluster** na página HDInsight do portal Azure para abrir a página de link do Cluster **Dashboards.**  Em seguida, selecione o painel **de painel de painel hdInsight** para abrir o UI Ambari.  É solicitado para as suas credenciais de login.

Para abrir uma lista de visualizações de serviço, selecione o painel **Ambari Views** na página do portal Azure para HDInsight.  Esta lista varia, dependendo das bibliotecas que instalou.  Por exemplo, pode ver YARN Queue Manager, Hive View e Tez View.  Selecione qualquer link de serviço para ver a configuração e informações de serviço.  A página Ambari UI **Stack and Version** fornece informações sobre o histórico de configuração e versão de serviço dos serviços de cluster. Para navegar nesta secção da UI Ambari, selecione o menu **Admin** e, em seguida, **Stacks e Versões**.  Selecione o separador **Versões** para ver as informações da versão de serviço.

![Apache Ambari admin Stack e versões](./media/hdinsight-log-management/ambari-stack-versions.png)

Utilizando o UI Ambari, pode descarregar a configuração para qualquer (ou todos) serviços em execução num determinado anfitrião (ou nó) no cluster.  Selecione o menu **Anfitriões** e, em seguida, o link para o anfitrião de interesse. Na página do anfitrião, selecione o botão **Ações de Anfitrião** e, em seguida, **Baixe o Cliente Configs**.

![Apache Ambari descarrega cliente anfitrião configs](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Ver os registos de ação do script

As [ações de script](hdinsight-hadoop-customize-cluster-linux.md) HDInsight executam scripts num cluster, manualmente ou quando especificados. Por exemplo, as ações de script podem ser usadas para instalar software adicional no cluster ou para alterar as definições de configuração a partir dos valores padrão. Os registos de ação do script podem fornecer informações sobre os erros ocorridos durante a configuração do cluster, bem como alterações de configuração que podem afetar o desempenho e disponibilidade do cluster.  Para ver o estado de uma ação do script, selecione o botão **de operações** no seu UI Ambari ou aceda aos registos de estado na conta de armazenamento predefinido. Os registos de armazenamento estão disponíveis em `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE` .

### <a name="view-ambari-alerts-status-logs"></a>Ver registos de estado de alerta de Ambari

Apache Ambari escreve alterações de estado de alerta para `ambari-alerts.log` . O caminho completo `/var/log/ambari-server/ambari-alerts.log` é. Para permitir a depuragem para o registo, altere um imóvel em Change e, em `/etc/ambari-server/conf/log4j.properties.` seguida, a entrada em baixo `# Log alert state changes` de:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Passo 3: Gerir os ficheiros de registo de execução de trabalho de cluster

O próximo passo é rever os ficheiros de registo de execução de emprego para os vários serviços.  Os serviços podem incluir Apache HBase, Apache Spark, e muitos outros. Um cluster Hadoop produz um grande número de troncos verbosos, de modo que determinar quais os troncos que são úteis (e que não são) pode ser demorado.  A compreensão do sistema de registo é importante para a gestão direcionada dos ficheiros de registo.  A imagem a seguir é um ficheiro de registo de exemplo.

![HdInsight exemplo de saída de amostra de ficheiro](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Aceda aos ficheiros de registo de Hadoop

O HDInsight armazena os seus ficheiros de registo tanto no sistema de ficheiros de cluster como no Azure Storage. Pode examinar ficheiros de registo no cluster abrindo uma ligação [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) ao cluster e navegando no sistema de ficheiros, ou utilizando o portal Hadoop YARN Status no servidor de nó de cabeça remoto. Pode examinar os ficheiros de registo no Azure Storage utilizando qualquer uma das ferramentas que podem aceder e descarregar dados a partir do Azure Storage. Exemplos são [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)e Visual Studio Server Explorer. Também pode utilizar as bibliotecas powerShell e Azure Storage Client, ou os Azure .NET SDKs, para aceder a dados no armazenamento de blob Azure.

Hadoop dirige o trabalho dos trabalhos como tentativas de *tarefa em vários* nós no cluster. O HDInsight pode iniciar tentativas de tarefas especulativas, terminando quaisquer outras tentativas de tarefa que não completem primeiro. Isto gera uma atividade significativa que é registada no controlador, stderr e syslog log files on-the-fly. Além disso, várias tentativas de tarefa estão a ser executando simultaneamente, mas um ficheiro de registo só pode exibir resultados linearmente.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Registos HDInsight escritos para armazenamento Azure Blob

Os clusters HDInsight são configurados para escrever registos de tarefas numa conta de armazenamento Azure Blob para qualquer trabalho que seja submetido utilizando os cmdlets Azure PowerShell ou as APIs de submissão de emprego .NET.  Se submeter empregos através de SSH ao cluster, então a informação de registo de execução é armazenada nas Tabelas Azure, conforme discutido na secção anterior.

Além dos ficheiros de registo de núcleo gerados pelo HDInsight, serviços instalados como o YARN também geram ficheiros de registo de execução de emprego.  O número e o tipo de ficheiros de registo dependem dos serviços instalados.  Os serviços comuns são Apache HBase, Apache Spark, e assim por diante.  Investigue os ficheiros de execução de registo de trabalho para cada serviço para entender os ficheiros de registo geral disponíveis no seu cluster.  Cada serviço tem os seus próprios métodos únicos de registo e locais para armazenar ficheiros de registo.  Como exemplo, os detalhes para aceder aos ficheiros de registo de serviço mais comuns (a partir de YARN) são discutidos na seguinte secção.

### <a name="hdinsight-logs-generated-by-yarn"></a>Registos HDInsight gerados por YARN

O YARN agrega registos em todos os contentores de um nó de trabalhador e armazena esses registos como um ficheiro de registo agregado por nó de trabalhador. Este registo é armazenado no sistema de ficheiros predefinidos após o fim de uma aplicação. A sua aplicação pode utilizar centenas ou milhares de contentores, mas os registos para todos os contentores que são executados num único nó de trabalhador são sempre agregados a um único ficheiro. Há apenas um registo por nó de trabalhador usado pela sua aplicação. A agregação de registos é ativada por padrão na versão 3.0 ou acima dos clusters HDInsight. Os troncos agregados estão localizados em armazenamento predefinido para o cluster.

```
/app-logs/<user>/logs/<applicationId>
```

Os registos agregados não são diretamente legíveis, uma vez que estão escritos num formato binário TFile indexado por contentor. Utilize os registos YARN ResourceManager ou ferramentas CLI para ver estes registos como texto simples para aplicações ou recipientes de interesse.

#### <a name="yarn-cli-tools"></a>Ferramentas YARN CLI

Para utilizar as ferramentas YARN CLI, tem primeiro de ligar-se ao cluster HDInsight utilizando SSH. Especificar o `<applicationId>` , , e a `<user-who-started-the-application>` `<containerId>` `<worker-node-address>` informação ao executar estes comandos. Pode ver os registos como texto simples com um dos seguintes comandos:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>UI de Gestão de Recursos DE YARN

A UI YARN ResourceManager funciona no nó de cabeça de cluster, e é acedida através da UI web Ambari. Utilize os seguintes passos para visualizar os registos YARN:

1. Num browser, navegue até `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME pelo nome do cluster do HDInsight.
2. Da lista de serviços à esquerda, selecione YARN.
3. A partir do dropdown de Links Rápidos, selecione um dos nós da cabeça do cluster e, em seguida, selecione **registos ResourceManager**. É-lhe apresentada uma lista de links para registos DE FIOS.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Passo 4: Previsão de tamanhos e custos de armazenamento de volume de registo

Depois de completar os passos anteriores, tem uma compreensão dos tipos e volumes de ficheiros de registo que o seu(s) cluster(s) HDInsight(s) está a produzir.

Em seguida, analise o volume de dados de registo em locais de armazenamento de registo sonoro durante um período de tempo. Por exemplo, pode analisar volume e crescimento ao longo de períodos de 30-60-90 dias.  Grave estas informações numa folha de cálculo ou utilize outras ferramentas como o Visual Studio, o Azure Storage Explorer ou a Power Query for Excel. ```

Agora tem informações suficientes para criar uma estratégia de gestão de registos para os registos de chaves.  Utilize a sua folha de cálculo (ou ferramenta de eleição) para prever o crescimento do tamanho do registo e os custos de serviço de armazenamento de registos Azure.  Considere também quaisquer requisitos de retenção de registos para o conjunto de registos que está a examinar.  Agora pode reformular os custos futuros de armazenamento de registos, depois de determinar quais os ficheiros de registo que podem ser eliminados (se houver) e quais os registos que devem ser conservados e arquivados para o Armazenamento Azure mais barato.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Passo 5: Determinar políticas e processos de arquivo de registos

Depois de determinar quais os ficheiros de registo que podem ser eliminados, pode ajustar os parâmetros de registo em muitos serviços hadoop para eliminar automaticamente os ficheiros de registo após um período de tempo especificado.

Para certos ficheiros de registo, pode utilizar uma abordagem de arquivamento de ficheiros de registo mais baixo. Para registos de atividade do Azure Resource Manager, pode explorar esta abordagem utilizando o portal Azure.  Confiúde o arquivo dos registos do Gestor de Recursos selecionando o link **'Registo de Atividade'** no portal Azure para o seu caso HDInsight.  No topo da página de pesquisa do Registo de Atividade, selecione o item do menu **Exportação** para abrir o painel **de registo de atividades de Exportação.**  Preencha a subscrição, região, se exporta para uma conta de armazenamento, e quantos dias para reter os registos. Neste mesmo painel, você também pode indicar se exporta para um centro de eventos.

![Pré-visualização do registo de atividade de exportação do portal Azure](./media/hdinsight-log-management/hdi-export-log-files.png)

Em alternativa, pode fazer login com PowerShell.  Para um exemplo, o script PowerShell, consulte [os registos da Automação Archive Azure para o armazenamento de blob Azure](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Aceder às métricas de armazenamento do Azure

O Azure Storage pode ser configurado para registar operações de armazenamento e acesso. Você pode usar estes registos muito detalhados para monitorização e planeamento de capacidades, e para auditar pedidos de armazenamento. As informações registadas incluem detalhes de latência, permitindo-lhe monitorizar e afinar o desempenho das suas soluções.
Pode utilizar o .NET SDK para Hadoop para examinar os ficheiros de registo gerados para o Azure Storage que detém os dados de um cluster HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Controle o tamanho e o número de índices de backup para ficheiros de registo antigos

Para controlar o tamanho e o número de ficheiros de registo retidos, detenha as seguintes propriedades do `RollingFileAppender` :

* `maxFileSize` é o tamanho crítico do ficheiro, acima do qual o ficheiro é enrolado. O valor predefinido é de 10 MB.
* `maxBackupIndex` especifica o número de ficheiros de cópia de segurança a criar, predefinido 1.

### <a name="other-log-management-techniques"></a>Outras técnicas de gestão de registos

Para evitar ficar sem espaço em disco, pode utilizar algumas ferramentas de SISTEMA, como [o logrotate,](https://linux.die.net/man/8/logrotate) para gerir o manuseamento de ficheiros de registo. Pode configurar `logrotate` para funcionar diariamente, comprimindo ficheiros de registo e removendo os antigos. A sua abordagem depende dos seus requisitos, tais como quanto tempo para manter os registos nos nós locais.  

Também pode verificar se a exploração madeireira DEBUG está ativada para um ou mais serviços, o que aumenta consideravelmente o tamanho do registo de saída.  

Para recolher os registos de todos os nós para uma localização central, pode criar um fluxo de dados, como ingerir todas as entradas de registo em Solr.

## <a name="next-steps"></a>Próximos passos

* [Prática de Monitorização e Registo para HDInsight](/previous-versions/msp-n-p/dn749790(v=pandp.10))
* [Aceder a registos de aplicações Apache Hadoop YARN em HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Como controlar o tamanho dos ficheiros de registo para vários componentes apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
