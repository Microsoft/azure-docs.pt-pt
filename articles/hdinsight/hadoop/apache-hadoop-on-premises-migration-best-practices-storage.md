---
title: 'Armazenamento: Migrar no local Apache Hadoop para Azure HDInsight'
description: Aprenda as melhores práticas de armazenamento para migrar no local clusters Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: b68e438a01f9f771c16fc712597308089f628f62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409478"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrar no local Apache Hadoop clusters para Azure HDInsight

Este artigo dá recomendações para armazenamento de dados em sistemas Azure HDInsight. Faz parte de uma série que fornece as melhores práticas para ajudar na migração dos sistemas Apache Hadoop para o Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Escolha o sistema de armazenamento certo para clusters HDInsight

A estrutura de diretórios Apache Hadoop File System (HDFS) no local pode ser recriada em Armazenamento Azure ou Armazenamento de Lagos De Dados Azure. Em seguida, pode eliminar com segurança os clusters HDInsight que são utilizados para a computação sem perder os dados do utilizador. Ambos os serviços podem ser utilizados tanto como o sistema de ficheiros predefinido como um sistema de ficheiros adicional para um cluster HDInsight. O cluster HDInsight e a conta de armazenamento devem ser alojados na mesma região.

### <a name="azure-storage"></a>Storage do Azure

Os clusters HDInsight podem utilizar o recipiente blob no Armazenamento Azure como o sistema de ficheiros predefinido ou um sistema de ficheiros adicional.A conta de armazenamento de nível Standard é suportada para utilização com clusters HDInsight. O primeiro escalão não é apoiado. O contentor de blobs predefinido armazena informações específicas do cluster, como o histórico de tarefas e os registos.Partilhar um recipiente de bolha como o sistema de ficheiros padrão para vários clusters não é suportado.

As contas de armazenamento que são definidas no processo `%HADOOP_HOME%/conf/core-site.xml` de criação e respetivas chaves estão armazenadas nos nós do cluster. Também podem ser acedidos sob a secção "Custom core site" na configuração HDFS na UI Ambari. A chave da conta de armazenamento é encriptada por padrão e um script de desencriptação personalizado é usado para desencriptar as teclas antes de ser passado para daemons Hadoop. Os trabalhos incluindo Hive, MapReduce, Hadoop streaming, e Pig, têm consigo uma descrição das contas de armazenamento e metadados.

O Armazenamento Azure pode ser geo-replicado. Embora a geo-replicação dê recuperação geográfica e redundância de dados, uma falha na localização geo-replicada afeta severamente o desempenho, e pode incorrer em custos adicionais. A recomendação é escolher a geo-replicação sabiamente e apenas se o valor dos dados valer o custo adicional.

Um dos seguintes formatos pode ser utilizado para aceder a dados armazenados no Armazenamento Azure:

|Formato de Acesso a Dados |Descrição |
|---|---|
|`wasb:///`|Aceder ao armazenamento predefinido utilizando comunicação não encriptada.|
|`wasbs:///`|Aceder ao armazenamento predefinido utilizando comunicação encriptada.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Utilizado quando se comunica com uma conta de armazenamento não predefinida. |

[Os objetivos](../../storage/common/scalability-targets-standard-account.md) de escalabilidade para contas de armazenamento padrão listam os limites atuais das contas de Armazenamento Azure. Se as necessidades da aplicação excederem os objetivos de escalabilidade de uma única conta de armazenamento, a aplicação pode ser construída para utilizar várias contas de armazenamento e, em seguida, objetos de dados de partição através dessas contas de armazenamento.

[O Azure Storage Analytics](../../storage/storage-analytics.md) fornece métricas para todos os serviços de armazenamento e o portal Azure pode ser configurado para recolher métricas para ser visualizado através de gráficos. Podem ser criados alertas para notificar quando os limiares tiverem sido atingidos para métricas de recursos de armazenamento.

O Azure Storage oferece [uma eliminação suave para objetos blob](../../storage/blobs/storage-blob-soft-delete.md) para ajudar a recuperar dados quando são acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de conta de armazenamento.

