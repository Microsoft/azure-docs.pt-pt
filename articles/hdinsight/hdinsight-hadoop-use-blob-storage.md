---
title: Consultar dados a partir do armazenamento do Azure compatível com HDFS - Azure HDInsight
description: Saiba como consultar dados de armazenamento do Azure e o armazenamento do Azure Data Lake para armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 24e0b61dfd9950a5c5990f8341e32d048453c5d6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689573"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Utilizar o armazenamento do Azure com clusters do Azure HDInsight

Para analisar dados num cluster do HDInsight, pode armazenar os dados no [armazenamento do Azure](../storage/common/storage-introduction.md), [do Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[do Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md), ou uma combinação. Estas opções de armazenamento permitem eliminar em segurança os clusters do HDInsight que são utilizados para o cálculo sem perda de dados de utilizador.

Apache Hadoop suporta uma noção do sistema de ficheiros predefinido. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação de cluster do HDInsight, pode especificar um contentor de BLOBs no armazenamento do Azure como o sistema de ficheiros predefinido ou com o HDInsight 3.6, pode selecionar o armazenamento do Azure ou do Azure Data Lake Storage Gen 1 / Azure Data Lake Storage Gen 2 como os arquivos padrão sistema com algumas exceções. Para a Suportabilidade da utilização do Data Lake Storage Gen 1 como tanto o armazenamento predefinido e ligado, consulte [disponibilidade para o cluster de HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Neste artigo, ficará a saber como o Armazenamento do Azure funciona com clusters do HDInsight. Para saber como o Data Lake Storage Gen 1 funciona com clusters do HDInsight, veja [armazenamento do uso do Azure Data Lake com o Azure HDInsight clusters](hdinsight-hadoop-use-data-lake-store.md). Para obter mais informações sobre como criar um cluster do HDInsight, consulte [Apache Hadoop criar clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

O armazenamento do Azure é uma solução de armazenamento para fins gerais robusta que se integra perfeitamente no HDInsight. O HDInsight pode utilizar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido para o cluster. Através de uma interface HDFS (Sistema de Ficheiros Distribuído Hadoop), o conjunto completo de componentes do HDInsight pode operar diretamente em dados estruturados ou não estruturados armazenados como blobs.

> [!WARNING]  
> Tipo de conta de armazenamento **BlobStorage** só pode ser utilizado como armazenamento secundário para clusters do HDInsight.

| Tipo de conta de armazenamento | Serviços suportados | Escalões de desempenho suportados | Suporte de acesso |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (general-purpose v2)  | Blobs     | Standard                    | Armazenamento frequente, esporádica, arquivo\*   |
| Armazenamento (para fins gerais v1)   | Blobs     | Standard                    | N/A                    |
| BlobStorage                    | Blobs     | Standard                    | Armazenamento frequente, esporádica, arquivo\*   |

Não recomendamos a utilização do contentor de blobs predefinido para armazenar dados empresariais. Eliminar o contentor de blobs predefinido depois de cada utilização para reduzir o custo de armazenamento é uma prática recomendada. O contentor predefinido contém o aplicativo e sistema de registos. Certifique-se de que obtém os registos antes de eliminar o contentor.

A partilha de um contentor de blobs como sistema de ficheiros predefinido para múltiplos clusters não é suportada.

> [!NOTE]  
> A camada de acesso de arquivo é uma camada offline que tenha uma latência de obtenção de hora várias e não é recomendada para utilização com o HDInsight. Para obter mais informações, consulte [camada de acesso de arquivo](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Se optar por proteger a sua conta de armazenamento com o **Firewalls e redes virtuais** restrições sobre **redes selecionadas**, certifique-se de que ativar a exceção **permitir fidedigna Microsoft serviços...**  para que o HDInsight pode aceder à sua conta de armazenamento.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight
O diagrama seguinte apresenta uma vista abstrata da arquitetura de armazenamento do HDInsight relativamente à utilização do Armazenamento do Azure:

![Os clusters do Hadoop utilizam a API do HDFS para aceder e armazenar dados estruturados e não estruturados no Armazenamento de Blobs.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Arquitetura do Armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, o HDInsight permite aceder aos dados armazenados no Armazenamento do Azure. A sintaxe é:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Seguem-se algumas considerações sobre a utilização da conta do Azure Storage com os clusters do HDInsight.

* **Contentores nas contas de armazenamento que estão ligadas a um cluster:** Como o nome de conta e chave são associados ao cluster durante a criação, tem acesso total aos blobs desses contentores.

* **Contentores públicos ou blobs públicos em contas de armazenamento que não estão ligados a um cluster:** Tem a permissão só de leitura para os blobs dos contentores.
  
> [!NOTE]  
> Os contentores públicos permitem obter uma lista de todos os blobs disponíveis nesse contentor, bem como os metadados do mesmo. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, consulte [gerir o acesso a contentores e blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Contentores privados em contas de armazenamento que não estão ligados a um cluster:** Não é possível aceder os blobs dos contentores, a menos que define a conta de armazenamento, ao submeter tarefas de WebHCat. Isto é explicado posteriormente neste artigo.

As contas de armazenamento que estão definidas no processo de criação e as chaves são armazenadas no `%HADOOP_HOME%/conf/core-site.xml` em nós do cluster. O comportamento predefinido do HDInsight é utilizar as contas do Storage definidas no ficheiro core-site.xml. Pode modificar esta definição através de [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Várias tarefas de WebHCat, incluindo Apache Hive, MapReduce, transmissão em fluxo do Apache Hadoop e Apache Pig, podem conter uma descrição das contas de armazenamento e de metadados com eles. (Atualmente, isto funciona para o Pig com contas do Storage, mas não com metadados.) Para obter mais informações, consulte [Utilizar um Cluster do HDInsight com Contas do Storage e Metastores Alternativos](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser utilizados para dados estruturados e não estruturados. Os contentores de blobs armazenam dados como pares chave/valor e não existe uma hierarquia de diretórios. No entanto, o caráter de barra (/) pode ser utilizado no nome da chave para fazer com que pareça que um ficheiro está armazenado numa estrutura de diretórios. Por exemplo, a chave de um blob poderá ser *input/log1.txt*. O diretório *input* não existe realmente, mas a presença do caráter de barra no nome da chave cria o aspeto de um caminho de ficheiro.

## <a id="benefits"></a>Vantagens do Armazenamento do Azure
O custo de desempenho implícito de não colocalizar clusters de computação e de recursos de armazenamento é atenuado a propósito, os clusters de cálculo são criados perto dos recursos da conta de armazenamento na região do Azure, onde a rede de alta velocidade permite eficiente para o nós para aceder a dados de armazenamento do Azure de computação.

Existem várias vantagens associadas ao armazenamento de dados no armazenamento do Azure em vez do HDFS:

* **Reutilização de dados e de partilha:** Os dados no HDFS estão localizados dentro do cluster de computação. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. Os dados no armazenamento do Azure podem ser acedidos através das APIs do HDFS ou através da [APIs de REST de armazenamento de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Assim, pode-se utilizar um conjunto maior de ferramentas e aplicações (incluindo outros clusters do HDInsight) para produzir e consumir dados.

* **Arquivamento de dados:** Armazenamento de dados no armazenamento do Azure permite que os clusters do HDInsight utilizados para o cálculo a eliminar em segurança sem perda de dados de utilizador.

* **Custo de armazenamento de dados:** Armazenar dados no DFS a longo prazo é mais dispendioso do que armazenar os dados no armazenamento do Azure, uma vez que o custo de um cluster de cálculo é superior ao custo de armazenamento do Azure. Além disso, uma vez que não é necessário recarregar os dados para cada geração de cluster de cálculo, também reduz os custos do carregamento de dados.

* **Aumento horizontal elástico:** Embora o HDFS forneça um sistema de ficheiros de escalamento horizontal, o dimensionamento é determinado pelo número de nós que criar para o seu cluster. A alteração do dimensionamento pode tornar-se um processo mais complexo do que depender das capacidades de dimensionamento elástico que obtém automaticamente no armazenamento do Azure.

* **Georreplicação:** O armazenamento do Azure pode ser georreplicado. Apesar de esta funcionalidade oferecer recuperação geográfica e redundância de dados, uma ativação pós-falha para a localização georreplicada afeta seriamente o desempenho e pode implicar custos adicionais. Por isso, recomendamos que escolha a georreplicação de forma sensata e apenas se o valor dos dados justificar o custo adicional.

Determinados pacotes e tarefas de MapReduce podem criar resultados intermédios que não quer realmente armazenar no armazenamento do Azure. Nesse caso, pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight utiliza o DFS para vários destes resultados intermédios nas tarefas do Hive e noutros processos.

> [!NOTE]  
> Maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) continuarão a funcionar conforme esperado. Apenas os comandos que são específicos para a implementação nativa do HDFS (que é conhecida como DFS), tal como `fschk` e `dfsadmin`, apresentam um comportamento diferente no armazenamento do Azure.

## <a name="address-files-in-azure-storage"></a>Endereçar ficheiros no armazenamento do Azure
O esquema URI para aceder a ficheiros no armazenamento do Azure a partir do HDInsight é:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

O esquema URI fornece acesso não encriptado (com o prefixo *wasb:*) e acesso encriptado por SSL (com *wasbs*). Recomendamos a utilização de *wasbs* sempre que possível, mesmo ao aceder a dados que se encontrem dentro da mesma região no Azure.

O `<BlobStorageContainerName>` identifica o nome do contentor de BLOBs no armazenamento do Azure.
O `<StorageAccountName>` identifica o nome da conta de armazenamento do Azure. É necessário um nome de domínio completamente qualificado (FQDN).

Se nenhum desses `<BlobStorageContainerName>` nem `<StorageAccountName>` foi especificado, é utilizado o sistema de ficheiros predefinido. Para os ficheiros no sistema de ficheiros predefinido, pode utilizar um caminho relativo ou um caminho absoluto. Por exemplo, é possível fazer referência ao ficheiro *hadoop-mapreduce-examples.jar* incluído nos clusters do HDInsight ao utilizar um dos seguintes procedimentos:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> O nome de ficheiro é `hadoop-examples.jar` em clusters do HDInsight versões 2.1 e 1.6.

O caminho é o nome de caminho de ficheiro ou diretório HDFS. Como os contentores no armazenamento do Azure são arquivos de chave-valor, não existe qualquer sistema de ficheiros hierárquico verdadeiro. Um caráter de barra (/) numa chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob de *hadoop-mapreduce-examples.jar* é:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhece o formato WASB e, em vez disso, espera um formato de caminho básico, tal como `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Contentores de BLOBs
Para utilizar blobs, primeiro crie uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Como parte deste processo, deve especificar uma região do Azure onde será criada a conta de armazenamento. O cluster e a conta do Storage têm de estar alojados na mesma região. A base dados de SQL Server do metastore do Hive e a base de dados do Apache Oozie metastore do SQL Server também devem estar localizados na mesma região.

Independentemente do local onde se encontre, cada blob que criar pertence a um contentor na sua conta do Storage do Azure. Este contentor pode ser um blob existente que tenha sido criado fora do HDInsight ou um contentor criado para um cluster do HDInsight.

O contentor de blobs predefinido armazena informações específicas do cluster, como o histórico de tarefas e os registos. Não partilhe um contentor de blobs predefinido com vários clusters do HDInsight. Isto pode danificar o histórico de tarefas. É recomendável utilizar um contentor diferente para cada cluster e colocar os dados partilhados numa conta do Storage ligada especificada na implementação de todos os clusters relevantes em vez da conta do Storage predefinida. Para obter mais informações sobre como configurar contas do storage ligadas, consulte [clusters do HDInsight criar](hdinsight-hadoop-provision-linux-clusters.md). No entanto, pode reutilizar um contentor de armazenamento predefinido depois de o cluster do HDInsight original ser eliminado. Para clusters do HBase, pode manter os dados e o esquema da tabela do HBase ao criar um novo cluster do HBase com o contentor de blobs predefinido utilizado por um cluster do HBase que foi eliminado.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagir com o armazenamento do Azure

A Microsoft fornece as seguintes ferramentas para trabalhar com o armazenamento do Azure:

| Ferramenta | Linux | SO X | Windows |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Utilizar contas de armazenamento adicionais

Ao criar um cluster do HDInsight, especifica a conta de armazenamento do Azure que quer associar ao mesmo. Além desta conta de armazenamento, pode adicionar mais contas de armazenamento da mesma subscrição do Azure ou de diferentes subscrições do Azure durante o processo de criação ou depois de um cluster ter sido criado. Para obter instruções sobre como adicionar mais contas do Storage, consulte [Create HDInsight clusters (Criar clusters do HDInsight)](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Não é suportado utilizar uma conta de armazenamento adicional numa localização diferente do cluster do HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a utilizar o armazenamento do Azure compatível com HDFS através do HDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução ao armazenamento do Azure Data Lake](../data-lake-store/data-lake-store-get-started-portal.md)
* [Carregar dados para o HDInsight](hdinsight-upload-data.md)
* [Utilizar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar assinaturas de acesso partilhado do Azure Storage para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md)