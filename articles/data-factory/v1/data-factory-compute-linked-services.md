---
title: Ambientes computacionais suportados pela Azure Data Factory
description: Saiba mais sobre ambientes computacionais que pode utilizar em oleodutos Azure Data Factory (como o Azure HDInsight) para transformar ou processar dados.
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
ms.openlocfilehash: 0cc7c3b7d8b364e0bcca671efaff2cf324695428
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79281551"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambientes computacionais suportados pela Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [os serviços ligados à Compute em](../compute-linked-services.md).

Este artigo explica os ambientes computacionais que pode usar para processar ou transformar dados. Também fornece detalhes sobre diferentes configurações (on-demand versus bring-your-own) que a Data Factory suporta quando configura serviços ligados que ligam estes ambientes computacionais a uma fábrica de dados Azure.

A tabela seguinte fornece uma lista de ambientes computacionais que são suportados pela Data Factory, e as atividades que podem ser executadas sobre eles. 

| Ambiente de computação                      | Atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster Azure HDInsight](#azure-hdinsight-on-demand-linked-service) a pedido ou [o seu próprio cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet,](data-factory-use-custom-activities.md) [Colmeia,](data-factory-hive-activity.md) [Porco,](data-factory-pig-activity.md) [MapReduce,](data-factory-map-reduce.md) [Streaming de Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL,](#azure-sql-linked-service)Armazém de [Dados Azure SQL,](#azure-sql-data-warehouse-linked-service) [Servidor SQL](#sql-server-linked-service) | [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versões HDInsight suportadas na Fábrica de Dados
O Azure HDInsight suporta várias versões de cluster Hadoop que pode supor a qualquer momento. Cada versão suportada cria uma versão específica da distribuição da Plataforma de Dados Hortonworks (HDP) e um conjunto de componentes na distribuição. 

A Microsoft atualiza a lista de versões HDInsight suportadas com os mais recentes componentes e correções do ecossistema Hadoop. Para obter informações detalhadas, consulte [as versões HDInsight suportadas](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> A versão 3.3 da HDInsight baseada em Linux foi aposentada a 31 de julho de 2017. Data Factory versão 1 on-demand Os clientes ligados ao HDInsight foram dados até 15 de dezembro de 2017, para testar e atualizar para uma versão posterior do HDInsight. HDInsight baseado no Windows será reformado a 31 de julho de 2018.
>
> 

### <a name="after-the-retirement-date"></a>Após a data da reforma 

Depois de 15 de dezembro de 2017:

- Já não é possível criar clusters de n.º 3.3 (ou versões anteriores) baseados em Linux, utilizando um serviço ligado ao HDInsight a pedido na versão 1 da Data Factory. 
- Se as propriedades [ **osType** e **Versão** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) não forem explicitamente especificadas na definição JSON para um serviço ligado à HDInsight a pedido existente, o valor predefinido é alterado de **Versão=3.1, osType=Windows** para **Versão=\<versão\>padrão HDI mais recente (osType=Linuxhttps://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)**.

Depois de 31 de julho de 2018:

- Já não é possível criar nenhuma versão dos clusters HDInsight baseados no Windows utilizando um serviço ligado ao HDInsight a pedido na versão 1 da Data Factory. 

### <a name="recommended-actions"></a>Ações recomendadas 

- Para garantir que pode utilizar os mais recentes componentes e correções do ecossistema Hadoop, atualize as propriedades [ **osType** e **Versão** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) da versão afetada da versão 1 da Fábrica de Dados afetada, a nº 1 a pedido do HDInsight, para as mais recentes versões HDInsight baseadas em Linux (HDInsight 3.6). 
- Antes de 15 de dezembro de 2017, teste data factory versão 1 Hive, Pig, MapReduce e Hadoop atividades de streaming que referenciam o serviço ligado afetado. Certifique-se de que são compatíveis com os novos valores predefinidos **do osType** e **da Versão** **(Versão=3.6**, **osType=Linux)** ou com a versão explícita HDInsight e o tipo OS a que está a atualizar. 
  Para saber mais sobre compatibilidade, consulte [Migrate de um cluster HDInsight baseado no Windows para um cluster baseado em Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) e [Quais são os componentes hadoop e versões disponíveis com hDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) 
- Para continuar a utilizar um serviço ligado à HDInsight da versão 1 da Data Factory para criar clusters HDInsight baseados no Windows, definir explicitamente **o osType** para **o Windows** antes de 15 de dezembro de 2017. Recomendamos que emigra para clusters HDInsight baseados em Linux antes de 31 de julho de 2018. 
- Se estiver a utilizar um serviço ligado ao HDInsight a pedido para executar a versão 1 DotNet Custom Activity, atualize a definição JSON de Atividade Personalizada do DotNet para, em vez disso, utilizar um serviço ligado ao Lote Azure. Para mais informações, consulte [Utilize atividades personalizadas num pipeline data factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Se utilizar o seu próprio cluster HDInsight ligado ao seu próprio cluster na versão 1 da Data Factory ou num serviço ligado ao HDInsight próprio e a pedido na Azure Data Factory, não é necessária qualquer ação. Nesses cenários, a mais recente política de suporte de versão dos clusters HDInsight já está aplicada. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente de computação a pedido
Numa configuração a pedido, a Data Factory gere totalmente o ambiente de computação. Data Factory cria automaticamente o ambiente de computação antes de um trabalho ser submetido para processamento de dados. Quando o trabalho está terminado, a Data Factory remove o ambiente de cálculo. 

Você pode criar um serviço ligado para um ambiente de computação a pedido. Utilize o serviço ligado para configurar o ambiente de computação e para controlar as configurações granulares para execução de emprego, gestão de clusters e ações de enfeitamento de botas.

> [!NOTE]
> Atualmente, a configuração on-demand é suportada apenas para clusters HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço ligado do Azure HDInsight a pedido
A Data Factory pode criar automaticamente um cluster HDInsight baseado no Windows ou baseado em Linux para processar dados. O cluster é criado na mesma região que a conta de armazenamento que está associada ao cluster. Utilize a propriedade JSON **linkedServiceName** para criar o cluster.

Note os seguintes *pontos-chave* sobre o serviço ligado ao HDInsight a pedido:

* O cluster HDInsight a pedido não aparece na sua subscrição Azure. O serviço Data Factory gere o cluster HDInsight a pedido em seu nome.
* Os registos de empregos que são executados num cluster HDInsight a pedido são copiados para a conta de armazenamento que está associada ao cluster HDInsight. Para aceder a estes registos, no portal Azure, aceda ao painel Dedetalhes de **Execução** de Atividades. Para mais informações, consulte [monitor e gerencie os gasodutos.](data-factory-monitor-manage-pipelines.md)
* É cobrado apenas pelo tempo em que o cluster HDInsight está a funcionar.

> [!IMPORTANT]
> Normalmente demora *20 minutos* ou mais para fornecer um cluster HDInsight a pedido.
>
> 

### <a name="example"></a>Exemplo
O seguinte JSON define um serviço ligado hDInsight baseado em Linux. A Data Factory cria automaticamente um cluster HDInsight *baseado em Linux* quando processa uma fatia de dados. 

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
> O cluster HDInsight cria um *recipiente predefinido* no armazenamento Azure Blob que especifica na propriedade ligada ao **Serviço de Serviços** Da JSON. Por design, o HDInsight não elimina este recipiente quando o cluster é eliminado. Num serviço ligado ao HDInsight a pedido, é criado um cluster HDInsight sempre que uma fatia precisa de ser processada, a menos que exista um cluster ao vivo existente **(timeToLive).** O cluster é eliminado quando o processamento está terminado. 
>
> À medida que mais fatias são processadas, você vê muitos recipientes no seu armazenamento Blob. Se não precisar dos recipientes para trabalhos de resolução de problemas, é melhor eliminar os contentores para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adf<your Data Factory name>-<linked service name>-<date and time>`. Pode utilizar uma ferramenta como o [Microsoft Storage Explorer](https://storageexplorer.com/) para eliminar contentores no armazenamento blob.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Necessário |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | Defina a propriedade do tipo para **HDInsightOnDemand**. | Sim      |
| clusterSize                  | O número de pessoas operárias e de dados no cluster. O cluster HDInsight é criado com 2 nós de cabeça, além do número de nós de trabalhador que especifica para esta propriedade. Os nós são de tamanho Standard_D3, que tem 4 núcleos. Um aglomerado de nó de 4 trabalhadores leva 24 núcleos (4\*\*4 = 16 núcleos para nós operários, mais 2 4 = 8 núcleos para nós de cabeça). Para mais detalhes sobre o Standard_D3 nível, consulte [Os clusters Hadoop baseados em Linux em HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Sim      |
| timeToLive                   | O tempo de inatividade permitido para o cluster HDInsight a pedido. Especifica quanto tempo o cluster HDInsight a pedido permanece vivo quando uma execução de atividade está terminada, se não houver outros trabalhos ativos no cluster.<br /><br />Por exemplo, se uma execução de atividade demorar 6 minutos e o **tempo ToLive** estiver definido para 5 minutos, o cluster permanece vivo durante 5 minutos após os 6 minutos de processamento da atividade. Se outra execução de atividade for executada na janela de 6 minutos, é processada pelo mesmo aglomerado.<br /><br />Criar um cluster HDInsight a pedido é uma operação dispendiosa (pode demorar algum tempo). Utilize esta definição conforme necessário para melhorar o desempenho de uma fábrica de dados, reutilizando um cluster HDInsight a pedido.<br /><br />Se definir o valor **tempoToLive** para **0**, o cluster é eliminado assim que a atividade termina. No entanto, se definir um valor elevado, o cluster pode permanecer inativo, resultando desnecessariamente em custos elevados. É importante definir o valor apropriado com base nas suas necessidades.<br /><br />Se o valor **timeToLive** for devidamente definido, vários oleodutos podem partilhar a instância do cluster HDInsight a pedido. | Sim      |
| versão                      | A versão do cluster HDInsight. Para versões HDInsight permitidas, consulte [versões HDInsight suportadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Se este valor não for especificado, a versão padrão mais recente do [HDI](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) é utilizada. | Não       |
| linkedServiceName            | O serviço ligado ao Armazenamento Azure deve ser utilizado pelo cluster a pedido para armazenar e processar dados. O cluster HDInsight é criado na mesma região que esta conta de armazenamento.<p>Atualmente, não é possível criar um cluster HDInsight a pedido que utiliza a Azure Data Lake Store como armazenamento. Se pretender armazenar os dados do resultado do processamento do HDInsight na Data Lake Store, utilize a Copy Activity para copiar os dados do armazenamento blob para data lake store. </p> | Sim      |
| nomes adicionais LinkedServiceNames | Especifica contas de armazenamento adicionais para o serviço ligado ao HDInsight. Data Factory regista as contas de armazenamento em seu nome. Estas contas de armazenamento devem estar na mesma região que o cluster HDInsight. O cluster HDInsight é criado na mesma região que a conta de armazenamento especificada pela propriedade **linkedServiceName.** | Não       |
| osType                       | O tipo de sistema operativo. Os valores permitidos são **Linux** e **Windows.** Se este valor não for especificado, **o Linux** é usado.  <br /><br />Recomendamos vivamente a utilização de clusters HDInsight baseados em Linux. A data de aposentadoria do HDInsight no Windows é 31 de julho de 2018. | Não       |
| hcatalogLinkedServiceName    | O nome do serviço ligado Azure SQL que aponta para a base de dados HCatalog. O cluster HDInsight a pedido é criado utilizando a base de dados SQL como metaloja. | Não       |

#### <a name="example-linkedservicenames-json"></a>Exemplo: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriedades avançadas
Para a configuração granular do cluster HDInsight a pedido, pode especificar as seguintes propriedades:

| Propriedade               | Descrição                              | Necessário |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguração      | Especifica os parâmetros de configuração do núcleo (core-site.xml) para a criação do cluster HDInsight. | Não       |
| hBaseConfigura     | Especifica os parâmetros de configuração HBase (hbase-site.xml) para o cluster HDInsight. | Não       |
| hdfsConfigura      | Especifica os parâmetros de configuração HDFS (hdfs-site.xml) para o cluster HDInsight. | Não       |
| hiveConfiguração      | Especifica os parâmetros de configuração da Colmeia (hive-site.xml) para o cluster HDInsight. | Não       |
| mapReduzir Configuração | Especifica os parâmetros de configuração MapReduce (mapred-site.xml) para o cluster HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração Oozie (oozie-site.xml) para o cluster HDInsight. | Não       |
| tempestadeConfiguração     | Especifica os parâmetros de configuração da tempestade (storm-site.xml) para o cluster HDInsight. | Não       |
| yarnConfiguração      | Especifica os parâmetros de configuração do ARN (arn-site.xml) para o cluster HDInsight. | Não       |

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

### <a name="node-sizes"></a>Tamanhos do nó
Para especificar o tamanho dos nós da cabeça, dados e ZooKeeper, utilize as seguintes propriedades: 

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Define o tamanho do nó da cabeça. O valor predefinido é **Standard_D3**. Para mais detalhes, consulte [Especificar tamanhos do nó](#specify-node-sizes). | Não       |
| dataNodeSize      | Define o tamanho do nó de dados. O valor predefinido é **Standard_D3**. | Não       |
| zookeeperNodeSize | Define o tamanho do nó ZooKeeper. O valor predefinido é **Standard_D3**. | Não       |

#### <a name="specify-node-sizes"></a>Especificar tamanhos dos nó
Para obter valores de cadeia que deve especificar para as propriedades descritas na secção anterior, consulte [tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md). Os valores devem estar em conformidade com os cmdlets e APIs referenciados em [tamanhos de máquinas virtuais](../../virtual-machines/linux/sizes.md). O tamanho do nó de dados Grande (padrão) tem 7 GB de memória. Isto pode não ser suficiente para o seu cenário. 

Se pretender criar nós de cabeça d4 e nós de trabalhador, especifique **Standard_D4** como o valor para as propriedades headNodeSize e **dataNodeSize:** **headNodeSize** 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se definir um valor incorreto para estas propriedades, poderá ver a seguinte mensagem:

  Falhou em criar cluster. Exceção: não foi possível concluir a operação de criação do cluster. A operação falhou com o código "400". Estado do cluster não concluído: "Erro". Mensagem: 'Pré-ClusterCreationValidaçãoFalha'. 
  
Se vir esta mensagem, certifique-se de que está a utilizar os nomes cmdlet e API da tabela em [tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Atualmente, a Data Factory não suporta clusters HDInsight que usam data lake store como a loja principal. Utilize o Armazenamento Azure como a loja principal para clusters HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Traga o seu próprio ambiente computacional
Pode registar um ambiente de computação existente como um serviço ligado na Data Factory. Gere-se o ambiente da computação. O serviço Data Factory utiliza o ambiente de computação para executar atividades.

Este tipo de configuração é suportado para os seguintes ambientes computacionais:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Base de Dados Azure SQL, Armazém de Dados Azure SQL, Servidor SQL

## <a name="azure-hdinsight-linked-service"></a>Serviço ligado azure HDInsight
Pode criar um serviço ligado ao HDInsight para registar o seu próprio cluster HDInsight com data factory.

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
| tipo              | Defina a propriedade do tipo para **HDInsight**. | Sim      |
| clusterUri        | O URI do cluster HDInsight.        | Sim      |
| o nome de utilizador          | O nome da conta de utilizador a utilizar para se ligar a um cluster HDInsight existente. | Sim      |
| palavra-passe          | A palavra-passe para a conta de utilizador.   | Sim      |
| linkedServiceName | O nome do serviço ligado ao armazenamento que se refere ao armazenamento Blob utilizado pelo cluster HDInsight. <p>Atualmente, não é possível especificar um serviço ligado à Data Lake Store para esta propriedade. Se o cluster HDInsight tiver acesso à Data Lake Store, poderá aceder a dados na Data Lake Store a partir de scripts Hive ou Pig. </p> | Sim      |

## <a name="azure-batch-linked-service"></a>Serviço ligado ao Lote Azure
Pode criar um serviço ligado ao Lote para registar um conjunto de lotes de máquinas virtuais (VMs) numa fábrica de dados. Pode executar as atividades personalizadas da Microsoft .NET utilizando o Batch ou o HDInsight.

Se for novo a utilizar o serviço Batch:

* Saiba mais sobre [o básico do Lote Azure.](../../batch/batch-technical-overview.md)
* Conheça o cmdlet [New-AzureBatchAccount.](https://msdn.microsoft.com/library/mt125880.aspx) Utilize este cmdlet para criar uma conta Batch. Ou, pode criar a conta Batch utilizando o [portal Azure](../../batch/batch-account-create-portal.md). Para obter informações detalhadas sobre a utilização do cmdlet, consulte [A Utilização da PowerShell para gerir uma conta De lote](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Saiba mais sobre o cmdlet [New-AzureBatchPool.](https://msdn.microsoft.com/library/mt125936.aspx) Utilize este cmdlet para criar uma piscina de lote.

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

Para a **propriedade accountName,** apêndice **\< . nome\> ** da região para o nome da sua conta de lote. Por exemplo:

```json
"accountName": "mybatchaccount.eastus"
```

Outra opção é fornecer o ponto final do **loteUri.** Por exemplo:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Necessário |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | Desloque a propriedade do tipo para **AzureBatch**. | Sim      |
| accountName       | O nome da conta Batch.         | Sim      |
| acessoChave         | A chave de acesso para a conta Batch.  | Sim      |
| nome da piscina          | O nome da piscina de VMs.    | Sim      |
| linkedServiceName | O nome do serviço ligado ao armazenamento que está associado a este serviço ligado ao Lote. Este serviço ligado é utilizado para encenar ficheiros que são necessários para executar a atividade e para armazenar registos de execução de atividade. | Sim      |

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado à Aprendizagem automática Azure
Pode criar um serviço ligado ao Machine Learning para registar um lote de machine learning que marca ponto final numa fábrica de dados.

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
| Tipo       | Desloque a propriedade do tipo para **AzureML**. | Sim      |
| mlEndpoint | O URL de pontuação do lote.                   | Sim      |
| apiKey     | A API do modelo de espaço de trabalho publicado.     | Sim      |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado ao Lago de Dados Azure Analytics
Pode criar um serviço ligado ao Data Lake Analytics para ligar um serviço de computação Data Lake Analytics a uma fábrica de dados Azure. A atividade de Data Lake Analytics U-SQL no pipeline refere-se a este serviço ligado. 

O quadro seguinte descreve as propriedades genéricas utilizadas na definição JSON:

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| tipo                 | Detete a propriedade tipo para **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | O nome da conta Data Lake Analytics.  | Sim                                      |
| dadosLakeAnalyticsUri | O Data Lake Analytics URI.           | Não                                       |
| subscriptionId       | O ID de assinatura Azure.                    | Não<br /><br />(Se não especificado, a subscrição da fábrica de dados é utilizada.) |
| resourceGroupName    | O nome do grupo de recursos Azure.                | Não<br /><br /> (Se não especificado, o grupo de recursos da fábrica de dados é utilizado.) |

### <a name="authentication-options"></a>Opções de autenticação
Para o seu serviço de ligação Data Lake Analytics, pode escolher entre autenticação utilizando um diretor de serviço ou uma credencial de utilizador.

#### <a name="service-principal-authentication-recommended"></a>Autenticação do principal de serviço (recomendado)
Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD). Em seguida, conceda ao Azure AD acesso à Data Lake Store. Para obter passos detalhados, consulte a [autenticação serviço-a-serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:
* ID da aplicação
* Chave de aplicação 
* ID do inquilino

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| serviçoPrincipalId  | A identificação do cliente do pedido.     | Sim      |
| serviçoPrincipalKey | A chave da aplicação.           | Sim      |
| inquilino              | As informações do inquilino (nome de domínio ou ID do inquilino) onde o seu pedido está localizado. Para obter esta informação, passe o rato no canto superior direito do portal Azure. | Sim      |

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
Para autenticação credencial do utilizador para Data Lake Analytics, especifique as seguintes propriedades:

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| autorização | Em Data Factory Editor, selecione o botão **Autorizar.** Introduza a credencial que atribui o URL de autorização autogerado a esta propriedade. | Sim      |
| sessionId     | O ID da sessão da OAuth da sessão de autorização da OAuth. Cada id da sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Data Factory Editor. | Sim      |

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

#### <a name="token-expiration"></a>Expiração do token
O código de autorização que gerou selecionando o botão **Autorizar** expira após um intervalo definido. 

Pode ver a seguinte mensagem de erro quando o token de autenticação expirar: 

  Erro de operação credencial: invalid_grant - AADSTS70002: Erro validando credenciais. AADSTS70008: A subvenção de acesso fornecida é expirada ou revogada. Identificação do traço: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

O quadro seguinte mostra expirações por tipo de conta de utilizador: 

| Tipo de utilizador                                | Expira depois                            |
| :--------------------------------------- | :--------------------------------------- |
| Contas de utilizador que *não* são geridas pela Azure AD (Hotmail, Live, e assim por diante) | 12 horas.                                 |
| Contas de utilizador que *são* geridas pela Azure AD | 14 dias após a última corrida. <br /><br />90 dias, se uma fatia baseada num serviço ligado baseado em OAuth funciona pelo menos uma vez a cada 14 dias. |

Para evitar ou resolver este erro, reautorize selecionando o botão **Autorizar** quando o token expirar. Em seguida, recolocar o serviço ligado. Também pode gerar valores para as propriedades **sessionId** e **autorização** programáticamente utilizando o seguinte código:

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

Para mais detalhes sobre as classes Data Factory que são usadas neste exemplo de código, consulte:
* [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Adicione uma referência à microsoft.identityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe **WindowsFormsWebAuthenticationDialog.** 

## <a name="azure-sql-linked-service"></a>Serviço ligado SQL do Azure
Pode criar um serviço ligado à SQL e usá-lo com a [Atividade do Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline data Factory. Para mais informações, consulte [o conector Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço ligado ao Armazém de Dados Azure SQL
Pode criar um serviço ligado ao SQL Data Warehouse e utilizá-lo com a [Atividade do Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline data Factory. Para mais informações, consulte o [conector Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Serviço ligado ao Servidor SQL
Pode criar um serviço ligado ao SQL Server e usá-lo com a [Atividade do Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline data Factory. Para mais informações, consulte o [conector SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

