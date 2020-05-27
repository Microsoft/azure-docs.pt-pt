---
title: Ambientes computacionais suportados pela Azure Data Factory
description: Saiba mais sobre ambientes computacionais que pode utilizar em oleodutos Azure Data Factory (como o Azure HDInsight) para transformar ou processar dados.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.date: 05/08/2019
ms.openlocfilehash: 3233292f0097330cc5e6ed07460de80934a278e4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849302"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambientes computacionais suportados pela Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explica diferentes ambientes de cálculo que pode usar para processar ou transformar dados. Também fornece detalhes sobre diferentes configurações (on-demand vs. bring your own) suportadas pela Data Factory ao configurar serviços ligados que ligam estes ambientes computacionais a uma fábrica de dados Azure.

A tabela seguinte fornece uma lista de ambientes computacionais suportados pela Data Factory e as atividades que podem ser executadas sobre eles. 

| Ambiente de computação                                          | atividades                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Cluster HDInsight a pedido](#azure-hdinsight-on-demand-linked-service) ou [o seu próprio cluster HDInsight](#azure-hdinsight-linked-service) | [Colmeia,](transform-data-using-hadoop-hive.md) [Porco,](transform-data-using-hadoop-pig.md) [Faísca,](transform-data-using-spark.md) [MapReduce,](transform-data-using-hadoop-map-reduce.md) [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Personalizar](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Gasoduto de execução de aprendizagem automática azure](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Gasoduto de execução de aprendizagem automática azure](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL,](#azure-sql-database-linked-service)Armazém de [Dados Azure SQL,](#azure-sql-data-warehouse-linked-service) [Servidor SQL](#sql-server-linked-service) | [Procedimento Armazenado](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Caderno](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Função do Azure](#azure-function-linked-service)         | [Atividade da Função Azure](control-flow-azure-function-activity.md)
>  

## <a name="hdinsight-compute-environment"></a>Ambiente computacional HDInsight

Consulte a tabela abaixo para obter detalhes sobre os tipos de serviço ligados ao armazenamento suportados para configuração em ambiente on-demand e BYOC (Traga o seu próprio cálculo).

| No Serviço Compute Linked | Nome da Propriedade                | Descrição                                                  | Blobs | ADLS Gen2 | BD SQL do Azure | ADLS Gen 1 |
| ------------------------- | ---------------------------- | ------------------------------------------------------------ | ---- | --------- | ------------ | ---------- |
| A pedido                 | linkedServiceName            | Serviço ligado ao Armazenamento Azure a utilizar pelo cluster a pedido para armazenar e processar dados. | Sim  | Sim       | Não           | Não         |
|                           | nomes adicionais LinkedServiceNames | Especifica contas de armazenamento adicionais para o serviço ligado ao HDInsight para que o serviço Data Factory as possa registar em seu nome. | Yes  | Não        | Não           | Não         |
|                           | hcatalogLinkedServiceName    | O nome do serviço ligado ao Azure SQL que aponta para a base de dados Do HCatalog. O cluster HDInsight a pedido é criado utilizando a base de dados Azure SQL como metaloja. | Não   | Não        | Yes          | No         |
| BYOC                      | linkedServiceName            | A referência de serviço ligada ao Armazenamento Azure.                | Sim  | Sim       | Não           | Não         |
|                           | nomes adicionais LinkedServiceNames | Especifica contas de armazenamento adicionais para o serviço ligado ao HDInsight para que o serviço Data Factory as possa registar em seu nome. | Não   | Não        | Não           | Não         |
|                           | hcatalogLinkedServiceName    | Uma referência ao serviço ligado ao Azure SQL que aponta para a base de dados HCatalog. | Não   | Não        | Não           | Não         |

### <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço ligado do Azure HDInsight a pedido

Neste tipo de configuração, o ambiente de computação é totalmente gerido pelo serviço Azure Data Factory. É automaticamente criado pelo serviço Data Factory antes de um trabalho ser submetido para processar dados e removido quando o trabalho está concluído. Você pode criar um serviço ligado para o ambiente de computação a pedido, configurá-lo e controlar configurações granulares para execução de emprego, gestão de clusters e ações de saque.

> [!NOTE]
> A configuração on-demand é atualmente suportada apenas para clusters Azure HDInsight. A Azure Databricks também apoia empregos a pedido utilizando agrupamentos de emprego. Para mais informações, consulte o serviço ligado aos tijolos de [dados azure.](#azure-databricks-linked-service)

O serviço Azure Data Factory pode criar automaticamente um cluster HDInsight a pedido para processar dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName no JSON) associada ao cluster. A conta de armazenamento `must` é uma conta de armazenamento de uso geral. 

Note os seguintes pontos **importantes** sobre o serviço ligado ao HDInsight a pedido:

* O cluster HDInsight a pedido é criado sob a sua subscrição Azure. Você é capaz de ver o cluster no seu portal Azure quando o cluster está em funcionamento. 
* Os registos de empregos que são executados num cluster HDInsight a pedido são copiados para a conta de armazenamento associada ao cluster HDInsight. O clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword definido na definição de serviço ligado são utilizados para iniciar sessão no cluster para uma resolução aprofundada de problemas durante o ciclo de vida do cluster. 
* É cobrado apenas pelo momento em que o cluster HDInsight está a funcionar.
* Pode utilizar um **Script Action** com o serviço ligado a pedido do Azure HDInsight.  

> [!IMPORTANT]
> Normalmente demora **20 minutos** ou mais a fornecer um cluster Azure HDInsight a pedido.

#### <a name="example"></a>Exemplo

O seguinte JSON define um serviço ligado hDInsight baseado em Linux. O serviço Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** para processar a atividade necessária. 

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
> O cluster HDInsight cria um **recipiente predefinido** no armazenamento blob especificado no JSON **(linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento. 
>
> À medida que mais atividade corre, você vê muitos recipientes no seu armazenamento de blob Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](https://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

#### <a name="properties"></a>Propriedades

| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | A propriedade do tipo deve ser definida para **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Número de nós de trabalhador/dados no cluster. O cluster HDInsight é criado com 2 nós de cabeça juntamente com o número de nós de trabalhador que especifica para esta propriedade. Os nós são de tamanho Standard_D3 que tem 4 núcleos, por isso um cluster de nó de 4 trabalhadores leva 24 núcleos (4 \* 4 = 16 núcleos para nós operários, mais 2 4 = 8 núcleos para nós de \* cabeça). Consulte [clusters de configuração em HDInsight com Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para mais detalhes. | Yes      |
| linkedServiceName            | Serviço ligado ao Armazenamento Azure a utilizar pelo cluster a pedido para armazenar e processar dados. O cluster HDInsight é criado na mesma região que esta conta de Armazenamento Azure. O Azure HDInsight tem limitação do número total de núcleos que pode utilizar em cada região do Azure que suporta. Certifique-se de que tem quotas centrais suficientes nessa região de Azure para atender ao clusterSize necessário. Para mais detalhes, consulte a Configuração de [clusters em HDInsight com Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Atualmente, não é possível criar um cluster HDInsight a pedido que utiliza um Armazenamento de Lago de Dados Azure (Gen 2) como armazenamento. Se pretender armazenar os dados do resultado do processamento do HDInsight num Armazém de Lagos De Dados Azure (Gen 2), utilize uma Atividade de Cópia para copiar os dados do Armazenamento de Blob Azure para o Armazenamento do Lago De dados Azure (Gen 2). </p> | Yes      |
| clusterResourceGroup         | O cluster HDInsight é criado neste grupo de recursos. | Yes      |
| timetolive                   | O tempo de inatividade permitido para o cluster HDInsight a pedido. Especifica quanto tempo o cluster HDInsight a pedido permanece vivo após a conclusão de uma atividade executada se não houver outros empregos ativos no cluster. O valor mínimo permitido é de 5 minutos (00:05:00).<br/><br/>Por exemplo, se uma execução de atividade demorar 6 minutos e o tempo de vida for programado para 5 minutos, o cluster permanece vivo durante 5 minutos após os 6 minutos de processamento da atividade. Se outra execução de atividade for executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>Criar um cluster HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo), por isso use esta definição conforme necessário para melhorar o desempenho de uma fábrica de dados, reutilizando um cluster HDInsight a pedido.<br/><br/>Se definir o valor de tempo para viver para 0, o cluster é eliminado assim que a atividade estiver concluída. Enquanto que, se definir um valor elevado, o cluster pode permanecer inativo para que você possa iniciar sessão para algum propósito de resolução de problemas, mas pode resultar em custos elevados. Por isso, é importante que detetete o valor adequado com base nas suas necessidades.<br/><br/>Se o valor da propriedade timetolive for devidamente definido, vários oleodutos podem partilhar a instância do cluster HDInsight a pedido. | Yes      |
| clusterType                  | O tipo de cluster HDInsight a ser criado. Os valores permitidos são "hadoop" e "faísca". Se não especificado, o valor predefinido é hadoop. O cluster ativado pelo Enterprise Security Package não pode ser criado a pedido, em vez disso, utilize um [cluster existente/ traga o seu próprio cálculo](#azure-hdinsight-linked-service). | No       |
| versão                      | Versão do cluster HDInsight. Se não especificado, está a utilizar a versão padrão definida pelo HDInsight. | No       |
| hostSubscriptionId           | O ID de subscrição Azure usado para criar o cluster HDInsight. Se não especificado, utiliza o ID de subscrição do seu contexto de login Azure. | No       |
| clusterNamePrefix           | O prefixo do nome do cluster HDI, um carimbo de tempo automaticamente anexa no final do nome do cluster| No       |
| sparkVersion                 | A versão da faísca se o tipo de cluster for "Faísca" | No       |
| nomes adicionais LinkedServiceNames | Especifica contas de armazenamento adicionais para o serviço ligado ao HDInsight para que o serviço Data Factory as possa registar em seu nome. Estas contas de armazenamento devem estar na mesma região que o cluster HDInsight, que é criado na mesma região que a conta de armazenamento especificada pelo linkedServiceName. | No       |
| osType                       | Tipo de sistema operativo. Os valores permitidos são: Linux e Windows (apenas para HDInsight 3.3). Padrão é Linux. | No       |
| hcatalogLinkedServiceName    | O nome do serviço ligado ao Azure SQL que aponta para a base de dados Do HCatalog. O cluster HDInsight a pedido é criado utilizando a Base de Dados Azure SQL como metaloja. | No       |
| connectVia                   | O Tempo de Integração a utilizar para enviar as atividades para este serviço ligado ao HDInsight. Para o serviço ligado ao HDInsight a pedido, apenas suporta o Tempo de Funcionamento da Integração Azure. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | No       |
| clusterUserName                   | O nome de utilizador para aceder ao cluster. | No       |
| clusterPassword                   | A palavra-passe no tipo de corda segura para aceder ao cluster. | No       |
| clusterSshUserName         | O nome de utilizador do SSH liga-se remotamente ao nó do cluster (para Linux). | No       |
| clusterSshPassword         | A palavra-passe no tipo de corda segura ao SSH liga remotamente o nó do cluster (para Linux). | No       |
| scriptAcções | Especifique o script para [personalizações de cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) durante a criação de cluster a pedido. <br />Atualmente, a ferramenta de autoria da Interface de Utilizador da Azure Data Factory suporta especificar apenas 1 ação de script, mas pode ultrapassar esta limitação no JSON (especifique várias ações de script no JSON). | No |


> [!IMPORTANT]
> O HDInsight suporta várias versões de cluster Hadoop que podem ser implementadas. Cada escolha de versão cria uma versão específica da distribuição da Plataforma de Dados Hortonworks (HDP) e um conjunto de componentes que estão contidos nessa distribuição. A lista de versões HDInsight suportadas continua a ser atualizada para fornecer os mais recentes componentes e correções do ecossistema Hadoop. Certifique-se de que refere sempre as informações mais recentes da [versão HDInsight suportada e](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) do Tipo OS para garantir que está a utilizar a versão suportada do HDInsight. 
>
> [!IMPORTANT]
> Atualmente, os serviços ligados ao HDInsight não suportam HBase, Consulta Interativa (Hive LLAP), Storm. 

* exemplo adicionalLinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

#### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

O serviço ligado ao HDInsight a pedido requer uma autenticação principal de serviço para criar clusters HDInsight em seu nome. Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o papel de **Contribuinte** da subscrição ou do grupo de recursos em que o cluster HDInsight é criado. Para passos detalhados, consulte [o portal Use para criar uma aplicação e diretoria de serviço sinuoso e de serviço sintetizado que possa aceder a recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

- ID da Aplicação
- Chave de aplicação 
- ID do inquilino

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **serviçoPrincipalId**  | Especifique a identificação do cliente do pedido.     | Yes      |
| **serviçoPrincipalKey** | Especifique a chave da aplicação.           | Yes      |
| **tenant**              | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Yes      |

#### <a name="advanced-properties"></a>Propriedades Avançadas

Também pode especificar as seguintes propriedades para a configuração granular do cluster HDInsight a pedido.

| Propriedade               | Descrição                              | Necessário |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguração      | Especifica os parâmetros de configuração do núcleo (como no core-site.xml) para a criação do cluster HDInsight. | No       |
| hBaseConfigura     | Especifica os parâmetros de configuração HBase (hbase-site.xml) para o cluster HDInsight. | No       |
| hdfsConfigura      | Especifica os parâmetros de configuração HDFS (hdfs-site.xml) para o cluster HDInsight. | No       |
| hiveConfiguração      | Especifica os parâmetros de configuração da colmeia (hive-site.xml) para o cluster HDInsight. | No       |
| mapReduzir Configuração | Especifica os parâmetros de configuração MapReduce (mapred-site.xml) para o cluster HDInsight. | No       |
| oozieConfiguration     | Especifica os parâmetros de configuração Oozie (oozie-site.xml) para o cluster HDInsight. | No       |
| tempestadeConfiguração     | Especifica os parâmetros de configuração da tempestade (storm-site.xml) para o cluster HDInsight. | No       |
| yarnConfiguração      | Especifica os parâmetros de configuração do fio (arn-site.xml) para o cluster HDInsight. | No       |

* Exemplo – Configuração de cluster HDInsight a pedido com propriedades avançadas

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

#### <a name="node-sizes"></a>Tamanhos do nó
Pode especificar os tamanhos dos nós de cabeça, dados e zookeeper utilizando as seguintes propriedades: 

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Especifica o tamanho do nó da cabeça. O valor padrão é: Standard_D3. Consulte a secção de tamanhos do **nó especificando** para obter mais detalhes. | No       |
| dataNodeSize      | Especifica o tamanho do nó de dados. O valor padrão é: Standard_D3. | No       |
| zookeeperNodeSize | Especifica o tamanho do nó do Guardião do Zoológico. O valor padrão é: Standard_D3. | No       |

* Especificando tamanhos de nó Veja o artigo [tamanhos das máquinas virtuais](../virtual-machines/linux/sizes.md) para valores de cordas que precisa especificar para as propriedades mencionadas na secção anterior. Os valores devem estar em conformidade com os **CMDLETs & APIS** referenciados no artigo. Como pode ver no artigo, o nó de dados do tamanho grande (padrão) tem memória de 7-GB, o que pode não ser bom o suficiente para o seu cenário. 

Se quiser criar nós de cabeça d4 e nós de trabalhador, especifique **Standard_D4** como o valor para as propriedades headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se especificar um valor errado para estas propriedades, poderá receber o seguinte **erro:** Não criou o cluster. Exceção: não foi possível concluir a operação de criação do cluster. A operação falhou com o código "400". Estado do cluster não concluído: "Erro". Mensagem: 'Pré-ClusterCreationValidaçãoFalha'. Ao receber este erro, certifique-se de que está a utilizar o nome **CMDLET & APIS** da tabela no artigo [Tamanhos das Máquinas Virtuais.](../virtual-machines/linux/sizes.md)        

### <a name="bring-your-own-compute-environment"></a>Traga o seu próprio ambiente computacional
Neste tipo de configuração, os utilizadores podem registar um ambiente de computação já existente como um serviço ligado na Data Factory. O ambiente de computação é gerido pelo utilizador e o serviço Data Factory utiliza-o para executar as atividades.

Este tipo de configuração é suportado para os seguintes ambientes computacionais:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço ligado azure HDInsight
Pode criar um serviço ligado ao Azure HDInsight para registar o seu próprio cluster HDInsight com data factory.

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
| tipo              | A propriedade do tipo deve ser definida para **HDInsight**.            | Yes      |
| clusterUri        | O URI do cluster HDInsight.                            | Yes      |
| o nome de utilizador          | Especifique o nome do utilizador a utilizar para se ligar a um cluster HDInsight existente. | Yes      |
| palavra-passe          | Especifique a palavra-passe para a conta de utilizador.                       | Yes      |
| linkedServiceName | Nome do serviço ligado ao Armazenamento Azure que se refere ao armazenamento de blob Azure utilizado pelo cluster HDInsight. <p>Atualmente, não é possível especificar um serviço ligado ao Lago De dados Azure (Gen 2) para esta propriedade. Se o cluster HDInsight tiver acesso à Data Lake Store, poderá aceder a dados no Armazenamento de Lagos De Dados Azure (Gen 2) a partir de scripts Hive/Pig. </p> | Yes      |
| isEspEnabled      | Especifique '*verdadeiro*' se o cluster HDInsight for [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) ativado. Padrão é*falso.* | No       |
| connectVia        | O Tempo de Integração a utilizar para enviar as atividades para este serviço ligado. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. <br />Para o Pacote de Segurança Empresarial (ESP) habilitado o cluster HDInsight utilize um tempo de funcionamento de integração auto-hospedado, que tenha uma linha de visão para o cluster ou deve ser implantado dentro da mesma Rede Virtual que o cluster ESP HDInsight. | No       |

> [!IMPORTANT]
> O HDInsight suporta várias versões de cluster Hadoop que podem ser implementadas. Cada escolha de versão cria uma versão específica da distribuição da Plataforma de Dados Hortonworks (HDP) e um conjunto de componentes que estão contidos nessa distribuição. A lista de versões HDInsight suportadas continua a ser atualizada para fornecer os mais recentes componentes e correções do ecossistema Hadoop. Certifique-se de que refere sempre as informações mais recentes da [versão HDInsight suportada e](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) do Tipo OS para garantir que está a utilizar a versão suportada do HDInsight. 
>
> [!IMPORTANT]
> Atualmente, os serviços ligados ao HDInsight não suportam HBase, Consulta Interativa (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Serviço ligado ao Lote Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode criar um serviço ligado ao Lote Azure para registar um conjunto de máquinas virtuais (VMs) numa fábrica de dados. Pode executar atividade personalizada utilizando o Lote Azure.

Consulte os seguintes artigos se for novo no serviço Azure Batch:

* [O básico do Lote Azure](../batch/batch-technical-overview.md) para uma visão geral do serviço Azure Batch.
* [O cmdlet New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) para criar um [portal Azure](../batch/batch-account-create-portal.md) Batch (ou) Azure para criar a conta Azure Batch utilizando o portal Azure. Consulte [a Utilização do PowerShell para gerir](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) o artigo da Conta de Lote Azure para obter instruções detalhadas sobre a utilização do cmdlet.
* [Cmdlet New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) para criar uma piscina de Lote Azure.

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
| tipo              | A propriedade tipo deve ser definida para **AzureBatch**. | Yes      |
| accountName       | Nome da conta Azure Batch.         | Yes      |
| acessoChave         | Chave de acesso para a conta Azure Batch.  | Yes      |
| batchUri          | URL para a sua conta Azure Batch, em formato de https://*batchaccountname.region*.batch.azure.com. | Yes      |
| nome da piscina          | Nome da piscina de máquinas virtuais.    | Yes      |
| linkedServiceName | Nome do serviço ligado ao Armazenamento Azure associado a este serviço ligado ao Lote Azure. Este serviço ligado é utilizado para encenar ficheiros necessários para executar a atividade. | Yes      |
| connectVia        | O Tempo de Integração a utilizar para enviar as atividades para este serviço ligado. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | No       |

## <a name="azure-machine-learning-studio-linked-service"></a>Serviço ligado ao Estúdio de Aprendizagem automática Azure
Cria um serviço ligado ao Azure Machine Learning Studio para registar um lote de machine learning que marca ponto final numa fábrica de dados.

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
| Tipo                   | A propriedade tipo deve ser definida para: **AzureML**. | Yes                                      |
| mlEndpoint             | O URL de pontuação do lote.                   | Yes                                      |
| apiKey                 | A API do modelo de espaço de trabalho publicado.     | Yes                                      |
| atualizaçãoResourceEndpoint | O URL de recurso de atualização para um ponto final do Serviço Web de Aprendizagem automática Azure usado para atualizar o Serviço Web preditivo com ficheiro de modelo treinado | No                                       |
| serviçoPrincipalId     | Especifique a identificação do cliente do pedido.     | Necessário se atualizar RecursoEndpoint é especificado |
| serviçoPrincipalKey    | Especifique a chave da aplicação.           | Necessário se atualizar RecursoEndpoint é especificado |
| inquilino                 | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Necessário se atualizar RecursoEndpoint é especificado |
| connectVia             | O Tempo de Integração a utilizar para enviar as atividades para este serviço ligado. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | No                                       |

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado à Aprendizagem automática Azure
Cria um serviço ligado à Aprendizagem automática Azure para ligar um espaço de trabalho azure machine learning a uma fábrica de dados.

> [!NOTE]
> Atualmente apenas a autenticação principal do serviço é suportada para o serviço ligado ao Azure Machine Learning.

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
| Tipo                   | A propriedade tipo deve ser definida para: **AzureMLService**. | Yes                                      |
| subscriptionId         | ID de subscrição azure              | Yes                                      |
| resourceGroupName      | name | Yes                                      |
| mlWorkspaceName        | Nome do espaço de trabalho Azure Machine Learning | Yes  |
| serviçoPrincipalId     | Especifique a identificação do cliente do pedido.     | No |
| serviçoPrincipalKey    | Especifique a chave da aplicação.           | No |
| inquilino                 | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Necessário se atualizar RecursoEndpoint é especificado | No |
| connectVia             | O Tempo de Integração a utilizar para enviar as atividades para este serviço ligado. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | No |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado ao Lago de Dados Azure Analytics
Cria um serviço ligado ao **Azure Data Lake Analytics** para ligar um serviço de computação Azure Data Lake Analytics a uma fábrica de dados Azure. A atividade de Data Lake Analytics U-SQL no pipeline refere-se a este serviço ligado. 

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
| tipo                 | A propriedade do tipo deve ser definida para: **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | Nome da conta azure Data Lake Analytics.  | Yes                                      |
| dadosLakeAnalyticsUri | Azure Data Lake Analytics URI.           | No                                       |
| subscriptionId       | ID de subscrição azure                    | No                                       |
| resourceGroupName    | Nome do grupo de recursos do Azure                | No                                       |
| serviçoPrincipalId   | Especifique a identificação do cliente do pedido.     | Yes                                      |
| serviçoPrincipalKey  | Especifique a chave da aplicação.           | Yes                                      |
| inquilino               | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Yes                                      |
| connectVia           | O Tempo de Integração a utilizar para enviar as atividades para este serviço ligado. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | No                                       |



## <a name="azure-databricks-linked-service"></a>Serviço ligado a Azure Databricks
Pode criar um serviço ligado aos **Databricks Azure** para registar o espaço de trabalho databricks que utiliza para executar as cargas de trabalho dos Databricks (caderno, frasco, pitão). 
> [!IMPORTANT]
> Os serviços ligados a databricks suportam [piscinas de Instância.](https://aka.ms/instance-pools) 

### <a name="example---using-new-job-cluster-in-databricks"></a>Exemplo - Utilização de novo cluster de emprego em Databricks

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exemplo - Utilização do cluster interativo existente em Databricks

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
| name                 | Nome do Serviço Vinculado               | Yes   |
| tipo                 | A propriedade tipo deve ser definida para: **Azure Databricks**. | Yes                                      |
| domínio               | Especifique a região de Azure em conformidade com base na região do espaço de trabalho databricks. Exemplo: https://eastus.azuredatabricks.net | Yes                                 |
| acessoToken          | É necessário um sinal de acesso para que a Fábrica de Dados autentique aos Tijolos de Dados Azure. O sinal de acesso tem de ser gerado a partir do espaço de trabalho dos databricks. Passos mais detalhados para encontrar o token de acesso podem ser encontrados [aqui](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Yes                                       |
| clusterid existente    | Identificação do cluster de um aglomerado existente para executar todos os trabalhos nisto. Este deve ser um Cluster Interativo já criado. Pode ser necessário reiniciar manualmente o cluster se parar de responder. Os databricks sugerem que se executam empregos em novos clusters para uma maior fiabilidade. Pode encontrar o Cluster ID de um Cluster Interativo em Databricks espaço de trabalho -> Clusters -> Nome de Cluster Interativo -> Etiquetas de configuração ->. [Mais detalhes](https://docs.databricks.com/user-guide/clusters/tags.html) | No 
| instânciaSPoolId    | Id pool de um pool existente no espaço de trabalho de databricks.  | No  |
| novaClusterVersion    | A versão Spark do cluster. Cria um aglomerado de emprego em tijolos de dados. | No  |
| novoClusterNumOfWorker| Número de nós operários que este aglomerado deveria ter. Um cluster tem um Spark Driver e executores num_workers para um total de num_workers + 1 nós de faísca. Uma corda formatada Int32, como "1" significa numOfWorker é 1 ou "1:10" significa escala automática de 1 como min e 10 como máx.  | No                |
| novoClusterNodeType   | Este campo codifica, através de um único valor, os recursos disponíveis para cada um dos nós de Faísca neste cluster. Por exemplo, os nós spark podem ser provisionados e otimizados para memória ou calcular cargas de trabalho intensivas. Este campo é necessário para um novo cluster                | No               |
| novoClusterSparkConf  | um conjunto de pares de chaves de configuração de faísca sinopse, especificados pelo utilizador. Os utilizadores também podem passar uma série de opções de JVM extra para o condutor e os executores através de spark.driver.extraJavaOptions e spark.executor.extraJavaOptions respectivamente. | No  |
| novos ClusterInitScripts| um conjunto de scripts de inicialização opcionais e definidos pelo utilizador para o novo cluster. Especificando o caminho dBFS para os scripts init. | No  |


## <a name="azure-sql-database-linked-service"></a>Serviço ligado da Base de Dados SQL do Azure

Cria um serviço ligado ao Azure SQL e utiliza-o com a [Atividade do Procedimento Armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado a partir de um pipeline data Factory. Consulte o artigo [do Azure SQL Connector](connector-azure-sql-database.md#linked-service-properties) para mais detalhes sobre este serviço ligado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço ligado ao Armazém de Dados Azure SQL

Cria um serviço ligado ao Armazém de Dados Azure SQL e utiliza-o com a [Atividade do Procedimento Armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado a partir de um pipeline data Factory. Consulte o artigo [do Connector do Armazém de Dados Azure SQL](connector-azure-sql-data-warehouse.md#linked-service-properties) para obter mais detalhes sobre este serviço ligado.

## <a name="sql-server-linked-service"></a>Serviço ligado ao Servidor SQL

Cria um serviço ligado ao SQL Server e utiliza-o com a Atividade do [Procedimento Armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado a partir de um pipeline data Factory. Consulte o artigo do [conector SQL Server](connector-sql-server.md#linked-service-properties) para obter detalhes sobre este serviço ligado.

## <a name="azure-function-linked-service"></a>Serviço ligado à Função Azure

Cria um serviço ligado à Função Azure e utiliza-o com a [atividade da Função Azure](control-flow-azure-function-activity.md) para executar funções Azure num pipeline data Factory. O tipo de devolução da função Azure tem de ser válido `JObject` . (Tenha em mente que [jArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *não* é uma `JObject` .) Qualquer tipo de devolução que não falhe e eleva o conteúdo de `JObject` resposta de erro do utilizador não é um *JObject válido*.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| tipo   | A propriedade tipo deve ser definida para: **AzureFunction** | sim |
| url de aplicativo de função | URL para a App função Azure. O formato é `https://<accountname>.azurewebsites.net` . Este URL é o valor da secção **URL** ao visualizar a sua App de Funções no portal Azure  | sim |
| chave de função | Chave de acesso para a Função Azure. Clique na secção **Gerir** para a respetiva função e copie a Chave de **Função** ou a **tecla anfitrião**. Saiba mais aqui: [Azure Functions HTTP gatilhos e encadernações](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | sim |
|   |   |   |

## <a name="next-steps"></a>Próximos passos

Para obter uma lista das atividades de transformação suportadas pela Azure Data Factory, consulte [dados Transform](transform-data.md).
