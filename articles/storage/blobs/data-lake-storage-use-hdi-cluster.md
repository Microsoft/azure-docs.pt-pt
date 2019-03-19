---
title: Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure
description: Saiba como consultar dados da geração 2 de armazenamento do Azure Data Lake e armazenar os resultados da sua análise.
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 15d9210e356da6d7757582dc4dad25cb2882ccfa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003233"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure

Para analisar dados num cluster do HDInsight, pode armazenar os dados em qualquer combinação de armazenamento de Blobs do Azure, armazenamento de Blobs do Azure com o Azure Data Lake Storage Gen2 ativada ou Gen1 de armazenamento do Azure Data Lake. Todas as opções de armazenamento permitem eliminar em segurança os clusters do HDInsight que são utilizados para o cálculo sem perda de dados de utilizador.

O Hadoop suporta uma noção do sistema de ficheiros predefinido. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação de cluster do HDInsight, pode especificar um contentor de BLOBs no armazenamento do Azure ou o espaço de nomes hierárquico oferecidos pela geração 2 de armazenamento do Data Lake como o sistema de ficheiros predefinido. Em alternativa com o HDInsight 3.5, pode selecionar um contentor ou o espaço de nomes hierárquico como o sistema de ficheiros predefinido com algumas exceções.

