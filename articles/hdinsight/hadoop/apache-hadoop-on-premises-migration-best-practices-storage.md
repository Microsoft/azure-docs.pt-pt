---
title: 'Armazenamento: Migrar para as instalações apache Hadoop para Azure HDInsight'
description: Aprenda as melhores práticas de armazenamento para migrar no local os clusters Hadoop para Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 89a2a659e195afcae336c930a101ed0c0b470cb4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715416"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrar para o local aglomerados Apache Hadoop para Azure HDInsight

Este artigo dá recomendações para armazenamento de dados em sistemas Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas Apache Hadoop para Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Escolha o sistema de armazenamento certo para clusters HDInsight

A estrutura de diretório do Sistema de Ficheiros Apache Hadoop (HDFS) no local pode ser recriada no armazenamento de Azure Blob ou no Azure Data Lake Storage. Em seguida, pode eliminar com segurança os clusters HDInsight que são utilizados para a computação sem perder os dados do utilizador. Ambos os serviços podem ser utilizados como o sistema de ficheiros predefinidos e um sistema de ficheiros adicional para um cluster HDInsight. O cluster HDInsight e a conta de armazenamento devem ser alojados na mesma região.

### <a name="azure-storage"></a>Storage do Azure

Os clusters HDInsight podem utilizar o recipiente blob no Azure Storage como o sistema de ficheiros predefinido ou um sistema de ficheiros adicional. A conta de armazenamento standard tier é suportada para utilização com clusters HDInsight. O primeiro escalão não é apoiado. O contentor de blobs predefinido armazena informações específicas do cluster, como o histórico de tarefas e os registos. A partilha de um recipiente blob como o sistema de ficheiros predefinidos para vários clusters não é suportado.

As contas de armazenamento definidas no processo de criação e respetivas chaves são armazenadas nos `%HADOOP_HOME%/conf/core-site.xml` nós do cluster. Também podem ser acedidos na secção "Site core personalizado" na configuração HDFS na UI Ambari. A chave da conta de armazenamento é encriptada por padrão e um script de desencriptação personalizado é usado para desencriptar as chaves antes de ser passado para os daemons hadoop. Os trabalhos, incluindo Hive, MapReduce, Hadoop streaming, e Pig, têm consigo uma descrição das contas de armazenamento e metadados.

O Armazenamento Azure pode ser geo-replicado. Embora a geo-replicação dê recuperação geográfica e redundância de dados, uma falha na localização geo-replicada impacta severamente o desempenho, e pode incorrer em custos adicionais. A recomendação é escolher a geo-replicação com sensatez e apenas se o valor dos dados valer o custo adicional.

Um dos seguintes formatos pode ser usado para aceder a dados armazenados no Azure Storage:

|Formato de Acesso a Dados |Descrição |
|---|---|
|`wasb:///`|Aceder ao armazenamento predefinido utilizando uma comunicação não encriptada.|
|`wasbs:///`|Aceder ao armazenamento predefinido usando comunicação encriptada.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Utilizado quando comunica com uma conta de armazenamento não padrão. |

[Os objetivos de escalabilidade das contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md) listam os limites atuais nas contas de Armazenamento Azure. Se as necessidades da aplicação excederem os objetivos de escalabilidade de uma única conta de armazenamento, a aplicação pode ser construída para utilizar várias contas de armazenamento e, em seguida, objetos de dados de partição através dessas contas de armazenamento.

[O Azure Storage Analytics](../../storage/common/storage-analytics.md) fornece métricas para todos os serviços de armazenamento e o portal Azure pode ser configurado recolher métricas para serem visualizadas através de gráficos. Podem ser criados alertas para notificar quando foram atingidos limiares para métricas de recursos de armazenamento.

O Azure Storage oferece [uma eliminação suave para objetos blob](../../storage/blobs/soft-delete-blob-overview.md) para ajudar a recuperar dados quando são acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de conta de armazenamento.

Pode criar [instantâneos blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob). Um instantâneo é uma versão só de leitura de uma bolha que é tirada em um ponto no tempo e fornece uma maneira de apoiar uma bolha. Uma vez criado um instantâneo, pode ser lido, copiado ou eliminado, mas não modificado.

