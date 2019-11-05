---
title: 'Armazenamento: migrar Apache Hadoop locais para o Azure HDInsight'
description: Aprenda as práticas recomendadas de armazenamento para migrar clusters Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: b22c3c7e7dbbf7a93fff10ded1fbb7bef8fc5900
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494948"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrar clusters de Apache Hadoop locais para o Azure HDInsight

Este artigo fornece recomendações para armazenamento de dados em sistemas HDInsight do Azure. É parte de uma série que fornece as práticas recomendadas para ajudar na migração de sistemas locais Apache Hadoop para o Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Escolha o sistema de armazenamento certo para clusters HDInsight

A estrutura de diretório do HDFS (Apache Hadoop File System) local pode ser recriada no armazenamento ou Azure Data Lake Storage do Azure. Você pode excluir com segurança os clusters HDInsight que são usados para computação sem perder dados do usuário. Ambos os serviços podem ser usados como o sistema de arquivos padrão e um sistema de arquivos adicional para um cluster HDInsight. O cluster HDInsight e a conta de armazenamento devem ser hospedados na mesma região.

### <a name="azure-storage"></a>Storage do Azure

Os clusters HDInsight podem usar o contêiner de blob no armazenamento do Azure como o sistema de arquivos padrão ou um sistema de arquivos adicional. A conta de armazenamento de camada Standard tem suporte para uso com clusters HDInsight. Não há suporte para a camada Premier. O contentor de blobs predefinido armazena informações específicas do cluster, como o histórico de tarefas e os registos. Não há suporte para o compartilhamento de um contêiner de blob como o sistema de arquivos padrão para vários clusters.

As contas de armazenamento que são definidas no processo de criação e suas respectivas chaves são armazenadas em `%HADOOP_HOME%/conf/core-site.xml` nos nós de cluster. Eles também podem ser acessados na seção "site de núcleo personalizado" na configuração do HDFS na interface do usuário do amAmbari. A chave da conta de armazenamento é criptografada por padrão e um script de descriptografia personalizado é usado para descriptografar as chaves antes de serem passadas para daemons do Hadoop. Os trabalhos, incluindo Hive, MapReduce, streaming do Hadoop e Pig, trazem uma descrição de contas de armazenamento e metadados com eles.

O armazenamento do Azure pode ser replicado geograficamente. Embora a replicação geográfica proporcione a recuperação geográfica e a redundância de dados, um failover para o local com replicação geográfica afeta seriamente o desempenho e pode incorrer em custos adicionais. A recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados valer o custo adicional.

Um dos seguintes formatos pode ser usado para acessar dados armazenados no armazenamento do Azure:

|Formato de acesso a dados |Descrição |
|---|---|
|`wasb:///`|Acesse o armazenamento padrão usando comunicação não criptografada.|
|`wasbs:///`|Acesse o armazenamento padrão usando a comunicação criptografada.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Usado ao se comunicar com uma conta de armazenamento não padrão. |


[Escalabilidade e metas de desempenho do armazenamento do Azure](../../storage/common/storage-scalability-targets.md) lista os limites atuais nas contas de armazenamento do Azure. Se as necessidades do aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, o aplicativo poderá ser criado para usar várias contas de armazenamento e, em seguida, particionar objetos de dados entre essas contas de armazenamento.

[Análise de Armazenamento do Azure](../../storage/storage-analytics.md) fornece métricas para todos os serviços de armazenamento e portal do Azure podem ser configurados para coletar métricas a serem visualizadas por meio de gráficos. Os alertas podem ser criados para notificar quando os limites tiverem sido atingidos para métricas de recursos de armazenamento.

O armazenamento do Azure oferece [exclusão reversível para objetos de blob](../../storage/blobs/storage-blob-soft-delete.md) para ajudar a recuperar dados quando eles são modificados acidentalmente ou excluídos por um aplicativo ou outro usuário da conta de armazenamento.

