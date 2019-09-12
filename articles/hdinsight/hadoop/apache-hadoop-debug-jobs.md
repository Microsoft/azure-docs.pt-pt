---
title: 'Apache Hadoop de depuração: Exibir logs e interpretar mensagens de erro-Azure HDInsight'
description: Saiba mais sobre as mensagens de erro que você pode receber ao administrar o HDInsight usando o PowerShell e as etapas que você pode seguir para recuperar.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 751d5b47006f5c99a747503ad4f052b3e03a043c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882441"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>Analisar logs de Apache Hadoop no Azure HDInsight

Cada cluster Apache Hadoop no Azure HDInsight tem uma conta de armazenamento do Azure usada como o sistema de arquivos padrão. A conta de armazenamento é referida como a conta de armazenamento padrão. O cluster usa o armazenamento de tabelas do Azure e o armazenamento de BLOBs na conta de armazenamento padrão para armazenar seus logs.  Para descobrir a conta de armazenamento padrão para o cluster, consulte [gerenciar clusters de Apache Hadoop no HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Os logs são mantidos na conta de armazenamento mesmo depois que o cluster é excluído.

## <a name="logs-written-to-azure-tables"></a>Logs gravados em tabelas do Azure

Os logs gravados nas tabelas do Azure fornecem um nível de insight sobre o que está acontecendo com um cluster HDInsight.

Quando você cria um cluster HDInsight, seis tabelas são criadas automaticamente para clusters baseados em Linux no armazenamento de tabela padrão:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Os nomes de arquivo de tabela são **u\<ClusterName\<> DDMonYYYYatHHMMSSsss TableName >** .

Essas tabelas contêm os seguintes campos:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Role
* RowIndex
* Inquilino
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Ferramentas para acessar os logs
Há muitas ferramentas disponíveis para acessar dados nessas tabelas:

* Visual Studio
* Explorador do Storage do Azure
* Power Query para Excel

#### <a name="use-power-query-for-excel"></a>Usar o Power Query para Excel
Power Query pode ser instalado de [Microsoft Power Query para Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Consulte a página de download para os requisitos do sistema.

**Usar Power Query para abrir e analisar o log de serviço**

1. Abra **o Microsoft Excel**.
2. No menu **Power Query** , clique em **do Azure**e, em seguida, clique em **de Microsoft Azure armazenamento de tabelas**.
   
    ![HDInsight Hadoop Excel PowerQuery abrir o armazenamento de tabelas do Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Insira o nome da conta de armazenamento, ou seja, o nome curto ou o FQDN.
4. Insira a chave da conta de armazenamento. Você deverá ver uma lista de tabelas:
   
    ![Logs do HDInsight Hadoop armazenados no armazenamento de tabelas do Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Clique com o botão direito do mouse na tabela hadoopservicelog no painel **navegador** e selecione **Editar**. Você deverá ver quatro colunas. Opcionalmente, exclua as colunas **chave de partição**, **chave de linha**e **carimbo de data/hora** selecionando-as e, em seguida, clicando em **remover colunas** das opções na faixa.
6. Clique no ícone de expansão na coluna conteúdo para escolher as colunas que você deseja importar para a planilha do Excel. Para esta demonstração, escolhi TraceLevel e ComponentName: Ele pode fornecer algumas informações básicas sobre quais componentes tiveram problemas.
   
    ![Logs do Hadoop no HDInsight escolher colunas Excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "Logs do Hadoop no HDInsight escolher colunas Excel")
7. Clique em **OK** para importar os dados.
8. Selecione as colunas **TraceLevel**, role e **ComponentName** e, em seguida, clique em **Agrupar por** controle na faixa de opções.
9. Clique em **OK** na caixa de diálogo agrupar por
10. Clique em * * aplicar & fechar * *.

Agora você pode usar o Excel para filtrar e classificar conforme necessário. Talvez você queira incluir outras colunas (como a mensagem) para fazer uma busca detalhada nos problemas quando eles ocorrerem, mas selecionar e agrupar as colunas descritas acima fornece uma imagem completa do que está acontecendo com os serviços do Hadoop. A mesma ideia pode ser aplicada às tabelas Setuplog e hadoopinstalllog.

#### <a name="use-visual-studio"></a>Utilizar o Visual Studio
**Utilizar o Visual Studio**

1. Abra o Visual Studio.
2. No menu **Exibir** , clique em **Cloud Explorer**. Ou simplesmente clique em **Ctrl\, + CTRL + X**.
3. No **Cloud Explorer**, selecione **tipos de recursos**.  A outra opção disponível é **grupos de recursos**.
4. Expanda **contas de armazenamento**, a conta de armazenamento padrão para o cluster e, em seguida, **tabelas**.
5. Clique duas vezes em **hadoopservicelog**.
6. Adicione um filtro. Por exemplo:
   
        TraceLevel eq 'ERROR'
   
    ![Logs do HDInsight Hadoop escolhem colunas vs](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "Logs do HDInsight Hadoop escolhem colunas vs")
   
    Para obter mais informações sobre como construir filtros, consulte [construir cadeias de caracteres de filtro para o designer de tabela](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Logs gravados no armazenamento de BLOBs do Azure
Os logs gravados nas tabelas do Azure fornecem um nível de insight sobre o que está acontecendo com um cluster HDInsight. No entanto, essas tabelas não fornecem logs no nível da tarefa, o que pode ser útil para detalhar os problemas quando eles ocorrerem. Para fornecer esse próximo nível de detalhes, os clusters HDInsight são configurados para gravar logs de tarefas em sua conta de armazenamento de BLOBs para qualquer trabalho enviado por meio do Templeton. Praticamente, isso significa que os trabalhos enviados usando os cmdlets Microsoft Azure PowerShell ou as APIs de envio de trabalho do .NET, não os trabalhos enviados por meio de acesso de linha de comando/RDP ao cluster. 

Para exibir os logs, consulte [acessar Apache Hadoop logs de aplicativo do yarn no HDInsight baseado em Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).


Para obter mais informações sobre logs de aplicativos, consulte [simplificando o gerenciamento e o acesso de logs de usuários no Apache HADOOP yarn](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).


## <a name="view-cluster-health-and-job-logs"></a>Exibir logs de trabalho e integridade do cluster
### <a name="access-the-ambari-ui"></a>Acessar a interface do usuário do amAmbari
No portal do Azure, clique em um nome de cluster HDInsight para abrir o painel do cluster. No painel cluster, clique em **painel**.

![Iniciar painel do cluster](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Acessar a interface do usuário do amYarn
No portal do Azure, clique em um nome de cluster HDInsight para abrir o painel do cluster. No painel cluster, clique em **painel**. Quando solicitado, insira as credenciais de administrador do cluster. Em Ambari, selecione **yarn** na lista de serviços à esquerda. Na página exibida, selecione **links rápidos**e, em seguida, selecione a entrada do nó de cabeçalho ativo e a interface do usuário do Resource Manager.

Você pode usar a interface do usuário do amYARN para fazer o seguinte:

* **Obter status do cluster**. No painel esquerdo, expanda **cluster**e clique em **sobre**. Isso apresenta detalhes de status do cluster, como memória alocada total, núcleos usados, estado do Gerenciador de recursos de cluster, versão do cluster e assim por diante.
  
    ![Iniciar o painel do cluster yarn](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "Iniciar o painel do cluster yarn")
* **Obter status do nó**. No painel esquerdo, expanda **cluster**e clique em **nós**. Isso lista todos os nós no cluster, o endereço HTTP de cada nó, os recursos alocados para cada nó, etc.
* **Monitorar o status do trabalho**. No painel esquerdo, expanda **cluster**e clique em **aplicativos** para listar todos os trabalhos no cluster. Se você quiser examinar os trabalhos em um estado específico (como novo, enviado, em execução, etc.), clique no link apropriado em **aplicativos**. Você pode clicar mais no nome do trabalho para saber mais sobre o trabalho, incluindo a saída, os logs, etc.

### <a name="access-the-hbase-ui"></a>Acessar a interface do usuário do HBase
No portal do Azure, clique em um nome de cluster HBase do HDInsight para abrir o painel do cluster. No painel cluster, clique em **painel**. Quando solicitado, insira as credenciais de administrador do cluster. Em Ambari, selecione HBase na lista de serviços. Selecione **links rápidos** na parte superior da página, aponte para o link do nó active Zookeeper e clique em interface do usuário do HBase Master.

## <a name="hdinsight-error-codes"></a>Códigos de erro do HDInsight
As mensagens de erro discriminadas nesta seção são fornecidas para ajudar os usuários do Hadoop no Azure HDInsight a entender possíveis condições de erro que podem encontrar ao administrar o serviço usando Azure PowerShell e para avisá-los sobre as etapas que podem ser executadas para se recuperar do erro.

Algumas dessas mensagens de erro também podem ser vistas no portal do Azure quando ele é usado para gerenciar clusters HDInsight. Mas outras mensagens de erro que você pode encontrar são menos granulares devido às restrições nas ações corretivas possíveis nesse contexto. Outras mensagens de erro são fornecidas nos contextos em que a mitigação é óbvia. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Descrição**: Forneça detalhes do banco de dados SQL do Azure para pelo menos um componente a fim de usar configurações personalizadas para metastores do hive e do Oozie.
* **Mitigação**: O usuário precisa fornecer um metastore de SQL Azure válido e tentar a solicitação novamente.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Descrição**: Não foi possível criar o cluster na região *nameOfYourRegion*. Use uma região do HDInsight válida e tente a solicitação novamente.
* **Mitigação**: O cliente deve criar a região de cluster que atualmente dá suporte a eles: Sudeste Asiático, Europa Ocidental, Europa Setentrional, leste dos EUA ou oeste dos EUA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Descrição**: O servidor não pôde localizar o registro de cluster solicitado.  
* **Mitigação**: Repita a operação.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Descrição**: O nome DNS do cluster *seunomedns* é inválido. Verifique se o nome começa e termina com alfanumérico e pode conter apenas o caractere especial '-'  
* **Mitigação**: Certifique-se de que você usou um nome DNS válido para o cluster que inicia e termina com alfanumérico e não contém caracteres especiais além do traço '-' e, em seguida, repita a operação.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Descrição**: O nome do cluster *yourClusterName* não está disponível. Escolha outro nome.  
* **Mitigação**: O usuário deve especificar um ClusterName que seja exclusivo e não exista e tente novamente. Se o usuário estiver usando o portal, a interface de usuário irá notificá-los se um nome de cluster já estiver sendo usado durante as etapas de criação.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Descrição**: A senha do cluster é inválida. A senha deve ter pelo menos 10 caracteres e deve conter pelo menos um número, uma letra maiúscula, uma letra minúscula e um caractere especial sem espaços e não deve conter o nome de usuário como parte dela.  
* **Mitigação**: Forneça uma senha de cluster válida e repita a operação.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Descrição**: O nome de usuário do cluster é inválido. Verifique se o nome de usuário não contém caracteres especiais ou espaços.  
* **Mitigação**: Forneça um nome de usuário de cluster válido e repita a operação.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Descrição**: O nome DNS do cluster *yourDnsClusterName* é inválido. Verifique se o nome começa e termina com alfanumérico e pode conter apenas o caractere especial '-'  
* **Mitigação**: Forneça um nome de usuário de cluster DNS válido e repita a operação.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Descrição**: O nome do contêiner no URI *seuuricontêiner* e o nome DNS *seunomedns* no corpo da solicitação devem ser iguais.  
* **Mitigação**: Verifique se o nome do contêiner e o nome DNS são iguais e tente a operação novamente.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Descrição**: Configuração de cluster inválida. Não é possível localizar nenhuma definição de nó de dados no tamanho do nó.  
* **Mitigação**: Repita a operação.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Descrição**: Falha na exclusão da implantação do cluster  
* **Mitigação**: Repita a operação de exclusão.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Descrição**: Erro de configuração do serviço. Informações de mapeamento DNS necessárias não encontradas.  
* **Mitigação**: Exclua o cluster e crie um novo cluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Descrição**: Tentativa de criação de contêiner de cluster duplicada. O registro existe para *nameOfYourContainer* , mas as ETags não coincidem.
* **Mitigação**: Forneça um nome exclusivo para o contêiner e repita a operação de criação.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Descrição**: O serviço hospedado *nameOfYourHostedService* já contém um cluster. Um serviço hospedado não pode conter vários clusters  
* **Mitigação**: Hospede o cluster em outro serviço hospedado.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Descrição**: O servidor não pôde atualizar o estado da implantação do cluster.  
* **Mitigação**: Repita a operação. Se isso acontecer várias vezes, entre em contato com o CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Descrição**: O *yourClusterName* do cluster foi excluído como parte da manutenção. Recrie o cluster.
* **Mitigação**: Recrie o cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Descrição**: Configuração de cluster inválida. Configuração de nó de cabeçalho necessária não encontrada em tamanhos de nó.
* **Mitigação**: Repita a operação.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Descrição**: Não é possível criar o serviço hospedado *nameOfYourHostedService*. Tente solicitar novamente.  
* **Mitigação**: Repita a solicitação.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Descrição**: O serviço hospedado *nameOfYourHostedService* já tem uma implantação de produção. Um serviço hospedado não pode conter várias implantações de produção. Repita a solicitação com um nome de cluster diferente.
* **Mitigação**: Use um nome de cluster diferente e repita a solicitação.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Descrição**: Não foi possível encontrar o serviço hospedado *nameOfYourHostedService* para o cluster.  
* **Mitigação**: Se o cluster estiver em estado de erro, exclua-o e tente novamente.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Descrição**: O serviço hospedado *nameOfYourHostedService* não tem nenhuma implantação associada.  
* **Mitigação**: Se o cluster estiver em estado de erro, exclua-o e tente novamente.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Descrição**: A SubscriptionId *suaidassinatura* não tem núcleos restantes para criar o cluster *yourClusterName*. Obrigatório: *resourcesRequired*, disponível: *resourcesAvailable*.  
* **Mitigação**: Libere recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Descrição**: A ID da assinatura *suaidassinatura* não tem uma cota para um novo HostedService criar o cluster *yourClusterName*.  
* **Mitigação**: Libere recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Descrição**: O servidor encontrou um erro interno. Tente solicitar novamente.  
* **Mitigação**: Repita a solicitação.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Descrição**: O local de armazenamento do Azure *Dataregionname* não é um local válido. Verifique se a região está correta e repita a solicitação.
* **Mitigação**: Selecione um local de armazenamento que ofereça suporte ao HDInsight, verifique se o cluster está colocalizado e repita a operação.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Descrição**: Tamanho de VM inválido para nós de dados. Somente o tamanho de ' VM grande ' tem suporte para todos os nós de dados.  
* **Mitigação**: Especifique o tamanho do nó com suporte para o nó de dados e repita a operação.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Descrição**: Tamanho de VM inválido para o nó de cabeçalho. Somente o tamanho ' VM ExtraLarge ' tem suporte para o nó de cabeçalho.  
* **Mitigação**: Especifique o tamanho do nó com suporte para o nó principal e repita a operação

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Descrição**: A ID da assinatura *suaidassinatura* que está sendo usada não tem permissões suficientes para executar a operação de exclusão para o cluster *yourClusterName*.  
* **Mitigação**: Se o cluster estiver em estado de erro, remova-o e tente novamente.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Descrição**: O nome do contêiner de BLOB da conta de armazenamento externo *yourContainerName* é inválido. Verifique se o nome começa com uma letra e contém apenas letras minúsculas, números e traços.  
* **Mitigação**: Especifique um nome de contêiner de blob de conta de armazenamento válido e repita a operação.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Descrição**: A configuração da conta de armazenamento externa *seunomecontaarmazenamento* é necessária para que os detalhes da chave secreta sejam definidos.  
* **Mitigação**: Especifique uma chave secreta válida para a conta de armazenamento e repita a operação.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Descrição**: O cabeçalho de versão *yourVersionHeader* não está no formato válido de aaaa-mm-dd.  
* **Mitigação**: Especifique um formato válido para o cabeçalho de versão e repita a solicitação.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Descrição**: Configuração de cluster inválida. Mais de uma configuração de nó de cabeçalho encontrada.  
* **Mitigação**: Edite a configuração para que apenas um nó de cabeçalho seja especificado.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Descrição**: A operação não pôde ser concluída dentro do tempo permitido ou o máximo de tentativas de repetição possível. Tente solicitar novamente.  
* **Mitigação**: Repita a solicitação.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Descrição**: O parâmetro *yourParameterName* não pode ser nulo ou vazio.  
* **Mitigação**: Especifique um valor válido para o parâmetro.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Descrição**: Uma ou mais das entradas de solicitação de criação de cluster não são válidas. Verifique se os valores de entrada estão corretos e repita a solicitação.  
* **Mitigação**: Verifique se os valores de entrada estão corretos e repita a solicitação.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Descrição**: A funcionalidade de região não está disponível para a região *seunomeregião* e a ID de assinatura *suaidassinatura*.  
* **Mitigação**: Especifique uma região que ofereça suporte a clusters HDInsight. As regiões com suporte público são: Sudeste Asiático, Europa Ocidental, Europa Setentrional, leste dos EUA ou oeste dos EUA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Descrição**: A conta de armazenamento *seunomecontaarmazenamento* está na região *nomeregiãoatual*. Deve ser o mesmo que a região de cluster *yourClusterRegionName*.  
* **Mitigação**: Especifique uma conta de armazenamento na mesma região em que seu cluster está ou se os dados já estiverem na conta de armazenamento, crie um novo cluster na mesma região que a conta de armazenamento existente. Se você estiver usando o portal, a interface do usuário notificará sobre esse problema com antecedência.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Descrição**: A ID de assinatura fornecida *suaidassinatura* não está ativa.  
* **Mitigação**: Reative sua assinatura ou obtenha uma nova assinatura válida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Descrição**: A ID de assinatura *suaidassinatura* não pôde ser encontrada.  
* **Mitigação**: Verifique se sua ID de assinatura é válida e repita a operação.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Descrição**: Não é possível resolver *YOURDNSURL*DNS. Verifique se a URL totalmente qualificada para o ponto de extremidade do blob foi fornecida.  
* **Mitigação**: Forneça uma URL de blob válida. A URL deve ser totalmente válida, incluindo a partir de *http://* e terminando em *. com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Descrição**: Não é possível verificar o local do recurso *yourDnsUrl*. Verifique se a URL totalmente qualificada para o ponto de extremidade do blob foi fornecida.  
* **Mitigação**: Forneça uma URL de blob válida. A URL deve ser totalmente válida, incluindo a partir de *http://* e terminando em *. com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Descrição**: A funcionalidade de versão não está disponível para a versão *versãoespecificada* e a ID de assinatura *suaidassinatura*.  
* **Mitigação**: Escolha uma versão que esteja disponível e repita a operação.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Descrição**: Não há suporte para a versão *versãoespecificada* .
* **Mitigação**: Escolha uma versão com suporte e repita a operação.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Descrição**: A versão *versãoespecificada* não está disponível na região do Azure *regiãoespecificada*.  
* **Mitigação**: Escolha uma versão com suporte na região especificada e repita a operação.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Descrição**: Configuração de cluster inválida. A configuração da conta WASB necessária não foi encontrada em contas externas.  
* **Mitigação**: Verifique se a conta existe e se está corretamente especificada na configuração e repita a operação.

## <a name="next-steps"></a>Passos seguintes

* [Habilitar despejos de heap para serviços de Apache Hadoop no HDInsight baseado em Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Manage HDInsight clusters by using the Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight através da IU da Web do Apache Ambari)
