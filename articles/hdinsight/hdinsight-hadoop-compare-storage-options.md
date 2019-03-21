---
title: Compare as opções de armazenamento para utilização com clusters do HDInsight do Azure
description: Fornece uma visão geral dos tipos de armazenamento e como eles funcionam com o Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: fa08d2fb2185bd4b6cd0e2e9d20e1c44a4a35eae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101487"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Compare as opções de armazenamento para utilização com clusters do HDInsight do Azure

Pode escolher entre alguns serviços de armazenamento do Azure diferente durante a criação de clusters do HDInsight:

* Storage do Azure
* Armazenamento do Azure Data Lake Ger2
* Armazenamento do Azure Data Lake Ger1

Este artigo fornece uma visão geral desses tipos de armazenamento e de seus recursos exclusivos.

A tabela seguinte resume os serviços de armazenamento do Azure que são suportados com diferentes versões do HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de espaço de nomes | Serviços suportados | Escalões de desempenho suportados | Suporte de acesso | Versão do HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Armazenamento do Azure Data Lake Ger2| Para fins gerais V2 | Hierárquica (sistema de ficheiros) | Blobs | Standard | Armazenamento frequente, esporádica, arquivo | 3.6 + | Todos |
|Storage do Azure| Para fins gerais V2 | Object | Blobs | Standard | Armazenamento frequente, esporádica, arquivo | 3.6 + | Todos |
|Storage do Azure| Para fins gerais V1 | Object | Blobs | Standard | N/A | Todos | Todos |
|Storage do Azure| Blob Storage | Object | Blobs | Standard | Armazenamento frequente, esporádica, arquivo | Todos | Todos |
|Armazenamento do Azure Data Lake Ger1| N/A | Hierárquica (sistema de ficheiros) | N/A | N/D | N/A | Apenas 3.6 | Tudo, exceto HBase |

Para obter mais informações sobre as camadas de acesso de armazenamento do Azure, consulte [armazenamento de Blobs do Azure: Premium (pré-visualização), as camadas de armazenamento frequente, esporádico e arquivo](../storage/blobs/storage-blob-storage-tiers.md)

Pode criar um cluster com diferentes combinações de serviços para armazenamento secundário primário e opcional. A tabela seguinte resume as configurações de armazenamento de cluster que são atualmente suportadas no HDInsight:

| Versão do HDInsight | Armazenamento Primário | Armazenamento secundário | Suportadas |
|---|---|---|---|
| 3.6 & 4.0 | BLOBs padrão | BLOBs padrão | Sim |
| 3.6 & 4.0 | BLOBs padrão | Armazenamento do Data Lake Ger2 | Não |
| 3.6 & 4.0 | BLOBs padrão | Armazenamento do Data Lake Ger1 | Sim |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Armazenamento do Data Lake Ger2 | Sim |
| 3.6 & 4.0 | Data Lake Storage Gen2* | BLOBs padrão | Sim |
| 3.6 & 4.0 | Armazenamento do Data Lake Ger2 | Armazenamento do Data Lake Ger1 | Não |
| 3.6 | Armazenamento do Data Lake Ger1 | Armazenamento do Data Lake Ger1 | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | BLOBs padrão | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | Armazenamento do Data Lake Ger2 | Não |
| 4.0 | Armazenamento do Data Lake Ger1 | Qualquer | Não |

* = Pode ser uma ou várias contas de geração 2 de armazenamento do Data Lake, desde que eles estão todos os configuração a utilizar a mesma identidade gerida para o acesso de cluster.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Utilizar o Azure Data Lake Storage Gen2 com o Apache Hadoop no HDInsight do Azure

O Azure Data Lake Gen2 de armazenamento necessário core as funcionalidades de geração 1 de armazenamento do Azure Data Lake e integra-se para o armazenamento de Blobs do Azure. Estas funcionalidades incluem um sistema de ficheiros que é compatível com o Hadoop, o Azure Active Directory (Azure AD), e listas de controlo de acesso baseadas em POSIX (ACL). Esta combinação permite-lhe tirar partido do desempenho de geração 1 de armazenamento do Azure Data Lake ao utilizar também o gerenciamento de ciclo de vida de disposição em camadas e os dados de armazenamento de Blobs.

