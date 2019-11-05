---
title: Ambientes de computação com suporte pelo Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os ambientes de computação que você pode usar em Azure Data Factory pipelines (como o Azure HDInsight) para transformar ou processar dados.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: fd874776e5be94831322bce839a502ebc43e1958
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481193"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambientes de computação com suporte pelo Azure Data Factory
Este artigo explica diferentes ambientes de computação que você pode usar para processar ou transformar dados. Ele também fornece detalhes sobre configurações diferentes (sob demanda versus traga a sua própria) com suporte pelo Data Factory ao configurar serviços vinculados vinculando esses ambientes de computação a uma data factory do Azure.

A tabela a seguir fornece uma lista de ambientes de computação com suporte pelo Data Factory e as atividades que podem ser executadas neles. 

| Ambiente de computação                                          | atividades                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Cluster Hdinsight sob demanda](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster hdinsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [streaming do Hadoop](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Personalizar](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning executar pipeline](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning executar pipeline](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [azure SQL data warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Procedimento Armazenado](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [python](transform-data-databricks-python.md) |
| [Função do Azure](#azure-function-linked-service)         | [Atividade da função do Azure](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>Ambiente de computação do HDInsight sob demanda
Nesse tipo de configuração, o ambiente de computação é totalmente gerenciado pelo serviço de Azure Data Factory. Ele é criado automaticamente pelo serviço de Data Factory antes de um trabalho ser enviado para processar dados e removido quando o trabalho for concluído. Você pode criar um serviço vinculado para o ambiente de computação sob demanda, configurá-lo e controlar as configurações granulares para execução de trabalho, gerenciamento de cluster e ações de inicialização.

> [!NOTE]
> Atualmente, a configuração sob demanda tem suporte apenas para clusters do Azure HDInsight. Azure Databricks também dá suporte a trabalhos sob demanda usando clusters de trabalho, consulte [serviço vinculado do Azure databricks](#azure-databricks-linked-service) para obter mais detalhes.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço vinculado sob demanda do Azure HDInsight
O serviço de Azure Data Factory pode criar automaticamente um cluster HDInsight sob demanda para processar dados. O cluster é criado na mesma região que a conta de armazenamento (Propriedade linkedServiceName no JSON) associada ao cluster. A conta de armazenamento deve ser uma conta de armazenamento do Azure padrão de uso geral. 

Observe os seguintes pontos **importantes** sobre o serviço vinculado do HDInsight sob demanda:

* O cluster HDInsight sob demanda é criado em sua assinatura do Azure. Você pode ver o cluster em seu portal do Azure quando o cluster está em execução. 
* Os logs de trabalhos que são executados em um cluster HDInsight sob demanda são copiados para a conta de armazenamento associada ao cluster HDInsight. O clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definido em sua definição de serviço vinculado são usados para fazer logon no cluster para solucionar problemas detalhados durante o ciclo de vida do cluster. 
* Você é cobrado somente pelo tempo em que o cluster HDInsight está funcionando e executando trabalhos.
* Você pode usar uma **ação de script** com o serviço vinculado do Azure HDInsight sob demanda.  

> [!IMPORTANT]
> Normalmente, leva **20 minutos** ou mais para provisionar um cluster do Azure HDInsight sob demanda.

### <a name="example"></a>Exemplo
O JSON a seguir define um serviço vinculado do HDInsight sob demanda baseado em Linux. O serviço de Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** para processar a atividade necessária. 

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
> À medida que mais atividades forem executadas, você verá muitos contêineres no armazenamento de BLOBs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](https://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | A propriedade Type deve ser definida como **HDInsightOnDemand**. | Sim      |
| clusterSize                  | Número de nós de trabalho/dados no cluster. O cluster HDInsight é criado com 2 nós de cabeçalho junto com o número de nós de trabalho que você especifica para essa propriedade. Os nós são de tamanho Standard_D3 com 4 núcleos, portanto, um cluster de quatro nós de trabalho usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho, mais 2\*4 = 8 núcleos para nós de cabeçalho). Consulte [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes. | Sim      |
| linkedServiceName            | O serviço vinculado do armazenamento do Azure a ser usado pelo cluster sob demanda para armazenar e processar dados. O cluster HDInsight é criado na mesma região que essa conta de armazenamento do Azure. O Azure HDInsight tem limitação do número total de núcleos que pode utilizar em cada região do Azure que suporta. Verifique se você tem cotas de núcleo suficientes na região do Azure para atender aos clusterSize necessários. Para obter detalhes, consulte [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>No momento, você não pode criar um cluster HDInsight sob demanda que usa um Azure Data Lake Store como armazenamento. Se você quiser armazenar os dados de resultado do processamento do HDInsight em um Azure Data Lake Store, use uma atividade de cópia para copiar os dados do armazenamento de BLOBs do Azure para o Azure Data Lake Store. </p> | Sim      |
| clusterResourceGroup         | O cluster HDInsight é criado nesse grupo de recursos. | Sim      |
| TimeToLive                   | O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica por quanto tempo o cluster HDInsight sob demanda permanece ativo após a conclusão de uma atividade de execução se não houver nenhum outro trabalho ativo no cluster. O valor mínimo permitido é de 5 minutos (00:05:00).<br/><br/>Por exemplo, se uma execução de atividade demorar 6 minutos e TimeToLive for definido como 5 minutos, o cluster permanecerá ativo por 5 minutos após os 6 minutos de processamento da execução da atividade. Se outra execução de atividade for executada com a janela de 6 minutos, ela será processada pelo mesmo cluster.<br/><br/>A criação de um cluster HDInsight sob demanda é uma operação cara (pode levar algum tempo), portanto, use essa configuração conforme necessário para melhorar o desempenho de um data factory reutilizando um cluster HDInsight sob demanda.<br/><br/>Se você definir o valor TimeToLive como 0, o cluster será excluído assim que a execução da atividade for concluída. Enquanto que, se você definir um valor alto, o cluster poderá permanecer ocioso para que você faça logon para solucionar o problema, mas isso pode resultar em altos custos. Portanto, é importante que você defina o valor apropriado com base em suas necessidades.<br/><br/>Se o valor da Propriedade TimeToLive estiver definido adequadamente, vários pipelines poderão compartilhar a instância do cluster HDInsight sob demanda. | Sim      |
| clusterType                  | O tipo do cluster HDInsight a ser criado. Os valores permitidos são "Hadoop" e "Spark". Se não for especificado, o valor padrão será Hadoop. Enterprise Security Package cluster habilitado não pode ser criado sob demanda, em vez disso, use um [cluster existente/traga sua própria computação](#azure-hdinsight-linked-service). | Não       |
| versão                      | Versão do cluster HDInsight. Se não for especificado, ele está usando a versão padrão definida do HDInsight atual. | Não       |
| hostSubscriptionId           | A ID de assinatura do Azure usada para criar o cluster HDInsight. Se não for especificado, ele usará a ID de assinatura do seu contexto de logon do Azure. | Não       |
| clusterNamePrefix           | O prefixo do nome do cluster HDI, um carimbo de data/hora será anexado automaticamente ao final do nome do cluster| Não       |
| sparkVersion                 | A versão do Spark se o tipo de cluster for "Spark" | Não       |
| additionalLinkedServiceNames | Especifica contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço de Data Factory possa registrá-las em seu nome. Essas contas de armazenamento devem estar na mesma região que o cluster HDInsight, que é criado na mesma região que a conta de armazenamento especificada por linkedServiceName. | Não       |
| osType                       | Tipo de sistema operacional. Os valores permitidos são: Linux e Windows (somente para HDInsight 3,3). O padrão é o Linux. | Não       |
| hcatalogLinkedServiceName    | O nome do serviço vinculado do SQL do Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda é criado usando o banco de dados SQL do Azure como o metastore. | Não       |
| connectVia                   | O Integration Runtime a ser usado para enviar as atividades para esse serviço vinculado do HDInsight. Para o serviço vinculado do HDInsight sob demanda, ele só dá suporte a Azure Integration Runtime. Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não       |
| clusterUserName                   | O nome de usuário para acessar o cluster. | Não       |
| clusterPassword                   | A senha no tipo de cadeia de caracteres segura para acessar o cluster. | Não       |
| clusterSshUserName         | O nome de usuário para SSH conecta-se remotamente ao nó do cluster (para Linux). | Não       |
| clusterSshPassword         | A senha no tipo de cadeia de caracteres segura para o SSH conectar remotamente o nó do cluster (para Linux). | Não       |
| scriptActions | Especifique o script para [personalizações do cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) durante a criação do cluster sob demanda. <br />Atualmente, a ferramenta de criação de interface do usuário Azure Data Factory dá suporte à especificação de apenas uma ação de script, mas você pode obter essa limitação no JSON (especifique várias ações de script no JSON). | Não |


> [!IMPORTANT]
> O HDInsight dá suporte a várias versões de cluster Hadoop que podem ser implantadas. Cada opção de versão cria uma versão específica da distribuição HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. A lista de versões do HDInsight com suporte continua sendo atualizada para fornecer correções e componentes mais recentes do ecossistema do Hadoop. Certifique-se de sempre consultar as informações mais recentes de [versão do Hdinsight com suporte e tipo de so](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para garantir que você esteja usando a versão com suporte do hdinsight. 
>
> [!IMPORTANT]
> Atualmente, os serviços vinculados do HDInsight não dão suporte ao HBase, consulta interativa (Hive LLAP), Storm. 
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

O serviço vinculado do HDInsight sob demanda requer uma autenticação de entidade de serviço para criar clusters HDInsight em seu nome. Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) e conceda a ela a função **colaborador** da assinatura ou do grupo de recursos no qual o cluster HDInsight é criado. Para obter etapas detalhadas, consulte [usar o portal para criar um aplicativo Azure Active Directory e uma entidade de serviço que possa acessar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Anote os seguintes valores, que você usa para definir o serviço vinculado:

- ID da aplicação
- Chave do aplicativo 
- ID do inquilino

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique a ID do cliente do aplicativo.     | Sim      |
| **servicePrincipalKey** | Especifique a chave do aplicativo.           | Sim      |
| **vários**              | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Você pode recuperá-lo passando o mouse no canto superior direito do portal do Azure. | Sim      |

### <a name="advanced-properties"></a>Propriedades avançadas

Você também pode especificar as propriedades a seguir para a configuração granular do cluster HDInsight sob demanda.

| Propriedade               | Descrição                              | Necessário |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração principais (como no Core-site. xml) para o cluster HDInsight a ser criado. | Não       |
| hBaseConfiguration     | Especifica os parâmetros de configuração do HBase (HBase-site. xml) para o cluster HDInsight. | Não       |
| hdfsConfiguration      | Especifica os parâmetros de configuração do HDFS (HDFS-site. xml) para o cluster HDInsight. | Não       |
| hiveConfiguration      | Especifica os parâmetros de configuração do hive (Hive-site. xml) para o cluster HDInsight. | Não       |
| mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred-site. xml) para o cluster HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração do Oozie (oozie-site. xml) para o cluster HDInsight. | Não       |
| stormConfiguration     | Especifica os parâmetros de configuração do Storm (Storm-site. xml) para o cluster HDInsight. | Não       |
| yarnConfiguration      | Especifica os parâmetros de configuração do yarn (yarn-site. xml) para o cluster HDInsight. | Não       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo – configuração de cluster HDInsight sob demanda com propriedades avançadas

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
Você pode especificar os tamanhos dos nós de cabeçalho, dados e Zookeeper usando as seguintes propriedades: 

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| Das      | Especifica o tamanho do nó de cabeçalho. O valor padrão é: Standard_D3. Consulte a seção **especificando tamanhos de nó** para obter detalhes. | Não       |
| dataNodeSize      | Especifica o tamanho do nó de dados. O valor padrão é: Standard_D3. | Não       |
| zookeeperNodeSize | Especifica o tamanho do nó de protetor do Zoo. O valor padrão é: Standard_D3. | Não       |

#### <a name="specifying-node-sizes"></a>Especificando tamanhos de nó
Consulte o artigo [tamanhos de máquinas virtuais](../virtual-machines/linux/sizes.md) para obter valores de cadeia de caracteres que você precisa especificar para as propriedades mencionadas na seção anterior. Os valores precisam estar em conformidade com os **cmdlets & APIs** referenciadas no artigo. Como você pode ver no artigo, o nó de dados do tamanho grande (padrão) tem 7 GB de memória, o que pode não ser bom o suficiente para seu cenário. 

Se você quiser criar nós de cabeçalho de tamanho D4 e nós de trabalho, especifique **Standard_D4** como o valor para as propriedades das e datanodes. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se você especificar um valor incorreto para essas propriedades, você poderá receber o seguinte **erro:** falha ao criar o cluster. Exceção: não foi possível concluir a operação de criação do cluster. A operação falhou com o código "400". Estado do cluster não concluído: "Erro". Mensagem: ' PreClusterCreationValidationFailure '. Ao receber esse erro, verifique se você está usando o **CMDLET &** nome das APIs da tabela no artigo [tamanhos de máquinas virtuais](../virtual-machines/linux/sizes.md) .        

## <a name="bring-your-own-compute-environment"></a>Traga seu próprio ambiente de computação
Nesse tipo de configuração, os usuários podem registrar um ambiente de computação já existente como um serviço vinculado no Data Factory. O ambiente de computação é gerenciado pelo usuário e o serviço de Data Factory o utiliza para executar as atividades.

Esse tipo de configuração tem suporte para os seguintes ambientes de computação:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Banco de BD SQL do Azure, SQL DW do Azure SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço vinculado do Azure HDInsight
Você pode criar um serviço vinculado do Azure HDInsight para registrar seu próprio cluster HDInsight com Data Factory.

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
| tipo              | A propriedade Type deve ser definida como **HDInsight**.            | Sim      |
| clusterUri        | O URI do cluster HDInsight.                            | Sim      |
| o nome de utilizador          | Especifique o nome do usuário a ser usado para se conectar a um cluster HDInsight existente. | Sim      |
| palavra-passe          | Especifique a senha para a conta de usuário.                       | Sim      |
| linkedServiceName | Nome do serviço vinculado do armazenamento do Azure que se refere ao armazenamento de BLOBs do Azure usado pelo cluster HDInsight. <p>No momento, não é possível especificar um serviço vinculado Azure Data Lake Store para essa propriedade. Se o cluster HDInsight tiver acesso ao Data Lake Store, você poderá acessar dados no Azure Data Lake Store de scripts Hive/Pig. </p> | Sim      |
| isEspEnabled      | Especifique '*true*' se o cluster HDInsight estiver [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) habilitado. O padrão é '*false*'. | Não       |
| connectVia        | O Integration Runtime a ser usado para enviar as atividades para esse serviço vinculado. Você pode usar o Integration Runtime Azure Integration Runtime ou auto-hospedado. Se não for especificado, ele usará o Azure Integration Runtime padrão. <br />Para Enterprise Security Package (ESP), o cluster HDInsight habilitado usa um tempo de execução de integração auto-hospedado, que tem uma linha de visão para o cluster ou deve ser implantado dentro da mesma rede virtual que o cluster do HDInsight do ESP. | Não       |

> [!IMPORTANT]
> O HDInsight dá suporte a várias versões de cluster Hadoop que podem ser implantadas. Cada opção de versão cria uma versão específica da distribuição HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. A lista de versões do HDInsight com suporte continua sendo atualizada para fornecer correções e componentes mais recentes do ecossistema do Hadoop. Certifique-se de sempre consultar as informações mais recentes de [versão do Hdinsight com suporte e tipo de so](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para garantir que você esteja usando a versão com suporte do hdinsight. 
>
> [!IMPORTANT]
> Atualmente, os serviços vinculados do HDInsight não dão suporte ao HBase, consulta interativa (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Serviço vinculado do lote do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode criar um serviço vinculado do lote do Azure para registrar um pool do lote de VMs (máquinas virtuais) em um data factory. Você pode executar a atividade personalizada usando o lote do Azure.

Consulte os seguintes artigos se você for novo no serviço de lote do Azure:

* [Noções básicas do lote do Azure](../batch/batch-technical-overview.md) para obter uma visão geral do serviço de lote do Azure.
* Cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) para criar uma conta do lote do Azure (ou) [portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do lote do Azure usando portal do Azure. Consulte o artigo [usando o PowerShell para gerenciar a conta do lote do Azure](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obter instruções detalhadas sobre como usar o cmdlet.
* Cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) para criar um pool do lote do Azure.

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
| tipo              | A propriedade Type deve ser definida como **AzureBatch**. | Sim      |
| accountName       | Nome da conta do lote do Azure.         | Sim      |
| accessKey         | Chave de acesso para a conta do lote do Azure.  | Sim      |
| batchUri          | URL para sua conta do lote do Azure, no formato de https://*batchaccountname. Region*. Batch.Azure.com. | Sim      |
| poolName          | Nome do pool de máquinas virtuais.    | Sim      |
| linkedServiceName | Nome do serviço vinculado do armazenamento do Azure associado a este serviço vinculado do lote do Azure. Esse serviço vinculado é usado para arquivos de preparação necessários para executar a atividade. | Sim      |
| connectVia        | O Integration Runtime a ser usado para enviar as atividades para esse serviço vinculado. Você pode usar o Integration Runtime Azure Integration Runtime ou auto-hospedado. Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não       |

## <a name="azure-machine-learning-studio-linked-service"></a>Azure Machine Learning Studio serviço vinculado
Você cria um serviço vinculado Azure Machine Learning Studio para registrar um ponto de extremidade de Pontuação de Machine Learning em um data factory.

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
| Tipo                   | A propriedade Type deve ser definida como: **AzureML**. | Sim                                      |
| mlEndpoint             | A URL de Pontuação do lote.                   | Sim                                      |
| apiKey                 | A API do modelo do espaço de trabalho publicado.     | Sim                                      |
| updateResourceEndpoint | A URL de recurso de atualização para um ponto de extremidade de serviço Web Azure Machine Learning usado para atualizar o serviço Web de previsão com arquivo de modelo treinado | Não                                       |
| servicePrincipalId     | Especifique a ID do cliente do aplicativo.     | Obrigatório se updateResourceEndpoint for especificado |
| servicePrincipalKey    | Especifique a chave do aplicativo.           | Obrigatório se updateResourceEndpoint for especificado |
| vários                 | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Você pode recuperá-lo passando o mouse no canto superior direito do portal do Azure. | Obrigatório se updateResourceEndpoint for especificado |
| connectVia             | O Integration Runtime a ser usado para enviar as atividades para esse serviço vinculado. Você pode usar o Integration Runtime Azure Integration Runtime ou auto-hospedado. Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning serviço vinculado
Você cria um serviço vinculado Azure Machine Learning para conectar um espaço de trabalho Azure Machine Learning a um data factory.

> [!NOTE]
> Atualmente, somente a autenticação de entidade de serviço tem suporte para o serviço vinculado Azure Machine Learning.

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Propriedades
| Propriedade               | Descrição                              | Necessário                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Tipo                   | A propriedade Type deve ser definida como: **AzureMLService**. | Sim                                      |
| subscriptionId         | ID da assinatura do Azure              | Sim                                      |
| resourceGroupName      | nome | Sim                                      |
| mlWorkspaceName        | Nome do espaço de trabalho Azure Machine Learning | Sim  |
| servicePrincipalId     | Especifique a ID do cliente do aplicativo.     | Não |
| servicePrincipalKey    | Especifique a chave do aplicativo.           | Não |
| vários                 | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Você pode recuperá-lo passando o mouse no canto superior direito do portal do Azure. | Obrigatório se updateResourceEndpoint for especificado | Não |
| connectVia             | O Integration Runtime a ser usado para enviar as atividades para esse serviço vinculado. Você pode usar o Integration Runtime Azure Integration Runtime ou auto-hospedado. Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não |    

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics serviço vinculado
Você cria um serviço vinculado **Azure data Lake Analytics** para vincular um serviço de computação Azure data Lake Analytics a uma data Factory do Azure. O Data Lake Analytics atividade U-SQL no pipeline refere-se a esse serviço vinculado. 

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
            "subscriptionId": "<optional, subscription ID of ADLA>",
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
| tipo                 | A propriedade Type deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | Nome da conta de Azure Data Lake Analytics.  | Sim                                      |
| dataLakeAnalyticsUri | URI de Azure Data Lake Analytics.           | Não                                       |
| subscriptionId       | ID da assinatura do Azure                    | Não                                       |
| resourceGroupName    | Nome do grupo de recursos do Azure                | Não                                       |
| servicePrincipalId   | Especifique a ID do cliente do aplicativo.     | Sim                                      |
| servicePrincipalKey  | Especifique a chave do aplicativo.           | Sim                                      |
| vários               | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Você pode recuperá-lo passando o mouse no canto superior direito do portal do Azure. | Sim                                      |
| connectVia           | O Integration Runtime a ser usado para enviar as atividades para esse serviço vinculado. Você pode usar o Integration Runtime Azure Integration Runtime ou auto-hospedado. Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks serviço vinculado
Você pode criar **Azure Databricks serviço vinculado** para registrar o espaço de trabalho do databricks que será usado para executar as cargas de trabalho do databricks (Notebook, jar, Python). 
> [!IMPORTANT]
> Os serviços vinculados do databricks dão suporte a [pools de instância](https://aka.ms/instance-pools). 

### <a name="example---using-new-job-cluster-in-databricks"></a>Exemplo-usando o novo cluster de trabalho no databricks

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exemplo – usando o cluster interativo existente no databricks

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
| nome                 | Nome do serviço vinculado               | Sim   |
| tipo                 | A propriedade Type deve ser definida como: **Azure Databricks**. | Sim                                      |
| Controlador               | Especifique a região do Azure de acordo com a região do espaço de trabalho do databricks. Exemplo: https://eastus.azuredatabricks.net | Sim                                 |
| accessToken          | O token de acesso é necessário para Data Factory autenticar para Azure Databricks. O token de acesso precisa ser gerado no espaço de trabalho do databricks. Etapas mais detalhadas para encontrar o token de acesso podem ser encontradas [aqui](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Sim                                       |
| existingClusterId    | ID de cluster de um cluster existente para executar todos os trabalhos. Esse deve ser um cluster interativo já criado. Talvez seja necessário reiniciar o cluster manualmente se ele parar de responder. O databricks sugere trabalhos em execução em novos clusters para maior confiabilidade. Você pode encontrar a ID do cluster de um cluster interativo no espaço de trabalho do databricks – clusters >-> nome do cluster interativo-> Configuration-> Tags. [Mais detalhes](https://docs.databricks.com/user-guide/clusters/tags.html) | Não 
| instancePoolId    | ID do pool de instâncias de um pool existente no espaço de trabalho do databricks.  | Não  |
| newClusterVersion    | A versão do Spark do cluster. Ele criará um cluster de trabalho no databricks. | Não  |
| newClusterNumOfWorker| Número de nós de trabalho que esse cluster deve ter. Um cluster tem um driver Spark e executores num_workers para um total de num_workers + 1 nós Spark. Um Int32 formatado por cadeia de caracteres, como "1" significa que numOfWorker é 1 ou "1:10" significa dimensionamento automático de 1 como mínimo e 10 como máximo.  | Não                |
| newClusterNodeType   | Esse campo codifica, por meio de um único valor, os recursos disponíveis para cada um dos nós do Spark neste cluster. Por exemplo, os nós do Spark podem ser provisionados e otimizados para a memória ou cargas de trabalho com uso intensivo de computação. Este campo é necessário para o novo cluster                | Não               |
| newClusterSparkConf  | um conjunto de pares chave-valor de configuração do Spark opcional especificado pelo usuário. Os usuários também podem passar uma cadeia de caracteres de opções adicionais da JVM para o driver e os executores por meio do Spark. driver. extraJavaOptions e do Spark. executor. extraJavaOptions, respectivamente. | Não  |
| newClusterInitScripts| um conjunto de scripts de inicialização opcionais definidos pelo usuário para o novo cluster. Especificando o caminho DBFS para os scripts init. | Não  |


## <a name="azure-sql-database-linked-service"></a>Serviço ligado da Base de Dados SQL do Azure
Você cria um serviço vinculado do SQL do Azure e o utiliza com a [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline data Factory. Confira o artigo [conector do SQL do Azure](connector-azure-sql-database.md#linked-service-properties) para obter detalhes sobre esse serviço vinculado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço vinculado do Azure SQL Data Warehouse
Você cria um serviço vinculado do Azure SQL Data Warehouse e o usa com a [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline de data Factory. Consulte o artigo [conector de SQL data warehouse do Azure](connector-azure-sql-data-warehouse.md#linked-service-properties) para obter detalhes sobre esse serviço vinculado.

## <a name="sql-server-linked-service"></a>SQL Server serviço vinculado
Você cria um SQL Server serviço vinculado e o usa com a [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline de data Factory. Consulte o artigo [conector SQL Server](connector-sql-server.md#linked-service-properties) para obter detalhes sobre esse serviço vinculado.

## <a name="azure-function-linked-service"></a>Serviço vinculado de funções do Azure
Você cria um serviço vinculado do Azure Function e o usa com a [atividade da função do Azure](control-flow-azure-function-activity.md) para executar Azure Functions em um pipeline data Factory. O tipo de retorno da função do Azure deve ser um `JObject`válido. (Tenha em mente que [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *não* é um `JObject`.) Qualquer tipo de retorno diferente de `JObject` falha e gera o conteúdo de resposta de erro do usuário *não é um JObject válido*.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo   | A propriedade Type deve ser definida como: **AzureFunction** | sim |
| URL do aplicativo de funções | URL para o Aplicativo de funções do Azure. O formato é `https://<accountname>.azurewebsites.net`. Essa URL é o valor na seção de **URL** ao exibir sua Aplicativo de funções no portal do Azure  | sim |
| chave de função | Chave de acesso para a função do Azure. Clique na seção **gerenciar** para a respectiva função e copie a **chave de função** ou a chave de **host**. Saiba mais aqui: [Azure Functions gatilhos e associações http](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | sim |
|   |   |   |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista das atividades de transformação com suporte pelo Azure Data Factory, consulte [transformar dados](transform-data.md).