Pode criar [imagens blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Um instantâneo é uma versão apenas de uma bolha que é tirada em um momento e fornece uma maneira de apoiar uma bolha. Uma vez criado um instantâneo, pode ser lido, copiado ou eliminado, mas não modificado.

> [!Note]
> Para versões mais antigas de Hadoop Distributions no local que não têm o certificado de "wasbs", precisam de ser importadas para a loja de fiduciários Java.

Os seguintes métodos podem ser utilizados para importar certificados para a loja de fidedignidade java:

Descarregue o Azure Blob SSL cert para um ficheiro

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importar o ficheiro acima para a loja de confiança Java em todos os nós

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Verifique se o cert adicionado está na loja de confiança

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Para obter mais informações, veja os artigos seguintes:

- [Utilize o Armazenamento Azure com clusters Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Metas de escalabilidade para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md)
- [Metas de escalabilidade e desempenho para armazenamento blob](../../storage/blobs/scalability-targets.md)
- [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../../storage/common/storage-performance-checklist.md)
- [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure (Monitor, diagnose, and troubleshoot Microsoft Azure Storage)](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorizar uma conta de armazenamento no portal do Azure](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

O Azure Data Lake Storage implementa o modelo de controlo de acesso ao estilo HDFS e POSIX. Proporciona integração de primeira classe com AAD para um controlo de acesso fino. Não há limites para o tamanho dos dados que pode armazenar, ou a sua capacidade de executar análises massivamente paralelas.

Para obter mais informações, veja os artigos seguintes:

- [Crie clusters HDInsight com Armazenamento de Lago de Dados utilizando o portal Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utilize armazenamento de data lake com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

Azure Data Lake Storage Gen2 é a mais recente oferta de armazenamento. Unifica as capacidades centrais da primeira geração de Armazenamento de Lagos De Dados Azure com um ponto final compatível com o sistema de ficheiros Hadoop diretamente integrado no Armazenamento de Blob Azure. Esta melhoria combina a escala e os benefícios de custos do armazenamento de objetos com a fiabilidade e desempenho tipicamente associados apenas com sistemas de ficheiros no local.

O ADLS Gen 2 é construído em cima do [armazenamento Do Blob Azure](../../storage/blobs/storage-blobs-introduction.md) e permite-lhe interagir com dados utilizando tanto o sistema de ficheiros como os paradigmas de armazenamento de objetos. As funcionalidades do [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), tais como semântica do sistema de ficheiros, segurança ao nível de ficheiros e escala são combinadas com armazenamento de baixo custo, hierárquico, capacidades de alta disponibilidade/recuperação de desastres, e um grande ecossistema SDK/ferramenta sdK do [armazenamento Azure Blob.](../../storage/blobs/storage-blobs-introduction.md) No Data Lake Storage Gen2, todas as qualidades de armazenamento de objetos permanecem, ao mesmo tempo que adicionam as vantagens de uma interface de sistema de ficheiros otimizada para cargas de trabalho analíticas.

Uma característica fundamental do Data Lake Storage Gen2 é a adição de um espaço de nome [hierárquico](../../storage/data-lake-storage/namespace.md)ao serviço de armazenamento Blob, que organiza objetos/ficheiros numa hierarquia de diretórios para acesso a dados performantes.A estrutura hierárquica permite que operações como renomear ou apagar um diretório sejam operações únicas de metadados atómicos no diretório em vez de enumerar e processar todos os objetos que partilham o nome prefixo do diretório.

No passado, a análise baseada na nuvem teve de se comprometer em áreas de desempenho, gestão e segurança. As principais características do Azure Data Lake Storage (ADLS) Gen2 são as seguintes:

- **Acesso compatível com hadoop**: Azure Data Lake Storage Gen2 permite-lhe gerir e aceder a dados tal como faria com um Sistema de [Ficheiros Distribuídos Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo  [controlador ABFS](../../storage/data-lake-storage/abfs-driver.md)está disponível em todos os ambientes Apache Hadoop que estão incluídos no [Azure HDInsight](../index.yml). Este controlador permite-lhe aceder aos dados armazenados no Data Lake Storage Gen2.

- **Um superconjunto de permissões POSIX**: O modelo de segurança para Data Lake Gen2 suporta totalmente permissões ACL e POSIX juntamente com alguma granularidade extra específica para Data Lake Storage Gen2. As definições podem ser configuradas através de ferramentas de administração ou através de estruturas como a Hive e a Spark.

- **Custo eficaz**: Data Lake Storage Gen2 possui capacidade de armazenamento de baixo custo e transações. À medida que os dados transitam através do seu ciclo de vida completo, as taxas de faturação mudam para minimizar os custos através de funcionalidades incorporadas, como o ciclo de vida de [armazenamento de Blob Azure.](../../storage/common/storage-lifecycle-management-concepts.md)

- **Trabalha com ferramentas de armazenamento Blob, quadros e aplicações**: Data Lake Storage Gen2 continua a trabalhar com um vasto leque de ferramentas, quadros e aplicações que existem hoje para armazenamento Blob.

- **Condutor otimizado**: O controlador de ficheiros Azure Blob (ABFS) está [otimizado especificamente](../../storage/data-lake-storage/abfs-driver.md) para análise de big data. As APIs de REPOUSO correspondentes são surgidas através do ponto final dfs, dfs.core.windows.net.

Um dos seguintes formatos pode ser usado para aceder a dados armazenados em ADLS Gen2:
- `abfs:///`: Aceda ao armazenamento padrão do Lago de Dados para o cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: Utilizado quando se comunica com um armazenamento não predefinido do Lago de Dados.

Para obter mais informações, veja os artigos seguintes:

- [Introdução ao Armazenamento de Lagos De Dados Azure Gen2](../../storage/data-lake-storage/introduction.md)
- [O controlador de ficheiros Azure Blob (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Chaves de armazenamento Azure seguras dentro da configuração do cluster Hadoop no local

As chaves de armazenamento Azure que são adicionadas aos ficheiros de configuração Hadoop, estabelecem conectividade entre as instalações HDFS e Azure Blob armazenamento. Estas chaves podem ser protegidas encriptando-as com a estrutura do fornecedor de credenciais Hadoop. Uma vez encriptados, podem ser armazenados e acedidos de forma segura.

**Para fornecer as credenciais:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Para adicionar o caminho do fornecedor acima ao core-site.xml ou à configuração Ambari sob o núcleo personalizado:**

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
> A propriedade do caminho do fornecedor também pode ser adicionada à linha de comando distcp em vez de armazenar a chave ao nível do cluster no core-site.xml da seguinte forma:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Restringir o acesso de dados de armazenamento azure usando sas

O HDInsight, por padrão, tem acesso total aos dados nas contas de Armazenamento Azure associadas ao cluster. As Assinaturas de Acesso Partilhado (SAS) no recipiente blob podem ser utilizadas para restringir o acesso aos dados, tais como fornecer aos utilizadores o acesso apenas aos dados.

### <a name="using-the-sas-token-created-with-python"></a>Usando o token SAS criado com pitão

1. Abra o ficheiro [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) e altere os seguintes valores:

    |Propriedade Token|Descrição|
    |---|---|
    |policy_name|O nome a usar para a política armazenada para criar.|
    |storage_account_name|O nome da sua conta de armazenamento.|
    |storage_account_key|A chave para a conta de armazenamento.|
    |storage_container_name|O recipiente na conta de armazenamento a que pretende restringir o acesso.|
    |example_file_path|O caminho para um ficheiro que é enviado para o recipiente.|

2. O ficheiro SASToken.py `ContainerPermissions.READ + ContainerPermissions.LIST` vem com as permissões e pode ser ajustado com base no caso de utilização.

3. Execute o guião da seguinte forma:`python SASToken.py`

4. Exibe o token SAS semelhante ao seguinte texto quando o script completa:`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Para limitar o acesso a um recipiente com Assinatura de Acesso Partilhado, adicione uma entrada personalizada na configuração do core-site para o cluster sob ambari HDFS Configs Advanced Custom core-site Adicionar propriedade.

6. Utilize os seguintes valores para os campos **Chave** e **Valor:**

    **Chave** `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` : **Valor**: A CHAVE SAS devolvida pela aplicação Python FROM step 4 above.

7. Clique no botão **Adicionar** para guardar esta tecla e valorizar e, em seguida, clique no botão **Guardar** para guardar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionar acesso ao armazenamento SAS" por exemplo) e, em seguida, clique em **Guardar**.

8. Na UI web ambari, selecione HDFS da lista à esquerda e, em seguida, selecione **Restart All Affected** from the Service Actions drop down list à direita. Quando solicitado, selecione **Confirmar Reiniciar Tudo**.

9. Repita este processo para MapReduce2 e YARN.

Há três coisas importantes a lembrar sobre o uso de Tokens SAS em Azure:

1. Quando as fichas SAS forem criadas com permissões "READ + LIST", os utilizadores que acedem ao contentor Blob com esse token SAS não poderão "escrever e apagar" dados. Os utilizadores que acedam ao recipiente Blob com esse token SAS `"This request is not authorized to perform this operation"`e tentem uma operação de escrita ou exclusão, receberão uma mensagem como .

2. Quando as fichas SAS são `READ + LIST + WRITE` geradas com `DELETE` permissões `hadoop fs -put` (apenas `\_COPYING\_` para restringir), comandos como primeiro escrevem para um ficheiro e depois tentam mudar o nome do ficheiro. Esta operação HDFS `copy+delete` mapeia para um WASB. Como `DELETE` a permissão não foi dada, o "put" falharia. A `\_COPYING\_` operação é uma funcionalidade Hadoop destinada a fornecer algum controlo de moeda. Atualmente, não existe forma de restringir apenas a operação "DELETE" sem afetar também as operações "WRITE".

3. Infelizmente, o fornecedor de credenciais de hadoop e o fornecedor chave de desencriptação (ShellDecryptionKeyProvider) atualmente não funcionam com as fichas SAS e, por isso, atualmente não pode ser protegido da visibilidade.

Para mais informações, consulte Utilize as assinaturas de [acesso partilhado seleção do Azure para restringir o acesso aos dados no HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Utilizar encriptação e replicação de dados

Todos os dados escritos para o Armazenamento Azure são automaticamente encriptados utilizando encriptação do serviço de [armazenamento (SSE)](../../storage/common/storage-service-encryption.md). Os dados da conta De armazenamento Azure são sempre replicados para alta disponibilidade.Ao criar uma conta de armazenamento, pode escolher uma das seguintes opções de replicação:

- [Armazenamento localmente redundante (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Armazenamento redundante em zona (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Armazenamento georredundante (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Armazenamento georredundante com acesso de leitura (RA-GRS)](../../storage/common/storage-redundancy.md)

O Armazenamento de Lagos De Dados Azure fornece armazenamento localmente redundante (LRS), mas também deve copiar dados críticos para outra conta de Armazenamento de Data Lake noutra região com uma frequência alinhada com as necessidades do plano de recuperação de desastres.Existem diferentes métodos para copiar dados, incluindo [ADLCopy,](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) [DistCp,](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)ou [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md).Também é recomendado para impor políticas de acesso para a conta de armazenamento de data lake para evitar a eliminação acidental.

Para obter mais informações, veja os artigos seguintes:

- [Réplica de armazenamento azure](../../storage/common/storage-redundancy.md)
- [Orientação de desastres para armazenamento de lagos de dados azure (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Anexar contas adicionais de armazenamento azure ao cluster

Durante o processo de criação do HDInsight, uma conta de Armazenamento Azure ou uma conta de Armazenamento de Lagos De Dados Azure é escolhida como o sistema de ficheiros padrão. Além desta conta de armazenamento por defeito, as contas de armazenamento adicionais podem ser adicionadas a partir da mesma subscrição Azure ou de diferentes subscrições Azure durante o processo de criação de cluster ou após a criação de um cluster.

A conta de armazenamento adicional pode ser adicionada numa das seguintes formas:
- Ambari HDFS Config Avançado Core-site Personalizado Adicione o nome da conta de armazenamento e chave reiniciando os serviços
- Usando a [ação script,](../hdinsight-hadoop-add-storage.md) passando o nome da conta de armazenamento e a chave

> [!Note]
> Em casos de utilização válidos, os limites do armazenamento Azure podem ser aumentados através de um pedido feito ao [Suporte Azure](https://azure.microsoft.com/support/faq/).

Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série: [As melhores práticas de migração de dados para a migração de Hadoop Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-data-migration.md).
