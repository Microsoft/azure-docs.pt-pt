---
title: Ambientes computativos suportados pela versão 1 da Azure Data Factory
description: Saiba mais sobre ambientes computativos que pode utilizar nos oleodutos Azure Data Factory (como a Azure HDInsight) para transformar ou processar dados.
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
ms.openlocfilehash: 2250a2565aa4fbab32daed54830fb701a3a2a1ac
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636192"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Ambientes computativos suportados pela versão 1 da Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [os serviços ligados ao Compute em](../compute-linked-services.md).

Este artigo explica os ambientes computacional que pode usar para processar ou transformar dados. Também fornece detalhes sobre diferentes configurações (a pedido versus bring-your-your-own) que a Data Factory suporta quando configura serviços ligados que ligam estes ambientes computativos a uma fábrica de dados Azure.

A tabela seguinte fornece uma lista de ambientes computativos que são suportados pela Data Factory, e as atividades que podem ser executadas neles. 

| Ambiente de computação                      | Atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster Azure HDInsight a pedido](#azure-hdinsight-on-demand-linked-service) ou [o seu próprio cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet,](data-factory-use-custom-activities.md) [Colmeia,](data-factory-hive-activity.md) [Porco,](data-factory-pig-activity.md) [MapReduce,](data-factory-map-reduce.md) [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (clássico)](#azure-machine-learning-studio-classic-linked-service) | [Atividades de estúdio (clássicos): Execução de Lote e Recurso de Atualização](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure Synapse Analytics,](#azure-synapse-analytics-linked-service) [SQL Server](#sql-server-linked-service) | [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versões HDInsight suportadas na Data Factory
O Azure HDInsight suporta várias versões de cluster Hadoop que pode implementar a qualquer momento. Cada versão suportada cria uma versão específica da distribuição da Plataforma de Dados Hortonworks (HDP) e um conjunto de componentes na distribuição. 

A Microsoft atualiza a lista de versões HDInsight suportadas com os mais recentes componentes e correções do ecossistema Hadoop. Para obter informações [detalhadas, consulte as versões HDInsight suportadas](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> A versão 3.3 do Linux baseado em HDInsight foi reformada a 31 de julho de 2017. Os clientes de serviços ligados hdinsight ligados à data factory foram prestados até 15 de dezembro de 2017, para testar e atualizar para uma versão posterior do HDInsight. O HDInsight baseado no Windows será retirado a 31 de julho de 2018.
>
> 

### <a name="after-the-retirement-date"></a>Após a data da reforma 

Depois de 15 de dezembro de 2017:

- Já não é possível criar clusters HDInsight baseados em Linux na versão 3.3 (ou versões anteriores) utilizando um serviço ligado a hdinsight a pedido na versão 1 da Data Factory. 
- Se as propriedades [ **do osType** e **da versão**](#azure-hdinsight-on-demand-linked-service) não forem explicitamente especificadas na definição JSON para um serviço ligado à base de dados 1 a pedido da Data Factory, o valor predefinido é alterado de **Versão=3.1, osType=Windows** para **Versão= \<latest HDI default version\> https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) (, osType=Linux** .

Depois de 31 de julho de 2018:

- Já não é possível criar qualquer versão de clusters HDInsight baseados no Windows utilizando um serviço ligado a hdInsight a pedido na versão 1 da Data Factory. 

### <a name="recommended-actions"></a>Ações recomendadas

- Para garantir que pode utilizar os mais recentes componentes e correções do ecossistema Hadoop, atualize as propriedades do [ **osType** e **versão**](#azure-hdinsight-on-demand-linked-service) da versão 1 da Data Factory afetada, as definições de serviço on-demand HDInsight para as versões HDInsight baseadas em Linux (HDInsight 3.6). 
- Antes de 15 de dezembro de 2017, teste data factory versão 1 Hive, Pig, MapReduce e Hadoop que referenciam o serviço ligado afetado. Certifique-se de que são compatíveis com os novos valores padrão **de osType** e **versão** **(Versão=3.6** , **osType=Linux)** ou com a versão hdInsight explícita e o tipo de SO a que está a atualizar. 
  Para saber mais sobre compatibilidade, consulte [Migrar de um cluster HDInsight baseado no Windows para um cluster baseado em Linux](../../hdinsight/index.yml) e [quais são os componentes e versões Hadoop disponíveis com HDInsight?](../../hdinsight/hdinsight-component-versioning.md) 
- Para continuar a utilizar um serviço ligado à hdinsight da versão 1 da Data Factory para criar clusters HDInsight baseados no Windows, defina explicitamente **os OsType** para **o Windows** antes de 15 de dezembro de 2017. Recomendamos que emigre para os clusters HDInsight baseados em Linux antes de 31 de julho de 2018. 
- Se estiver a utilizar um serviço ligado a HDInsight a pedido para executar a versão 1 DotNet Custom Activity, atualize a definição JSON de Atividade Personalizada DotNet para, em vez disso, utilizar um serviço ligado a Azure Batch. Para obter mais informações, consulte [utilizar as atividades personalizadas num oleoduto da Data Factory.](./data-factory-use-custom-activities.md) 

> [!Note]
> Se utilizar o dispositivo existente, ligado ao cluster HDInsight na versão 1 da Data Factory ou um serviço de ligação HDInsight a pedido e próprio na Azure Data Factory, não é necessária qualquer ação. Nesses cenários, a mais recente política de suporte de versão dos clusters HDInsight já está aplicada. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente de computação a pedido
Numa configuração a pedido, a Data Factory gere totalmente o ambiente computacional. A Data Factory cria automaticamente o ambiente computacional antes de ser submetido um trabalho para processamento de dados. Quando o trabalho estiver concluído, a Data Factory remove o ambiente computacional. 

Pode criar um serviço ligado para um ambiente de computação a pedido. Utilize o serviço ligado para configurar o ambiente computacional e controlar as configurações granulares para a execução de empregos, gestão de clusters e ações de armadilhagem de botas.

> [!NOTE]
> Atualmente, a configuração on-demand é suportada apenas para clusters HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço ligado do Azure HDInsight a pedido
A Data Factory pode criar automaticamente um cluster HDInsight baseado no Windows ou no Linux para processamento de dados. O cluster é criado na mesma região que a conta de armazenamento que está associada ao cluster. Utilize a propriedade JSON **linkedServiceName** para criar o cluster.

Note os *key* seguintes pontos-chave sobre o serviço ligado a hdinsight a pedido:

* O cluster HDInsight a pedido não aparece na sua subscrição Azure. O serviço Data Factory gere o cluster HDInsight a pedido em seu nome.
* Os registos de empregos que são executados num cluster HDInsight a pedido são copiados para a conta de armazenamento que está associada ao cluster HDInsight. Para aceder a estes registos, no portal Azure, aceda ao painel **De Detalhes do Funcionamento** da Atividade. Para obter mais informações, consulte [Monitor e gere os oleodutos.](data-factory-monitor-manage-pipelines.md)
* É cobrado apenas pelo tempo que o cluster HDInsight está a funcionar.

> [!IMPORTANT]
> Normalmente leva *20 minutos* ou mais para providenciar um cluster HDInsight a pedido.
>
> 

### <a name="example"></a>Exemplo
O JSON seguinte define um serviço hdinsight baseado na procura do Linux. A Data Factory cria automaticamente um cluster HDInsight *baseado em Linux* quando processa uma fatia de dados. 

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
> O cluster HDInsight cria um *recipiente predefinido* no armazenamento Azure Blob que especifica na propriedade JSON **linkedServiceName.** Por design, o HDInsight não apaga este recipiente quando o cluster é eliminado. Num serviço on-demand HDInsight ligado, um cluster HDInsight é criado sempre que uma fatia precisa de ser processada, a menos que exista um cluster vivo existente **(timeToLive).** O cluster é eliminado quando o processamento está terminado. 
>
> À medida que mais fatias são processadas, você vê muitos recipientes no seu armazenamento Blob. Se não precisar dos contentores para resolver problemas, é melhor apagar os contentores para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf<your Data Factory name>-<linked service name>-<date and time>`. Pode utilizar uma ferramenta como [o Microsoft Azure Storage Explorer](https://storageexplorer.com/) para eliminar contentores no armazenamento Blob.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Obrigatório |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | Defina a propriedade tipo para **HDInsightOnDemand** . | Sim      |
| clusterSize                  | O número de trabalhadores e nó de dados no cluster. O cluster HDInsight é criado com 2 nós de cabeça, além do número de nós de trabalhadores que especifica para esta propriedade. Os nós são de tamanho Standard_D3, que tem 4 núcleos. Um aglomerado de nó de 4 trabalhadores leva 24 núcleos \* (4 4 = 16 núcleos para os nóns dos trabalhadores, mais 2 \* 4 = 8 núcleos para os nosdes da cabeça). Para obter mais informações sobre o nível Standard_D3, consulte [os clusters Hadoop baseados em Linux em HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Sim      |
| timeToLive                   | O tempo de inatividade permitido para o cluster HDInsight a pedido. Especifica quanto tempo o cluster HDInsight on-demand permanece vivo quando uma atividade é terminada, se não houver outros empregos ativos no cluster.<br /><br />Por exemplo, se uma corrida de atividade demorar 6 minutos e **o tempoToLive** estiver definido para 5 minutos, o cluster permanece vivo durante 5 minutos após os 6 minutos de processamento da atividade. Se outra atividade for executada na janela de 6 minutos, é processada pelo mesmo cluster.<br /><br />A criação de um cluster HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo). Utilize esta definição conforme necessário para melhorar o desempenho de uma fábrica de dados reutilizando um cluster HDInsight a pedido.<br /><br />Se definir o **valor do tempoToLive** para **0,** o cluster é eliminado assim que a atividade termina. No entanto, se definir um valor elevado, o cluster pode ficar inativo, resultando desnecessariamente em custos elevados. É importante definir o valor apropriado com base nas suas necessidades.<br /><br />Se o valor **timeToLive** for definido adequadamente, vários oleodutos podem partilhar o caso do cluster HDInsight a pedido. | Sim      |
| versão                      | A versão do cluster HDInsight. Para ver versões HDInsight permitidas, consulte [ver versões HDInsight suportadas](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Se este valor não for especificado, a [versão padrão mais recente](../../hdinsight/hdinsight-component-versioning.md) do HDI é utilizada. | Não       |
| linkedServiceName            | O serviço ligado ao Armazenamento Azure deve ser utilizado pelo cluster a pedido para armazenar e processar dados. O cluster HDInsight é criado na mesma região que esta conta de armazenamento.<p>Atualmente, não é possível criar um cluster HDInsight a pedido que utilize a Azure Data Lake Store como armazenamento. Se pretender armazenar os dados dos resultados do processamento de HDInsight na Data Lake Store, utilize a Copy Activity para copiar os dados do armazenamento blob para a Data Lake Store. </p> | Sim      |
| adicionalLinkedServiceNames | Especifica contas de armazenamento adicionais para o serviço ligado ao HDInsight. A Data Factory regista as contas de armazenamento em seu nome. Estas contas de armazenamento devem situar-se na mesma região que o cluster HDInsight. O cluster HDInsight é criado na mesma região que a conta de armazenamento especificada pela propriedade **linkedServiceName.** | Não       |
| osTipos                       | O tipo de sistema operativo. Os valores permitidos são **Linux** e **Windows.** Se este valor não for especificado, **o Linux** é utilizado.  <br /><br />Recomendamos vivamente a utilização de clusters HDInsight baseados em Linux. A data de reforma do HDInsight no Windows é 31 de julho de 2018. | Não       |
| hcatalogLinkedServiceName    | O nome do serviço ligado Azure SQL que aponta para a base de dados HCatalog. O cluster HDInsight a pedido é criado utilizando a base de dados SQL como a metástaria. | Não       |

#### <a name="example-linkedservicenames-json"></a>Exemplo: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriedades avançadas
Para a configuração granular do cluster HDInsight a pedido, pode especificar as seguintes propriedades:

| Propriedade               | Descrição                              | Obrigatório |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração do núcleo (core-site.xml) para a criação do cluster HDInsight. | Não       |
| hConfiguration base     | Especifica os parâmetros de configuração HBase (hbase-site.xml) para o cluster HDInsight. | Não       |
| hdfsConfiguration      | Especifica os parâmetros de configuração HDFS (hdfs-site.xml) para o cluster HDInsight. | Não       |
| hiveConfiguration      | Especifica os parâmetros de configuração da Colmeia (hive-site.xml) para o cluster HDInsight. | Não       |
| mapReduceConfiguration | Especifica os parâmetros de configuração mapReduce (mapred-site.xml) para o cluster HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração Oozie (oozie-site.xml) para o cluster HDInsight. | Não       |
| tempestadeConfiguration     | Especifica os parâmetros de configuração da tempestade (storm-site.xml) para o cluster HDInsight. | Não       |
| yarConfiguration      | Especifica os parâmetros de configuração yarn (yarn-site.xml) para o cluster HDInsight. | Não       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo: Configuração de cluster HDInsight a pedido com propriedades avançadas

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

### <a name="node-sizes"></a>Tamanhos dos nómdais
Para especificar o tamanho dos nosmos de cabeça, dados e ZooKeeper, utilize as seguintes propriedades: 

| Propriedade          | Descrição                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Define o tamanho do nó da cabeça. O valor **predefinido** é Standard_D3 . Para mais [informações, consulte especificar os tamanhos dos nós.](#specify-node-sizes) | Não       |
| dataNodeSize      | Define o tamanho do nó de dados. O valor **predefinido** é Standard_D3 . | Não       |
| zookeeperNodeSize | Define o tamanho do nó ZooKeeper. O valor **predefinido** é Standard_D3 . | Não       |

#### <a name="specify-node-sizes"></a>Especificar tamanhos de nó
Para valores de cadeia que deve especificar para as propriedades descritas na secção anterior, consulte os [tamanhos da máquina virtual](../../virtual-machines/sizes.md). Os valores devem estar em conformidade com os cmdlets e APIs referenciados em [tamanhos de máquina virtual](../../virtual-machines/sizes.md). O tamanho do nó de dados grande (padrão) tem 7 GB de memória. Isto pode não ser suficiente para o seu cenário. 

Se pretender criar nos nó de cabeça de tamanho D4 e nosdes de trabalhador, especifique **Standard_D4** como o valor para o **headNodeSize** e **dadosNodeSize** propriedades: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se definir um valor incorreto para estas propriedades, poderá ver a seguinte mensagem:

  Falhou em criar um aglomerado. Exceção: não foi possível concluir a operação de criação do cluster. A operação falhou com o código "400". Estado do cluster não concluído: "Erro". Mensagem: 'PreClusterCreationValidationFailure'. 
  
Se vir esta mensagem, certifique-se de que está a utilizar os nomes cmdlet e API da tabela em [tamanhos de máquina virtual](../../virtual-machines/sizes.md).  

> [!NOTE]
> Atualmente, a Data Factory não suporta clusters HDInsight que usam a Data Lake Store como a loja principal. Utilize o Azure Storage como loja primária para clusters HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Traga o seu próprio ambiente de computação
Pode registar um ambiente de computação existente como um serviço ligado na Data Factory. Gere o ambiente computacional. O serviço Data Factory utiliza o ambiente computacional para executar atividades.

Este tipo de configuração é suportado para os seguintes ambientes computativos:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (clássico)
* Azure Data Lake Analytics
* Azure SQL Database, Azure Synapse Analytics (anteriormente SQL Data Warehouse), SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço ligado a Azure HDInsight
Pode criar um serviço ligado a HDInsight para registar o seu próprio cluster HDInsight com data factory.

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
| Propriedade          | Descrição                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | Defina a propriedade tipo para **HDInsight** . | Sim      |
| clusterUri        | O URI do cluster HDInsight.        | Sim      |
| nome de utilizador          | O nome da conta de utilizador a utilizar para ligar a um cluster HDInsight existente. | Sim      |
| palavra-passe          | A palavra-passe para a conta de utilizador.   | Sim      |
| linkedServiceName | O nome do serviço ligado ao armazenamento que se refere ao armazenamento Blob utilizado pelo cluster HDInsight. <p>Atualmente, não é possível especificar um serviço ligado à Data Lake Store para esta propriedade. Se o cluster HDInsight tiver acesso à Data Lake Store, poderá aceder a dados na Data Lake Store a partir de scripts de Colmeia ou Porco. </p> | Sim      |

## <a name="azure-batch-linked-service"></a>Serviço ligado a Azure Batch
Pode criar um serviço ligado ao Batch para registar um conjunto de máquinas virtuais (VMs) numa fábrica de dados. Pode executar as atividades personalizadas do Microsoft .NET utilizando o Batch ou o HDInsight.

Se for novo a utilizar o serviço Batch:

* Saiba mais sobre [o básico do Azure Batch](../../azure-sql/database/sql-database-paas-overview.md).
* Saiba mais sobre o [cmdlet New-AzureBatchAccount.](/previous-versions/azure/mt125880(v=azure.100)) Utilize este cmdlet para criar uma conta Batch. Ou, pode criar a conta Batch utilizando o [portal Azure](../../batch/batch-account-create-portal.md). Para obter informações detalhadas sobre a utilização do cmdlet, consulte [utilizar o PowerShell para gerir uma conta Batch](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account).
* Saiba mais sobre o [cmdlet New-AzureBatchPool.](/previous-versions/azure/mt125936(v=azure.100)) Utilize este cmdlet para criar uma piscina de lote.

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

Para a **contaName** propriedade, apêndice **\<region name\> .** para o nome da sua conta de lote. Por exemplo:

```json
"accountName": "mybatchaccount.eastus"
```

Outra opção é fornecer o ponto final **do batchUri.** Por exemplo:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | Desa estabaja a propriedade tipo **para AzureBatch** . | Sim      |
| accountName       | O nome da conta do Lote.         | Sim      |
| acessoKey         | A chave de acesso para a conta Batch.  | Sim      |
| poolName          | O nome da piscina de VMs.    | Sim      |
| linkedServiceName | O nome do serviço ligado ao armazenamento que está associado a este serviço ligado ao Lote. Este serviço ligado é utilizado para a realização de ficheiros que são necessários para executar a atividade e para armazenar registos de execução de atividades. | Sim      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (clássico) serviço ligado
Pode criar um serviço ligado ao Azure Machine Learning Studio (clássico) para registar um ponto final de pontuação de lote (clássico) para uma fábrica de dados.

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
| Propriedade   | Descrição                              | Obrigatório |
| ---------- | ---------------------------------------- | -------- |
| Tipo       | Desa esta medida a **AzureML.** | Sim      |
| mlEndpoint | O URL de pontuação do lote.                   | Sim      |
| apiKey     | A API do modelo de espaço de trabalho publicado.     | Sim      |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado a Azure Data Lake Analytics
Pode criar um serviço de data lake analytics ligado para ligar um serviço de computação Data Lake Analytics a uma fábrica de dados Azure. A atividade U-SQL do Data Lake Analytics no oleoduto refere-se a este serviço ligado. 

A tabela a seguir descreve as propriedades genéricas que são utilizadas na definição JSON:

| Propriedade                 | Descrição                              | Obrigatório                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| tipo                 | Desaperte a propriedade tipo para **AzureDataLakeAnalytics** . | Sim                                      |
| accountName          | O nome da conta Data Lake Analytics.  | Sim                                      |
| dataLakeAnalyticsUri | O Data Lake Analytics URI.           | Não                                       |
| subscriptionId       | O ID de assinatura Azure.                    | Não<br /><br />(Se não for especificada, a subscrição da fábrica de dados é utilizada.) |
| resourceGroupName    | O nome do grupo de recursos Azure.                | Não<br /><br /> (Se não for especificado, é utilizado o grupo de recursos da fábrica de dados.) |

### <a name="authentication-options"></a>Opções de autenticação
Para o seu serviço ligado ao Data Lake Analytics, pode escolher entre autenticação utilizando um titular de serviço ou uma credencial de utilizador.

#### <a name="service-principal-authentication-recommended"></a>Autenticação principal do serviço (recomendado)
Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD). Em seguida, conceda ao Azure AD acesso à Data Lake Store. Para etapas detalhadas, consulte [a autenticação do Serviço ao Serviço.](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:
* ID da Aplicação
* Chave de aplicação 
* ID do inquilino

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Obrigatório |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | A identificação do cliente da candidatura.     | Sim      |
| servicePrincipalKey | A chave da inscrição.           | Sim      |
| inquilino              | A informação do inquilino (nome de domínio ou ID do inquilino) onde o seu pedido está localizado. Para obter esta informação, paire o rato no canto superior direito do portal Azure. | Sim      |

**Exemplo: Autenticação principal do serviço**
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

#### <a name="user-credential-authentication"></a>Autenticação credencial do utilizador
Para a autenticação credencial do utilizador para data lake analytics, especifique as seguintes propriedades:

| Propriedade          | Descrição                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| autorização | No Data Factory Editor, selecione o botão **Authorize.** Introduza a credencial que atribui o URL de autorização autogerado a esta propriedade. | Sim      |
| sessionId     | O ID da sessão OAuth da sessão de autorização da OAuth. Cada ID de sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Editor de Fábrica de Dados. | Sim      |

**Exemplo: Autenticação credencial do utilizador**
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

#### <a name="token-expiration"></a>Expiração simbólica
O código de autorização gerado selecionando o botão **Authorize** expira após um intervalo definido. 

Pode ver a seguinte mensagem de erro quando o token de autenticação expirar: 

  Erro de funcionamento credencial: invalid_grant - AADSTS70002: Credenciais de validação de erros. AADSTS70008: A subvenção de acesso prestada é caducada ou revogada. ID de vestígio: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31

A tabela a seguir mostra expirações por tipo de conta de utilizador: 

| Tipo de utilizador                                | Expira depois                            |
| :--------------------------------------- | :--------------------------------------- |
| Contas de utilizador que *não* são geridas pela Azure AD (Hotmail, Live, e assim por diante) | 12 horas.                                 |
| Contas de utilizador que *são* geridas pela Azure AD | 14 dias após a última corrida. <br /><br />90 dias, se uma fatia baseada num serviço ligado à OAuth for executado pelo menos uma vez a cada 14 dias. |

Para evitar ou resolver este erro, reautorize selecionando o botão **'Autorizo'** quando o token expirar. Em seguida, recolocar o serviço ligado. Também pode gerar valores para as propriedades **de sessãoId** e **de autorização** programáticamente utilizando o seguinte código:

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

Para obter mais informações sobre as classes data factory que são utilizadas neste exemplo de código, consulte:
* [Classe AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [Classe AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [Aula de AutorizaçãoSsionGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

Adicione uma referência a Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe **WindowsFormsWebAuthenticationDialog.** 

## <a name="azure-sql-linked-service"></a>Serviço ligado SQL do Azure
Pode criar um serviço ligado ao SQL e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um oleoduto data factory. Para mais informações, consulte [o conector Azure SQL.](data-factory-azure-sql-connector.md#linked-service-properties)

## <a name="azure-synapse-analytics-linked-service"></a>Serviço Azure Synapse Analytics ligado
Pode criar um serviço Azure Synapse Analytics ligado e usá-lo com a [Atividade do Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um oleoduto data factory. Para mais informações, consulte [o conector Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Serviço ligado ao SQL Server
Pode criar um serviço ligado ao SQL Server e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline da Data Factory. Para obter mais informações, consulte [o conector SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).