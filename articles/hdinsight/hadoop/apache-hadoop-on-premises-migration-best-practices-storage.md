---
title: Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de armazenamento
description: Aprenda as melhores práticas do storage para migrar clusters do Hadoop no local para Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: ae435c379a55ee2b3246cb3e757b79295c57988e
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338297"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>Migrar clusters do Apache Hadoop no local para o Azure HDInsight - práticas recomendadas de armazenamento

Este artigo oferece recomendações para o armazenamento de dados em sistemas de Azure HDInsight. É parte de uma série que fornece as práticas recomendadas para ajudar a migrar sistemas de Apache Hadoop no local para Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Escolha o sistema de armazenamento correta para clusters do HDInsight

A estrutura de diretórios do Apache Hadoop File System (HDFS) no local pode ser criada novamente no armazenamento do Azure ou o armazenamento do Azure Data Lake. Em seguida, pode eliminar em segurança os clusters do HDInsight que são utilizados para o cálculo sem perda de dados de utilizador. Ambos os serviços podem ser utilizados como o sistema de ficheiros predefinido e um sistema de ficheiros adicionais para um cluster do HDInsight. O cluster do HDInsight e a conta de armazenamento tem de estar alojados na mesma região.

### <a name="azure-storage"></a>Storage do Azure

Clusters do HDInsight podem utilizar o contentor de BLOBs no armazenamento do Azure como o sistema de ficheiros predefinido ou um sistema de ficheiros adicionais. A conta de armazenamento de escalão Standard é suportada para utilização com clusters do HDInsight. O Premier escalão não é suportado. O contentor de blobs predefinido armazena informações específicas do cluster, como o histórico de tarefas e os registos. A partilha de um contentor de blobs como sistema de ficheiros predefinido para múltiplos clusters não é suportada.

As contas de armazenamento que estão definidas no processo de criação e respetivas chaves são armazenadas no `%HADOOP_HOME%/conf/core-site.xml` em nós do cluster. Também pode ser acessados na seção "Site de principal personalizado" na configuração de HDFS na IU do Ambari. A chave de conta de armazenamento é criptografado por padrão e um script personalizado de desencriptação é utilizado para desencriptar as chaves antes de ser passado para daemons do Hadoop. As tarefas, incluindo Hive, MapReduce, transmissão em fluxo Hadoop e Pig, conter uma descrição das contas de armazenamento e de metadados com eles.

O armazenamento do Azure pode ser georreplicado. Embora os replicação geográfica fornece recuperação geográfica e redundância de dados, uma ativação pós-falha para a localização georreplicada afeta gravemente o desempenho e pode implicar custos adicionais. A recomendação é escolher a replicação geográfica com sabedoria e apenas se o valor dos dados é justificar o custo adicional.

Um dos formatos seguintes pode ser utilizado para aceder aos dados armazenados no armazenamento do Azure:

|Formato de acesso de dados |Descrição |
|---|---|
|`wasb:///`|Aceder ao armazenamento de predefinido com comunicação desencriptada.|
|`wasbs:///`|Armazenamento de padrão de acesso através da comunicação criptografada.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Utilizado quando estão a comunicar com uma conta de armazenamento não predefinido. |


[Metas de desempenho e escalabilidade do Storage do Azure](../../storage/common/storage-scalability-targets.md) lista os limites atuais em contas de armazenamento do Azure. Se as necessidades do aplicativo excederem os destinos de escalabilidade de uma única conta de armazenamento, o aplicativo pode ser criado para utilizar várias contas de armazenamento e, em seguida, objetos de dados de partição entre essas contas de armazenamento.

[Análise de armazenamento do Azure](../../storage/storage-analytics.md) fornece métricas para todos os serviços de armazenamento e o portal do Azure podem ser configuradas métricas recolher a ser visualizado por meio de gráficos. Alertas podem ser criados para notificar quando forem atingidos limiares de métricas de recurso de armazenamento.

Armazenamento do Azure oferece [eliminação de forma recuperável para objetos de blob](../../storage/blobs/storage-blob-soft-delete.md) para ajudar a recuperar dados quando acidentalmente é modificada ou eliminada por uma aplicação ou outro utilizador da conta de armazenamento.