Para obter mais informações sobre a geração 2 de armazenamento do Azure Data Lake, veja [introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade principal de geração 2 de armazenamento do Azure Data Lake

* **Acesso compatível com o Hadoop:** No ger2 de armazenamento do Azure Data Lake, pode gerir e aceder aos dados, tal como faria com um Hadoop Distributed File System (HDFS). O driver de sistema de ficheiros de Blob do Azure (ABFS) está disponível em todos os ambientes do Apache Hadoop, incluindo o Azure HDInsight e o Azure Databricks. Utilize ABFS para acessar dados armazenados na geração 2 de armazenamento do Data Lake.

* **Um conjunto mais amplo de permissões de POSIX:** O modelo de segurança para a geração 2 do Data Lake suporta permissões de ACL e POSIX, juntamente com alguns extra granularidade específica de geração 2 de armazenamento do Data Lake. Definições podem ser configuradas por meio de ferramentas de administração ou estruturas como o Apache Hive e Apache Spark.

* **Relação custo-eficácia:** Geração 2 de armazenamento do Data Lake oferece a capacidade de armazenamento de baixo custo e transações. Recursos como o ciclo de vida de armazenamento de Blobs do Azure ajudam a reduzir os custos ao ajustar as taxas de faturas, como os dados são movidos através de seu ciclo de vida.

* **Compatibilidade com aplicações, estruturas e ferramentas de armazenamento de BLOBs:** Geração 2 de armazenamento do Data Lake continua a trabalhar com um vasto leque de ferramentas, estruturas e aplicativos para o armazenamento de Blobs.

* **Controlador otimizada:** O driver ABFS está otimizado especificamente para análise de macrodados. As APIs REST correspondente são apresentadas através do ponto de final do (DFS sistema) de ficheiros distribuído, dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>O que há de novo para o Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Gerido identidades para acesso de arquivo seguro

O Azure HDInsight utiliza identidades geridas para proteger o acesso de cluster para ficheiros na geração 2 de armazenamento do Azure Data Lake. Identidades geridas são uma funcionalidade do Azure Active Directory que fornece serviços do Azure com um conjunto de credenciais automaticamente geridos. Estas credenciais podem ser utilizadas para autenticar a qualquer serviço que suporta a autenticação do Active Directory. A utilização de identidades geridas não requer armazenamento de credenciais nos arquivos de código ou configuração.

Para obter mais informações, consulte [geridos identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Driver de sistema de ficheiros Blob do Azure

Aplicações do Apache Hadoop nativamente esperam ler e gravar dados de armazenamento de disco local. Um driver de sistema de arquivos do Hadoop como ABFS permite que as aplicações do Hadoop trabalhar com o armazenamento na cloud emulando regulares operações de sistema de ficheiros Hadoop. O driver converte esses comandos recebidos da aplicação em operações que compreende a plataforma de armazenamento na cloud reais.

Anteriormente, o controlador de sistema de ficheiros Hadoop convertido todas as operações de sistema de arquivos para chamadas de API de REST de armazenamento do Azure no lado do cliente e, em seguida, invocada a API REST. Este cliente conversão, no entanto, resultado na API de REST vários chama-se para uma operação do sistema de ficheiro único, como a alteração do nome de um ficheiro. ABFS foi movido parte da lógica de sistema de ficheiros de Hadoop do lado do cliente no lado do servidor. A API de geração 2 de armazenamento do Azure Data Lake é agora executada em paralelo com a API de Blobs. Esta migração melhora o desempenho, porque agora operações comuns do sistema de ficheiro do Hadoop podem ser executadas com uma chamada à REST API.

Para obter mais informações, consulte [driver de sistema de ficheiros de Blob do Azure (ABFS): Um driver de armazenamento do Azure dedicado para o Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Esquema de URI para o Azure Data Lake Storage Gen 2 

Geração de armazenamento 2 do Azure Data Lake utiliza um novo esquema de URI para aceder aos ficheiros no armazenamento do Azure do HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

O esquema URI fornece acesso de encriptação SSL (`abfss://` prefixo) e não encriptados, acesso (`abfs://` prefixo). Utilize `abfss` sempre que possível, até mesmo ao aceder a dados que se encontrem dentro da mesma região no Azure.

`<FILE_SYSTEM_NAME>` identifica o caminho do sistema de arquivos de geração 2 de armazenamento do Data Lake.

`<ACCOUNT_NAME>` identifica o nome da conta de armazenamento do Azure. É necessário um nome de domínio completamente qualificado (FQDN).

`<PATH>` é o nome de caminho de ficheiro ou diretório HDFS.

Se os valores para `<FILE_SYSTEM_NAME>` e `<ACCOUNT_NAME>` não estiver especificado, é utilizado o sistema de ficheiros predefinido. Para os ficheiros no sistema de ficheiros predefinido, utilize um caminho relativo ou um caminho absoluto. Por exemplo, o `hadoop-mapreduce-examples.jar` ficheiro que é fornecido com clusters do HDInsight pode ser referido utilizando um dos seguintes caminhos:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> O nome de ficheiro é `hadoop-examples.jar` em clusters do HDInsight versões 2.1 e 1.6. Quando está trabalhando com arquivos fora do HDInsight, descobrirá que a maioria dos utilitários não reconhecer as ABFS formatar, mas em vez disso, esperar que um formato de caminho básico, tal como `example/jars/hadoop-mapreduce-examples.jar`.

Para obter mais informações, consulte [utilizam o URI de geração 2 de armazenamento do Azure Data Lake](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Storage do Azure

O armazenamento do Azure é uma solução de armazenamento para fins gerais robusta que se integra perfeitamente com o HDInsight. O HDInsight pode utilizar um contentor de blobs no Armazenamento do Azure como o sistema de ficheiros predefinido para o cluster. Através de uma interface HDFS, o conjunto completo de componentes no HDInsight pode operar diretamente em dados estruturados ou não estruturados armazenados como blobs.

Recomendamos que utilize contentores de armazenamento separada para o armazenamento de cluster predefinido e os seus dados de negócio, isolar os registos do HDInsight e os arquivos temporários de seus próprios dados de negócio. Também recomendamos a eliminar o contentor de BLOBs predefinido, que contém os registos de sistema da aplicação e, após cada utilização para reduzir o custo de armazenamento. Certifique-se de que obtém os registos antes de eliminar o contentor.

### <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama seguinte apresenta uma vista abstrata da arquitetura do armazenamento do Azure HDInsight:

![Diagrama que mostra como os clusters do Hadoop utilizam a API do HDFS para aceder e armazenar dados estruturados e não estruturados no armazenamento de BLOBs](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "arquitetura de armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de ficheiros distribuído que está ligado localmente aos nós de computação. É possível aceder a este sistema de ficheiros ao utilizar o URI completamente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Também pode aceder a dados no armazenamento do Azure através do HDInsight. A sintaxe é o seguinte:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Ao utilizar uma conta de armazenamento do Azure com clusters do HDInsight, considere os seguintes princípios:

* **Contentores nas contas de armazenamento que estão ligadas a um cluster:** Como o nome de conta e chave são associados ao cluster durante a criação, tem acesso total aos blobs desses contentores.

* **Contentores públicos ou blobs públicos em contas de armazenamento que são *não* ligadas a um cluster:** Tem a permissão só de leitura para os blobs dos contentores.
  
  > [!NOTE]  
  > Contentores públicos permitem que para obter uma lista de todos os blobs disponíveis nesse contentor e obter metadados do contentor. Os blobs públicos permitem aceder aos blobs apenas se souber o URL exato. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../storage/blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

* **Contentores privados em contas de armazenamento que são *não* ligadas a um cluster:** Não é possível aceder os blobs dos contentores, a menos que define a conta de armazenamento, ao submeter as tarefas de WebHCat. 

As contas do Storage definidas durante o processo de criação e as respetivas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. Por predefinição, o HDInsight utiliza as contas de armazenamento definidas no ficheiro core-site. Pode modificar esta definição utilizando [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Várias tarefas de WebHCat, incluindo Apache Hive, MapReduce, transmissão em fluxo do Apache Hadoop e Apache Pig, podem conter uma descrição das contas de armazenamento e de metadados com eles. (Isso é atualmente verdadeiro para o Pig com contas de armazenamento, mas não para os metadados.) Para obter mais informações, consulte [com um cluster do HDInsight com contas de armazenamento alternativo e metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser utilizados para dados estruturados e não estruturados. Os contentores de BLOBs armazenam dados como pares chave/valor e não ter nenhuma hierarquia de diretório. No entanto, o nome da chave pode incluir um caráter de barra (/) para fazer com que pareça como se um ficheiro é armazenado numa estrutura de diretórios. Por exemplo, a chave de um blob pode ser `input/log1.txt`. Não real `input` o diretório existe, mas devido ao caráter de barra no nome da chave, a chave se parece com um caminho de ficheiro.

### <a id="benefits"></a>Vantagens do Armazenamento do Azure
Clusters de computação e de recursos de armazenamento que não estão colocalizados tem implícita custos de desempenho. Esses custos são atenuados, a propósito, que os clusters de cálculo são criados perto dos recursos da conta de armazenamento na região do Azure. Nesta região, os nós de computação com eficiência podem acessar os dados através da rede de alta velocidade no interior do armazenamento do Azure.

Quando armazena os dados no armazenamento do Azure em vez do HDFS, obtém várias vantagens:

* **Reutilização de dados e de partilha:** Os dados no HDFS estão localizados dentro do cluster de computação. Apenas as aplicações que têm acesso ao cluster de cálculo podem utilizar os dados ao utilizar as APIs do HDFS. Por outro lado, os dados no armazenamento do Azure, podem ser acedidos através das APIs do HDFS ou o REST APIs do armazenamento de Blobs. Devido a essa organização, pode servir-se um conjunto maior de aplicações (incluindo outros clusters do HDInsight) e ferramentas para produzir e consumir os dados.

* **Arquivamento de dados:** Quando dados são armazenados no armazenamento do Azure, os clusters do HDInsight utilizados para o cálculo podem ser eliminados com segurança sem perda de dados de utilizador.

* **Custo de armazenamento de dados:** Armazenar dados no DFS a longo prazo é mais dispendioso do que armazenar os dados no armazenamento do Azure, uma vez que o custo de um cluster de cálculo é superior ao custo do armazenamento do Azure. Além disso, pois os dados não possuem ser recarregado para que cada geração de cluster de computação, estará economizando também os custos de carregamento de dados.

* **Aumento horizontal elástico:** Embora o HDFS forneça um sistema de ficheiros de escalamento horizontal, o dimensionamento é determinado pelo número de nós que criar para o seu cluster. A alteração do dimensionamento pode ser mais complicada do que depender das capacidades que obtém automaticamente no armazenamento do Azure de dimensionamento elástico.

* **Georreplicação:** Armazenamento do Azure pode ser georreplicado. Embora os replicação geográfica dá-lhe recuperação geográfica e redundância de dados, uma ativação pós-falha para a localização georreplicada afeta gravemente o desempenho e ele pode implicar custos adicionais. Por isso, escolha a georreplicação com cuidado e apenas se o valor dos dados justifica o custo adicional.

Determinados pacotes e tarefas de MapReduce podem criar resultados intermédios que não pretende armazenar no armazenamento do Azure. Nesse caso, pode optar por armazenar os dados no local HDFS. HDInsight utiliza o DFS para vários destes resultados intermédios nas tarefas do Hive e outros processos.

> [!NOTE]  
> Maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal`, e `mkdir`) funcionar conforme esperado no armazenamento do Azure. Apenas os comandos que são específicos para a implementação nativa do HDFS (que é conhecida como DFS), tal como `fschk` e `dfsadmin`, apresentam um comportamento diferente no armazenamento do Azure.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Descrição geral do Azure Data Lake Storage Gen1

Geração de armazenamento 1 do Azure Data Lake é um repositório de hiperescala de toda a empresa para cargas de trabalho de análise de macrodados. Utilizar o Azure Data Lake, pode capturar dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais.

Acesso Data Lake Storage Gen1 do Hadoop (disponível com um cluster do HDInsight) utilizando as APIs de REST compatíveis com WebHDFS. Geração 1 de armazenamento do Data Lake foi concebido para permitir análises em dados armazenados e está otimizado para desempenho em cenários de análise de dados. Uso imediato, inclui as funcionalidades que são essenciais para casos de utilização empresarial reais. Esses recursos incluem segurança, capacidade de gerenciamento, escalabilidade, fiabilidade e disponibilidade.

Para obter mais informações sobre a geração 1 de armazenamento do Azure Data Lake, veja o detalhadas [descrição geral do Azure Data Lake armazenamento Gen1](../data-lake-store/data-lake-store-overview.md).

Seguem-se as principais capacidades de geração 1 de armazenamento do Data Lake.

### <a name="compatibility-with-hadoop"></a>Compatibilidade com o Hadoop

Geração 1 de armazenamento do Data Lake é um sistema de ficheiros Apache Hadoop compatível com HDFS e funciona com o ecossistema Hadoop.  O HDInsight aplicações ou serviços que utilizam a API de WebHDFS podem integrar facilmente com geração 1 de armazenamento do Data Lake. Geração 1 de armazenamento do Data Lake também expõe uma interface REST compatível com WebHDFS para aplicações.

Dados armazenados no Data Lake Storage Gen1 podem ser facilmente analisados utilizando as estruturas de análise de Hadoop, tais como MapReduce ou Hive. Clusters do HDInsight do Azure podem ser aprovisionados e configurados para aceder diretamente aos dados armazenados no Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, ficheiros petabyte

Geração 1 de armazenamento do Data Lake fornece armazenamento ilimitado e é adequado para armazenar diversos dados para análise. Ele não impõe limites de tamanhos de conta, tamanhos de ficheiro ou a quantidade de dados que podem ser armazenados num data lake. Ficheiros individuais podem variar de tamanho de quilobytes a petabytes, fazendo uma escolha ideal para armazenar qualquer tipo de dados de geração 1 de armazenamento do Data Lake. Os dados são armazenados de maneira duradoura, fazendo várias cópias e não há nenhum limite no quanto os dados podem ser armazenados no data lake.

### <a name="performance-tuning-for-big-data-analytics"></a>Ajuste de desempenho para grandes volumes de dados analytics

Geração 1 de armazenamento do Data Lake foi concebido para executar sistemas de análise em grande escala que requeiram débito em massa para consultar e analisar grandes quantidades de dados. O data lake propaga partes de um ficheiro por vários servidores de armazenamento individuais. Quando está a analisar dados, esta configuração melhora o débito de leitura quando o arquivo é lido em paralelo.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Preparação para a empresa: Elevada disponibilidade e segura

Geração 1 de armazenamento do Data Lake fornece disponibilidade de norma da indústria e fiabilidade. Recursos de dados são armazenados de maneira duradoura: cópias redundantes proteção contra falhas inesperadas. As empresas podem utilizar a geração 1 de armazenamento do Data Lake nas respetivas soluções como uma parte importante da respetiva plataforma de dados.

Geração 1 de armazenamento do Data Lake também fornece segurança de nível empresarial para os dados armazenados. Para obter mais informações, consulte [proteger dados no Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Estruturas de dados flexíveis

Geração 1 de armazenamento do Data Lake pode armazenar quaisquer dados em seu formato nativo, tal como está, sem a necessidade de transformações anteriores. Geração 1 de armazenamento do Data Lake não requer um esquema antes dos dados são carregados. A estrutura de análise individual interpreta os dados e define um esquema no momento da análise. Uma vez que ele pode armazenar ficheiros de formatos e tamanhos arbitrários, Gen1 de armazenamento do Data Lake pode processar dados não estruturados, semi-estruturados e estruturados.

Contentores de Lake Gen1 de armazenamento de dados para os dados são essencialmente pastas e ficheiros. Operar nos dados armazenados com SDKs, o portal do Azure e o Azure PowerShell. Desde que coloque os dados para o arquivo utilizando estas interfaces e os contentores adequados, pode armazenar qualquer tipo de dados. Geração 1 de armazenamento do Data Lake não executa qualquer processamento especial de dados com base no tipo de dados que armazena.

## <a name="DataLakeStoreSecurity"></a>Segurança de dados no Data Lake Storage Gen1
Utiliza o Data Lake Gen1 de armazenamento do Azure Active Directory para acesso de autenticação e utiliza controla listas (ACLs) para gerir o acesso aos seus dados.

| **Funcionalidade** | **Descrição** |
| --- | --- |
| Authentication |Geração 1 de armazenamento do Data Lake integra-se com o Azure Active Directory (Azure AD) para a gestão de identidades e acessos para todos os dados armazenados no Data Lake Storage Gen1. Devido à integração, Gen1 de armazenamento do Data Lake beneficia-se de todas as funcionalidades do Azure AD. Estas funcionalidades incluem a autenticação multifator, acesso condicional, controlo de acesso baseado em funções, monitorização da utilização de aplicações, monitorização de segurança e alertas e assim por diante. Geração 1 de armazenamento do Data Lake suporta o protocolo OAuth 2.0 para a autenticação dentro da interface REST. Consulte [autenticação dentro de geração 1 de armazenamento do Azure Data Lake com o Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controlo de acesso |Geração 1 de armazenamento do Data Lake fornece controlo de acesso ao suportar permissões do estilo POSIX expostas pelo protocolo WebHDFS. As ACLs podem ser ativadas na pasta raiz, nas subpastas e nos ficheiros individuais. Para obter mais informações sobre como as ACLs funcionam no contexto de geração 1 de armazenamento do Data Lake, veja [controlo de acesso no Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Encriptação |Geração 1 de armazenamento do Data Lake também fornece encriptação para dados armazenados na conta. Especifique as definições de encriptação durante a criação de uma conta de geração 1 de armazenamento do Data Lake. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Para obter mais informações, consulte [encriptação no Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Para obter instruções sobre como fornecer uma configuração relacionada com encriptação, consulte [introdução à geração 1 de armazenamento do Azure Data Lake com o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Para saber mais sobre como proteger dados na geração 1 de armazenamento do Data Lake, veja [proteger os dados armazenados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplicativos que são compatíveis com a geração 1 de armazenamento do Data Lake
Geração 1 de armazenamento do Data Lake é compatível com a maioria dos componentes de código-fonte aberto no ecossistema do Hadoop. Também é integrado corretamente com outros serviços do Azure.  Siga os links abaixo para obter mais informações sobre como geração 1 de armazenamento do Data Lake pode ser utilizado tanto com componentes de código-fonte aberto, bem como outros serviços do Azure.

* Ver [aplicações de macrodados de código-fonte aberto que funcionam com o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicações de código-fonte aberto que interoperam com geração 1 de armazenamento do Data Lake.
* Ver [integrar o Azure Data Lake armazenamento Gen1 com outros serviços do Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) para compreender como utilizar Gen1 de armazenamento do Data Lake com outros serviços do Azure para ativar uma vasta gama de cenários.
* Ver [usando o Azure Data Lake armazenamento Gen1 para requisitos de grandes volumes de dados](../data-lake-store/data-lake-store-data-scenarios.md) para aprender a utilizar o Data Lake Storage Gen1 em cenários como a ingestão de dados, processamento de dados, transferência de dados e visualização de dados.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Sistema de ficheiros do Data Lake Storage Gen1 (adl: / /)
Em ambientes do Hadoop (disponíveis com um cluster do HDInsight), pode aceder a geração 1 de armazenamento do Data Lake através do novo sistema de arquivos, o AzureDataLakeFilesystem (adl: / /). O desempenho de aplicações e serviços que utilizam adl: / / pode ser otimizado de forma a que não está atualmente disponível no WebHDFS. Como resultado, quando utilizar o Data Lake Storage Gen1, obtém a flexibilidade para o estado de prontidão o melhor desempenho ao utilizar o adl recomendada: / / ou manter o código existente ao continuar a utilizar a API WebHDFS diretamente. Azure HDInsight tira total partido do AzureDataLakeFilesystem para proporcionar o melhor desempenho na geração 1 de armazenamento do Data Lake.

Aceder aos seus dados na geração 1 de armazenamento do Data Lake ao utilizar o seguinte:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Para obter mais informações sobre como acessar os dados na geração 1 de armazenamento do Data Lake, veja [ações disponíveis nos dados armazenados](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)