> [!Note]
> Para versões mais antigas de Hadoop Distributions que não têm o certificado "wasbs", precisam de ser importadas para a loja de confiança Java.

Os seguintes métodos podem ser utilizados para importar certificados para a loja de confiança Java:

Descarregue o Azure Blob TLS/SSL cert para um ficheiro

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importe o ficheiro acima para a loja de confiança Java em todos os nós

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Verifique se o certificado adicionado está na loja de confiança

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Para obter mais informações, veja os seguintes artigos:

- [Use o armazenamento Azure com clusters Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Metas de escalabilidade para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md)
- [Metas de escalabilidade e desempenho para armazenamento blob](../../storage/blobs/scalability-targets.md)
- [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../../storage/blobs/storage-performance-checklist.md)
- [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure (Monitor, diagnose, and troubleshoot Microsoft Azure Storage)](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorizar uma conta de armazenamento no portal do Azure](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

Azure Data Lake Storage Gen1 implementa o modelo de controlo de acesso ao estilo HDFS e POSIX. Proporciona integração de primeira classe com a Azure AD para um controlo de acesso fino. Não há limites para o tamanho dos dados que pode armazenar, ou a sua capacidade de executar análises massivamente paralelas.

Para obter mais informações, veja os seguintes artigos:

- [Criar clusters HDInsight com Data Lake Storage Gen1 utilizando o portal Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Use data lake storage Gen1 com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

Azure Data Lake Storage Gen2 é a mais recente oferta de armazenamento. Unifica as capacidades centrais da primeira geração do Azure Data Lake Storage Gen1 com um ponto final do sistema de ficheiros compatível com Hadoop diretamente integrado no Azure Blob Storage. Esta melhoria combina a escala e os benefícios de custos do armazenamento de objetos com a fiabilidade e desempenho tipicamente associados apenas com sistemas de ficheiros no local.

O Azure Data Lake Storage Gen 2 é construído em cima do [armazenamento Azure Blob](../../storage/blobs/storage-blobs-introduction.md) e permite-lhe interagir com dados usando tanto o sistema de ficheiros como os paradigmas de armazenamento de objetos. As funcionalidades da [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), tais como semântica do sistema de ficheiros, segurança de nível de ficheiros e escala são combinadas com armazenamento de baixo custo, tiered, alta disponibilidade/capacidade de recuperação de desastres, e um grande ecossistema SDK/ferramenta do [armazenamento Azure Blob.](../../storage/blobs/storage-blobs-introduction.md) No Data Lake Storage Gen2, todas as qualidades de armazenamento de objetos permanecem, adicionando as vantagens de uma interface do sistema de ficheiros otimizada para cargas de trabalho analíticas.

Uma característica fundamental do Data Lake Storage Gen2 é a adição de um [espaço hierárquico](../../storage/blobs/data-lake-storage-namespace.md) de nomes ao serviço de armazenamento Blob, que organiza objetos/ficheiros numa hierarquia de diretórios para acesso a dados performantes. A estrutura hierárquica permite que operações como renomear ou eliminar um diretório sejam operações únicas de metadados atómicos no diretório, em vez de enumerar e processar todos os objetos que partilham o prefixo de nome do diretório.

No passado, a análise baseada na nuvem teve de se comprometer em áreas de desempenho, gestão e segurança. As principais características do Azure Data Lake Storage (ADLS) Gen2 são as seguintes:

- **Acesso compatível com Hadoop**: Azure Data Lake Storage Gen2 permite-lhe gerir e aceder a dados tal como faria com um [Sistema de Ficheiros Distribuídos Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [controlador ABFS](../../storage/blobs/data-lake-storage-abfs-driver.md) está disponível em todos os ambientes Apache Hadoop que estão incluídos no [Azure HDInsight](../index.yml). Este controlador permite-lhe aceder aos dados armazenados na Data Lake Storage Gen2.

- **Um superconjunto de permissões POSIX**: O modelo de segurança para Data Lake Gen2 suporta totalmente permissões ACL e POSIX, juntamente com alguma granularidade extra específica para data lake storage gen2. As definições podem ser configuradas através de ferramentas de administração ou através de estruturas como a Colmeia e a Spark.

- **Custo-eficácia**: Data Lake Storage Gen2 possui capacidade de armazenamento de baixo custo e transações. À medida que os dados transitam através do seu ciclo de vida completo, as taxas de faturação mudam para minimizar os custos através de funcionalidades incorporadas, como [o ciclo de vida de armazenamento Azure Blob](../../storage/blobs/storage-lifecycle-management-concepts.md).

- **Trabalha com ferramentas de armazenamento Blob, estruturas e aplicações**: Data Lake Storage Gen2 continua a trabalhar com um vasto leque de ferramentas, estruturas e aplicações que existem hoje para armazenamento blob.

- **Controlador otimizado**: O controlador de ficheiros Azure Blob (ABFS) está [otimizado especificamente](../../storage/blobs/data-lake-storage-abfs-driver.md) para análise de big data. As APIs de REST correspondentes são emergidas através do ponto final dfs, dfs.core.windows.net.

Um dos seguintes formatos pode ser usado para aceder a dados que são armazenados na ADLS Gen2:
- `abfs:///`: Aceda ao armazenamento predefinido do Data Lake para o cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: Utilizado ao comunicar com um armazenamento não padrão do Lago de Dados.

Para obter mais informações, veja os seguintes artigos:

- [Introdução ao Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [O controlador de ficheiros Azure Blob (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Chaves de armazenamento Azure seguras dentro das instalações da configuração do cluster Hadoop

As teclas de Armazenamento Azure adicionadas aos ficheiros de configuração Hadoop, estabelecem conectividade entre as instalações HDFS e o armazenamento Azure Blob. Estas chaves podem ser protegidas encriptando-as com a estrutura do fornecedor de credenciais Hadoop. Uma vez encriptados, podem ser armazenados e acedidos de forma segura.

**Para a provisionar as credenciais:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Para adicionar o caminho do fornecedor acima ao core-site.xml ou à configuração Ambari no core-site personalizado:**

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
> A propriedade do caminho do fornecedor também pode ser adicionada à linha de comando distcp em vez de armazenar a chave ao nível do cluster a nível core-site.xml seguintes:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Restringir o acesso de dados de armazenamento Azure usando SAS

O HDInsight por padrão tem acesso total aos dados nas contas de Armazenamento Azure associadas ao cluster. As Assinaturas de Acesso Partilhado (SAS) no recipiente blob podem ser usadas para restringir o acesso aos dados, tais como fornecer aos utilizadores acesso apenas de leitura aos dados.

### <a name="using-the-sas-token-created-with-python"></a>Usando o símbolo SAS criado com python

1. Abra o ficheiro  [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) e altere os seguintes valores:

    |Propriedade Token|Descrição|
    |---|---|
    |policy_name|O nome a usar para a política armazenada para criar.|
    |storage_account_name|O nome da sua conta de armazenamento.|
    |storage_account_key|A chave para a conta de armazenamento.|
    |storage_container_name|O recipiente na conta de armazenamento a que pretende restringir o acesso.|
    |example_file_path|O caminho para um ficheiro que é enviado para o contentor.|

2. O ficheiro SASToken.py vem com as `ContainerPermissions.READ + ContainerPermissions.LIST` permissões e pode ser ajustado com base no caso de utilização.

3. Execute o script da seguinte forma: `python SASToken.py`

4. Exibe o símbolo SAS semelhante ao seguinte texto quando o script termina: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Para limitar o acesso a um recipiente com Assinatura de Acesso Partilhado, adicione uma entrada personalizada à configuração do núcleo do cluster sob a propriedade Ambari HDFS Configs Advanced Custom Add.

6. Utilize os seguintes valores para os campos **Chave** e **Valor:**

    **Tecla**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Valor**: A chave SAS devolvida pela aplicação Python a partir do passo 4 acima.

7. Clique no botão **Adicionar** para guardar esta tecla e valor e, em seguida, clique no botão **Guardar** para guardar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionar acesso ao armazenamento SAS" por exemplo) e, em seguida, clique em **Guardar**.

8. Na UI web Ambari, selecione HDFS da lista à esquerda e, em seguida, **selecione Restart All Affected** from the Service Actions drop down list à direita. Quando solicitado, **selecione ConfirmE Reinício Tudo**.

9. Repita este processo para MapReduce2 e YARN.

Há três coisas importantes a lembrar sobre o uso de sas tokens em Azure:

1. Quando os tokens SAS são criados com permissões "READ + LIST", os utilizadores que acedam ao recipiente Blob com esse token SAS não serão capazes de "escrever e apagar" dados. Os utilizadores que acedam ao recipiente Blob com esse token SAS e tentem uma operação de escrita ou eliminação, receberão uma mensagem como `"This request is not authorized to perform this operation"` .

2. Quando os tokens SAS são gerados com `READ + LIST + WRITE` permissões (para restringir `DELETE` apenas), comandos como `hadoop fs -put` primeiro escrever para um ficheiro `\_COPYING\_` e, em seguida, tentar renomear o ficheiro. Esta operação HDFS mapeia para um `copy+delete` para WASB. Como a `DELETE` permissão não foi dada, o "put" falharia. A `\_COPYING\_` operação é uma funcionalidade Hadoop destinada a fornecer algum controlo de concordância. Atualmente não há forma de restringir apenas a operação "DELETE" sem afetar também as operações "WRITE".

3. Infelizmente, o fornecedor de credenciais de hadoop e o fornecedor de chaves de desencriptação (ShellDecryptionKeyProvider) atualmente não funcionam com os tokens SAS e, por isso, atualmente não pode ser protegido da visibilidade.

Para obter mais informações, consulte [Use Azure Storage Shared Access Signatures para restringir o acesso aos dados em HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Utilizar encriptação e replicação de dados

Todos os dados escritos no Azure Storage são automaticamente encriptados utilizando [encriptação do serviço de armazenamento (SSE)](../../storage/common/storage-service-encryption.md). Os dados na conta de Armazenamento Azure são sempre replicados para uma elevada disponibilidade. Ao criar uma conta de armazenamento, pode escolher uma das seguintes opções de replicação:

- [Armazenamento localmente redundante (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Armazenamento com redundância entre zonas (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Armazenamento georredundante (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Armazenamento georredundante com acesso de leitura (RA-GRS)](../../storage/common/storage-redundancy.md)

O Azure Storage fornece armazenamento localmente redundante (LRS), mas também deve copiar dados críticos para outra conta de Armazenamento Azure noutra região com uma frequência alinhada com as necessidades do plano de recuperação de desastres. Existem diferentes métodos para copiar dados, incluindo [ADLCopy,](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) [DistCp,](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) [Azure PowerShell,](../../data-lake-store/data-lake-store-get-started-powershell.md)ou [Azure Data Factory.](../../data-factory/connector-azure-data-lake-store.md) Também é recomendado para impor políticas de acesso para conta de armazenamento Azure para evitar a eliminação acidental.

Para obter mais informações, veja os seguintes artigos:

- [Replicação do Armazenamento do Azure](../../storage/common/storage-redundancy.md)
- [Orientação de desastres para Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Anexar contas adicionais de armazenamento Azure ao cluster

Durante o processo de criação hdInsight, uma conta de armazenamento Azure, Azure Data Lake Storage Gen1 ou Azure Data Lake Storage Gen2 é escolhida como o sistema de ficheiros padrão. Além desta conta de armazenamento padrão, as contas de armazenamento adicionais podem ser adicionadas a partir da mesma subscrição Azure ou diferentes subscrições Azure durante o processo de criação do cluster ou após a criação de um cluster.

A conta de armazenamento adicional pode ser adicionada de uma sobre as seguintes formas:
- Ambari HDFS Config Avançado Site de núcleo personalizado Adicionar o nome da conta de armazenamento e a chave Reiniciar os serviços
- Usando [a ação do Script](../hdinsight-hadoop-add-storage.md) passando o nome da conta de armazenamento e chave

> [!Note]
> Em casos de utilização válidos, os limites do armazenamento Azure podem ser aumentados através de um pedido feito ao [Suporte Azure](https://azure.microsoft.com/support/faq/).

Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passos seguintes

Leia o artigo seguinte nesta série: [As melhores práticas de migração de dados para a migração de Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md).