---
title: Consultar dados a partir do armazenamento do Azure compatível com HDFS - Azure HDInsight
description: Saiba como consultar dados do armazenamento do Azure e Azure Data Lake Storage para armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936863"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Utilizar o armazenamento do Azure com clusters do Azure HDInsight

Para analisar dados no cluster HDInsight, você pode armazenar os dados no [armazenamento do Azure](../storage/common/storage-introduction.md), [Azure data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)ou uma combinação. Essas opções de armazenamento permitem que você exclua com segurança os clusters HDInsight que são usados para computação sem perder dados do usuário.

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blob no armazenamento do Azure como o sistema de arquivos padrão ou com o HDInsight 3,6, você pode selecionar o armazenamento do Azure ou Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 como os arquivos padrão sistema com algumas exceções. Para obter suporte ao usar Data Lake Storage Gen 1 como o armazenamento padrão e vinculado, consulte [disponibilidade para o cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Neste artigo, ficará a saber como o Armazenamento do Azure funciona com clusters do HDInsight. Para saber como Data Lake Storage Gen 1 funciona com clusters HDInsight, confira [usar o Azure data Lake Storage com clusters do Azure hdinsight](hdinsight-hadoop-use-data-lake-store.md). Para obter mais informações sobre como criar um cluster HDInsight, consulte [criar Apache Hadoop clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

O armazenamento do Azure é uma solução de armazenamento para fins gerais robusta que se integra perfeitamente no HDInsight. O HDInsight pode utilizar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido para o cluster. Através de uma interface HDFS (Sistema de Ficheiros Distribuído Hadoop), o conjunto completo de componentes do HDInsight pode operar diretamente em dados estruturados ou não estruturados armazenados como blobs.

> [!IMPORTANT]  
> O tipo de conta de armazenamento **BlobStorage** pode ser usado apenas como armazenamento secundário para clusters HDInsight.

| Tipo de conta de armazenamento | Serviços suportados | Níveis de desempenho com suporte | Níveis de acesso com suporte |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (general-purpose v2)  | Blob     | Standard                    | Quente, frio e arquivo morto\*   |
| Armazenamento (uso geral v1)   | Blob     | Standard                    | N/A                    |
| BlobStorage                    | Blob     | Standard                    | Quente, frio e arquivo morto\*   |

Não recomendamos que você use o contêiner de blob padrão para armazenar dados corporativos. Eliminar o contentor de blobs predefinido depois de cada utilização para reduzir o custo de armazenamento é uma prática recomendada. O contêiner padrão contém logs do aplicativo e do sistema. Certifique-se de que obtém os registos antes de eliminar o contentor.

Não há suporte para o compartilhamento de um contêiner de blob como o sistema de arquivos padrão para vários clusters.

> [!NOTE]  
> A camada de acesso de arquivamento é uma camada offline que tem uma latência de recuperação de várias horas e não é recomendada para uso com o HDInsight. Para obter mais informações, consulte [arquivar camada de acesso](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Se você optar por proteger sua conta de armazenamento com as restrições de **redes virtuais e firewalls** em **redes selecionadas**, certifique-se de habilitar a exceção **permitir serviços confiáveis da Microsoft...** para que o HDInsight possa acessar seu armazenamento considerar.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama seguinte apresenta uma vista abstrata da arquitetura de armazenamento do HDInsight relativamente à utilização do Armazenamento do Azure:

![Os clusters do Hadoop usam a API do HDFS para acessar e armazenar dados no armazenamento de BLOBs](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "Arquitetura de armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, o HDInsight permite aceder aos dados armazenados no Armazenamento do Azure. A sintaxe é:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Seguem-se algumas considerações sobre a utilização da conta do Azure Storage com os clusters do HDInsight.

* **Contêineres nas contas de armazenamento que estão conectadas a um cluster:** Como o nome da conta e a chave estão associados ao cluster durante a criação, você tem acesso completo aos BLOBs nesses contêineres.

* **Contêineres públicos ou BLOBs públicos em contas de armazenamento que não estão conectadas a um cluster:** Você tem permissão somente leitura para os BLOBs nos contêineres.
  
> [!NOTE]  
> Os contentores públicos permitem obter uma lista de todos os blobs disponíveis nesse contentor, bem como os metadados do mesmo. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, consulte [gerenciar o acesso a contêineres e blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Contêineres privados em contas de armazenamento que não estão conectadas a um cluster:** Não é possível acessar os BLOBs nos contêineres, a menos que você defina a conta de armazenamento ao enviar trabalhos do WebHCat. Isto é explicado posteriormente neste artigo.

As contas de armazenamento que são definidas no processo de criação e suas chaves são armazenadas `%HADOOP_HOME%/conf/core-site.xml` no nos nós do cluster. O comportamento predefinido do HDInsight é utilizar as contas do Storage definidas no ficheiro core-site.xml. Você pode modificar essa configuração usando o [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos do WebHCat, incluindo Apache Hive, MapReduce, Apache Hadoop streaming e Apache Pig, podem conter uma descrição de contas de armazenamento e metadados com eles. (Atualmente, isto funciona para o Pig com contas do Storage, mas não com metadados.) Para obter mais informações, consulte [Utilizar um Cluster do HDInsight com Contas do Storage e Metastores Alternativos](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser utilizados para dados estruturados e não estruturados. Os contêineres de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caráter de barra (/) pode ser utilizado no nome da chave para fazer com que pareça que um ficheiro está armazenado numa estrutura de diretórios. Por exemplo, a chave de um blob poderá ser *input/log1.txt*. O diretório *input* não existe realmente, mas a presença do caráter de barra no nome da chave cria o aspeto de um caminho de ficheiro.

## <a id="benefits"></a>Vantagens do Armazenamento do Azure

O custo de desempenho implícito de não colocar clusters de computação e recursos de armazenamento é mitigado pela maneira como os clusters de computação são criados próximos aos recursos da conta de armazenamento na região do Azure, em que a rede de alta velocidade torna a ti eficiente para o nós de computação para acessar os dados dentro do armazenamento do Azure.

Existem várias vantagens associadas ao armazenamento de dados no armazenamento do Azure em vez do HDFS:

* **Reutilização e compartilhamento de dados:** Os dados no HDFS estão localizados dentro do cluster de computação. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. Os dados no armazenamento do Azure podem ser acessados por meio das APIs HDFS ou por meio das [APIs REST do armazenamento de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Assim, pode-se utilizar um conjunto maior de ferramentas e aplicações (incluindo outros clusters do HDInsight) para produzir e consumir dados.

* **Arquivamento de dados:** Armazenar dados no armazenamento do Azure permite que os clusters HDInsight usados para computação sejam excluídos com segurança sem perder dados do usuário.

* **Custo de armazenamento de dados:** O armazenamento de dados no DFS por longo prazo é mais dispendioso do que armazenar os dados no armazenamento do Azure, pois o custo de um cluster de computação é maior do que o custo do armazenamento do Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você também está salvando os custos de carregamento de dados.

* **Expansão elástica:** Embora o HDFS forneça um sistema de arquivos expandido, a escala é determinada pelo número de nós que você cria para o cluster. A alteração do dimensionamento pode tornar-se um processo mais complexo do que depender das capacidades de dimensionamento elástico que obtém automaticamente no armazenamento do Azure.

* **Replicação geográfica:** O armazenamento do Azure pode ser replicado geograficamente. Apesar de esta funcionalidade oferecer recuperação geográfica e redundância de dados, uma ativação pós-falha para a localização georreplicada afeta seriamente o desempenho e pode implicar custos adicionais. Por isso, recomendamos que escolha a georreplicação de forma sensata e apenas se o valor dos dados justificar o custo adicional.

Determinados pacotes e tarefas de MapReduce podem criar resultados intermédios que não quer realmente armazenar no armazenamento do Azure. Nesse caso, pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight utiliza o DFS para vários destes resultados intermédios nas tarefas do Hive e noutros processos.

> [!NOTE]  
> A maioria dos comandos do HDFS ( `ls`por `copyFromLocal` exemplo `mkdir`, e) ainda funciona como esperado. Somente os comandos que são específicos para a implementação nativa do HDFS (que é conhecida como DFS), `fschk` como e `dfsadmin`, mostram um comportamento diferente no armazenamento do Azure.

## <a name="address-files-in-azure-storage"></a>Endereçar ficheiros no armazenamento do Azure

O esquema URI para aceder a ficheiros no armazenamento do Azure a partir do HDInsight é:

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

O esquema URI fornece acesso não encriptado (com o prefixo *wasb:* ) e acesso encriptado por SSL (com *wasbs*). Recomendamos a utilização de *wasbs* sempre que possível, mesmo ao aceder a dados que se encontrem dentro da mesma região no Azure.

O `<BlobStorageContainerName>` identifica o nome do contêiner de blob no armazenamento do Azure.
O `<StorageAccountName>` identifica o nome da conta de armazenamento do Azure. É necessário um nome de domínio completamente qualificado (FQDN).

Se nem `<StorageAccountName>` nem tiver sido especificado, o sistema de arquivos padrão será usado. `<BlobStorageContainerName>` Para os ficheiros no sistema de ficheiros predefinido, pode utilizar um caminho relativo ou um caminho absoluto. Por exemplo, é possível fazer referência ao ficheiro *hadoop-mapreduce-examples.jar* incluído nos clusters do HDInsight ao utilizar um dos seguintes procedimentos:

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> O nome do arquivo `hadoop-examples.jar` está em clusters do HDInsight versões 2,1 e 1,6.

O caminho é o nome do caminho do HDFS do arquivo ou diretório. Como os contêineres no armazenamento do Azure são repositórios de chave-valor, não há nenhum sistema de arquivos hierárquico verdadeiro. Um caráter de barra (/) numa chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob de *hadoop-mapreduce-examples.jar* é:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhece o formato WASB e, em vez disso, espera um formato de caminho básico, tal como `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="blob-containers"></a>Contêineres de BLOB

Para usar BLOBs, você primeiro cria uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Como parte deste processo, deve especificar uma região do Azure onde será criada a conta de armazenamento. O cluster e a conta do Storage têm de estar alojados na mesma região. O banco de dados do metastore do Hive SQL Server e do Apache Oozie metastore SQL Server também devem estar localizados na mesma região.

Independentemente do local onde se encontre, cada blob que criar pertence a um contentor na sua conta do Storage do Azure. Este contentor pode ser um blob existente que tenha sido criado fora do HDInsight ou um contentor criado para um cluster do HDInsight.

O contentor de blobs predefinido armazena informações específicas do cluster, como o histórico de tarefas e os registos. Não partilhe um contentor de blobs predefinido com vários clusters do HDInsight. Isto pode danificar o histórico de tarefas. É recomendável usar um contêiner diferente para cada cluster e colocar dados compartilhados em uma conta de armazenamento vinculada especificada na implantação de todos os clusters relevantes em vez da conta de armazenamento padrão. Para obter mais informações sobre como configurar contas de armazenamento vinculadas, consulte [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). No entanto, pode reutilizar um contentor de armazenamento predefinido depois de o cluster do HDInsight original ser eliminado. Para clusters HBase, você pode realmente manter o esquema de tabela do HBase e os dados criando um novo cluster HBase usando o contêiner de blob padrão que é usado por um cluster HBase que foi excluído.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagindo com o armazenamento do Azure

A Microsoft fornece as seguintes ferramentas para trabalhar com o armazenamento do Azure:

| Ferramenta | Linux | SO X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Utilizar contas de armazenamento adicionais

Ao criar um cluster do HDInsight, especifica a conta de armazenamento do Azure que quer associar ao mesmo. Além desta conta de armazenamento, pode adicionar mais contas de armazenamento da mesma subscrição do Azure ou de diferentes subscrições do Azure durante o processo de criação ou depois de um cluster ter sido criado. Para obter instruções sobre como adicionar mais contas do Storage, consulte [Create HDInsight clusters (Criar clusters do HDInsight)](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Não é suportado utilizar uma conta de armazenamento adicional numa localização diferente do cluster do HDInsight.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a utilizar o armazenamento do Azure compatível com HDFS através do HDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução ao Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar assinaturas de acesso compartilhado do armazenamento do Azure para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
