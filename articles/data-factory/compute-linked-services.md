---
title: Computação ambientes suportados pelo Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre os ambientes de computação que pode utilizar nos pipelines da fábrica de dados do Azure (por exemplo, o Azure HDInsight) para dados de transformação ou processo.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: douglasl
ms.openlocfilehash: 490f11b4a35bb7e5669ccf1554c3a73f5156f3c7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445658"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Suportado pelo Azure Data Factory de ambientes de computação
Este artigo explica os diferentes ambientes de computação que pode utilizar para processar ou transformar dados. Ele também fornece detalhes sobre as configurações diferentes (sob demanda versus traga seu próprio) suportados pelo Data Factory, quando configurar os serviços ligados de ligação estes ambientes de uma fábrica de dados do Azure de computação.

A tabela seguinte fornece uma lista dos ambientes de computação suportados pela fábrica de dados e as atividades que podem ser executadas nos mesmos. 

| Ambiente de computação                                          | atividades                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Cluster de HDInsight a pedido](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster do HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Personalizado](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Atividades do Machine Learning: Execução de lotes e atualizar recurso](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Procedimento Armazenado](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-hdinsight-compute-environment"></a>Ambiente de computação do HDInsight a pedido
Este tipo de configuração, o ambiente de computação totalmente gerido pelo serviço do Azure Data Factory. Ele é criado automaticamente pelo serviço Data Factory antes de uma tarefa é submetida para processar dados e removida quando a tarefa estiver concluída. Pode criar um serviço ligado para o ambiente de computação a pedido, configurá-lo e controlar as definições granulares para execução da tarefa, gerenciamento de cluster e ações de inicialização.

> [!NOTE]
> A configuração de sob demanda é atualmente suportada apenas para clusters do HDInsight do Azure. O Azure Databricks também suporta tarefas a pedido com clusters de tarefa, consulte [serviço ligado do Azure databricks](#azure-databricks-linked-service) para obter mais detalhes.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço ligado do Azure HDInsight a pedido
O serviço Azure Data Factory pode criar automaticamente um cluster do HDInsight a pedido para processar dados. O cluster é criado na mesma região que a conta de armazenamento (linkedServiceName propriedade no JSON) associada ao cluster. A conta de armazenamento tem de ser uma conta de armazenamento do Azure standard para fins gerais. 

Tenha em atenção o seguinte procedimento **importante** pontos sobre o HDInsight a pedido de serviço ligado:

* O cluster de HDInsight a pedido é criado na sua subscrição do Azure. É capaz de ver o cluster no portal do Azure quando o cluster está ativo e em execução. 
* Os registos para tarefas que são executados num cluster do HDInsight a pedido são copiados para a conta de armazenamento associada ao cluster do HDInsight. O clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definido na sua definição de serviço ligado são utilizados para iniciar sessão para o cluster para resolução de problemas detalhada durante o ciclo de vida do cluster. 
* É cobrado apenas para o tempo quando o cluster de HDInsight está ativo e tarefas em execução.
* Pode utilizar um **ação de Script** com a Azure HDInsight a pedido de serviço ligado.  

> [!IMPORTANT]
> Normalmente, demora **20 minutos** ou mais para aprovisionar um cluster do Azure HDInsight a pedido.

### <a name="example"></a>Exemplo
O seguinte JSON define um serviço de ligado de HDInsight a pedido baseado em Linux. O serviço Data Factory cria automaticamente uma **baseado em Linux** cluster do HDInsight para processar a atividade necessária. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento. 
>
> Conforme mais atividade é executada, verá muitos contentores no armazenamento de Blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | A propriedade de tipo deve ser definida como **HDInsightOnDemand**. | Sim      |
| clusterSize                  | Número de nós de trabalho/dados no cluster. O cluster do HDInsight é criado com 2 nós principais, juntamente com o número de nós de trabalho que especificou para esta propriedade. Os nós são do tamanho Standard_D3 com 4 núcleos, para que um cluster de nó de 4 trabalho usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho, além de 2\*4 = 8 núcleos para nós principais). Ver [configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes. | Sim      |
| linkedServiceName            | Serviço ligado do armazenamento do Azure a ser utilizado pelo cluster sob demanda para armazenar e processar dados. O cluster do HDInsight é criado na mesma região que esta conta de armazenamento do Azure. O Azure HDInsight tem limitação do número total de núcleos que pode utilizar em cada região do Azure que suporta. Certifique-se de que tem suficiente de quotas de núcleo nessa região do Azure para satisfazer o clusterSize necessária. Para obter detalhes, consulte [configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Atualmente, não é possível criar um cluster do HDInsight a pedido que utiliza um Store do Azure Data Lake como o armazenamento. Se pretende armazenar os dados de resultado do processamento num Store do Azure Data Lake do HDInsight, utilize uma atividade de cópia para copiar os dados do armazenamento de Blobs do Azure para o Store do Azure Data Lake. </p> | Sim      |
| clusterResourceGroup         | O cluster do HDInsight é criado neste grupo de recursos. | Sim      |
| TimeToLive                   | O tempo de inatividade permitido para o cluster de HDInsight a pedido. Especifica o tempo que o cluster de HDInsight a pedido fique vivo após a conclusão de uma atividade executar se não existirem não existem outras tarefas ativas no cluster. O valor permitido mínimo é de 5 minutos (00: 05:00).<br/><br/>Por exemplo, se uma execução de atividade demora 6 minutos e timetolive é definido para 5 minutos, o cluster fique vivo durante 5 minutos após a execução de seis minutos de atividade de processamento. Se outra execução de atividade é executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>Criar um cluster do HDInsight a pedido é uma operação dispendiosa (podem demorar algum tempo), por isso, utilize esta definição como necessário para melhorar o desempenho de uma fábrica de dados ao reutilizar um cluster do HDInsight a pedido.<br/><br/>Se definir o valor de timetolive para 0, o cluster é eliminado assim que a execução de atividade for concluída. Ao passo que, se definir um valor elevado, o cluster pode permanecer inativo para que possa iniciar sessão para uma solução de problemas finalidade, mas ele poderia resultar em custos elevados. Por conseguinte, é importante que defina o valor apropriado com base nas suas necessidades.<br/><br/>Se o valor da propriedade timetolive adequadamente estiver definido, vários pipelines podem partilhar a instância do cluster de HDInsight a pedido. | Sim      |
| clusterType                  | O tipo de cluster de HDInsight a ser criada. Valores permitidos são "hadoop" e "spark". Se não for especificado, o valor predefinido é hadoop. Enterprise Security Package cluster ativado não é possível criar a pedido, em vez disso, utilize um [existente cluster / traga seu próprio computação](#azure-hdinsight-linked-service). | Não       |
| versão                      | Versão do cluster do HDInsight. Se não for especificado, está a utilizar a versão atual do padrão definido do HDInsight. | Não       |
| hostSubscriptionId           | O ID de subscrição do Azure utilizado para criar cluster do HDInsight. Se não for especificado, ele usa o ID de subscrição de seu contexto de início de sessão do Azure. | Não       |
| clusterNamePrefix           | O prefixo do nome do cluster do HDI, um timestamp será automaticamente anexado no final do nome do cluster| Não       |
| sparkVersion                 | A versão do spark, se o tipo de cluster é o "Spark" | Não       |
| additionalLinkedServiceNames | Especifica o serviço de ligado de contas de armazenamento adicional para o HDInsight, para que o serviço Data Factory pode registá-los em seu nome. Estas contas de armazenamento tem de ser na mesma região que o cluster do HDInsight, o que é criado na mesma região que a conta de armazenamento especificada pelo Nomedoserviçoligado. | Não       |
| osType                       | Tipo de sistema operativo. Valores permitidos são: Linux e Windows (para o HDInsight 3.3 apenas). A predefinição é o Linux. | Não       |
| hcatalogLinkedServiceName    | O nome de SQL do Azure vinculada serviço que apontam para a base de dados do HCatalog. O cluster de HDInsight a pedido é criado ao utilizar a base de dados SQL do Azure como o metastore. | Não       |
| connectVia                   | O Runtime de integração a ser utilizado para distribuir as atividades para este serviço ligado do HDInsight. Para o serviço de ligado de HDInsight a pedido, só suporta o Runtime de integração do Azure. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não       |
| clusterUserName                   | O nome de utilizador para aceder ao cluster. | Não       |
| clusterPassword                   | A palavra-passe no tipo de cadeia segura para aceder ao cluster. | Não       |
| clusterSshUserName         | O nome de utilizador SSH ligar remotamente ao nó do cluster (para Linux). | Não       |
| clusterSshPassword         | A palavra-passe no tipo de cadeia segura para encaminhar o SSH ligar remotamente o nó do cluster (para Linux). | Não       |
| scriptActions | Especifique o script para [personalizações de cluster do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) durante a criação de clusters a pedido. <br />Atualmente, a ferramenta de criação de Interface do usuário do Azure Data Factory suporta a especificação de ação de script apenas 1, mas pode obter através desta limitação no JSON (especificar várias ações de script no JSON). | Não |


> [!IMPORTANT]
> HDInsight oferece suporte a várias versões de cluster de Hadoop que podem ser implementadas. Cada opção de versão cria uma versão específica da distribuição Hortonworks Data Platform (HDP) e um conjunto de componentes que estão contidos dentro dessa distribuição. A lista de versões suportadas do HDInsight continua a ser atualizado para fornecer mais recente componentes de ecossistema do Hadoop e correções. Certifique-se de que sempre consultar informações mais recentes do [suportado HDInsight versão e o tipo de SO](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para garantir que está a utilizar uma versão suportada do HDInsight. 
>
> [!IMPORTANT]
> Atualmente, os serviços ligados HDInsight não suporta o HBase, Interactive Query (LLAP do Hive), o Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>exemplo de JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

O serviço de HDInsight a pedido ligada requer uma autenticação do principal de serviço para criar clusters do HDInsight em seu nome. Para utilizar a autenticação do principal de serviço, registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) e conceda-o **contribuinte** função da subscrição ou o grupo de recursos no qual o cluster do HDInsight é criado. Para obter passos detalhados, consulte [utilize o portal para criar um Azure Active Directory principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

- ID da aplicação
- Chave da aplicação 
- ID do inquilino

Utilize autenticação do principal de serviço ao especificar as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique o ID de cliente. da aplicação     | Sim      |
| **servicePrincipalKey** | Especifique a chave da aplicação.           | Sim      |
| **tenant**              | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim      |

### <a name="advanced-properties"></a>Propriedades avançadas

Também pode especificar as seguintes propriedades para a configuração granular do cluster de HDInsight a pedido.

| Propriedade               | Descrição                              | Necessário |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração do núcleo (como no core-site) para o cluster de HDInsight a ser criada. | Não       |
| hBaseConfiguration     | Especifica os parâmetros de configuração de HBase (hbase-site) para o cluster do HDInsight. | Não       |
| hdfsConfiguration      | Especifica os parâmetros de configuração do HDFS (site hdfs) para o cluster do HDInsight. | Não       |
| hiveConfiguration      | Especifica os parâmetros de configuração do hive (site do hive) para o cluster do HDInsight. | Não       |
| mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred-site) para o cluster do HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração de Oozie (oozie-site) para o cluster do HDInsight. | Não       |
| stormConfiguration     | Especifica os parâmetros de configuração do Storm (site de storm) para o cluster do HDInsight. | Não       |
| yarnConfiguration      | Especifica os parâmetros de configuração do Yarn (yarn-site) para o cluster do HDInsight. | Não       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo – configuração de cluster do HDInsight a pedido com propriedades avançadas

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Tamanhos de nó
Pode especificar os tamanhos de cabeça, dados e nós zookeeper, utilizando as seguintes propriedades: 

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Especifica o tamanho do nó principal. O valor predefinido é: Standard_D3. Consulte a **especificar tamanhos de nó** secção para obter detalhes. | Não       |
| dataNodeSize      | Especifica o tamanho do nó de dados. O valor predefinido é: Standard_D3. | Não       |
| zookeeperNodeSize | Especifica o tamanho do nó Zoo extinção do responsável. O valor predefinido é: Standard_D3. | Não       |

#### <a name="specifying-node-sizes"></a>Especificar tamanhos de nó
Consulte a [tamanhos das máquinas virtuais](../virtual-machines/linux/sizes.md) artigo para valores de cadeia de caracteres, precisa especificar para as propriedades mencionadas na secção anterior. Os valores tem de estar em conformidade com o **CMDLETs e APIS** referenciado neste artigo. Como pode ver no artigo, o nó de dados de tamanho grande (predefinição) tem 7 GB memória, que não pode ser bom o suficiente para o seu cenário. 

Se quiser criar D4 em tamanho normal de nós principais e nós de trabalho, especifique **Standard_D4** como o valor de propriedades headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se especificar um valor incorreto para a essas propriedades, poderá receber o seguinte **erro:** Falha ao criar o cluster. Exceção: Não foi possível concluir a criação do cluster operação. A operação falhou com o código "400". À esquerda por trás do Estado do cluster: "Error". Mensagem: 'PreClusterCreationValidationFailure'. Quando receber este erro, certifique-se de que está a utilizar o **CMDLET e APIS** nome da tabela no [tamanhos de máquinas virtuais](../virtual-machines/linux/sizes.md) artigo.        

## <a name="bring-your-own-compute-environment"></a>Traga seu próprio ambiente de computação
Este tipo de configuração, os utilizadores podem registar um ambiente de computação já existente como um serviço ligado no Data Factory. O ambiente de computação é gerido pelo utilizador e o serviço Data Factory utiliza para executar as atividades.

Este tipo de configuração é suportado para os seguintes ambientes de computação:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço ligado de HDInsight do Azure
Pode criar um serviço ligado de HDInsight de Azure para registar o seu próprio cluster do HDInsight com o Data Factory.

### <a name="example"></a>Exemplo

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                                                  | Necessário |
| ----------------- | ------------------------------------------------------------ | -------- |
| tipo              | A propriedade de tipo deve ser definida como **HDInsight**.            | Sim      |
| clusterUri        | O URI do HDInsight cluster.                            | Sim      |
| o nome de utilizador          | Especifique o nome do utilizador a ser utilizado para ligar a um cluster do HDInsight existente. | Sim      |
| palavra-passe          | Especifique a palavra-passe da conta de utilizador.                       | Sim      |
| linkedServiceName | Nome do serviço ligado do armazenamento do Azure que se refere-se para o armazenamento de Blobs do Azure utilizado pelo cluster do HDInsight. <p>Atualmente, não é possível especificar que um Store do Azure Data Lake serviço ligado para esta propriedade. Se o cluster do HDInsight tem acesso ao Store de Lake dados, pode aceder aos dados a Store do Azure Data Lake de scripts Hive/Pig. </p> | Sim      |
| isEspEnabled      | Especificar '*true*' se o cluster do HDInsight está [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction) ativada. A predefinição é "*false*'. | Não       |
| connectVia        | O Runtime de integração a ser utilizado para distribuir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou do Integration Runtime autoalojado. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. <br />Para o pacote de segurança da empresa (ESP) ativada a utilização do cluster de HDInsight um runtime de integração autoalojado que tem uma linha Visual para o cluster ou que deve ser implementado dentro da mesma rede Virtual do cluster de HDInsight ESP. | Não       |

> [!IMPORTANT]
> HDInsight oferece suporte a várias versões de cluster de Hadoop que podem ser implementadas. Cada opção de versão cria uma versão específica da distribuição Hortonworks Data Platform (HDP) e um conjunto de componentes que estão contidos dentro dessa distribuição. A lista de versões suportadas do HDInsight continua a ser atualizado para fornecer mais recente componentes de ecossistema do Hadoop e correções. Certifique-se de que sempre consultar informações mais recentes do [suportado HDInsight versão e o tipo de SO](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para garantir que está a utilizar uma versão suportada do HDInsight. 
>
> [!IMPORTANT]
> Atualmente, os serviços ligados HDInsight não suporta o HBase, Interactive Query (LLAP do Hive), o Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Serviço Azure Batch ligado

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode criar um serviço ligado do Azure Batch para registar um conjunto do Batch de máquinas virtuais (VMs) para uma fábrica de dados. Pode executar a atividade personalizada com o Azure Batch.

Consulte o seguinte tópicos, se estiver familiarizado com o serviço Azure Batch:

* [Noções básicas do Azure Batch](../batch/batch-technical-overview.md) para uma descrição geral do serviço Azure Batch.
* [Novo AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet para criar uma conta do Azure Batch (ou) [portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do Azure Batch com portal do Azure. Ver [utilizar o PowerShell para gerir a conta do Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) tópico para obter instruções detalhadas sobre como utilizar o cmdlet.
* [Novo AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) cmdlet para criar um conjunto do Batch do Azure.

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | A propriedade de tipo deve ser definida como **AzureBatch**. | Sim      |
| accountName       | Nome da conta do Azure Batch.         | Sim      |
| accessKey         | Chave de acesso para a conta do Azure Batch.  | Sim      |
| batchUri          | URL para a sua conta do Azure Batch, no formato https://*batchaccountname.region*. batch.azure.com. | Sim      |
| poolName          | Nome do conjunto de máquinas virtuais.    | Sim      |
| linkedServiceName | Nome de armazenamento do Azure vinculada serviço associado este serviço ligado do Azure Batch. Este serviço ligado é utilizado para testar os arquivos necessários para executar a atividade. | Sim      |
| connectVia        | O Runtime de integração a ser utilizado para distribuir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou do Integration Runtime autoalojado. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não       |

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado do Machine Learning do Azure
Criar um serviço ligado do Azure Machine Learning para registar um ponto final de classificação para uma fábrica de dados de lote do Machine Learning.

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Propriedades
| Propriedade               | Descrição                              | Necessário                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | A propriedade de tipo deve ser definida como: **AzureML**. | Sim                                      |
| mlEndpoint             | O URL de classificação do lote.                   | Sim                                      |
| apiKey                 | API do modelo de área de trabalho publicado.     | Sim                                      |
| updateResourceEndpoint | O URL de recurso de atualização para um ponto de extremidade de serviço do Azure ML Web utilizado para atualizar o serviço Web preditivo com o ficheiro de modelo preparado | Não                                       |
| servicePrincipalId     | Especifique o ID de cliente. da aplicação     | Necessário se updateResourceEndpoint for especificado |
| servicePrincipalKey    | Especifique a chave da aplicação.           | Necessário se updateResourceEndpoint for especificado |
| inquilino                 | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Necessário se updateResourceEndpoint for especificado |
| connectVia             | O Runtime de integração a ser utilizado para distribuir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou do Integration Runtime autoalojado. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço do Azure Data Lake Analytics ligado
Criar uma **do Azure Data Lake Analytics** serviço para uma fábrica de dados do Azure de computação do serviço ligado para ligar um Azure Data Lake Analytics. Este serviço ligado refere-se a atividade de U-SQL do Data Lake Analytics no pipeline. 

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Propriedades

| Propriedade             | Descrição                              | Necessário                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| tipo                 | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | Nome da conta do Azure Data Lake Analytics.  | Sim                                      |
| dataLakeAnalyticsUri | URI do Azure Data Lake Analytics.           | Não                                       |
| subscriptionId       | Id de subscrição do Azure                    | Não                                       |
| resourceGroupName    | Nome do grupo de recursos do Azure                | Não                                       |
| servicePrincipalId   | Especifique o ID de cliente. da aplicação     | Sim                                      |
| servicePrincipalKey  | Especifique a chave da aplicação.           | Sim                                      |
| inquilino               | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim                                      |
| connectVia           | O Runtime de integração a ser utilizado para distribuir as atividades para este serviço ligado. Pode utilizar o Runtime de integração do Azure ou do Integration Runtime autoalojado. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não                                       |



## <a name="azure-databricks-linked-service"></a>Serviço ligado do Databricks do Azure
Pode criar **serviço ligado do Azure Databricks** para registar a área de trabalho do Databricks que irá utilizar para executar o workloads(notebooks) do Databricks.

### <a name="example---using-new-job-cluster-in-databricks"></a>Exemplo - usando o novo cluster de trabalhos no Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exemplo - usando o cluster do Interactive existente no Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Propriedades

| Propriedade             | Descrição                              | Necessário                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| nome                 | Nome do serviço ligado               | Sim   |
| tipo                 | A propriedade de tipo deve ser definida como: **AzureDatabricks**. | Sim                                      |
| domínio               | Especifica a região do Azure em conformidade com base na região da área de trabalho do Databricks. Exemplo: https://eastus.azuredatabricks.net | Sim                                 |
| accessToken          | Token de acesso é necessário para o Data Factory autenticar para o Azure Databricks. Token de acesso tem de ser gerado a partir da área de trabalho do databricks. Obter mais passos para encontrar o token de acesso pode ser encontrado [aqui](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Sim                                       |
| existingClusterId    | ID de cluster de um cluster existente para executar todas as tarefas sobre isso. Deve ser um Cluster do Interactive já criado. Terá de reiniciar manualmente o cluster se ele deixa de responder. Databricks sugerir tarefas em execução em clusters de novo para maior fiabilidade. Pode encontrar o ID de Cluster de um Cluster do Interactive no Databricks -> a área de trabalho Clusters -> nome do Cluster interativo -> configuração -> etiquetas. [Obter mais detalhes](https://docs.databricks.com/user-guide/clusters/tags.html) | Não 
| newClusterVersion    | A versão de Spark do cluster. Ele criará um cluster de trabalhos no databricks. | Não  |
| newClusterNumOfWorker| Número de nós de trabalho que deve ter este cluster. Um cluster tem um controlador do Spark e num_workers executores para um total de num_workers + 1 nós de Spark. Uma cadeia de caracteres formatada Int32, como "1" significa numOfWorker é 1 ou "1:10" significa que o dimensionamento automático de 1 como min e 10 como máx.  | Não                |
| newClusterNodeType   | Este campo codifica, por meio de um valor único, os recursos disponíveis para cada um de nós neste cluster Spark. Por exemplo, o Spark nós podem ser aprovisionados e otimizados para memória ou de computação cargas de trabalho intensivas este campo é obrigatório para o novo cluster                | Não               |
| newClusterSparkConf  | um conjunto de opcionais, especificado pelo utilizador pares de chave-valor de configuração de Spark. Os utilizadores também podem passar uma cadeia de caracteres de opções de JVM Extras para o driver e os executores via spark.driver.extraJavaOptions e spark.executor.extraJavaOptions, respetivamente. | Não  |


## <a name="azure-sql-database-linked-service"></a>Serviço ligado da Base de Dados SQL do Azure
Criar um serviço ligado SQL do Azure e utilizá-lo com o [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory. Ver [conector do SQL Azure](connector-azure-sql-database.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço ligado do SQL Data Warehouse do Azure
Criar um serviço ligado do Azure SQL Data Warehouse e utilizá-lo com o [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory. Ver [conector do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="sql-server-linked-service"></a>Serviço ligado do SQL Server
Criar um serviço ligado do SQL Server e utilizá-lo com o [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory. Ver [conector do SQL Server](connector-sql-server.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista das atividades de transformação do Azure Data Factory suporta, consulte [transformar dados](transform-data.md).
