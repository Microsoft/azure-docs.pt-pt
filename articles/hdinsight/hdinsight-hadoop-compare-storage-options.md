---
title: Comparar as opções de armazenamento para uso com clusters do Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como eles funcionam com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: b73810b37020bf01c1088f194bd426e93fd95d2c
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180762"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Comparar as opções de armazenamento para uso com clusters do Azure HDInsight

Você pode escolher entre alguns serviços de armazenamento do Azure diferentes ao criar clusters HDInsight:

* Storage do Azure
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Este artigo fornece uma visão geral desses tipos de armazenamento e seus recursos exclusivos.

A tabela a seguir resume os serviços de armazenamento do Azure que têm suporte com versões diferentes do HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de namespace | Serviços suportados | Níveis de desempenho com suporte | Níveis de acesso com suporte | Versão do HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Uso geral v2 | Hierárquico (Filesystem) | Blob | Standard | Quente, frio e arquivo morto | 3.6 + | Todos |
|Storage do Azure| Uso geral v2 | Objeto | Blob | Standard | Quente, frio e arquivo morto | 3.6 + | Todos |
|Storage do Azure| Uso geral v1 | Objeto | Blob | Standard | N/A | Todos | Todos |
|Storage do Azure| Armazenamento de BLOBs * * | Objeto | Blob de Blocos | Standard | Quente, frio e arquivo morto | Todos | Todos |
|Azure Data Lake Storage Gen1| N/A | Hierárquico (Filesystem) | N/A | N/D | N/A | apenas 3,6 | Todos, exceto HBase |

\* * Para clusters HDInsight, somente contas de armazenamento secundárias podem ser do tipo BlobStorage e o blob de página não é uma opção de armazenamento com suporte.

Para obter mais informações sobre os tipos de conta de armazenamento do Azure, consulte [visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md)

Para obter mais informações sobre as camadas de acesso de armazenamento [do Azure, consulte armazenamento de BLOBs do Azure: Camadas de armazenamento Premium (visualização), quentes, frias e de arquivo](../storage/blobs/storage-blob-storage-tiers.md)

Você pode criar um cluster usando diferentes combinações de serviços para armazenamento secundário primário e opcional. A tabela a seguir resume as configurações de armazenamento de cluster que atualmente têm suporte no HDInsight:

| Versão do HDInsight | Armazenamento primário | Armazenamento secundário | Suportadas |
|---|---|---|---|
| 3,6 & 4,0 | Uso Geral v1, Uso Geral v2 | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3,6 & 4,0 | Uso Geral v1, Uso Geral v2 | Data Lake Storage Gen2 | Não |
| 3,6 & 4,0 | Uso Geral v1, Uso Geral v2 | Armazenamento do Data Lake Ger1 | Sim |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Sim |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Armazenamento do Data Lake Ger1 | Não |
| 3.6 | Armazenamento do Data Lake Ger1 | Armazenamento do Data Lake Ger1 | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | Data Lake Storage Gen2 | Não |
| 4.0 | Armazenamento do Data Lake Ger1 | Any | Não |

\* = Isso pode ser uma ou várias contas de Data Lake Storage Gen2, desde que todas as configurações usem a mesma identidade gerenciada para acesso ao cluster.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Usar Azure Data Lake Storage Gen2 com Apache Hadoop no Azure HDInsight

Azure Data Lake Storage Gen2 usa os principais recursos do Azure Data Lake Storage Gen1 e os integra ao armazenamento de BLOBs do Azure. Esses recursos incluem um sistema de arquivos compatível com Hadoop, Azure Active Directory (Azure AD) e listas de controle de acesso (ACLs) baseadas em POSIX. Essa combinação permite que você aproveite o desempenho do Azure Data Lake Storage Gen1 ao mesmo tempo em que usa camadas e o gerenciamento do ciclo de vida de dados do armazenamento de BLOBs.