Pode criar [instantâneos de blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Um instantâneo é uma versão só de leitura de um blob que está a ser utilizado num ponto no tempo e fornece uma forma de fazer cópias de segurança de um blob. Assim que tiver sido criado um instantâneo, ele possa ser lido, copiado, ou eliminado, mas não modificado.

> [!Note]
> Para versões mais antigas das distribuições de Hadoop no local que não têm o certificado de "wasbs", eles precisam ser importados para o arquivo de confiança de Java.

Os seguintes métodos podem ser utilizados para importar certificados para o arquivo de confiança de Java:

Transferir o certificado de ssl de Blobs do Azure para um ficheiro

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importe o ficheiro acima para o arquivo de confiança de Java em todos os nós

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Certifique-se de que o certificado foi adicionado está no arquivo de confiança

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Para obter mais informações, veja os artigos seguintes:

- [Utilizar o armazenamento do Azure com clusters do HDInsight do Azure](../hdinsight-hadoop-use-blob-storage.md)
- [Metas de Dimensionamento e Desempenho do Armazenamento do Azure](../../storage/common/storage-scalability-targets.md)
- [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../../storage/common/storage-performance-checklist.md)
- [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorizar uma conta de armazenamento no portal do Azure](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

Armazenamento do Azure Data Lake implementa HDFS e POSIX modelo de controlo de acesso de estilo. Ele fornece integração de primeira classe com o AAD para controlo de acesso granulares tudo bem. Não há nenhum limite para o tamanho dos dados que pode armazenar ou sua capacidade de executar análises paralelas em massa.

Para obter mais informações, veja os artigos seguintes:

- [Criar clusters do HDInsight com o armazenamento do Data Lake com o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utilizar o armazenamento do Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2-preview"></a>Geração de armazenamento 2 do Azure Data Lake (pré-visualização)

Geração de armazenamento 2 do Azure Data Lake é a mais recente do armazenamento da oferta e está em pré-visualização no momento da redação deste artigo. Ele unifica os principais recursos da primeira geração de armazenamento do Azure Data Lake com um ponto de extremidade para os sistema de ficheiros compatível Hadoop, que se integrado diretamente ao armazenamento de Blobs do Azure. Esta melhoria combina os benefícios de dimensionamento e custos de armazenamento de objetos com a fiabilidade e desempenho normalmente associados apenas com sistemas de ficheiros no local.

ADLS Gen 2 está embutido no início da [armazenamento de Blobs do Azure](../../storage/blobs/storage-blobs-introduction.md) e permite-lhe interagir com dados através de ambos os paradigmas de armazenamento de sistema e o objeto de ficheiro. Funcionalidades do [Gen1 de armazenamento do Azure Data Lake](../../data-lake-store/index.md), como semântica do sistema de ficheiros, ao nível do ficheiro segurança e dimensionamento são combinados com armazenamento em camadas e de baixo custo, capacidades de recuperação após desastre/disponibilidade elevada e uma grande/ferramentas do SDK o ecossistema de desde [armazenamento de Blobs do Azure](../../storage/blobs/storage-blobs-introduction.md). No ger2 de armazenamento do Data Lake, todas as qualidades de armazenamento de objetos permanecem enquanto adicionar as vantagens de uma interface de sistema de ficheiros otimizados para análise de cargas de trabalho.

Um recurso fundamental de geração 2 de armazenamento do Data Lake é a adição de um [espaço de nomes hierárquico](../../storage/data-lake-storage/namespace.md) ao serviço de armazenamento de BLOBs, que organiza os ficheiros de objetos de/para uma hierarquia de diretórios para acesso a dados de elevado desempenho. A estrutura hierárquica permite operações como mudar o nome ou eliminar um diretório sejam operações de metadados de atômica única no diretório em vez de enumerar e processamento de todos os objetos que compartilham o prefixo do nome do diretório.

No passado, análise baseada na cloud tinha de comprometer nas áreas de desempenho, gerenciamento e segurança. Os principais recursos de geração 2 do Azure Data Lake Storage (ADLS) são os seguintes:

- **Acesso compatível do Hadoop**: Geração de armazenamento 2 do Azure Data Lake permite-lhe gerir e aceder aos dados, tal como faria com um [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). A nova [ABFS driver](../../storage/data-lake-storage/abfs-driver.md) está disponível em todos os ambientes do Apache Hadoop que estão incluídos na [Azure HDInsight](../index.yml). Este controlador permite-lhe aceder aos dados armazenados na geração 2 de armazenamento do Data Lake.

- **Um conjunto mais amplo de permissões de POSIX**: O modelo de segurança para a geração 2 do Data Lake é totalmente compatível permissões de ACL e POSIX, juntamente com alguns extra granularidade específica de geração 2 de armazenamento do Data Lake. Definições podem ser configuradas por meio de ferramentas de administração ou estruturas como o Hive e do Spark.

- **Custo-eficiência**: Geração 2 de armazenamento do Data Lake oferece a capacidade de armazenamento de baixo custo e transações. Como as transições de dados por meio de seu ciclo de vida completo, taxas de faturação, alterar para minimizar os custos por meio de recursos incorporados, tal como [ciclo de vida de armazenamento de Blobs do Azure](../../storage/common/storage-lifecycle-management-concepts.md).

- **Funciona com aplicações, estruturas e ferramentas de armazenamento de BLOBs**: Geração 2 de armazenamento do Data Lake continua a trabalhar com um vasto leque de ferramentas, estruturas e aplicativos que existem hoje mesmo para o armazenamento de Blobs.

- **Controlador otimizada**: O driver de sistema de ficheiros do Azure Blob (ABFS) esteja [otimizado especificamente](../../storage/data-lake-storage/abfs-driver.md) para análise de macrodados. As APIs REST correspondente são apresentadas através do ponto final de dfs, dfs.core.windows.net.

Um dos formatos seguintes pode ser utilizado para aceder aos dados armazenados no ADLS Gen2:
- `abfs:///`: Acesso a predefinição de armazenamento do Data Lake para o cluster.
- `abfs[s]://file_system@account_name.dfs.core.windows.net`: Utilizado quando estão a comunicar com um armazenamento do Data Lake não predefinido.

Para obter mais informações, veja os artigos seguintes:

- [Introdução à pré-visualização do Azure Data Lake Storage geração 2](../../storage/data-lake-storage/introduction.md)
- [O driver de sistema de ficheiros do Azure Blob (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Chaves de armazenamento do Azure seguras na configuração de cluster de Hadoop no local

As chaves de armazenamento do Azure que são adicionadas para os ficheiros de configuração do Hadoop, estabelecer conectividade no armazenamento do HDFS e BLOBs do Azure no local. Essas chaves podem ser protegidas por meio da criptografia-los com o framework de provedor de credenciais do Hadoop. Depois de criptografar, podem ser armazenados e acedidos em segurança.

**Para aprovisionar as credenciais:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Para adicionar o caminho de fornecedor acima para o core-site ou para a configuração do Ambari em core-site personalizado:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> A propriedade do caminho de fornecedor também pode ser adicionada à linha de comando distcp em vez de armazenar a chave no nível de cluster no site principal da seguinte forma:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Restringir o acesso de dados de armazenamento do Azure com SAS

HDInsight por predefinição tem acesso total aos dados nas contas de armazenamento do Azure associadas ao cluster. Assinaturas de acesso partilhado (SAS) no contentor de BLOBs pode ser utilizado para restringir o acesso aos dados, como fornecer aos utilizadores com acesso só de leitura aos dados.

### <a name="using-the-sas-token-created-with-python"></a>Com o token SAS criado com python

1. Abra o [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) de ficheiros e alterar os valores seguintes:

    |Propriedade de token|Descrição|
    |---|---|
    |policy_name|O nome a utilizar para a política armazenada para criar.|
    |storage_account_name|O nome da conta de armazenamento.|
    |storage_account_key|A chave para a conta de armazenamento.|
    |storage_container_name|O contentor na conta de armazenamento que pretende restringir o acesso ao.|
    |example_file_path|O caminho para um ficheiro que é carregado para o contentor.|

2. O ficheiro de SASToken.py vem com o `ContainerPermissions.READ + ContainerPermissions.LIST` permissões e pode ser ajustado com base no caso de utilização.

3. Execute o script da seguinte forma: `python SASToken.py`

4. Apresenta o token SAS semelhante ao seguinte texto quando o script tiver concluído: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Para limitar o acesso a um contentor com a assinatura de acesso partilhado, adicione uma entrada personalizada para a configuração de site principal para o cluster em Ambari HDFS configurações avançadas de site principal Adicionar propriedade personalizada.

6. Utilize os seguintes valores para o **chave** e **valor** campos:

    **chave**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Valor**: A chave de SAS devolvido pelo passo FROM da aplicação Python 4 acima.

7. Clique nas **Add** botão para guardar esta chave e valor, em seguida, clique nas **guardar** botão para guardar as alterações de configuração. Quando lhe for pedido, adicione uma descrição da alteração ("adicionar acesso do armazenamento SAS" por exemplo) e, em seguida, clique em **guardar**.

8. No Ambari web UI, selecione o HDFS da lista à esquerda e, em seguida, selecione **reiniciar todos os afetados** das ações do serviço de lista pendente à direita. Quando lhe for pedido, selecione **confirmar reiniciar todos os**.

9. Repita este processo para MapReduce2 e YARN.

Existem três coisas importantes a serem lembrados sobre a utilização de Tokens de SAS no Azure:

1. Quando os tokens SAS são criados com permissões de "Ler a lista de +", os usuários que acessam o contentor de Blobs com esse token SAS não será capazes de "escrita e eliminação" dados. Os utilizadores que aceder ao contentor de Blobs com esse token SAS e experimente uma gravação ou operação de eliminação, irá receber uma mensagem como `"This request is not authorized to perform this operation"`.

2. Quando os tokens de SAS são gerados com `READ + LIST + WRITE` permissões (para restringir `DELETE` apenas), comandos como `hadoop fs -put` primeiro escreverá um `\_COPYING\_` de ficheiros e, em seguida, tentar renomear o arquivo. Esta operação de HDFS mapeia para uma `copy+delete` para WASB. Uma vez que o `DELETE` permissão não foi fornecida, "put" falhe. O `\_COPYING\_` operação é um recurso de Hadoop destinado a fornecer algum controlo de simultaneidade. Atualmente não é possível restringir apenas a operação de "DELETE" sem afetar as operações de "Escrita" também.

3. Infelizmente, o provedor de credenciais do hadoop e o principal fornecedor de desencriptação (ShellDecryptionKeyProvider) atualmente não funcionam com os tokens SAS e por isso, atualmente não pode ser protegido visibilidade.

Para obter mais informações, consulte [Use Azure armazenamento assinaturas de acesso partilhado para restringir o acesso aos dados no HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>A utilização de encriptação de dados e replicação

Todos os dados escritos no armazenamento do Azure são automaticamente encriptados usando [Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md). Os dados na conta de armazenamento do Azure são sempre replicados para elevada disponibilidade. Quando cria uma conta de armazenamento, pode escolher uma das seguintes opções de replicação:

- [Armazenamento localmente redundante (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Armazenamento com redundância entre zonas (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Armazenamento georredundante (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Armazenamento georredundante com acesso de leitura (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Armazenamento do Azure Data Lake fornece armazenamento localmente redundante (LRS), mas também deve copiar os dados críticos para outra conta de armazenamento do Data Lake noutra região com uma frequência alinhada com as necessidades do plano de recuperação após desastre. Há uma variedade de métodos para copiar dados, incluindo [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), DistCp, [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), ou [do Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Recomenda-se também para impor políticas de acesso para a conta de armazenamento do Data Lake para impedir a eliminação acidental.

Para obter mais informações, veja os artigos seguintes:

- [Replicação de armazenamento do Azure](../../storage/common/storage-redundancy.md)
- [Documentação de orientação de desastres para o Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Anexar a contas de armazenamento do Azure adicionais em cluster

Durante o processo de criação do HDInsight, uma conta de armazenamento do Azure ou a conta de armazenamento do Azure Data Lake é escolhida como o sistema de ficheiros predefinido. Além desta conta de armazenamento predefinida, podem ser adicionadas contas de armazenamento adicional da mesma subscrição do Azure ou de diferentes subscrições do Azure, durante o processo de criação de cluster ou de um cluster ter sido criado.

Conta de armazenamento adicionais pode ser adicionada em uma das seguintes formas:
- Site principal de configuração avançada personalizado do HDFS Ambari adicionar o nome da conta de armazenamento e reiniciar os serviços de chave
- Usando [ação de Script](../hdinsight-hadoop-add-storage.md) passando o nome da conta de armazenamento e a chave

> [!Note]
> Em casos de utilização válidos, é possível aumentar os limites no armazenamento do Azure através de uma solicitação feita ao [suporte do Azure](https://azure.microsoft.com/support/faq/).

Para obter mais informações, veja os artigos seguintes:
- [Adicionar mais contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md)

## <a name="next-steps"></a>Passos Seguintes

Leia o artigo seguinte nesta série:

- [Práticas recomendadas com uma migração de dados no local para a migração do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
