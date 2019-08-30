---
title: Ambientes de computação com suporte pelo Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os ambientes de computação que você pode usar em Azure Data Factory pipelines (como o Azure HDInsight) para transformar ou processar dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 12d12e96616d94360e5d193cf2b778a9ae389062
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140251"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambientes de computação com suporte pelo Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [Serviços vinculados de computação no](../compute-linked-services.md).

Este artigo explica os ambientes de computação que você pode usar para processar ou transformar dados. Ele também fornece detalhes sobre configurações diferentes (sob demanda versus traga sua própria) que Data Factory dá suporte ao configurar serviços vinculados que vinculam esses ambientes de computação a uma data factory do Azure.

A tabela a seguir fornece uma lista de ambientes de computação com suporte pelo Data Factory e as atividades que podem ser executadas neles. 

| Ambiente de computação                      | Atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster do Azure Hdinsight sob demanda](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster hdinsight](#azure-hdinsight-linked-service) | [Dotnet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streaming do Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning atividades: Recurso de atualização e execução em lote](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versões do HDInsight com suporte no Data Factory
O Azure HDInsight dá suporte a várias versões de cluster Hadoop que você pode implantar a qualquer momento. Cada versão com suporte cria uma versão específica da distribuição HDP (Hortonworks Data Platform) e um conjunto de componentes na distribuição. 

A Microsoft atualiza a lista de versões do HDInsight com suporte com os componentes e correções mais recentes do ecossistema do Hadoop. Para obter informações detalhadas, consulte [versões do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> A versão 3,3 do HDInsight baseada em Linux foi desativada em 31 de julho de 2017. Os clientes de serviços vinculados do HDInsight de Data Factory versão 1 sob demanda foram fornecidos até 15 de dezembro de 2017, para testar e atualizar para uma versão posterior do HDInsight. O HDInsight baseado no Windows será desativado em 31 de julho de 2018.
>
> 

### <a name="after-the-retirement-date"></a>Após a data de desativação 

Após 15 de dezembro de 2017:

- Você não pode mais criar clusters do HDInsight com base em Linux versão 3,3 (ou versões anteriores) usando um serviço vinculado do HDInsight sob demanda no Data Factory versão 1. 
- Se as [Propriedades **OsType** e **version** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) não forem especificadas explicitamente na definição JSON para um serviço vinculado HDInsight existente de data Factory versão 1 sob demanda, o valor padrão será alterado de **version = 3.1, osType = Windows**  **versão =\<versão https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) padrão mais recente do HDI (, osType = Linux.\>**

Após 31 de julho de 2018:

- Você não pode mais criar qualquer versão de clusters HDInsight baseados no Windows usando um serviço vinculado do HDInsight sob demanda no Data Factory versão 1. 

### <a name="recommended-actions"></a>Ações recomendadas 

- Para garantir que você possa usar os componentes e correções mais recentes do ecossistema do Hadoop, atualize as propriedades de [ **OsType** e **versão** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) das definições de serviço vinculado do hdinsight data Factory versão 1 sob demanda para o hdinsight mais recente baseado em Linux versões (HDInsight 3,6). 
- Antes de 15 de dezembro de 2017, teste Data Factory versão 1 Hive, Pig, MapReduce e atividades de streaming do Hadoop que fazem referência ao serviço vinculado afetado. Verifique se eles são compatíveis com os novos valores padrão de **osType** e **versão** (**version = 3.6**, **osType = Linux**) ou a versão explícita do HDInsight e o tipo de so para o qual você está atualizando. 
  Para saber mais sobre compatibilidade, consulte [migrar de um cluster HDInsight baseado em Windows para um cluster baseado em Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) e [quais são os componentes e versões do Hadoop disponíveis com o HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Para continuar usando um serviço vinculado do HDInsight sob demanda Data Factory versão 1 para criar clusters HDInsight baseados no Windows, defina explicitamente **osType** como **Windows** antes de 15 de dezembro de 2017. Recomendamos que você migre para clusters HDInsight baseados em Linux antes de 31 de julho de 2018. 
- Se você estiver usando um serviço vinculado do HDInsight sob demanda para executar Data Factory atividade personalizada DotNet versão 1, atualize a definição de JSON da atividade personalizada DotNet para usar um serviço vinculado do lote do Azure. Para obter mais informações, consulte [usar atividades personalizadas em um pipeline de data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Se você usar o seu próprio dispositivo vinculado HDInsight do cluster existente no Data Factory versão 1 ou um serviço vinculado do HDInsight traga seu próprio e sob demanda no Azure Data Factory, nenhuma ação será necessária. Nesses cenários, a política de suporte de versão mais recente dos clusters HDInsight já está imposta. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente de computação sob demanda
Em uma configuração sob demanda, Data Factory gerencia totalmente o ambiente de computação. Data Factory cria automaticamente o ambiente de computação antes de um trabalho ser enviado para o processamento de dados. Quando o trabalho for concluído, Data Factory removerá o ambiente de computação. 

Você pode criar um serviço vinculado para um ambiente de computação sob demanda. Use o serviço vinculado para configurar o ambiente de computação e para controlar as configurações granulares para execução de trabalho, gerenciamento de cluster e ações de inicialização.

> [!NOTE]
> Atualmente, a configuração sob demanda tem suporte apenas para clusters HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço vinculado sob demanda do Azure HDInsight
Data Factory pode criar automaticamente um cluster HDInsight sob demanda baseado em Windows ou Linux para processar dados. O cluster é criado na mesma região que a conta de armazenamento associada ao cluster. Use a propriedade JSON **linkedServiceName** para criar o cluster.

Observe os seguintes pontos *principais* sobre o serviço vinculado do HDInsight sob demanda:

* O cluster HDInsight sob demanda não aparece na sua assinatura do Azure. O serviço de Data Factory gerencia o cluster HDInsight sob demanda em seu nome.
* Os logs de trabalhos que são executados em um cluster HDInsight sob demanda são copiados para a conta de armazenamento associada ao cluster HDInsight. Para acessar esses logs, na portal do Azure, vá para o painel **detalhes da execução da atividade** . Para obter mais informações, consulte [monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).
* Você é cobrado apenas pelo tempo em que o cluster HDInsight está funcionando e executando trabalhos.

> [!IMPORTANT]
> Normalmente, leva *20 minutos* ou mais para provisionar um cluster HDInsight sob demanda.
>
> 

### <a name="example"></a>Exemplo
O JSON a seguir define um serviço vinculado do HDInsight sob demanda baseado em Linux. Data Factory cria automaticamente um cluster HDInsight *baseado em Linux* quando processa uma fatia de dados. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> O cluster HDInsight cria um *contêiner padrão* no armazenamento de BLOBs do Azure que você especifica na propriedade JSON **linkedServiceName** . Por design, o HDInsight não exclui esse contêiner quando o cluster é excluído. Em um serviço vinculado do HDInsight sob demanda, um cluster HDInsight é criado toda vez que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (**TimeToLive**). O cluster é excluído quando o processamento é concluído. 
>
> À medida que mais fatias forem processadas, você verá muitos contêineres em seu armazenamento de BLOBs. Se você não precisar dos contêineres para a solução de problemas de trabalhos, talvez queira excluir os contêineres para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf<your Data Factory name>-<linked service name>-<date and time>`. Você pode usar uma ferramenta como [o Microsoft Gerenciador de armazenamento](https://storageexplorer.com/) para excluir contêineres no armazenamento de BLOBs.
>
> 

### <a name="properties"></a>properties
| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Defina a propriedade Type como **HDInsightOnDemand**. | Sim      |
| clusterSize                  | O número de nós de trabalho e de dados no cluster. O cluster HDInsight é criado com dois nós de cabeçalho, além do número de nós de trabalho que você especifica para essa propriedade. Os nós são do tamanho Standard_D3, que tem 4 núcleos. Um cluster de nós de 4 trabalhadores leva 24 núcleos (\*4 4 = 16 núcleos para nós de trabalho,\*mais 2 4 = 8 núcleos para nós de cabeçalho). Para obter detalhes sobre a camada Standard_D3, consulte [Criar clusters Hadoop baseados em Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Sim      |
| timeToLive                   | O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica por quanto tempo o cluster HDInsight sob demanda permanece ativo quando uma execução de atividade é concluída, se não houver nenhum outro trabalho ativo no cluster.<br /><br />Por exemplo, se uma execução de atividade demorar 6 minutos e **TimeToLive** for definido como 5 minutos, o cluster permanecerá ativo por 5 minutos após os 6 minutos de processamento da execução da atividade. Se outra execução de atividade for executada na janela de 6 minutos, ela será processada pelo mesmo cluster.<br /><br />A criação de um cluster HDInsight sob demanda é uma operação cara (pode levar algum tempo). Use essa configuração conforme necessário para melhorar o desempenho de um data factory reutilizando um cluster HDInsight sob demanda.<br /><br />Se você definir o valor **TimeToLive** como **0**, o cluster será excluído assim que a execução da atividade for concluída. No entanto, se você definir um valor alto, o cluster poderá permanecer ocioso, o que resulta em custos altos. É importante definir o valor apropriado com base em suas necessidades.<br /><br />Se o valor **TimeToLive** estiver definido adequadamente, vários pipelines poderão compartilhar a instância do cluster HDInsight sob demanda. | Sim      |
| version                      | A versão do cluster HDInsight. Para versões do HDInsight permitidas, consulte [versões do Hdinsight com suporte](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Se esse valor não for especificado, a [versão padrão mais recente do HDI](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) será usada. | Não       |
| linkedServiceName            | O serviço vinculado do armazenamento do Azure a ser usado pelo cluster sob demanda para armazenar e processar dados. O cluster HDInsight é criado na mesma região que essa conta de armazenamento.<p>No momento, não é possível criar um cluster HDInsight sob demanda que usa Azure Data Lake Store como armazenamento. Se você quiser armazenar os dados de resultado do processamento do HDInsight no Data Lake Store, use a atividade de cópia para copiar os dados do armazenamento de BLOBs para Data Lake Store. </p> | Sim      |
| additionalLinkedServiceNames | Especifica contas de armazenamento adicionais para o serviço vinculado do HDInsight. Data Factory registra as contas de armazenamento em seu nome. Essas contas de armazenamento devem estar na mesma região que o cluster HDInsight. O cluster HDInsight é criado na mesma região que a conta de armazenamento especificada pela propriedade **linkedServiceName** . | Não       |
| osType                       | O tipo de sistema operacional. Os valores permitidos são **Linux** e **Windows**. Se esse valor não for especificado, o **Linux** será usado.  <br /><br />É altamente recomendável usar clusters HDInsight baseados em Linux. A data de aposentadoria do HDInsight no Windows é 31 de julho de 2018. | Não       |
| hcatalogLinkedServiceName    | O nome do serviço vinculado do SQL do Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda é criado usando o banco de dados SQL como o metastore. | Não       |

#### <a name="example-linkedservicenames-json"></a>Exemplo: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriedades avançadas
Para a configuração granular do cluster HDInsight sob demanda, você pode especificar as seguintes propriedades:

| Propriedade               | Descrição                              | Requerido |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração principais (Core-site. xml) para o cluster HDInsight a ser criado. | Não       |
| hBaseConfiguration     | Especifica os parâmetros de configuração do HBase (HBase-site. xml) para o cluster HDInsight. | Não       |
| hdfsConfiguration      | Especifica os parâmetros de configuração do HDFS (HDFS-site. xml) para o cluster HDInsight. | Não       |
| hiveConfiguration      | Especifica os parâmetros de configuração do hive (Hive-site. xml) para o cluster HDInsight. | Não       |
| mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred-site. xml) para o cluster HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração do Oozie (oozie-site. xml) para o cluster HDInsight. | Não       |
| stormConfiguration     | Especifica os parâmetros de configuração do Storm (Storm-site. xml) para o cluster HDInsight. | Não       |
| yarnConfiguration      | Especifica os parâmetros de configuração do YARN (yarn-site. xml) para o cluster HDInsight. | Não       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo: Configuração de cluster HDInsight sob demanda com propriedades avançadas

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Tamanhos de nó
Para especificar o tamanho dos nós de cabeçalho, dados e ZooKeeper, use as seguintes propriedades: 

| Propriedade          | Descrição                              | Requerido |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Define o tamanho do nó principal. O valor padrão é **Standard_D3**. Para obter detalhes, consulte [especificar tamanhos de nó](#specify-node-sizes). | Não       |
| dataNodeSize      | Define o tamanho do nó de dados. O valor padrão é **Standard_D3**. | Não       |
| zookeeperNodeSize | Define o tamanho do nó ZooKeeper. O valor padrão é **Standard_D3**. | Não       |

#### <a name="specify-node-sizes"></a>Especificar tamanhos de nó
Para valores de cadeia de caracteres que você deve especificar para as propriedades descritas na seção anterior, consulte [tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md). Os valores devem estar em conformidade com os cmdlets e APIs referenciados em [tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md). O tamanho do nó de dados grande (padrão) tem 7 GB de memória. Isso pode não ser suficiente para seu cenário. 

Se você quiser criar nós de cabeçalho de tamanho D4 e nós de trabalho, especifique **Standard_D4** como o valor para as propriedades **das** e datanodes: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se você definir um valor incorreto para essas propriedades, poderá ver a seguinte mensagem:

  Falha ao criar o cluster. Exception Não é possível concluir a operação de criação do cluster. A operação falhou com o código "400". Cluster deixado para trás do Estado: ' Erro '. Mensagem: 'PreClusterCreationValidationFailure'. 
  
Se você vir essa mensagem, verifique se está usando os nomes de cmdlet e API da tabela em [tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Atualmente, Data Factory não dá suporte a clusters HDInsight que usam Data Lake Store como armazenamento primário. Use o armazenamento do Azure como o repositório primário para clusters HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Traga seu próprio ambiente de computação
Você pode registrar um ambiente de computação existente como um serviço vinculado no Data Factory. Você gerencia o ambiente de computação. O serviço de Data Factory usa o ambiente de computação para executar atividades.

Esse tipo de configuração tem suporte para os seguintes ambientes de computação:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Banco de dados SQL do Azure, SQL Data Warehouse do Azure, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço vinculado do Azure HDInsight
Você pode criar um serviço vinculado do HDInsight para registrar seu próprio cluster HDInsight com Data Factory.

### <a name="example"></a>Exemplo

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>properties
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| type              | Defina a propriedade Type como **HDInsight**. | Sim      |
| clusterUri        | O URI do cluster HDInsight.        | Sim      |
| username          | O nome da conta de usuário a ser usada para se conectar a um cluster HDInsight existente. | Sim      |
| password          | A senha da conta de usuário.   | Sim      |
| linkedServiceName | O nome do serviço vinculado de armazenamento que se refere ao armazenamento de blob usado pelo cluster HDInsight. <p>No momento, você não pode especificar um serviço vinculado Data Lake Store para essa propriedade. Se o cluster HDInsight tiver acesso ao Data Lake Store, você poderá acessar dados em Data Lake Store de scripts Hive ou Pig. </p> | Sim      |

## <a name="azure-batch-linked-service"></a>Serviço vinculado do lote do Azure
Você pode criar um serviço vinculado do lote para registrar um pool do lote de VMs (máquinas virtuais) em um data factory. Você pode executar Microsoft .NET atividades personalizadas usando o lote ou o HDInsight.

Se você não estiver familiarizado com o uso do serviço de lote:

* Saiba mais sobre as [noções básicas do lote do Azure](../../batch/batch-technical-overview.md).
* Saiba mais sobre o cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) . Use este cmdlet para criar uma conta do lote. Ou, você pode criar a conta do lote usando o [portal do Azure](../../batch/batch-account-create-portal.md). Para obter informações detalhadas sobre como usar o cmdlet, consulte [usando o PowerShell para gerenciar uma conta do lote](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Saiba mais sobre o cmdlet [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) . Use este cmdlet para criar um pool do lote.

### <a name="example"></a>Exemplo

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Para a propriedade **accountName** , acrescente **.\< nome\> da região** para o nome da sua conta do lote. Por exemplo:

```json
"accountName": "mybatchaccount.eastus"
```

Outra opção é fornecer o ponto de extremidade **batchUri** . Por exemplo:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>properties
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| type              | Defina a propriedade Type como **AzureBatch**. | Sim      |
| accountName       | O nome da conta do lote.         | Sim      |
| accessKey         | A chave de acesso para a conta do lote.  | Sim      |
| poolName          | O nome do pool de VMs.    | Sim      |
| linkedServiceName | O nome do serviço vinculado de armazenamento associado a este serviço vinculado do lote. Esse serviço vinculado é usado para arquivos de preparação que são necessários para executar a atividade e para armazenar logs de execução de atividade. | Sim      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning serviço vinculado
Você pode criar um serviço vinculado Machine Learning para registrar um ponto de extremidade de Pontuação de Machine Learning em um data factory.

### <a name="example"></a>Exemplo

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>properties
| Propriedade   | Descrição                              | Requerido |
| ---------- | ---------------------------------------- | -------- |
| Type       | Defina a propriedade Type como **AzureML**. | Sim      |
| mlEndpoint | A URL de Pontuação do lote.                   | Sim      |
| apiKey     | A API do modelo do espaço de trabalho publicado.     | Sim      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics serviço vinculado
Você pode criar um serviço vinculado Data Lake Analytics para vincular um serviço de computação Data Lake Analytics a uma data factory do Azure. O Data Lake Analytics atividade U-SQL no pipeline refere-se a esse serviço vinculado. 

A tabela a seguir descreve as propriedades genéricas que são usadas na definição de JSON:

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Defina a propriedade Type como **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | O nome da conta de Data Lake Analytics.  | Sim                                      |
| dataLakeAnalyticsUri | O URI de Data Lake Analytics.           | Não                                       |
| subscriptionId       | A ID da assinatura do Azure.                    | Não<br /><br />(Se não for especificado, a assinatura data factory será usada.) |
| resourceGroupName    | O nome do grupo de recursos do Azure.                | Não<br /><br /> (Se não for especificado, o grupo de recursos data factory será usado.) |

### <a name="authentication-options"></a>Opções de autenticação
Para o serviço vinculado do Data Lake Analytics, você pode escolher entre a autenticação usando uma entidade de serviço ou uma credencial do usuário.

#### <a name="service-principal-authentication-recommended"></a>Autenticação de entidade de serviço (recomendada)
Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (AD do Azure). Em seguida, conceda acesso ao Azure AD para Data Lake Store. Para obter passos detalhados, consulte [autenticação serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do inquilino

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | A ID do cliente do aplicativo.     | Sim      |
| servicePrincipalKey | A chave do aplicativo.           | Sim      |
| tenant              | As informações de locatário (nome de domínio ou ID de locatário) em que seu aplicativo está localizado. Para obter essas informações, passe o mouse no canto superior direito do portal do Azure. | Sim      |

**Exemplo: Autenticação de entidade de serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Autenticação de credenciais de usuário
Para autenticação de credenciais de usuário para Data Lake Analytics, especifique as seguintes propriedades:

| Propriedade          | Descrição                              | Requerido |
| :---------------- | :--------------------------------------- | :------- |
| authorization | No editor de Data Factory, selecione o botão autorizar. Insira a credencial que atribui a URL de autorização gerada automaticamente a essa propriedade. | Sim      |
| sessionId     | A ID da sessão OAuth da sessão de autorização do OAuth. Cada ID de sessão é exclusiva e pode ser usada apenas uma vez. Essa configuração é gerada automaticamente quando você usa o editor de Data Factory. | Sim      |

**Exemplo: Autenticação de credenciais de usuário**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização que você gerou selecionando o botão autorizar expira após um intervalo definido. 

Você poderá ver a seguinte mensagem de erro quando o token de autenticação expirar: 

  Erro de operação de credencial: invalid_grant-AADSTS70002: Erro ao validar as credenciais. AADSTS70008: A concessão de acesso fornecida expirou ou foi revogada. ID de rastreamento: ID de correlação de d18629e8-af88-43c5-88e3-d8419eb1fca1: carimbo de data/hora fac30a0c-6be6-4e02-8D69-a776d2ffefd7: 2015-12-15 21:09:31Z

A tabela a seguir mostra expirações por tipo de conta de usuário: 

| Tipo de utilizador                                | Expira após                            |
| :--------------------------------------- | :--------------------------------------- |
| Contas de usuário que *não* são gerenciadas pelo Azure AD (hotmail, Live e assim por diante) | 12 horas.                                 |
| Contas de usuário que *são* gerenciadas pelo Azure AD | 14 dias após a última execução da fatia. <br /><br />90 dias, se uma fatia com base em um serviço vinculado baseado em OAuth for executada pelo menos uma vez a cada 14 dias. |

Para evitar ou resolver esse erro, reautorize selecionando o botão **autorizar** quando o token expirar. Em seguida, reimplante o serviço vinculado. Você também pode gerar valores para as propriedades **SessionID** e **Authorization** programaticamente usando o seguinte código:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Para obter detalhes sobre as classes de Data Factory usadas neste exemplo de código, consulte:
* [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Adicione uma referência a Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms. dll para a classe **WindowsFormsWebAuthenticationDialog** . 

## <a name="azure-sql-linked-service"></a>Serviço vinculado do SQL do Azure
Você pode criar um serviço vinculado do SQL e usá-lo com a [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline data Factory. Para obter mais informações, consulte [conector do SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço vinculado do Azure SQL Data Warehouse
Você pode criar um SQL Data Warehouse serviço vinculado e usá-lo com a [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline de data Factory. Para obter mais informações, consulte [conector de SQL data warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>SQL Server serviço vinculado
Você pode criar um SQL Server serviço vinculado e usá-lo com a [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline de data Factory. Para obter mais informações, consulte [conector de SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