Você pode criar [instantâneos de blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Um instantâneo é uma versão somente leitura de um blob que é obtido em um ponto no tempo e fornece uma maneira de fazer backup de um blob. Depois que um instantâneo tiver sido criado, ele poderá ser lido, copiado ou excluído, mas não modificado.

> [!Note]
> Para versões mais antigas de distribuições do Hadoop locais que não têm o certificado "wasbs", eles precisam ser importados para o repositório de confiança do Java.

Os métodos a seguir podem ser usados para importar certificados para o repositório de confiança Java:

Baixar o certificado SSL do blob do Azure para um arquivo

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importar o arquivo acima para o repositório de confiança Java em todos os nós

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Verifique se o certificado adicionado está no repositório de confiança

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Para obter mais informações, veja os artigos seguintes:

- [Utilizar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Metas de Dimensionamento e Desempenho do Armazenamento do Azure](../../storage/common/storage-scalability-targets.md)
- [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../../storage/common/storage-performance-checklist.md)
- [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorizar uma conta de armazenamento no portal do Azure](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

Azure Data Lake Storage implementa o modelo de controle de acesso de estilo de HDFS e POSIX. Ele fornece integração de primeira classe com o AAD para um controle de acesso refinado. Não há limites para o tamanho dos dados que ele pode armazenar ou sua capacidade de executar análises maciçamente paralelas.

Para obter mais informações, veja os artigos seguintes:

- [Criar clusters HDInsight com Data Lake Storage usando o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Usar Data Lake Storage com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

Azure Data Lake Storage Gen2 é a oferta de armazenamento mais recente. Ele unifica os principais recursos da primeira geração de Azure Data Lake Storage com um ponto de extremidade do sistema de arquivos compatível com Hadoop diretamente integrado ao armazenamento de BLOBs do Azure. Esse aprimoramento combina os benefícios de escala e custo do armazenamento de objetos com a confiabilidade e o desempenho normalmente associados apenas a sistemas de arquivos locais.

O ADLS Gen 2 é criado com base no [armazenamento de BLOBs do Azure](../../storage/blobs/storage-blobs-introduction.md) e permite que você utilize a interface com os dados usando os paradigmas de armazenamento de objeto e de sistema de arquivos. Os recursos de [Azure data Lake Storage Gen1](../../data-lake-store/index.md), como a semântica do sistema de arquivos, a segurança em nível de arquivo e a escala, são combinados com armazenamento em camadas de baixo custo, alta disponibilidade/recuperação de desastres e um grande ecossistema de SDK/ferramentas do [Azure Armazenamento de BLOBs](../../storage/blobs/storage-blobs-introduction.md). Em Data Lake Storage Gen2, todas as qualidades do armazenamento de objetos permanecem ao adicionar as vantagens de uma interface de sistema de arquivos otimizada para cargas de trabalho de análise.

Um recurso fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](../../storage/data-lake-storage/namespace.md) ao serviço de armazenamento de BLOBs, que organiza objetos/arquivos em uma hierarquia de diretórios para acesso a dados de alto desempenho. A estrutura hierárquica permite que operações como renomear ou excluir um diretório sejam operações de metadados atômicas únicas no diretório, em vez de enumerar e processar todos os objetos que compartilham o prefixo de nome do diretório.

No passado, a análise baseada em nuvem tinha de comprometer as áreas de desempenho, gerenciamento e segurança. Os principais recursos do Azure Data Lake Storage (ADLS) Gen2 são os seguintes:

- **Acesso compatível com Hadoop**: o Azure data Lake Storage Gen2 permite que você gerencie e acesse dados da mesma forma que faria com um [sistema de arquivos distribuído do Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [Driver ABFS](../../storage/data-lake-storage/abfs-driver.md) está disponível em todos os ambientes de Apache Hadoop que estão incluídos no [Azure HDInsight](../index.yml). Esse driver permite que você acesse os dados armazenados no Data Lake Storage Gen2.

- **Um superconjunto de permissões POSIX**: o modelo de segurança para data Lake Gen2 dá suporte total às permissões de ACL e POSIX, juntamente com alguma granularidade extra específica para data Lake Storage Gen2. As configurações podem ser configuradas por meio de ferramentas de administração ou por meio de estruturas como Hive e Spark.

- **Econômico: o**data Lake Storage Gen2 apresenta as transações e a capacidade de armazenamento de baixo custo. À medida que os dados são transferidos por meio de seu ciclo de vida completo, as taxas de cobrança mudam para minimizar os custos por meio de recursos internos, como o [ciclo de vida do armazenamento de BLOBs do Azure](../../storage/common/storage-lifecycle-management-concepts.md).

- **Funciona com ferramentas, estruturas e aplicativos de armazenamento de BLOBs**: data Lake Storage Gen2 continua trabalhando com uma ampla gama de ferramentas, estruturas e aplicativos que existem hoje para o armazenamento de BLOBs.

- **Driver otimizado**: o ABFS (driver de sistema de arquivos de blob do Azure) é [otimizado especificamente](../../storage/data-lake-storage/abfs-driver.md) para análise de Big Data. As APIs REST correspondentes são apresentadas por meio do ponto de extremidade do DFS, dfs.core.windows.net.

Um dos seguintes formatos pode ser usado para acessar dados armazenados no ADLS Gen2:
- `abfs:///`: acesse o Data Lake Storage padrão para o cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: usado ao se comunicar com um Data Lake Storage não padrão.

Para obter mais informações, veja os artigos seguintes:

- [Introdução ao Azure Data Lake Storage Gen2](../../storage/data-lake-storage/introduction.md)
- [O driver do sistema de arquivos de blob do Azure (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Proteger chaves de armazenamento do Azure na configuração de cluster Hadoop local

As chaves de armazenamento do Azure que são adicionadas aos arquivos de configuração do Hadoop, estabelecem a conectividade entre o HDFS local e o armazenamento de BLOBs do Azure. Essas chaves podem ser protegidas criptografando-as com a estrutura do provedor de credenciais do Hadoop. Depois de criptografados, eles podem ser armazenados e acessados com segurança.

**Para provisionar as credenciais:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Para adicionar o caminho do provedor acima ao Core-site. xml ou à configuração do Ambari em Core-site personalizado:**

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
> A propriedade de caminho do provedor também pode ser adicionada à linha de comando distcp em vez de armazenar a chave no nível do cluster em Core-site. XML da seguinte maneira:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Restringir o acesso a dados do armazenamento do Azure usando SAS

Por padrão, o HDInsight tem acesso completo aos dados nas contas de armazenamento do Azure associadas ao cluster. As assinaturas de acesso compartilhado (SAS) no contêiner de blob podem ser usadas para restringir o acesso aos dados, como fornecer aos usuários acesso somente leitura aos dados.

### <a name="using-the-sas-token-created-with-python"></a>Usando o token SAS criado com Python

1. Abra o arquivo [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) e altere os seguintes valores:

    |Propriedade do token|Descrição|
    |---|---|
    |Nome_da_Diretiva|O nome a ser usado para a política armazenada a ser criada.|
    |storage_account_name|O nome da sua conta de armazenamento.|
    |storage_account_key|A chave para a conta de armazenamento.|
    |storage_container_name|O contêiner na conta de armazenamento ao qual você deseja restringir o acesso.|
    |example_file_path|O caminho para um arquivo que é carregado no contêiner.|

2. O arquivo SASToken.py vem com as permissões `ContainerPermissions.READ + ContainerPermissions.LIST` e pode ser ajustado com base no caso de uso.

3. Execute o script da seguinte maneira: `python SASToken.py`

4. Ele exibe o token SAS semelhante ao seguinte texto quando o script é concluído: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Para limitar o acesso a um contêiner com assinatura de acesso compartilhado, adicione uma entrada personalizada à configuração de site principal para o cluster em Ambari HDFS configurações avançado personalizado Core-site Add Property.

6. Use os seguintes valores para os campos de **chave** e **valor** :

    **Chave**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **valor**: a chave SAS retornada pelo aplicativo Python da etapa 4 acima.

7. Clique no botão **Adicionar** para salvar essa chave e o valor e, em seguida, clique no botão **salvar** para salvar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionando acesso de armazenamento SAS", por exemplo) e, em seguida, clique em **salvar**.

8. Na interface do usuário da Web do amAmbari, selecione HDFS na lista à esquerda e, em seguida, selecione **reiniciar todos os afetados** na lista suspensa ações de serviço à direita. Quando solicitado, selecione **confirmar reiniciar tudo**.

9. Repita esse processo para MapReduce2 e YARN.

Há três coisas importantes a serem lembradas sobre o uso de tokens SAS no Azure:

1. Quando os tokens SAS são criados com permissões de "leitura + lista", os usuários que acessam o contêiner de blob com esse token SAS não poderão "gravar e excluir" dados. Os usuários que acessam o contêiner de blob com esse token SAS e tentarem uma operação de gravação ou exclusão receberão uma mensagem como `"This request is not authorized to perform this operation"`.

2. Quando os tokens SAS são gerados com as permissões `READ + LIST + WRITE` (para restringir `DELETE` somente), comandos como `hadoop fs -put` primeiro gravam em um arquivo `\_COPYING\_` e, em seguida, tentam renomear o arquivo. Essa operação HDFS é mapeada para um `copy+delete` para WASB. Como a permissão `DELETE` não foi fornecida, o "Put" falharia. A operação `\_COPYING\_` é um recurso do Hadoop destinado a fornecer algum controle de simultaneidade. Atualmente, não há como restringir apenas a operação "excluir" sem afetar as operações de "gravação" também.

3. Infelizmente, o provedor de credenciais do Hadoop e o provedor de chave de descriptografia (ShellDecryptionKeyProvider) atualmente não funcionam com os tokens SAS e, portanto, não podem ser protegidos da visibilidade no momento.

Para obter mais informações, consulte [usar assinaturas de acesso compartilhado do armazenamento do Azure para restringir o acesso a dados no HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Usar criptografia de dados e replicação

Todos os dados gravados no armazenamento do Azure são criptografados automaticamente usando o [criptografia do serviço de armazenamento (SSE)](../../storage/common/storage-service-encryption.md). Os dados na conta de armazenamento do Azure sempre são replicados para alta disponibilidade. Ao criar uma conta de armazenamento, você pode escolher uma das seguintes opções de replicação:

- [Armazenamento localmente redundante (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Armazenamento com redundância entre zonas (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Armazenamento georredundante (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Armazenamento georredundante com acesso de leitura (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

O Azure Data Lake Storage fornece armazenamento com redundância local (LRS), mas você também deve copiar dados críticos para outra conta de Data Lake Storage em outra região com uma frequência alinhada às necessidades do plano de recuperação de desastre. Há uma variedade de métodos para copiar dados, incluindo [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), DistCp, [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)ou [Azure data Factory](../../data-factory/connector-azure-data-lake-store.md). Também é recomendável impor políticas de acesso para Data Lake Storage conta para evitar a exclusão acidental.

Para obter mais informações, veja os artigos seguintes:

- [Replicação do armazenamento do Azure](../../storage/common/storage-redundancy.md)
- [Diretrizes de desastre para Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Anexar contas de armazenamento do Azure adicionais ao cluster

Durante o processo de criação do HDInsight, é escolhida uma conta de armazenamento do Azure ou Azure Data Lake conta de armazenamento como o sistema de arquivos padrão. Além dessa conta de armazenamento padrão, as contas de armazenamento adicionais podem ser adicionadas da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação do cluster ou após a criação de um cluster.

Uma conta de armazenamento adicional pode ser adicionada de uma das seguintes maneiras:
- Ambari HDFS config avançado personalizado Core-site adicionar o nome da conta de armazenamento e a chave reiniciando os serviços
- Usando a [ação de script](../hdinsight-hadoop-add-storage.md) passando o nome e a chave da conta de armazenamento

> [!Note]
> Em casos de uso válidos, os limites no armazenamento do Azure podem ser aumentados por meio de uma solicitação feita ao [suporte do Azure](https://azure.microsoft.com/support/faq/).

Para obter mais informações, veja os artigos seguintes:
- [Adicionar mais contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md)

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [Práticas recomendadas de migração de dados para migração local para Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
