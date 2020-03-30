---
title: Compare opções de armazenamento para uso com clusters Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como funcionam com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79095542"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Compare opções de armazenamento para uso com clusters Azure HDInsight

Pode escolher entre alguns serviços de armazenamento Azure diferentes ao criar clusters HDInsight:

* Storage do Azure
* Armazenamento do Azure Data Lake Ger2
* Armazenamento do Azure Data Lake Ger1

Este artigo fornece uma visão geral destes tipos de armazenamento e suas características únicas.

A tabela que se segue resume os serviços de Armazenamento Azure que são suportados com diferentes versões do HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de espaço de nome | Serviços suportados | Níveis de desempenho suportados | Níveis de acesso suportados | Versão HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Armazenamento do Azure Data Lake Ger2| V2 de uso geral | Hierárquico (sistema de ficheiros) | Blobs | Standard | Hot, Cool, Archive | 3.6+ | Todos exceto o Spark 2.1 e 2.2|
|Storage do Azure| V2 de uso geral | Objeto | Blobs | Standard | Hot, Cool, Archive | 3.6+ | Todos |
|Storage do Azure| V1 de uso geral | Objeto | Blobs | Standard | N/D | Todos | Todos |
|Storage do Azure| Armazenamento Blob** | Objeto | Bloco Blob | Standard | Hot, Cool, Archive | Todos | Todos |
|Armazenamento do Azure Data Lake Ger1| N/D | Hierárquico (sistema de ficheiros) | N/D | N/D | N/D | 3.6 Apenas | Todos, exceto HBase |

**Para os clusters HDInsight, apenas as contas de armazenamento secundárias podem ser do tipo BlobStorage e Page Blob não é uma opção de armazenamento suportada.

Para obter mais informações sobre os tipos de conta de armazenamento do Azure, consulte a visão geral da conta de [armazenamento do Azure](../storage/common/storage-account-overview.md)

Para obter mais informações sobre os níveis de acesso ao Armazenamento Azure, consulte o [armazenamento Do Blob: Premium (pré-visualização), Hot, Cool e Archive](../storage/blobs/storage-blob-storage-tiers.md)

Você pode criar um cluster usando diferentes combinações de serviços para armazenamento secundário primário e opcional. A tabela que se segue resume as configurações de armazenamento de cluster que são atualmente suportadas no HDInsight:

| Versão HDInsight | Armazenamento Primário | Armazenamento Secundário | Suportado |
|---|---|---|---|
| 3,6 & 4.0 | Finalidade Geral V1 , Propósito Geral V2 | Objetivo Geral V1 , Propósito Geral V2, BlobStorage (Bloco blobs) | Sim |
| 3,6 & 4.0 | Finalidade Geral V1 , Propósito Geral V2 | Armazenamento do Data Lake Ger2 | Não |
| 3,6 & 4.0 | Data Lake Storage Gen2* | Armazenamento do Data Lake Ger2 | Sim |
| 3,6 & 4.0 | Data Lake Storage Gen2* | Objetivo Geral V1 , Propósito Geral V2, BlobStorage (Bloco blobs) | Sim |
| 3,6 & 4.0 | Armazenamento do Data Lake Ger2 | Armazenamento do Data Lake Ger1 | Não |
| 3.6 | Armazenamento do Data Lake Ger1 | Armazenamento do Data Lake Ger1 | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | Objetivo Geral V1 , Propósito Geral V2, BlobStorage (Bloco blobs) | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | Armazenamento do Data Lake Ger2 | Não |
| 4,0 | Armazenamento do Data Lake Ger1 | Qualquer | Não |
| 4,0 | Finalidade Geral V1 , Propósito Geral V2 | Armazenamento do Data Lake Ger1 | Não |

*=Esta pode ser uma ou múltiplas contas de Data Lake Storage Gen2, desde que todas estejam configuradas para usar a mesma identidade gerida para acesso ao cluster.

> [!Note] 
> O armazenamento primário do Data Lake Gen2 não é suportado para clusters Spark 2.1 ou 2.2. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Use Azure Data Lake Storage Gen2 com Apache Hadoop em Azure HDInsight