Para obter mais informações sobre Azure Data Lake Storage Gen2, consulte [introdução ao Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade básica do Azure Data Lake Storage Gen2

* **Acesso compatível com o Hadoop:** No Azure Data Lake Storage Gen2, você pode gerenciar e acessar dados exatamente como faria com um Sistema de Arquivos Distribuído do Hadoop (HDFS). O driver ABFS (sistema de arquivos de blob do Azure) está disponível em todos os ambientes de Apache Hadoop, incluindo o Azure HDInsight e o Azure Databricks. Use ABFS para acessar dados armazenados no Data Lake Storage Gen2.

* **Um superconjunto de permissões POSIX:** O modelo de segurança para Data Lake Gen2 dá suporte a permissões de ACL e POSIX, juntamente com uma granularidade extra específica para Data Lake Storage Gen2. As configurações podem ser definidas por meio de ferramentas de administração ou estruturas como Apache Hive e Apache Spark.

* **Economia de custo:** A Data Lake Storage Gen2 oferece capacidade e transações de armazenamento de baixo custo. Recursos como o ciclo de vida do armazenamento de BLOBs do Azure ajudam a reduzir os custos ajustando as taxas de cobrança conforme os dados passam pelo ciclo de vida.

* **Compatibilidade com ferramentas, estruturas e aplicativos de armazenamento de BLOBs:** Data Lake Storage Gen2 continua trabalhando com uma ampla gama de ferramentas, estruturas e aplicativos para o armazenamento de BLOBs.

* **Driver otimizado:** O driver ABFS é otimizado especificamente para análise de Big Data. As APIs REST correspondentes são apresentadas por meio do ponto de extremidade do DFS (sistema de arquivos distribuído), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>O que há de novo no Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Identidades gerenciadas para acesso seguro a arquivos

O Azure HDInsight usa identidades gerenciadas para proteger o acesso de cluster a arquivos no Azure Data Lake Storage Gen2. Identidades gerenciadas são um recurso do Azure Active Directory que fornece aos serviços do Azure um conjunto de credenciais gerenciadas automaticamente. Essas credenciais podem ser usadas para autenticar em qualquer serviço que dê suporte à autenticação Active Directory. O uso de identidades gerenciadas não exige que você armazene credenciais em arquivos de código ou de configuração.

Para obter mais informações, consulte [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Driver do sistema de arquivos do blob do Azure

Apache Hadoop aplicativos nativamente esperam ler e gravar dados do armazenamento de disco local. Um driver de sistema de arquivos Hadoop como o ABFS permite que os aplicativos Hadoop trabalhem com o armazenamento em nuvem emulando operações regulares do sistema de arquivos do Hadoop. O driver converte os comandos recebidos do aplicativo em operações que a plataforma real de armazenamento em nuvem compreende.

Anteriormente, o driver do sistema de arquivos do Hadoop converteu todas as operações do sistema de arquivos em chamadas à API REST do armazenamento do Azure no lado do cliente e depois invocava a API REST. No entanto, essa conversão do lado do cliente resultou em várias chamadas da API REST para uma operação de sistema de arquivos única, como a renomeação de um arquivo. ABFS moveu parte da lógica do sistema de arquivos do Hadoop do lado do cliente para o lado do servidor. A API de Azure Data Lake Storage Gen2 agora é executada em paralelo com a API de BLOB. Essa migração melhora o desempenho porque agora as operações comuns do sistema de arquivos Hadoop podem ser executadas com uma chamada à API REST.

Para obter mais informações, [consulte o driver do sistema de arquivos de blob do Azure (ABFS): Um driver de armazenamento do Azure dedicado](../storage/blobs/data-lake-storage-abfs-driver.md)para Hadoop.

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Esquema de URI para Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 usa um novo esquema de URI para acessar arquivos no armazenamento do Azure do HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

O esquema de URI fornece acesso criptografado por SSL.

`<FILE_SYSTEM_NAME>`Identifica o caminho do sistema de arquivos Data Lake Storage Gen2.

`<ACCOUNT_NAME>`Identifica o nome da conta de armazenamento do Azure. É necessário um nome de domínio completamente qualificado (FQDN).

`<PATH>`é o nome do caminho HDFS do arquivo ou diretório.

Se os valores `<FILE_SYSTEM_NAME>` de `<ACCOUNT_NAME>` e não forem especificados, o sistema de arquivos padrão será usado. Para os arquivos no sistema de arquivos padrão, use um caminho relativo ou um caminho absoluto. Por exemplo, o `hadoop-mapreduce-examples.jar` arquivo que vem com clusters HDInsight pode ser referenciado usando um dos seguintes caminhos:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> O nome do arquivo `hadoop-examples.jar` está em clusters do HDInsight versões 2,1 e 1,6. Quando estiver trabalhando com arquivos fora do HDInsight, você descobrirá que a maioria dos utilitários não reconhece o formato ABFS, mas, em vez disso, espera um `example/jars/hadoop-mapreduce-examples.jar`formato de caminho básico, como.

Para obter mais informações, consulte [usar o URI de Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Storage do Azure

O armazenamento do Azure é uma solução de armazenamento de uso geral robusta que se integra perfeitamente com o HDInsight. O HDInsight pode utilizar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido para o cluster. Por meio de uma interface HDFS, o conjunto completo de componentes no HDInsight pode operar diretamente em dados estruturados ou não estruturados armazenados como BLOBs.

É recomendável usar contêineres de armazenamento separados para o armazenamento de cluster padrão e seus dados corporativos, para isolar os logs do HDInsight e os arquivos temporários de seus próprios dados corporativos. Também é recomendável excluir o contêiner de blob padrão, que contém logs do aplicativo e do sistema, após cada uso para reduzir o custo de armazenamento. Certifique-se de que obtém os registos antes de eliminar o contentor.

Se você optar por proteger sua conta de armazenamento com as restrições de **redes virtuais e firewalls** em **redes selecionadas**, certifique-se de habilitar a exceção **permitir serviços confiáveis da Microsoft...** para que o HDInsight possa acessar seu armazenamento considerar.

### <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama a seguir fornece uma exibição abstrata da arquitetura do HDInsight do armazenamento do Azure:

![Arquitetura de armazenamento do HDInsight](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Arquitetura de armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Por meio do HDInsight, você também pode acessar dados no armazenamento do Azure. A sintaxe é a seguinte:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Considere os seguintes princípios ao usar uma conta de armazenamento do Azure com clusters HDInsight:

* **Contêineres nas contas de armazenamento que estão conectadas a um cluster:** Como o nome da conta e a chave estão associados ao cluster durante a criação, você tem acesso completo aos BLOBs nesses contêineres.

* **Contêineres públicos ou BLOBs públicos em contas de armazenamento que *não* estão conectadas a um cluster:** Você tem permissão somente leitura para os BLOBs nos contêineres.
  
  > [!NOTE]  
  > Os contêineres públicos permitem obter uma lista de todos os blobs que estão disponíveis no contêiner e obter metadados do contêiner. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../storage/blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

* **Contêineres privados em contas de armazenamento que *não* estão conectadas a um cluster:** Não é possível acessar os BLOBs nos contêineres, a menos que você defina a conta de armazenamento ao enviar os trabalhos do WebHCat. 

As contas do Storage definidas durante o processo de criação e as respetivas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. Por padrão, o HDInsight usa as contas de armazenamento definidas no arquivo Core-site. xml. Você pode modificar essa configuração usando o [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos do WebHCat, incluindo Apache Hive, MapReduce, Apache Hadoop streaming e Apache Pig, podem conter uma descrição de contas de armazenamento e metadados com eles. (Isso é verdadeiro no momento para Pig com contas de armazenamento, mas não para metadados.) Para obter mais informações, consulte [usando um cluster HDInsight com metastores e contas de armazenamento alternativas](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser utilizados para dados estruturados e não estruturados. Os contêineres de blob armazenam dados como pares de chave/valor e não têm nenhuma hierarquia de diretório. No entanto, o nome da chave pode incluir um caractere de barra (/) para que ele apareça como se um arquivo fosse armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode `input/log1.txt`ser. Não existe `input` nenhum diretório real, mas devido ao caractere de barra no nome da chave, a chave é semelhante a um caminho de arquivo.

### <a id="benefits"></a>Vantagens do Armazenamento do Azure
Os clusters de computação e os recursos de armazenamento que não estão colocalizados têm custos de desempenho implícitos. Esses custos são mitigados da maneira como os clusters de computação são criados perto dos recursos da conta de armazenamento dentro da região do Azure. Nessa região, os nós de computação podem acessar com eficiência os dados na rede de alta velocidade no armazenamento do Azure.

Ao armazenar os dados no armazenamento do Azure em vez de no HDFS, você obtém vários benefícios:

* **Reutilização e compartilhamento de dados:** Os dados no HDFS estão localizados dentro do cluster de computação. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. Os dados no armazenamento do Azure, por outro lado, podem ser acessados por meio das APIs HDFS ou das APIs REST do armazenamento de BLOBs. Por causa dessa disposição, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas pode ser usado para produzir e consumir os dados.

* **Arquivamento de dados:** Quando os dados são armazenados no armazenamento do Azure, os clusters HDInsight usados para computação podem ser excluídos com segurança sem perder dados do usuário.

* **Custo de armazenamento de dados:** O armazenamento de dados no DFS por longo prazo é mais dispendioso do que armazenar os dados no armazenamento do Azure, pois o custo de um cluster de computação é maior do que o custo do armazenamento do Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você também está economizando custos de carregamento de dados.

* **Expansão elástica:** Embora o HDFS forneça um sistema de arquivos expandido, a escala é determinada pelo número de nós que você cria para o cluster. A alteração da escala pode ser mais complicada do que depender dos recursos de dimensionamento elástico que você obtém automaticamente no armazenamento do Azure.

* **Replicação geográfica:** O armazenamento do Azure pode ser replicado geograficamente. Embora a replicação geográfica forneça a você a recuperação geográfica e a redundância de dados, um failover para o local com replicação geográfica afeta seriamente o desempenho e pode incorrer em custos adicionais. Portanto, escolha a replicação geográfica com cuidado e somente se o valor dos dados justificar o custo adicional.

Determinados trabalhos e pacotes MapReduce podem criar resultados intermediários que você não gostaria de armazenar no armazenamento do Azure. Nesse caso, você pode optar por armazenar os dados no HDFS local. O HDInsight usa o DFS para vários desses resultados intermediários em trabalhos do hive e outros processos.

> [!NOTE]  
> A maioria dos comandos do HDFS ( `ls`por `copyFromLocal`exemplo, `mkdir`, e) funciona como esperado no armazenamento do Azure. Somente os comandos que são específicos para a implementação nativa do HDFS (que é conhecida como DFS), `fschk` como e `dfsadmin`, mostram um comportamento diferente no armazenamento do Azure.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Visão geral do Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 é um repositório de hiperescala de toda a empresa para Big Data cargas de trabalho analíticas. Usando Azure Data Lake, você pode capturar dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análise operacional e exploratório.

Acesse Data Lake Storage Gen1 do Hadoop (disponível com um cluster HDInsight) usando as APIs REST compatíveis com WebHDFS. O Data Lake Storage Gen1 foi projetado para habilitar a análise nos dados armazenados e é ajustado para desempenho em cenários de análise de dados. Isso inclui os recursos que são essenciais para casos de uso empresarial do mundo real. Esses recursos incluem segurança, capacidade de gerenciamento, escalabilidade, confiabilidade e disponibilidade.

Para obter mais informações sobre Azure Data Lake Storage Gen1, consulte a [visão geral detalhada do Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Os principais recursos do Data Lake Storage Gen1 incluem o seguinte.

### <a name="compatibility-with-hadoop"></a>Compatibilidade com o Hadoop

Data Lake Storage Gen1 é um sistema de arquivos Apache Hadoop que é compatível com o HDFS e funciona com o ecossistema do Hadoop.  Seus aplicativos ou serviços HDInsight existentes que usam a API WebHDFS podem facilmente integrar com Data Lake Storage Gen1. O Data Lake Storage Gen1 também expõe uma interface REST compatível com WebHDFS para aplicativos.

Os dados armazenados no Data Lake Storage Gen1 podem ser facilmente analisados usando estruturas analíticas do Hadoop, como MapReduce ou Hive. Os clusters do Azure HDInsight podem ser provisionados e configurados para acessar diretamente os dados armazenados no Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte

O Data Lake Storage Gen1 fornece armazenamento ilimitado e é adequado para armazenar uma variedade de dados para análise. Ele não impõe limites de tamanhos de conta, tamanhos de arquivo ou a quantidade de dados que podem ser armazenados em um data Lake. Os arquivos individuais podem variar de tamanho de kilobytes a petabytes, tornando Data Lake Storage Gen1 uma ótima opção para armazenar qualquer tipo de dados. Os dados são armazenados permanentemente fazendo várias cópias e não há limites de quanto tempo os dados podem ser armazenados no data Lake.

### <a name="performance-tuning-for-big-data-analytics"></a>Ajuste de desempenho para análise de Big Data

O Data Lake Storage Gen1 é criado para executar sistemas analíticos em grande escala que exigem uma enorme produtividade para consultar e analisar grandes quantidades de dados. O data Lake espalha partes de um arquivo em vários servidores de armazenamento individuais. Quando você estiver analisando dados, essa configuração melhorará a taxa de transferência de leitura quando o arquivo for lido em paralelo.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Preparação para a empresa: Altamente disponível e seguro

O Data Lake Storage Gen1 fornece disponibilidade e confiabilidade padrão do setor. Os ativos de dados são armazenados permanentemente: as cópias redundantes protegem contra falhas inesperadas. As empresas podem usar Data Lake Storage Gen1 em suas soluções como uma parte importante de sua plataforma de dados existente.

Data Lake Storage Gen1 também fornece segurança de nível empresarial para dados armazenados. Para obter mais informações, consulte [Securing data in Azure data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Estruturas de dados flexíveis

Data Lake Storage Gen1 pode armazenar qualquer dado em seu formato nativo, como está, sem a necessidade de transformações anteriores. Data Lake Storage Gen1 não exige que um esquema seja definido antes que os dados sejam carregados. A estrutura analítica individual interpreta os dados e define um esquema no momento da análise. Como ele pode armazenar arquivos de tamanhos e formatos arbitrários, Data Lake Storage Gen1 pode lidar com dados estruturados, semiestruturados e não estruturados.

Os contêineres de Data Lake Storage Gen1 para dados são essencialmente pastas e arquivos. Você opera nos dados armazenados usando SDKs, o portal do Azure e Azure PowerShell. Desde que você coloque seus dados na loja usando essas interfaces e os contêineres apropriados, você pode armazenar qualquer tipo de dados. Data Lake Storage Gen1 não executa qualquer manipulação especial de dados com base no tipo de dados que ele armazena.

## <a name="DataLakeStoreSecurity"></a>Segurança de dados no Data Lake Storage Gen1
Data Lake Storage Gen1 usa Azure Active Directory para autenticação e usa ACLs (listas de controle de acesso) para gerenciar o acesso aos seus dados.

| **Funcionalidade** | **Descrição** |
| --- | --- |
| Authentication |O Data Lake Storage Gen1 integra-se com o Azure Active Directory (AD do Azure) para gerenciamento de identidade e acesso para todos os dados armazenados no Data Lake Storage Gen1. Devido à integração, Data Lake Storage Gen1 benefícios de todos os recursos do Azure AD. Esses recursos incluem autenticação multifator, acesso condicional, controle de acesso baseado em função, monitoramento de uso do aplicativo, monitoramento e alertas de segurança e assim por diante. Data Lake Storage Gen1 dá suporte ao protocolo OAuth 2,0 para autenticação na interface REST. Consulte [autenticação em Azure data Lake Storage Gen1 usando Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controlo de acesso |Data Lake Storage Gen1 fornece controle de acesso oferecendo suporte a permissões de estilo POSIX que são expostas pelo protocolo WebHDFS. As ACLs podem ser ativadas na pasta raiz, nas subpastas e nos ficheiros individuais. Para obter mais informações sobre como as ACLs funcionam no contexto de Data Lake Storage Gen1, consulte [controle de acesso em data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Encriptação |O Data Lake Storage Gen1 também fornece criptografia para os dados armazenados na conta. Especifique as configurações de criptografia ao criar uma conta de Data Lake Storage Gen1. Você pode optar por ter seus dados criptografados ou optar por não ter criptografia. Para obter mais informações, consulte [criptografia em data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Para obter instruções sobre como fornecer uma configuração relacionada à criptografia, consulte Introdução [ao Azure data Lake Storage Gen1 usando o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Para saber mais sobre como proteger dados em Data Lake Storage Gen1, consulte [protegendo os dados armazenados em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplicativos que são compatíveis com o Data Lake Storage Gen1
Data Lake Storage Gen1 é compatível com a maioria dos componentes de código-fonte aberto no ecossistema do Hadoop. Também é integrado corretamente com outros serviços do Azure.  Siga os links abaixo para saber mais sobre como Data Lake Storage Gen1 pode ser usado com componentes de software livre, bem como outros serviços do Azure.

* Consulte aplicativos de Big Data de software livre [que funcionam com Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicativos de software livre que interoperam com data Lake Storage Gen1.
* Consulte [integrando Azure data Lake Storage Gen1 com outros serviços do Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) para entender como usar data Lake Storage Gen1 com outros serviços do Azure para habilitar uma variedade maior de cenários.
* Consulte [usando Azure data Lake Storage Gen1 para Big data requisitos](../data-lake-store/data-lake-store-data-scenarios.md) para saber como usar data Lake Storage Gen1 em cenários como ingestão de dados, processamento de dados, download de dados e visualização de dados.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Adl://(sistema de arquivos de Data Lake Storage Gen1)
Em ambientes Hadoop (disponíveis com um cluster HDInsight), você pode acessar Data Lake Storage Gen1 por meio do novo sistema de arquivos, o AzureDataLakeFilesystem (adl://). O desempenho de aplicativos e serviços que usam o adl://pode ser otimizado de maneiras que atualmente não estão disponíveis no WebHDFS. Como resultado, ao usar Data Lake Storage Gen1, você obtém a flexibilidade para ter o melhor desempenho usando o adl://recomendado ou manter o código existente continuando a usar a API do WebHDFS diretamente. O Azure HDInsight aproveita ao máximo o AzureDataLakeFilesystem para fornecer o melhor desempenho em Data Lake Storage Gen1.

Acesse seus dados no Data Lake Storage Gen1 usando o seguinte:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Para obter mais informações sobre como acessar os dados em Data Lake Storage Gen1, consulte [ações disponíveis nos dados armazenados](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