Neste artigo, ficará a saber como a geração 2 de armazenamento do Data Lake funciona com clusters do HDInsight. Para obter mais informações sobre como criar um cluster do HDInsight, consulte [configurar o HDInsight clusters com o armazenamento do Azure Data Lake com o Hadoop, Spark, Kafka e muito mais](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

O armazenamento do Azure é uma solução de armazenamento para fins gerais robusta que se integra perfeitamente no HDInsight. HDInsight pode utilizar o armazenamento do Azure Data Lake como sistema de ficheiros predefinido para o cluster. Através de uma interface de system (HDFS) de ficheiros distribuído Hadoop, o conjunto completo de componentes no HDInsight pode operar diretamente em ficheiros no armazenamento do Azure Data Lake.

Não recomendamos que utilize o sistema de ficheiros predefinido para armazenar dados empresariais. A eliminar o sistema de ficheiros predefinido após cada utilização para reduzir o custo de armazenamento é uma boa prática. Observação desse contentor predefinido contém o aplicativo e sistema de registos. Certifique-se de que obtém os registos antes de eliminar o contentor.

A partilha de um sistema de ficheiros para múltiplos clusters não é suportada.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama seguinte apresenta uma vista abstrata da arquitetura de armazenamento do HDInsight relativamente à utilização do Armazenamento do Azure:

![Os clusters do Hadoop utilizam a API do HDFS para aceder e armazenar dados estruturados e não estruturados no Armazenamento de Blobs.](./media/data-lake-storage-use-hdi-cluster/HDI.ABFS.Arch.png "Arquitetura do Armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<NAME_NODE_HOST>/<PATH>

Além disso, o HDInsight permite-lhe aceder aos dados armazenados no armazenamento do Azure Data Lake. A sintaxe é:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

Aqui estão algumas considerações sobre quando utilizar uma conta de armazenamento do Azure com clusters do HDInsight.

* **Ficheiros em contas de armazenamento que estão ligados a um cluster** tem o nome da conta e a chave associada ao cluster durante a criação. Esta configuração dá-lhe acesso total aos ficheiros no sistema de arquivos.

* **Ficheiros públicos em contas de armazenamento que não estão ligados a um cluster** expor permissões só de leitura aos ficheiros no sistema de arquivos.
  
  > [!NOTE]
  > Sistemas de ficheiros públicos permitem-lhe obter uma lista de todos os ficheiros disponíveis no sistema de ficheiros e aceder aos metadados. Sistemas de ficheiros públicos permitem-lhe aceder aos ficheiros apenas se souber o URL exato. Para obter mais informações, consulte [restringir o acesso a contentores e blobs](https://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (as regras para contentores e blobs funcionam da mesma tona ficheiros e o sistema de ficheiros).
 
* **Sistemas de ficheiros privados em contas de armazenamento que não estão ligados a um cluster** não permitir aceder a ficheiros no sistema de arquivos, a menos que define a conta de armazenamento, ao submeter as tarefas de WebHCat. Razões para esta restrição são explicados neste artigo.

As contas de armazenamento que estão definidas no processo de criação e as chaves são armazenadas no *%HADOOP_HOME%/conf/core-site.xml* em nós do cluster. O comportamento padrão do HDInsight é utilizar as contas de armazenamento definidas no *core-site* ficheiro. Pode modificar esta definição através do [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md)

Várias tarefas de WebHCat, incluindo Hive, MapReduce, transmissão em fluxo do Hadoop e Pig, podem conter uma descrição das contas do Storage e metadados. (Essa abordagem funciona atualmente para o Pig com contas de armazenamento, mas não para os metadados.) Para obter mais informações, consulte [Utilizar um Cluster do HDInsight com Contas do Storage e Metastores Alternativos](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

## <a id="benefits"></a>Vantagens do Armazenamento do Azure

O custo de desempenho implícito de não ter clusters de cálculo e recursos de armazenamento colocalizados é atenuado pela forma como os clusters de cálculo são criados perto dos recursos da conta de armazenamento na região do Azure, onde a rede de alta velocidade permite que os nós de cálculo acedam aos dados de armazenamento do Azure de forma eficiente.

Existem várias vantagens associadas ao armazenamento de dados no armazenamento do Azure em vez do HDFS:

* **Reutilização de dados e de partilha:** Os dados no HDFS estão localizados dentro do cluster de computação. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. É possível aceder aos dados no armazenamento do Azure através das APIs do HDFS ou através das [APIs REST do Armazenamento de Blobs][blob-storage-restAPI]. Assim, pode-se utilizar um conjunto maior de ferramentas e aplicações (incluindo outros clusters do HDInsight) para produzir e consumir dados.

* **Arquivamento de dados:** Armazenamento de dados no armazenamento do Azure permite que os clusters do HDInsight utilizados para o cálculo a eliminar em segurança sem perda de dados de utilizador.

* **Custo de armazenamento de dados:** Armazenamento de dados do hdfs nativo a longo prazo é mais dispendioso do que armazenar os dados no armazenamento do Azure, uma vez que o custo de um cluster de cálculo é superior ao custo de armazenamento do Azure. Além disso, uma vez que não é necessário recarregar os dados para cada geração de cluster de cálculo, também reduz os custos do carregamento de dados.

* **Aumento horizontal elástico:** Embora o HDFS forneça um sistema de ficheiros de escalamento horizontal, o dimensionamento é determinado pelo número de nós que criar para o seu cluster. A alteração do dimensionamento pode tornar-se um processo mais complexo do que depender das capacidades de dimensionamento elástico que obtém automaticamente no armazenamento do Azure.

* **Georreplicação:** Os dados de armazenamento do Azure podem ser georreplicado. Embora esta capacidade dá-lhe recuperação geográfica e redundância de dados, suporte gravemente a uma ativação pós-falha para a localização georreplicada afeta seriamente o desempenho e pode resultar em custos adicionais. Por conseguinte, escolha a georreplicação com cuidado e apenas se o valor dos dados é justificar o custo adicional.

* **Gestão de ciclo de vida de dados:** Todos os dados em qualquer sistema de arquivos passa pelo seu próprio ciclo de vida. Dados, muitas vezes, começa a ser muito valiosa e acedidos com frequência, faz a transição para sendo menos importantes e que necessitam de menos acesso e, por fim, necessita de arquivo ou de eliminação. Armazenamento do Azure fornece a criação de camadas de dados e o ciclo de vida de políticas de gestão que a camada de dados adequadamente para seu estágio do ciclo de vida.

Determinados pacotes e tarefas de MapReduce podem criar resultados intermédios que não quer realmente armazenar no armazenamento do Azure. Nesse caso, pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight utiliza a implementação nativa do HDFS (que é conhecida como DFS) para vários destes resultados intermédios nas tarefas do Hive e outros processos.

> [!NOTE]
> Maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) continuarão a funcionar conforme esperado. Apenas os comandos que são específicos para o DFS, tal como `fschk` e `dfsadmin`, apresentam um comportamento diferente no armazenamento do Azure.

## <a name="create-a-data-lake-storage-file-system"></a>Criar um sistema de ficheiros de armazenamento do Data Lake

Para utilizar o sistema de ficheiros, primeiro crie uma [conta de armazenamento do Azure][azure-storage-create]. Como parte deste processo, deve especificar uma região do Azure onde a conta de armazenamento é criada. O cluster e a conta do Storage têm de estar alojados na mesma região. A base de dados do SQL Server do metastore do Hive e a base de dados do SQL Server do metastore do Oozie também têm de estar localizadas na mesma região.

Independentemente de onde se encontre, cada blob que criar pertence a um sistema de ficheiros na sua conta de armazenamento.

O sistema de ficheiros de geração 2 de armazenamento do Data Lake predefinido armazena informações específicas do cluster, como o histórico de tarefas e registos. Não partilhe um sistema de ficheiros de geração 2 de armazenamento do Data Lake predefinida com vários clusters do HDInsight. Isto pode danificar o histórico de tarefas. Recomenda-se para utilizar um sistema de ficheiros diferentes para cada cluster e colocar os dados partilhados numa conta do storage ligada especificada na implementação de todos os clusters relevantes em vez da conta de armazenamento predefinida. Para obter mais informações sobre como configurar contas do Storage ligadas, consulte [Create HDInsight clusters (Criar clusters do HDInsight)][hdinsight-creation]. No entanto, pode reutilizar um sistema de ficheiros de armazenamento predefinido depois do cluster do HDInsight original foi eliminado. Para clusters do HBase, pode manter os dados e esquema de tabela do HBase ao criar um novo cluster do HBase com o contentor de BLOBs predefinido utilizado por um cluster do HBase eliminado que tenha sido eliminado.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Quando criar um cluster do HDInsight a partir do Portal, tem as opções (conforme mostrado na captura de ecrã seguinte) para fornecer os detalhes da conta de armazenamento. Também pode especificar se pretende que uma conta de armazenamento adicional associada ao cluster e, se assim for, escolher a partir de qualquer uma das opções de armazenamento disponível para armazenamento adicional.

![origem de dados de criação do HDInsight hadoop](./media/data-lake-storage-use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> Não é suportado utilizar uma conta de armazenamento adicional numa localização diferente do cluster do HDInsight.

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se [instalado e configurado o Azure PowerShell][powershell-install], pode utilizar o código seguinte prompt do PowerShell do Azure para criar uma conta de armazenamento e um contentor:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

```azurepowershell
$SubscriptionID = "<Your Azure Subscription ID>"
$ResourceGroupName = "<New Azure Resource Group Name>"
$Location = "westus2"

$StorageAccountName = "<New Azure Storage Account Name>"
$containerName = "<New Azure Blob Container Name>"

Select-AzSubscription -SubscriptionId $SubscriptionID

# Create resource group
New-AzResourceGroup -name $ResourceGroupName -Location $Location

# Create default storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
  -Name $StorageAccountName `
  -Location $Location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True

# Create default blob containers
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
$destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
New-AzStorageContainer -Name $containerName -Context $destContext
```

> [!NOTE]
> A criação de um contentor é sinônimo de criação de um sistema de ficheiros no Data Lake Storage Gen2.

### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Se tiver [instalado e configurado a CLI do Azure](../../cli-install-nodejs.md), pode utilizar o seguinte comando para uma conta do Storage e um contentor.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

É-lhe pedido que especifique a região geográfica na qual foi criada a conta de armazenamento. Crie a conta de armazenamento na mesma região que pretenda criar o seu cluster do HDInsight.

Assim que a conta do Storage for criada, utilize o seguinte comando para obter as chaves da conta do Storage:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Para criar um contentor, utilize o seguinte comando:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> A criação de um contentor é sinônimo de criação de um sistema de ficheiros no Data Lake Storage Gen2.

## <a name="address-files-in-azure-storage"></a>Endereçar ficheiros no armazenamento do Azure

O esquema URI para aceder a ficheiros no armazenamento do Azure a partir do HDInsight é:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>

O esquema URI fornece acesso não encriptado (com o *abfs:* prefixo) e acesso encriptado por SSL (com *abfss*). Recomendamos que utilize *abfss* sempre que possível, até mesmo ao aceder a dados que se encontrem dentro da mesma região no Azure.

* &lt;FILE_SYSTEM_NAME&gt; identifica o caminho do sistema de arquivos de geração 2 de armazenamento do Data Lake.
* &lt;Nome_conta&gt; identifica o nome da conta de armazenamento do Azure. É necessário um nome de domínio completamente qualificado (FQDN).

    Se os valores para &lt;FILE_SYSTEM_NAME&gt; nem &lt;nome_conta&gt; foram especificadas, o sistema de ficheiros predefinido é utilizado. Para os ficheiros no sistema de ficheiros predefinido, pode utilizar um caminho relativo ou um caminho absoluto. Por exemplo, o *hadoop-mapreduce-examples* ficheiro que é fornecido com clusters do HDInsight pode ser referido utilizando um dos seguintes caminhos:
    
        abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> O nome de ficheiro é *hadoop-examples.jar* nos clusters do HDInsight versões 2.1 e 1.6.

* O &lt;caminho&gt; é o nome de caminho de ficheiro ou diretório HDFS.

> [!NOTE]
> Ao trabalhar com arquivos fora do HDInsight, a maioria dos utilitários não reconhece os ABFS formatar e em vez disso, esperar que um formato de caminho básico, tal como `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Utilizar contas de armazenamento adicionais

Ao criar um cluster do HDInsight, especifica a conta de armazenamento do Azure que quer associar ao mesmo. Além desta conta de armazenamento, pode adicionar mais contas de armazenamento da mesma subscrição do Azure ou de diferentes subscrições do Azure durante o processo de criação ou depois de um cluster ter sido criado. Para obter instruções sobre como adicionar mais contas do Storage, consulte [Create HDInsight clusters (Criar clusters do HDInsight)](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> Não é suportado utilizar uma conta de armazenamento adicional numa localização diferente do cluster do HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a utilizar o armazenamento do Azure compatível com HDFS através do HDInsight. Esta abordagem permite-lhe criar soluções de aquisição de dados dimensionáveis e de longo prazo, arquivamento e utilizar o HDInsight para aceder às informações contidas armazenado estruturados e dados não estruturados.

Para obter mais informações, consulte:

* [O driver de sistema de ficheiros do ABFS Hadoop para a geração 2 de armazenamento do Azure Data Lake](data-lake-storage-abfs-driver.md)
* [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Ingestão de dados na geração 2 de armazenamento do Azure Data Lake, utilizar o distcp](data-lake-storage-use-distcp.md)

[powershell-install]: /powershell/azure/install-az-ps
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/data-lake-storage-use-hdi-cluster/HDI.PowerShell.BlobCommands.png