O Azure Data Lake Storage Gen2 tem características fundamentais do Azure Data Lake Storage Gen1 e integra-as no armazenamento azure Blob. Estas funcionalidades incluem um sistema de ficheiros compatível com Hadoop, Azure Ative Directory (Azure AD) e listas de controlo de acesso baseadas em POSIX (ACLs). Esta combinação permite-lhe aproveitar o desempenho do Azure Data Lake Storage Gen1, utilizando também a gestão do ciclo de vida de nidratos e dados do armazenamento blob.

Para mais informações sobre o Azure Data Lake Storage Gen2, consulte Introdução ao Armazenamento de [Lagos De dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade central do Azure Data Lake Storage Gen2

* **Acesso compatível com Hadoop:** No Azure Data Lake Storage Gen2, pode gerir e aceder a dados tal como faria com um Sistema de Ficheiros Distribuídos hadoop (HDFS). O controlador do Sistema de Ficheiros Azure Blob (ABFS) está disponível em todos os ambientes Apache Hadoop, incluindo Azure HDInsight e Azure Databricks. Utilize a ABFS para aceder aos dados armazenados no Data Lake Storage Gen2.

* **Um superconjunto de permissões POSIX:** O modelo de segurança para Data Lake Gen2 suporta permissões ACL e POSIX juntamente com alguma granularidade extra específica para Data Lake Storage Gen2. As definições podem ser configuradas através de ferramentas ou estruturas administrativas como a Apache Hive e a Apache Spark.

* **Eficácia dos custos:** Data Lake Storage Gen2 oferece capacidade de armazenamento de baixo custo e transações. Funcionalidades como o ciclo de vida de armazenamento Do Azure Blob ajudam a reduzir os custos, ajustando as taxas de faturação à medida que os dados se movem ao longo do seu ciclo de vida.

* **Compatibilidade com ferramentas de armazenamento Blob, quadros e aplicações:** Data Lake Storage Gen2 continua a trabalhar com uma grande variedade de ferramentas, quadros e aplicações para armazenamento blob.

* **Condutor otimizado:** O condutor da ABFS está otimizado especificamente para análise de big data. As APIs de repouso correspondentes são surgidas através do ponto final do sistema de ficheiros distribuído (DFS), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Novidades para Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Identidades geridas para acesso seguro de ficheiros

O Azure HDInsight utiliza identidades geridas para garantir o acesso ao cluster a ficheiros em Azure Data Lake Storage Gen2. Identidades geridas são uma característica do Azure Ative Directory que fornece aos serviços Azure um conjunto de credenciais geridas automaticamente. Estas credenciais podem ser utilizadas para autenticar qualquer serviço que suporte a autenticação do Diretório Ativo. A utilização de identidades geridas não requer que guarde credenciais em códigoou ficheiros de configuração.

Para mais informações, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Condutor do sistema de ficheiros Azure Blob

As aplicações Apache Hadoop esperam que leiam e escrevam dados do armazenamento local do disco. Um controlador de sistema de ficheiros Hadoop como o ABFS permite que as aplicações Hadoop funcionem com o armazenamento em nuvem, emitindo operações regulares do sistema de ficheiros Hadoop. O condutor converte os comandos recebidos da aplicação em operações que a plataforma real de armazenamento em nuvem compreende.

Anteriormente, o controlador do sistema de ficheiros Hadoop converteu todas as operações do sistema de ficheiros para as chamadas API do Lado do Cliente e, em seguida, invocou a API REST. Esta conversão do lado do cliente, no entanto, resultou em múltiplas chamadas REST API para uma única operação do sistema de ficheiros como o renomeamento de um ficheiro. A ABFS moveu parte da lógica do sistema de ficheiros Hadoop do lado do cliente para o lado do servidor. A API de Armazenamento de Lago de Dados Azure Gen2 agora funciona em paralelo com a API Blob. Esta migração melhora o desempenho porque agora as operações comuns do sistema de ficheiros Hadoop podem ser executadas com uma chamada de API REST.

Para mais informações, consulte [o controlador de ficheiros Azure Blob (ABFS): um controlador de armazenamento azure dedicado para Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Esquema URI para armazenamento de lagos de dados Azure Gen 2 

O Azure Data Lake Storage Gen2 utiliza um novo esquema URI para aceder a ficheiros no Armazenamento Azure a partir do HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

O regime URI proporciona acesso encriptado a SSL.

`<FILE_SYSTEM_NAME>`identifica o caminho do sistema de ficheiros Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identifica o nome da conta Azure Storage. É necessário um nome de domínio completamente qualificado (FQDN).

`<PATH>`é o nome do caminho do ficheiro ou diretório HDFS.

Se os `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` valores for e não forem especificados, o sistema de ficheiros predefinido é utilizado. Para os ficheiros do sistema de ficheiros predefinidos, utilize um caminho relativo ou um caminho absoluto. Por exemplo, `hadoop-mapreduce-examples.jar` o ficheiro que vem com clusters HDInsight pode ser referido utilizando um dos seguintes caminhos:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> O nome `hadoop-examples.jar` do ficheiro está nas versões HDInsight 2.1 e 1.6. Quando estiver a trabalhar com ficheiros fora do HDInsight, verá que a maioria dos utilitários não reconhece `example/jars/hadoop-mapreduce-examples.jar`o formato ABFS, mas espera um formato de caminho básico, como .

Para mais informações, consulte [Use o Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Storage do Azure

O Azure Storage é uma solução robusta de armazenamento geral que se integra perfeitamente com o HDInsight. O HDInsight pode utilizar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido para o cluster. Através de uma interface HDFS, o conjunto completo de componentes no HDInsight pode funcionar diretamente em dados estruturados ou não estruturados armazenados como bolhas.

Recomendamos que utilize recipientes de armazenamento separados para o seu armazenamento de cluster sinuoso e os seus dados de negócio, para isolar os registos HDInsight e ficheiros temporários dos seus próprios dados de negócio. Recomendamos também a apagar o recipiente de bolhas predefinido, que contém registos de aplicação e sistema, após cada utilização para reduzir o custo de armazenamento. Certifique-se de que obtém os registos antes de eliminar o contentor.

Se optar por proteger a sua conta de armazenamento com as **restrições de Firewalls e redes virtuais** em **redes Selecionadas,** certifique-se de que permite a exceção **Permitir serviços confiáveis da Microsoft...** para que o HDInsight possa aceder à sua conta de armazenamento.

### <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama seguinte proporciona uma visão abstrata da arquitetura HDInsight do Armazenamento Azure:

![Arquitetura de armazenamento HDInsight](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Arquitetura de armazenamento HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Através do HDInsight também pode aceder a dados no Armazenamento Azure. A sintaxe é a seguinte:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Considere os seguintes princípios ao utilizar uma conta de Armazenamento Azure com clusters HDInsight:

* **Contentores nas contas do Storage ligadas a um cluster:** como o nome e a chave da conta são associados ao cluster durante a criação, tem acesso total aos blobs desses contentores.

* **Contentores públicos ou bolhas públicas em contas de armazenamento que *não* estejam ligadas a um cluster:** Tem apenas autorização para ler as bolhas nos contentores.
  
  > [!NOTE]  
  > Os contentores públicos permitem-lhe obter uma lista de todas as bolhas que estão disponíveis nesse contentor e obter metadados de contentores. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../storage/blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

* **Contentores privados em contas de armazenamento que *não* estejam ligados a um cluster:** Não pode aceder às bolhas nos contentores a menos que defina a conta de armazenamento quando submete os trabalhos webHCat. 

As contas do Storage definidas durante o processo de criação e as respetivas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. Por predefinição, o HDInsight utiliza as contas de armazenamento definidas no ficheiro core-site.xml. Pode modificar esta definição utilizando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos webHCat, incluindo Apache Hive, MapReduce, Apache Hadoop streaming, e Apache Pig, podem levar consigo uma descrição de contas de armazenamento e metadados. (Isto é atualmente verdade para o Porco com contas de armazenamento, mas não para metadados.) Para mais informações, consulte [Utilizar um cluster HDInsight com contas de armazenamento alternativas e metalojas](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser utilizados para dados estruturados e não estruturados. Os recipientes blob armazenam dados como par de chaves/valor e não têm hierarquia de direção. No entanto, o nome-chave pode incluir um personagem de corte (/ ) para fazer parecer que um ficheiro está armazenado dentro de uma estrutura de diretório. Por exemplo, a chave de `input/log1.txt`uma bolha pode ser. Não `input` existe um diretório real, mas por causa do personagem slash no nome chave, a chave parece um caminho de arquivo.

### <a name="benefits-of-azure-storage"></a><a id="benefits"></a>Vantagens do Armazenamento do Azure
Os clusters computacionais e os recursos de armazenamento que não estão colocalizados têm custos de desempenho implícitos. Estes custos são atenuados pela forma como os clusters de cálculo são criados perto dos recursos da conta de armazenamento dentro da região de Azure. Nesta região, os nós de computação podem aceder eficientemente aos dados sobre a rede de alta velocidade dentro do Armazenamento Azure.

Ao armazenar os dados no Armazenamento Azure em vez de HDFS, obtém vários benefícios:

* **Partilha e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de cálculo. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. Os dados em Armazenamento Azure, pelo contrário, podem ser acedidos através das APIs hdfs ou do armazenamento Blob REST APIs. Devido a este arranjo, um conjunto maior de aplicações (incluindo outros clusters HDInsight) e ferramentas podem ser usadas para produzir e consumir os dados.

* **Arquivamento de dados:** Quando os dados são armazenados no Armazenamento Azure, os clusters HDInsight utilizados para a computação podem ser eliminados com segurança sem perder dados do utilizador.

* **Custo de armazenamento de dados:** Armazenar dados em DFS a longo prazo é mais dispendioso do que armazenar os dados no Armazenamento Azure porque o custo de um cluster de cálculo é superior ao custo do Armazenamento Azure. Além disso, como os dados não têm de ser recarregados para cada geração de cluster de cálculo, também estás a poupar custos de carregamento de dados.

* **Aumento horizontal elástico:** embora o HDFS forneça um sistema de ficheiros ampliado horizontalmente, o dimensionamento é determinado pelo número de nós que cria para o cluster. Mudar a balança pode ser mais complicado do que confiar nas capacidades elásticas de escala que obtém automaticamente no Armazenamento Azure.

* **Geo-replicação:** O seu Armazenamento Azure pode ser geo-replicado. Embora a geo-replicação lhe dê recuperação geográfica e redundância de dados, uma falha na localização geo-replicada afeta severamente o seu desempenho, e pode incorrer em custos adicionais. Por isso, escolha a geo-replicação com cautela e apenas se o valor dos dados justificar o custo adicional.

Certos trabalhos e pacotes MapReduce podem criar resultados intermédios que não gostaria de armazenar no Armazenamento Azure. Nesse caso, pode optar por armazenar os dados no HDFS local. O HDInsight utiliza o DFS para vários destes resultados intermédios em empregos da Colmeia e outros processos.

> [!NOTE]  
> A maioria dos comandos HDFS (por exemplo, `ls`e `copyFromLocal` `mkdir`) funcionam como esperado no Armazenamento Azure. Apenas os comandos específicos da implementação nativa do HDFS (que é referido como DFS), tais como `fschk` e `dfsadmin`, mostram diferentes comportamentos no Armazenamento Azure.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Visão geral do Armazenamento de Lagos De Dados Azure Gen1

Azure Data Lake Storage Gen1 é um repositório de hiperescala em toda a empresa para grandes cargas de trabalho analíticas de dados. Utilizando o Lago de Dados Azure, pode capturar dados de qualquer tamanho, tipo e velocidade de ingestão num só local para análiseoperacional e exploratória.

Aceda ao Data Lake Storage Gen1 a partir de Hadoop (disponível com um cluster HDInsight) utilizando as APIs REST compatíveis com webHDFS. Data Lake Storage Gen1 foi concebido para permitir análises nos dados armazenados e está sintonizado para o desempenho em cenários de análise de dados. Fora da caixa, inclui as capacidades que são essenciais para casos de uso de empresas no mundo real. Estas capacidades incluem segurança, gestão, escalabilidade, fiabilidade e disponibilidade.

Para obter mais informações sobre o Azure Data Lake Storage Gen1, consulte a visão detalhada do Armazenamento do [Lago De dados Azure Gen1](../data-lake-store/data-lake-store-overview.md).

As principais capacidades do Data Lake Storage Gen1 incluem as seguintes.

### <a name="compatibility-with-hadoop"></a>Compatibilidade com Hadoop

Data Lake Storage Gen1 é um sistema de ficheiros Apache Hadoop que é compatível com hDFS e trabalha com o ecossistema Hadoop.  As suas aplicações ou serviços HDInsight existentes que utilizam a API WebHDFS podem facilmente integrar-se com data Lake Storage Gen1. Data Lake Storage Gen1 também expõe uma interface REST compatível com WebHDFS para aplicações.

Os dados armazenados em Data Lake Storage Gen1 podem ser facilmente analisados usando estruturas analíticas hadoop, tais como MapReduce ou Hive. Os clusters Azure HDInsight podem ser provisionados e configurados para aceder diretamente aos dados armazenados em Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte

Data Lake Storage Gen1 fornece armazenamento ilimitado e é adequado para armazenar uma variedade de dados para análise. Não impõe limites ao tamanho das contas, tamanhos de ficheiros ou à quantidade de dados que podem ser armazenados num lago de dados. Os ficheiros individuais podem variar em tamanho, desde quilobytes a petabytes, tornando o Data Lake Storage Gen1 uma ótima escolha para armazenar qualquer tipo de dados. Os dados são armazenados de forma duradoura fazendo várias cópias, e não há limites para o tempo que os dados podem ser armazenados no lago de dados.

### <a name="performance-tuning-for-big-data-analytics"></a>Sintonização de desempenho para análise de big data

Data Lake Storage Gen1 foi construído para executar sistemas analíticos em larga escala que requerem uma entrada massiva para consultar e analisar grandes quantidades de dados. O lago de dados espalha partes de um ficheiro sobre vários servidores de armazenamento individuais. Ao analisar dados, esta configuração melhora a leitura da entrada quando o ficheiro é lido em paralelo.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Prontidão para empresa: Altamente disponível e seguro

Data Lake Storage Gen1 fornece disponibilidade e fiabilidade padrão da indústria. Os ativos de dados são armazenados de forma duradoura: cópias redundantes protegem contra falhas inesperadas. As empresas podem utilizar o Data Lake Storage Gen1 nas suas soluções como parte importante da sua plataforma de dados existente.

Data Lake Storage Gen1 também fornece segurança de nível empresarial para dados armazenados. Para mais informações, consulte A Segurança dos Dados no Armazenamento de [Lagos Azure Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Estruturas de dados flexíveis

Data Lake Storage Gen1 pode armazenar quaisquer dados no seu formato nativo, como é, sem exigir transformações prévias. Data Lake Storage Gen1 não requer que um esquema seja definido antes de os dados serem carregados. O quadro analítico individual interpreta os dados e define um esquema no momento da análise. Uma vez que pode armazenar ficheiros de tamanhos e formatos arbitrários, o Data Lake Storage Gen1 pode lidar com dados estruturados, semiestruturados e não estruturados.

Recipientes Gen1 de armazenamento de data Lake para dados são essencialmente pastas e ficheiros. Opera os dados armazenados utilizando SDKs, o portal Azure e o Azure PowerShell. Desde que coloque os seus dados na loja utilizando estas interfaces e os recipientes apropriados, pode armazenar qualquer tipo de dados. Data Lake Storage Gen1 não realiza qualquer tratamento especial de dados com base no tipo de dados que armazena.

## <a name="data-security-in-data-lake-storage-gen1"></a><a name="DataLakeStoreSecurity"></a>Segurança de dados em Data Lake Storage Gen1
Data Lake Storage Gen1 utiliza o Diretório Ativo Azure para autenticação e utiliza listas de controlo de acesso (ACLs) para gerir o acesso aos seus dados.

| **Funcionalidade** | **Descrição** |
| --- | --- |
| Autenticação |Data Lake Storage Gen1 integra-se com o Azure Ative Directory (Azure AD) para gestão de identidade e acesso para todos os dados armazenados em Data Lake Storage Gen1. Devido à integração, data Lake Storage Gen1 beneficia de todas as funcionalidades da Azure AD. Estas funcionalidades incluem autenticação multifactor, Acesso Condicional, controlo de acesso baseado em funções, monitorização de utilização de aplicações, monitorização e alerta de segurança, e assim por diante. Data Lake Storage Gen1 suporta o protocolo OAuth 2.0 para autenticação dentro da interface REST. Ver Autenticação dentro do [Azure Data Lake Storage Gen1 usando o Diretório Ativo Azure](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controlo de acesso |Data Lake Storage Gen1 fornece controlo de acesso suportando permissões ao estilo POSIX que são expostas pelo protocolo WebHDFS. As ACLs podem ser ativadas na pasta raiz, nas subpastas e nos ficheiros individuais. Para obter mais informações sobre como os ACLs funcionam no contexto do Data Lake Storage Gen1, consulte o controlo de [acesso em Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Encriptação |Data Lake Storage Gen1 também fornece encriptação para dados que são armazenados na conta. Especifica as definições de encriptação ao criar uma conta Gen1 de Armazenamento de Data Lake. Pode optar por ter os seus dados encriptados ou optar por nenhuma encriptação. Para mais informações, consulte [encriptação em Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Para obter instruções sobre como fornecer uma configuração relacionada com encriptação, consulte Iniciar com [o Azure Data Lake Storage Gen1 utilizando o portal Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Para saber mais sobre a obtenção de dados no Data Lake Storage Gen1, consulte [a Segurança dos Dados Armazenados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplicações compatíveis com Data Lake Storage Gen1
Data Lake Storage Gen1 é compatível com a maioria dos componentes de código aberto no ecossistema Hadoop. Também é integrado corretamente com outros serviços do Azure.  Siga os links abaixo para saber mais sobre como o Data Lake Storage Gen1 pode ser usado tanto com componentes de código aberto como com outros serviços Azure.

* Consulte [aplicações de big data de código aberto que trabalham com o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicações de código aberto que interoperam com data Lake Storage Gen1.
* Consulte a Integração de Dados [Azure Storage Gen1 com outros serviços Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) para entender como usar data Lake Storage Gen1 com outros serviços Azure para permitir uma ampla gama de cenários.
* Consulte [a utilização do Azure Data Lake Storage Gen1 para obter requisitos](../data-lake-store/data-lake-store-data-scenarios.md) de big data para aprender a usar data Lake Storage Gen1 em cenários como ingerir dados, processar dados, descarregar dados e visualizar dados.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Sistema de ficheiros Gen1 de Armazenamento de Data Lake (adl://)
Nos ambientes Hadoop (disponíveis com um cluster HDInsight), pode aceder ao Data Lake Storage Gen1 através do novo sistema de ficheiros, o Sistema AzureDataLakeFile (adl://). O desempenho de aplicações e serviços que usam adl:// pode ser otimizado de formas que não estão atualmente disponíveis no WebHDFS. Como resultado, quando utiliza o Data Lake Storage Gen1, obtém-se a flexibilidade para utilizar o melhor desempenho utilizando o adl:// recomendado ou mantendo o código existente, continuando a utilizar diretamente a API WebHDFS. O Azure HDInsight aproveita ao máximo o sistema AzureDataLakeFile para proporcionar o melhor desempenho no Data Lake Storage Gen1.

Aceda aos seus dados no Data Lake Storage Gen1 utilizando o seguinte:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Para obter mais informações sobre como aceder aos dados em Data Lake Storage Gen1, consulte [Ações disponíveis nos dados armazenados](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Armazenamento de Lagos De Dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
