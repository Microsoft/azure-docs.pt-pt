---
title: 'Depure o Apache Hadoop: Ver registos e interpretar mensagens de erro - Azure HDInsight'
description: Saiba mais sobre a mensagens de erro, que poderá receber ao administrar o HDInsight com o PowerShell e os passos que pode tomar para recuperar.
services: hdinsight
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 4d3d45434a90e89e011bf8aa7c3fd37909e25386
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203069"
---
# <a name="analyze-apache-hadoop-logs"></a>Analisar registos do Apache Hadoop

Cada cluster do Apache Hadoop no HDInsight do Azure tem uma conta de armazenamento do Azure utilizada como o sistema de ficheiros predefinido. A conta de armazenamento é referida como a conta de armazenamento predefinida. Cluster utiliza o armazenamento de tabelas do Azure e o armazenamento de BLOBs na conta de armazenamento predefinida para armazenar os seus registos.  Para obter a conta de armazenamento predefinida para o seu cluster, veja [Apache Hadoop gerir clusters no HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Mantém os registos na conta de armazenamento, mesmo depois do cluster é eliminado.

## <a name="logs-written-to-azure-tables"></a>Registos escritos para as tabelas do Azure

Os registos escritos para as tabelas do Azure fornecem um nível de informações sobre o que acontece com um cluster do HDInsight.

Quando cria um cluster do HDInsight, seis tabelas são criadas automaticamente para os clusters baseados em Linux no armazenamento de tabela do padrão:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Os nomes de ficheiro de tabela são **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Estas tabelas contêm os seguintes campos:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Anfitrião
* MALoggingHash
* Mensagem
* N
* PreciseTimeStamp
* Função
* RowIndex
* Inquilino
* CARIMBO DE DATA/HORA
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Ferramentas para acessar os registos
Há muitas ferramentas disponíveis para o acesso a dados nestas tabelas:

* Visual Studio
* Explorador do Storage do Azure
* Power Query para Excel

#### <a name="use-power-query-for-excel"></a>Utilize o Power Query para Excel
Power Query pode ser instalada a partir [Microsoft Power Query para Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Consulte a página de download para os requisitos de sistema.

**Utilize o Power Query para abrir e analise o registo de serviço**

1. Open **Microsoft Excel**.
2. Partir do **Power Query** menu, clique em **do Azure**e, em seguida, clique em **armazenamento de tabelas do Azure da Microsoft**.
   
    ![HDInsight Hadoop Excel PowerQuery abrir o armazenamento de tabelas do Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Introduza o nome de conta de armazenamento, o nome abreviado ou o FQDN.
4. Introduza a chave de conta de armazenamento. Deverá ver uma lista de tabelas:
   
    ![Registos de Hadoop do HDInsight, armazenados no armazenamento de tabelas do Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Com o botão direito a tabela hadoopservicelog a **Navigator** painel e selecione **editar**. Deverá ver quatro colunas. Opcionalmente, elimine o **chave de partição**, **chave de linha**, e **Timestamp** colunas por selecionando-os, em seguida, clique em **remover colunas** do as opções na faixa de opções.
6. Clique no ícone de expansão na coluna de conteúdo para escolher as colunas que pretende importar para a folha de cálculo do Excel. Para esta demonstração, optei por TraceLevel e ComponentName: Ele pode me dá algumas informações básicas, no qual os componentes tinham problemas.
   
    ![Os registos de Hadoop do HDInsight escolher colunas](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Clique em **OK** para importar os dados.
8. Selecione o **TraceLevel**, função, e **ComponentName** colunas e clique em **Group By** controlo na faixa de opções.
9. Clique em **OK** na caixa de diálogo Group By
10. Clique em * * aplicam-se & Close * *.

Agora, pode utilizar o Excel para filtrar e ordenar conforme necessário. Convém incluir outras colunas (por exemplo, a mensagem) para analisar detalhadamente problemas quando estes ocorrem, mas selecionando e as colunas descritas acima de agrupamento fornecem uma visão decente do que está acontecendo com os serviços do Hadoop. A mesma idéia pode ser aplicada às tabelas setuplog e hadoopinstalllog.

#### <a name="use-visual-studio"></a>Utilizar o Visual Studio
**Utilizar o Visual Studio**

1. Abra o Visual Studio.
2. Do **View** menu, clique em **Cloud Explorer**. Ou clique simplesmente **CTRL +\, CTRL + X**.
3. Partir **Cloud Explorer**, selecione **tipos de recurso**.  A outra opção disponível é **grupos de recursos**.
4. Expanda **contas de armazenamento**, a conta de armazenamento predefinida para o seu cluster e, em seguida **tabelas**.
5. Faça duplo clique em **hadoopservicelog**.
6. Adicione um filtro. Por exemplo:
   
        TraceLevel eq 'ERROR'
   
    ![Os registos de Hadoop do HDInsight escolher colunas](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Para obter mais informações sobre a construção de filtros, veja [construir as cadeias de filtro para o Designer de tabela](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Registos escritos no armazenamento de Blobs do Azure
Os registos escritos para as tabelas do Azure fornecem um nível de informações sobre o que acontece com um cluster do HDInsight. No entanto, estas tabelas não fornecer registos de nível de tarefa, os quais podem ser úteis na exploração mais detalhadamente problemas quando estes ocorrerem. Para fornecer esse próximo nível de detalhe, os clusters do HDInsight são configurados para escrever registos de tarefas para a sua conta de armazenamento de BLOBs para qualquer tarefa que é enviada por meio de Templeton. Na prática, isso significa que as tarefas submetidas através de cmdlets do Microsoft Azure PowerShell ou as APIs de submissão de tarefa no .NET, não as tarefas submetidas por meio do acesso RDP/linha de comandos para o cluster. 

Para ver os registos, consulte [registos de aplicações de acesso Apache Hadoop YARN no HDInsight baseado em Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).


Para obter mais informações sobre os registos de aplicação, consulte [simplifica a gestão de registos de utilizador e o acesso no Apache Hadoop YARN](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).


## <a name="view-cluster-health-and-job-logs"></a>Ver os registos de estado de funcionamento e a tarefa de cluster
### <a name="access-the-ambari-ui"></a>Aceder à IU do Ambari
A partir do portal do Azure, clique num nome de cluster do HDInsight para abrir o painel do cluster. No painel do cluster, clique em **Dashboard**.

![Iniciar o dashboard do cluster](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Acesso o IU do Yarn
A partir do portal do Azure, clique num nome de cluster do HDInsight para abrir o painel do cluster. No painel do cluster, clique em **Dashboard**. Quando lhe for pedido, introduza as credenciais de administrador de cluster. No Ambari, selecione **YARN** na lista de serviços à esquerda. Na página que aparece, selecione **ligações rápidas**, em seguida, selecione a entrada de nó principal Active Directory e Gestor de recursos da interface do Usuário.

Pode utilizar a IU do YARN para fazer o seguinte:

* **Obter estado do cluster**. No painel à esquerda, expanda **Cluster**e clique em **sobre**. Neste momento cluster detalhes de estado como total alocado memória, núcleos utilizados, o estado do Gestor de recursos de cluster, versão do cluster e assim por diante.
  
    ![Iniciar o dashboard do cluster](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Obter estado do nó**. No painel à esquerda, expanda **Cluster**e clique em **nós**. Isto apresenta uma lista de todos os nós do cluster, o endereço HTTP de cada nó, recursos alocados a cada nó, etc.
* **Monitorizar o estado da tarefa**. No painel à esquerda, expanda **Cluster**e, em seguida, clique em **aplicativos** para listar todas as tarefas no cluster. Se quiser examinar as tarefas num estado específico (por exemplo, a execução de novo, submetida, etc.), clique no link apropriado em **aplicativos**. Pode clicar ainda mais o nome da tarefa para obter mais informações sobre a tarefa de tais incluindo a saída, os logs, etc.

### <a name="access-the-hbase-ui"></a>Acessar a interface do Usuário do HBase
A partir do portal do Azure, clique num nome de cluster do HBase do HDInsight para abrir o painel do cluster. No painel do cluster, clique em **Dashboard**. Quando lhe for pedido, introduza as credenciais de administrador de cluster. No Ambari, selecione o HBase na lista de serviços. Selecione **ligações rápidas** no topo da página, aponte para a ligação de nó ativa do Zookeeper e, em seguida, clique em IU principal do HBase.

## <a name="hdinsight-error-codes"></a>Códigos de erro do HDInsight
As mensagens de erro descritas nesta secção são fornecidas para ajudar os usuários do Hadoop no Azure HDInsight, compreender as condições de erro possíveis que podem surgir quando administrar o serviço com o Azure PowerShell e aconselha-los sobre as etapas que podem ser realizadas Para recuperar do erro.

Algumas dessas mensagens de erro podem também ser vistas no portal do Azure quando é utilizado para gerir os clusters do HDInsight. Mas outras mensagens de erro que poderá encontrar existem menos granular devido às restrições sobre as ações corretivas possíveis neste contexto. Outras mensagens de erro são fornecidas em contextos em que a atenuação é óbvia. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Descrição**: Introduza os detalhes de base de dados SQL do Azure para, pelo menos, um componente para utilizar definições personalizadas para metastores Hive e Oozie.
* **Atenuação**: O utilizador tem de fornecer um metastore do SQL Azure válido e repita o pedido.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Descrição**: Não foi possível criar o cluster na região *nameOfYourRegion*. Utilize uma região de HDInsight válida e repita o pedido.
* **Atenuação**: Cliente deve criar a região de cluster que suporta atualmente: Sudeste asiático, Europa Ocidental, Europa do Norte, E.U.A. Leste ou E.U.A. oeste.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Descrição**: O servidor não foi possível localizar o registo de pedido do cluster.  
* **Atenuação**: Repita a operação.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Descrição**: Nome DNS do cluster *yourDnsName* é inválido. Certifique-se nome comece e termine com alfanumérico e só pode conter "-" caráter especial  
* **Atenuação**: Certifique-se de que utiliza um nome DNS válido para o cluster que inicia e termina com alfanuméricos e não contém nenhuma especial de outros carateres para além de travessão "-" e, em seguida, repita a operação.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Descrição**: Nome do cluster *yourClusterName* não está disponível. Escolha outro nome.  
* **Atenuação**: O utilizador deve especificar um clustername exclusiva e não existe e repita. Se o utilizador estiver a utilizar o Portal, a interface do Usuário irá notificá-los se um nome de cluster já está a ser utilizado durante os passos de criar.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Descrição**: Palavra-passe do cluster é inválido. Palavra-passe tem de ser no mínimo 10 carateres de comprimento e tem de conter pelo menos um número, letra maiúscula, letra minúscula e um caráter especial sem espaços e não deve conter o nome de utilizador como parte do mesmo.  
* **Atenuação**: Forneça uma palavra-passe do cluster válido e repita a operação.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Descrição**: Nome de utilizador do cluster é inválido. Certifique-se de que nome de utilizador não contém carateres especiais ou espaços.  
* **Atenuação**: Forneça um nome de utilizador do cluster válido e repita a operação.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Descrição**: Nome DNS do cluster *yourDnsClusterName* é inválido. Certifique-se nome comece e termine com alfanumérico e só pode conter "-" caráter especial  
* **Atenuação**: Forneça um nome de utilizador de cluster DNS válido e repita a operação.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Descrição**: Nome do contentor URI *yourcontainerURI* e o nome DNS *yourDnsName* no pedido corpo têm de ser iguais.  
* **Atenuação**: Certifique-se de que seu nome de contentor e o nome de DNS são os mesmos e repita a operação.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Descrição**: Configuração de cluster inválida. Não é possível encontrar quaisquer definições de nó de dados de tamanho de nó.  
* **Atenuação**: Repita a operação.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Descrição**: Falha na eliminação da implementação para o Cluster  
* **Atenuação**: Repita a operação de eliminação.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Descrição**: Erro de configuração do serviço. Informações de mapeamento de DNS necessárias não foi encontradas.  
* **Atenuação**: Eliminar o cluster e criar um novo cluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Descrição**: Duplicar a tentativa de criação do contentor de cluster. Registo existe para *nameOfYourContainer* mas Etags não coincidem.
* **Atenuação**: Forneça um nome exclusivo para o contentor e repita a operação de criação.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Descrição**: O serviço alojado *nameOfYourHostedService* já contém um cluster. Um serviço alojado não pode conter vários clusters  
* **Atenuação**: Aloje o cluster no outro serviço alojado.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Descrição**: O servidor não foi possível atualizar o estado da implementação do cluster.  
* **Atenuação**: Repita a operação. Se isto acontecer várias vezes, contacte o CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Descrição**: Cluster *yourClusterName* foram eliminados como parte da manutenção. Volte a criar o cluster.
* **Atenuação**: Recrie o cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Descrição**: Configuração de cluster inválida. Configuração de nó principal necessária não foi encontrada em tamanhos de nó.
* **Atenuação**: Repita a operação.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Descrição**: Não é possível criar o serviço alojado *nameOfYourHostedService*. Tente novamente o pedido.  
* **Atenuação**: Repita o pedido.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Descrição**: O serviço alojado *nameOfYourHostedService* já tem uma implementação de produção. Um serviço alojado não pode conter várias implementações de produção. Repita o pedido com um nome de cluster diferente.
* **Atenuação**: Utilize outro nome de cluster e repita o pedido.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Descrição**: O serviço alojado *nameOfYourHostedService* para não foi possível encontrar o cluster.  
* **Atenuação**: Se o cluster está no Estado com erros, eliminá-lo e, em seguida, tente novamente.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Descrição**: O serviço alojado *nameOfYourHostedService* não tem nenhuma implementação associada.  
* **Atenuação**: Se o cluster está no Estado com erros, eliminá-lo e, em seguida, tente novamente.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Descrição**: O SubscriptionId *yourSubscriptionId* não tem núcleos à esquerda para criar cluster *yourClusterName*. Necessário: *resourcesRequired*, disponível: *resourcesAvailable*.  
* **Atenuação**: Liberte recursos na sua subscrição ou aumentar os recursos disponíveis para a subscrição e tente criar o cluster novamente.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Descrição**: ID de subscrição *yourSubscriptionId* não tem quota para um novo HostedService Criar cluster *yourClusterName*.  
* **Atenuação**: Liberte recursos na sua subscrição ou aumentar os recursos disponíveis para a subscrição e tente criar o cluster novamente.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Descrição**: O servidor encontrou um erro interno. Tente novamente o pedido.  
* **Atenuação**: Repita o pedido.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Descrição**: Localização de armazenamento do Azure *dataRegionName* não é uma localização válida. Certifique-se de que a região está correta e repita o pedido.
* **Atenuação**: Selecione uma localização de armazenamento que suporte o HDInsight, verifique que o cluster estiver localizado conjuntamente e repita a operação.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Descrição**: Tamanho da VM inválido para nós de dados. Tamanho de 'VM grande' só é suportado para todos os nós de dados.  
* **Atenuação**: Especifique o tamanho de nó suportados para o nó de dados e repita a operação.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Descrição**: Tamanho VM inválido para o nó principal. Tamanho de 'ExtraLarge VM' só é suportado para o nó principal.  
* **Atenuação**: Especificar o tamanho do nó suportados para o nó principal e repita a operação

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Descrição**: ID de subscrição *yourSubscriptionId* a ser utilizado não tem permissões suficientes para executar a operação de eliminação para o cluster *yourClusterName*.  
* **Atenuação**: Se o cluster está no Estado com erros, soltá-la e, em seguida, tente novamente.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Descrição**: Nome do contentor de blob de conta de armazenamento externo *yourContainerName* é inválido. Certificar-se de que nome começa com uma letra e contenha apenas letras minúsculas, números e dash.  
* **Atenuação**: Especifique um nome de contentor de blob da conta de armazenamento válido e repita a operação.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Descrição**: Configuração para conta de armazenamento externo *yourStorageAccountName* é necessária para ter os detalhes da chave secretas para ser definida.  
* **Atenuação**: Especifique uma chave secreta válida para a conta de armazenamento e repita a operação.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Descrição**: Cabeçalho de versão *yourVersionHeader* não está num formato válido de aaaa-mm-dd.  
* **Atenuação**: Especifique um formato válido para o cabeçalho da versão e repita o pedido.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Descrição**: Configuração de cluster inválida. Foi encontrado mais do que uma configuração de nó principal.  
* **Atenuação**: Edite a configuração, de modo que apenas um nó principal está especificado.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Descrição**: Não foi possível concluir a operação no tempo permitido ou o número máximo de repetições possíveis. Tente novamente o pedido.  
* **Atenuação**: Repita o pedido.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Descrição**: Parâmetro *yourParameterName* não pode ser nulo nem estar vazio.  
* **Atenuação**: Especifique um valor válido para o parâmetro.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Descrição**: Um ou mais das entradas de pedido de criação de cluster não são válido. Certifique-se de que os valores de entrada estão corretos e repita o pedido.  
* **Atenuação**: Certifique-se de que os valores de entrada estão corretos e repita o pedido.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Descrição**: Capacidade de região não está disponível para a região *yourRegionName* e o ID de subscrição *yourSubscriptionId*.  
* **Atenuação**: Especifique uma região que oferece suporte a clusters do HDInsight. As regiões publicamente suportadas são: Sudeste asiático, Europa Ocidental, Europa do Norte, E.U.A. Leste ou E.U.A. oeste.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Descrição**: Conta de armazenamento *yourStorageAccountName* está na região *currentRegionName*. Ele deve ser a mesmo que a região de cluster *yourClusterRegionName*.  
* **Atenuação**: Especifique uma conta de armazenamento na mesma região que o cluster estiver no ou se os dados já estão a ser a conta de armazenamento, criar um novo cluster na mesma região que a conta de armazenamento existente. Se estiver a utilizar o Portal, a interface do Usuário irá notificá-los deste problema com antecedência.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Descrição**: Fornecido ID de subscrição *yourSubscriptionId* não está ativa.  
* **Atenuação**: Reativar a sua subscrição ou obter uma nova subscrição válida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Descrição**: ID de subscrição *yourSubscriptionId* nebyl nalezen.  
* **Atenuação**: Verificar se o seu ID de subscrição é válido e repita a operação.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Descrição**: Não é possível resolver o DNS *yourDnsUrl*. Certifique-se de que o URL completamente qualificado para o ponto de final do blob é fornecido.  
* **Atenuação**: Forneça um URL de blob válido. O URL tem de ser totalmente válido, incluindo a partir *http://* e termina numa *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Descrição**: Não é possível verificar a localização do recurso *yourDnsUrl*. Certifique-se de que o URL completamente qualificado para o ponto de final do blob é fornecido.  
* **Atenuação**: Forneça um URL de blob válido. O URL tem de ser totalmente válido, incluindo a partir *http://* e termina numa *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Descrição**: Capacidade de versão não está disponível para a versão *specifiedVersion* e o ID de subscrição *yourSubscriptionId*.  
* **Atenuação**: Escolha uma versão que está disponível e repita a operação.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Descrição**: Versão *specifiedVersion* não suportado.
* **Atenuação**: Escolha uma versão suportada e repita a operação.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Descrição**: Versão *specifiedVersion* não está disponível na região do Azure *specifiedRegion*.  
* **Atenuação**: Escolha uma versão que é suportada na região especificada e repita a operação.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Descrição**: Configuração de cluster inválida. Configuração de conta do necessárias WASB não foi encontrada na contas externas.  
* **Atenuação**: Certifique-se de que a conta existe e está corretamente especificado na configuração e repita a operação.

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar vistas do Apache Ambari para depurar tarefas do Apache Tez no HDInsight](../hdinsight-debug-ambari-tez-view.md)
* [Ativar capturas de área dinâmica para dados dos serviços de Apache Hadoop no HDInsight baseado em Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Manage HDInsight clusters by using the Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)
