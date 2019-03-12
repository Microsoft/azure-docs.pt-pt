---
title: Computação ambientes suportados pelo Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre os ambientes de computação que pode utilizar nos pipelines da fábrica de dados do Azure (por exemplo, o Azure HDInsight) para dados de transformação ou processo.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0e0a249c53c90d3d8d03dcdb5fbb4f11f31c54df
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545168"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Suportado pelo Azure Data Factory de ambientes de computação
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [serviços ligados de computação](../compute-linked-services.md).

Este artigo explica os ambientes de computação que pode utilizar para processar ou transformar dados. Também fornece detalhes sobre as configurações diferentes (sob demanda versus bring-your-own) que suporta a fábrica de dados quando configurar os serviços ligados que ligação-las computação ambientes para uma fábrica de dados do Azure.

A tabela seguinte fornece uma lista dos ambientes de computação que são suportados pela fábrica de dados e as atividades que podem ser executadas nos mesmos. 

| Ambiente de computação                      | Atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster do Azure HDInsight a pedido](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster do HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Atividades do Machine Learning: Execução de lotes e atualizar recurso](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versões de HDInsight suportadas no Data Factory
O Azure HDInsight oferece suporte a várias versões de cluster de Hadoop que pode implementar em qualquer altura. Cada versão suportada cria uma versão específica da distribuição Hortonworks Data Platform (HDP) e um conjunto de componentes na distribuição. 

A Microsoft atualiza a lista de versões suportadas do HDInsight com os componentes de ecossistema do Hadoop mais recentes e correções. Para obter informações detalhadas, consulte [versões suportadas HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> HDInsight baseado em Linux versão 3.3 foi descontinuado a 31 de Julho de 2017. Versão 1 do Data Factory serviços aos clientes foram dadas até 15 de Dezembro de 2017, para testar e atualizar para uma versão posterior do HDInsight ligado do HDInsight a pedido. HDInsight baseado em Windows vai ser descontinuado a 31 de Julho de 2018.
>
> 

### <a name="after-the-retirement-date"></a>Após a data de extinção 

Após 15 de Dezembro de 2017:

- Já não pode criar o HDInsight baseado em Linux versão 3.3 (ou versões anteriores) serviço no Data Factory versão 1 de ligado de clusters ao utilizar um HDInsight a pedido. 
- Se o [ **osType** e **versão** propriedades](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) não estão explicitamente especificados na definição de JSON para um serviço de ligado de HDInsight a pedido do existente Data Factory versão 1 , o valor predefinido é alterado de **versão = 3.1, osType = Windows** ao **versão =\<mais recente versão de padrão HDI\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType = Linux**.

Após 31 de Julho de 2018:

- Já não pode criar qualquer versão de clusters do HDInsight baseado em Windows com um serviço de ligado de HDInsight a pedido no Data Factory versão 1. 

### <a name="recommended-actions"></a>Ações recomendadas 

- Para garantir que pode usar os componentes de ecossistema do Hadoop e correções mais recentes, atualize o [ **osType** e **versão** propriedades](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) de afetados Data Factory versão 1 a pedido Definições de serviço ligado do HDInsight para versões mais recentes do HDInsight baseado em Linux (HDInsight 3.6). 
- Antes de 15 de Dezembro de 2017, teste o Data Factory versão 1 do Hive, Pig, MapReduce e Hadoop transmissão em fluxo atividades que referenciam o serviço ligado afetado. Certifique-se de que sejam compatíveis com o novo **osType** e **versão** valores predefinidos (**versão = 3.6**, **osType = Linux**) ou o HDInsight versão e sistema operacional explícitos escreva que estiver a atualizar para. 
  Para saber mais sobre a compatibilidade, veja [migrar de um cluster do HDInsight baseado em Windows para um cluster baseado em Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) e [quais são os componentes do Hadoop e versões disponíveis com o HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Para continuar a utilizar um serviço de ligado de HDInsight a pedido do Data Factory versão 1 para criar clusters do HDInsight baseado em Windows, defina explicitamente **osType** ao **Windows** antes de 15 de Dezembro de 2017. Recomendamos que migre para clusters do HDInsight baseado em Linux antes de 31 de Julho de 2018. 
- Se estiver a utilizar um serviço de ligado de HDInsight a pedido para executar a versão 1 do Data Factory atividade personalizada do DotNet, atualização vinculada a definição de JSON de atividade DotNet personalizado para utilizar em vez disso, um Azure Batch service. Para obter mais informações, consulte [utilizar atividades personalizadas num pipeline do Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Se usar já existentes, traga o seu próprio cluster do HDInsight associado dispositivo na versão do Data Factory 1 ou um serviço ligado de HDInsight traga a sua própria e a pedido no Azure Data Factory, nenhuma ação é necessário. Esses cenários, a política de suporte a versão mais recente dos clusters do HDInsight já é imposta. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente de computação a pedido
Uma configuração de sob demanda, o Data Factory gere totalmente o ambiente de computação. Data Factory cria automaticamente o ambiente de computação antes de uma tarefa for submetida para processamento de dados. Quando a tarefa estiver concluída, o Data Factory remove o ambiente de computação. 

Pode criar um serviço ligado para um ambiente de computação a pedido. Utilize o serviço ligado para configurar o ambiente de computação e para controlar as definições granulares para execução da tarefa, gerenciamento de cluster e ações de inicialização.

> [!NOTE]
> Atualmente, a configuração de sob demanda é suportada apenas para clusters do HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço ligado do Azure HDInsight a pedido
Fábrica de dados pode criar automaticamente um cluster do HDInsight a pedido com base no Windows ou baseado em Linux para processamento de dados. O cluster é criado na mesma região que a conta de armazenamento associada ao cluster. Utilizar o JSON **linkedServiceName** propriedade para criar o cluster.

Tenha em atenção o seguinte procedimento *chave* pontos sobre o HDInsight a pedido de serviço ligado:

* O cluster de HDInsight a pedido não aparece na sua subscrição do Azure. O serviço Data Factory gere o cluster de HDInsight a pedido em seu nome.
* Os registos para tarefas que são executados num cluster do HDInsight a pedido são copiados para a conta de armazenamento que está associada ao cluster do HDInsight. Para aceder a estes registos, no portal do Azure, vá para o **detalhes da execução da atividade** painel. Para obter mais informações, consulte [monitorizar e gerir pipelines](data-factory-monitor-manage-pipelines.md).
* É-lhe cobrada apenas o tempo que o cluster do HDInsight está ativo e tarefas em execução.

> [!IMPORTANT]
> Normalmente, demora *20 minutos* ou mais para aprovisionar um cluster do HDInsight a pedido.
>
> 

### <a name="example"></a>Exemplo
O seguinte JSON define um serviço de ligado de HDInsight a pedido baseado em Linux. Data Factory cria automaticamente uma *baseado em Linux* cluster do HDInsight quando processa um setor de dados. 

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
> O cluster do HDInsight cria um *contentor predefinido* no armazenamento de Blobs do Azure que especificar no JSON **linkedServiceName** propriedade. Por predefinição, o HDInsight não elimina este contentor quando o cluster é eliminado. Num serviço ligado de HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processada, a menos que exista um cluster em direto (**timeToLive**). O cluster é eliminado quando o processamento é concluído. 
>
> Como são processados mais setores, verá muitos contentores no armazenamento de Blobs. Se não precisar dos contentores para tarefas de resolução de problemas, poderá eliminar os contentores para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf<your Data Factory name>-<linked service name>-<date and time>`. Pode usar uma ferramenta como o [Explorador de armazenamento do Microsoft](https://storageexplorer.com/) para eliminar contentores no armazenamento de Blobs.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | Defina a propriedade de tipo **HDInsightOnDemand**. | Sim      |
| clusterSize                  | O número de nós de trabalho e de dados do cluster. O cluster do HDInsight é criado com 2 nós principais, para além do número de nós de trabalho que especificou para esta propriedade. Os nós são do tamanho Standard_D3, que tem 4 núcleos. Um cluster de nó de trabalho de 4 usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho, além de 2\*4 = 8 núcleos para nós principais). Para obter detalhes sobre o escalão de Standard_D3, consulte [clusters do Hadoop de baseados em criar Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Sim      |
| timeToLive                   | O tempo de inatividade permitido para o cluster de HDInsight a pedido. Especifica o tempo que o cluster de HDInsight a pedido fique vivo quando uma execução de atividade estiver concluída, se existirem não existem outras tarefas ativas no cluster.<br /><br />Por exemplo, se uma atividade executar demora 6 minutos e **timeToLive** está definido para 5 minutos, o permanece de cluster ativo durante 5 minutos após os seis minutos de execução de atividade de processamento. Se outra execução de atividade é executada na janela de 6 minutos, é processada pelo mesmo cluster.<br /><br />Criar um cluster do HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo). Utilize esta definição conforme necessário para melhorar o desempenho de uma fábrica de dados ao reutilizar um cluster do HDInsight a pedido.<br /><br />Se definir o **timeToLive** valor **0**, o cluster é eliminado assim que a atividade de execução estiver concluída. No entanto, se definir um valor elevado, o cluster poderão permanecer ocioso, desnecessariamente resulta em custos elevados. É importante definir o valor apropriado com base nas suas necessidades.<br /><br />Se o **timeToLive** valor está definido corretamente, vários pipelines podem partilhar a instância do cluster de HDInsight a pedido. | Sim      |
| versão                      | A versão do cluster do HDInsight. Para versões do HDInsight permitidas, consulte [versões suportadas HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Se este valor não for especificado, o [mais recente versão de padrão HDI](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) é utilizado. | Não       |
| linkedServiceName            | O serviço ligado do armazenamento do Azure a ser utilizado pelo cluster sob demanda para armazenar e processar dados. O cluster do HDInsight é criado na mesma região que esta conta de armazenamento.<p>Atualmente, não é possível criar um cluster do HDInsight a pedido que utiliza o Azure Data Lake Store como armazenamento. Se pretende armazenar os dados de resultado do HDInsight de processamento no Data Lake Store, utilize a atividade de cópia para copiar os dados do armazenamento de BLOBs para o Data Lake Store. </p> | Sim      |
| additionalLinkedServiceNames | Especifica as contas de armazenamento adicional para o serviço ligado do HDInsight. Fábrica de dados registra as contas de armazenamento em seu nome. Estas contas de armazenamento tem de ser na mesma região que o cluster do HDInsight. O cluster do HDInsight é criado na mesma região que a conta de armazenamento especificada pelos **linkedServiceName** propriedade. | Não       |
| osType                       | O tipo de sistema operativo. Valores permitidos são **Linux** e **Windows**. Se este valor não for especificado, **Linux** é utilizado.  <br /><br />Recomendamos vivamente utilizar clusters do HDInsight baseado em Linux. A data de retirada para HDInsight no Windows é 31 de Julho de 2018. | Não       |
| hcatalogLinkedServiceName    | O nome do serviço ligado SQL do Azure que aponta para a base de dados do HCatalog. O cluster de HDInsight a pedido é criado ao utilizar a base de dados SQL como o metastore. | Não       |

#### <a name="example-linkedservicenames-json"></a>Exemplo: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriedades avançadas
Para a configuração granular do cluster de HDInsight a pedido, pode especificar as seguintes propriedades:

| Propriedade               | Descrição                              | Necessário |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração do core (core-site) para o cluster de HDInsight a ser criada. | Não       |
| hBaseConfiguration     | Especifica os parâmetros de configuração de HBase (hbase-site) para o cluster do HDInsight. | Não       |
| hdfsConfiguration      | Especifica os parâmetros de configuração do HDFS (site hdfs) para o cluster do HDInsight. | Não       |
| hiveConfiguration      | Especifica os parâmetros de configuração do Hive (site do hive) para o cluster do HDInsight. | Não       |
| mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred-site) para o cluster do HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração de Oozie (oozie-site) para o cluster do HDInsight. | Não       |
| stormConfiguration     | Especifica os parâmetros de configuração do Storm (site de storm) para o cluster do HDInsight. | Não       |
| yarnConfiguration      | Especifica os parâmetros de configuração do YARN (yarn-site) para o cluster do HDInsight. | Não       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo: Configuração de cluster do HDInsight a pedido com propriedades avançadas

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
Para especificar o tamanho de nós principais, dados e ZooKeeper, utilize as seguintes propriedades: 

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Define o tamanho do nó principal. O valor predefinido é **Standard_D3**. Para obter detalhes, consulte [especificar tamanhos de nó](#specify-node-sizes). | Não       |
| dataNodeSize      | Define o tamanho do nó de dados. O valor predefinido é **Standard_D3**. | Não       |
| zookeeperNodeSize | Define o tamanho do nó do ZooKeeper. O valor predefinido é **Standard_D3**. | Não       |

#### <a name="specify-node-sizes"></a>Especificar tamanhos de nó
Para valores de cadeia de caracteres que tem de especificar para as propriedades descritas na secção anterior, consulte [tamanhos de máquinas virtuais](../../virtual-machines/linux/sizes.md). Os valores devem estar em conformidade com os cmdlets e APIs referenciado no [tamanhos de máquinas virtuais](../../virtual-machines/linux/sizes.md). O tamanho de nó de dados de grande (predefinição) tem 7 GB de memória. Isso pode não ser suficiente para o seu cenário. 

Se quiser criar nós principais do tamanho do D4 e nós de trabalho, especifique **Standard_D4** como o valor para o **headNodeSize** e **dataNodeSize** propriedades: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se definir um valor incorreto para estas propriedades, poderá ver a mensagem seguinte:

  Falha ao criar o cluster. Exceção: Não foi possível concluir a criação do cluster operação. A operação falhou com o código "400". À esquerda por trás do Estado do cluster: "Error". Mensagem: 'PreClusterCreationValidationFailure'. 
  
Se vir esta mensagem, certifique-se de que está a utilizar o cmdlet e os nomes de API da tabela no [tamanhos de máquinas virtuais](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Atualmente, o Data Factory não suporta clusters do HDInsight que utilizam o Data Lake Store como armazenamento primário. Utilize o armazenamento do Azure como armazenamento primário para clusters do HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Traga o seu próprio ambiente de computação
Pode registrar um ambiente de computação existente como um serviço ligado no Data Factory. Gerir o ambiente de computação. O serviço Data Factory utiliza o ambiente de computação para executar atividades.

Este tipo de configuração é suportado para os seguintes ambientes de computação:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Servidor SQL de base de dados, armazém de dados SQL do Azure, do SQL do Azure

## <a name="azure-hdinsight-linked-service"></a>Serviço ligado de HDInsight do Azure
Pode criar um serviço ligado do HDInsight para registar o seu próprio cluster do HDInsight com o Data Factory.

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

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | Defina a propriedade de tipo **HDInsight**. | Sim      |
| clusterUri        | O URI do HDInsight cluster.        | Sim      |
| o nome de utilizador          | O nome da conta de utilizador a utilizar para ligar a um cluster do HDInsight existente. | Sim      |
| palavra-passe          | A palavra-passe da conta de utilizador.   | Sim      |
| linkedServiceName | O nome do serviço ligado de armazenamento que se refere-se para o armazenamento de Blob utilizado pelo cluster do HDInsight. <p>Atualmente, não é possível especificar que um Store de Lake dados serviço ligado para esta propriedade. Se o cluster do HDInsight tem acesso ao Data Lake Store, pode aceder a dados no Data Lake Store a partir de scripts do Hive ou Pig. </p> | Sim      |

## <a name="azure-batch-linked-service"></a>Serviço Azure Batch ligado
Pode criar um serviço de Batch ligado para registar um conjunto do Batch de máquinas virtuais (VMs) para uma fábrica de dados. Pode executar atividades personalizadas do Microsoft .NET com o Batch ou HDInsight.

Se estiver a utilizar o serviço Batch:

* Saiba mais sobre [Noções básicas do Azure Batch](../../batch/batch-technical-overview.md).
* Saiba mais sobre o [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet. Utilize este cmdlet para criar uma conta do Batch. Em alternativa, pode criar a conta do Batch com o [portal do Azure](../../batch/batch-account-create-portal.md). Para obter informações detalhadas sobre como utilizar o cmdlet, consulte [utilizar o PowerShell para gerir uma conta do Batch](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Saiba mais sobre o [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet. Utilize este cmdlet para criar um conjunto do Batch.

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

Para o **accountName** propriedade, acrescentar **.\< nome da região\>**  para o nome da sua conta do batch. Por exemplo:

```json
"accountName": "mybatchaccount.eastus"
```

Outra opção é fornecer a **batchUri** ponto final. Por exemplo:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | Defina a propriedade de tipo **AzureBatch**. | Sim      |
| accountName       | O nome da conta do Batch.         | Sim      |
| accessKey         | A chave de acesso da conta do Batch.  | Sim      |
| poolName          | O nome do conjunto de VMs.    | Sim      |
| linkedServiceName | O nome do serviço ligado de armazenamento que está associada esse lote de serviço ligado. Este serviço ligado é utilizado para os ficheiros de testes que são necessários para executar a atividade e para armazenar registos de execução de atividade. | Sim      |

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado do Machine Learning do Azure
Pode criar um serviço ligado do Machine Learning para registar um ponto final de classificação para uma fábrica de dados de lote do Machine Learning.

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

### <a name="properties"></a>Propriedades
| Propriedade   | Descrição                              | Necessário |
| ---------- | ---------------------------------------- | -------- |
| Type       | Defina a propriedade de tipo **AzureML**. | Sim      |
| mlEndpoint | O URL de classificação do lote.                   | Sim      |
| apiKey     | API do modelo de área de trabalho publicado.     | Sim      |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço do Azure Data Lake Analytics ligado
Pode criar um serviço ligado do Data Lake Analytics para ligar um serviço de computação do Data Lake Analytics a um Azure data factory. Este serviço ligado refere-se a atividade de U-SQL do Data Lake Analytics no pipeline. 

A tabela seguinte descreve as propriedades genéricas que são utilizadas na definição de JSON:

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| tipo                 | Defina a propriedade de tipo **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | O nome da conta do Data Lake Analytics.  | Sim                                      |
| dataLakeAnalyticsUri | O URI de Lake análise de dados.           | Não                                       |
| subscriptionId       | O ID de subscrição do Azure.                    | Não<br /><br />(Se não for especificado, a subscrição da fábrica de dados é usada.) |
| resourceGroupName    | O nome do grupo de recursos do Azure.                | Não<br /><br /> (Se não for especificado, o grupo de recursos de fábrica de dados é utilizado.) |

### <a name="authentication-options"></a>Opções de autenticação
Para o seu serviço ligado do Data Lake Analytics, pode escolher entre a autenticação ao utilizar um principal de serviço ou uma credencial de utilizador.

#### <a name="service-principal-authentication-recommended"></a>Autenticação do principal de serviço (recomendada)
Para utilizar autenticação do principal de serviço, registre-se uma entidade de aplicação no Azure Active Directory (Azure AD). Em seguida, conceda acesso ao Azure AD para o Data Lake Store. Para obter passos detalhados, consulte [autenticação serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do inquilino

Utilize autenticação do principal de serviço ao especificar as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | ID de cliente. da aplicação     | Sim      |
| servicePrincipalKey | Chave da aplicação.           | Sim      |
| inquilino              | As informações de inquilino (inquilino ou nome do ID de domínio) em que a sua aplicação esteja localizada. Para obter essas informações, coloque o cursor do rato no canto superior direito do portal do Azure. | Sim      |

**Exemplo: Autenticação do principal de serviço**
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

#### <a name="user-credential-authentication"></a>Autenticação de credenciais de utilizador
Para a autenticação de credenciais de utilizador para o Data Lake Analytics, especifique as seguintes propriedades:

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| Autorização | No Editor do Data Factory, selecione o **autorizar** botão. Introduza as credenciais que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim      |
| sessionId     | O ID de sessão OAuth a partir da sessão de autorização de OAuth. Cada ID de sessão é exclusivo e pode ser usada apenas uma vez. Esta definição é gerada automaticamente ao utilizar o Editor do Data Factory. | Sim      |

**Exemplo: Autenticação de credenciais de utilizador**
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
O código de autorização que gerou selecionando o **autorizar** botão expira após um intervalo definido. 

Poderá ver a seguinte mensagem de erro quando o token de autenticação expira: 

  Erro de operação de credenciais: invalid_grant - Error":"invalid_grant","error_description":"aadsts70002: Credenciais de validação de erro. AADSTS70008: A concessão de acesso fornecido está expirada ou revogada. ID de rastreio: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

A tabela seguinte mostra as expirações por tipo de conta de utilizador: 

| Tipo de utilizador                                | Expira após                            |
| :--------------------------------------- | :--------------------------------------- |
| Contas de utilizador que são *não* gerida pelo Azure AD (Hotmail, em direto etc.) | 12 horas.                                 |
| Contas de usuário *são* gerida pelo Azure AD | Execute 14 dias após o último setor. <br /><br />90 dias, se um setor que se baseia num serviço ligado com base em OAuth é executado, pelo menos, uma vez a cada 14 dias. |

Para evitar ou resolver este erro, autorizar, selecionando o **autorizar** botão quando o token expirar. Em seguida, volte a implementar o serviço ligado. Também pode gerar valores para o **sessionId** e **autorização** propriedades por meio de programação, utilizando o seguinte código:

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

Para obter detalhes sobre as classes de fábrica de dados que são utilizadas neste exemplo de código, consulte:
* [Classe de AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Classe de AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Classe de AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Adicionar uma referência ao Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para o **WindowsFormsWebAuthenticationDialog** classe. 

## <a name="azure-sql-linked-service"></a>Serviço ligado SQL do Azure
Pode criar um serviço ligado SQL e utilizá-lo com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory. Para obter mais informações, consulte [conector do Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço ligado do SQL Data Warehouse do Azure
Pode criar um serviço ligado do SQL Data Warehouse e utilizá-lo com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory. Para obter mais informações, consulte [conector do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Serviço ligado do SQL Server
Pode criar um serviço ligado do SQL Server e utilizá-lo com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory. Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

