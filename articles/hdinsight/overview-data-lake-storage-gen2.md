---
title: Visão geral do Azure Data Lake Storage Gen2 em HDInsight
description: Visão geral do Data Lake Storage Gen2 em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873345"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Visão geral do Azure Data Lake Storage Gen2 em HDInsight

O Azure Data Lake Storage Gen2 tem características fundamentais do Azure Data Lake Storage Gen1 e integra-as no armazenamento azure Blob. Estas funcionalidades incluem um sistema de ficheiros compatível com Hadoop, Azure Ative Directory (Azure AD) e listas de controlo de acesso baseadas em POSIX (ACLs). Esta combinação permite-lhe aproveitar o desempenho do Azure Data Lake Storage Gen1. Ao mesmo tempo que utiliza o tiering e a gestão do ciclo de vida dos dados do armazenamento blob.

Para mais informações sobre o Azure Data Lake Storage Gen2, consulte Introdução ao Armazenamento de [Lagos De dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade central do Azure Data Lake Storage Gen2

* **Acesso compatível com Hadoop:** No Azure Data Lake Storage Gen2, pode gerir e aceder a dados tal como faria com um Sistema de Ficheiros Distribuídos hadoop (HDFS). O controlador do Sistema de Ficheiros Azure Blob (ABFS) está disponível em todos os ambientes Apache Hadoop, incluindo Azure HDInsight e Azure Databricks. Utilize a ABFS para aceder aos dados armazenados no Data Lake Storage Gen2.

* **Um superconjunto de permissões POSIX:** O modelo de segurança para Data Lake Gen2 suporta permissões ACL e POSIX juntamente com alguma granularidade extra específica para Data Lake Storage Gen2. As definições podem ser configuradas através de ferramentas ou estruturas administrativas como a Apache Hive e a Apache Spark.

* **Eficácia dos custos:** Data Lake Storage Gen2 oferece capacidade de armazenamento de baixo custo e transações. Os ciclos de vida de armazenamento da Azure Blob ajudam a reduzir os custos, ajustando as taxas de faturação à medida que os dados se movem ao longo do seu ciclo de vida.

* **Compatibilidade com ferramentas de armazenamento Blob, quadros e aplicações:** Data Lake Storage Gen2 continua a trabalhar com uma grande variedade de ferramentas, quadros e aplicações para armazenamento blob.

* **Condutor otimizado:** O condutor da ABFS está otimizado especificamente para análise de big data. As APIs de repouso correspondentes são surgidas através do ponto final do sistema de ficheiros distribuído (DFS), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Novidades para Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Identidades geridas para acesso seguro de ficheiros

O Azure HDInsight utiliza identidades geridas para garantir o acesso ao cluster a ficheiros em Azure Data Lake Storage Gen2. Identidades geridas são uma característica do Azure Ative Directory que fornece aos serviços Azure um conjunto de credenciais geridas automaticamente. Estas credenciais podem ser utilizadas para autenticar qualquer serviço que suporte a autenticação do Diretório Ativo. A utilização de identidades geridas não requer que guarde credenciais em códigoou ficheiros de configuração.

Para mais informações, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Condutor do sistema de ficheiros Azure Blob

As aplicações Apache Hadoop esperam que leiam e escrevam dados do armazenamento local do disco. Um controlador de sistema de ficheiros Hadoop como o ABFS permite que as aplicações Hadoop funcionem com armazenamento em nuvem. Funciona emitindo operações regulares do sistema de ficheiros Hadoop. O condutor converte os comandos recebidos da aplicação em operações que a plataforma real de armazenamento em nuvem compreende.

Anteriormente, o controlador do sistema de ficheiros Hadoop converteu todas as operações do sistema de ficheiros para chamadas a API do Lado do Cliente. E, em seguida, invocou a API REST. Esta conversão do lado do cliente, no entanto, resultou em múltiplas chamadas REST API para uma única operação do sistema de ficheiros como o renomeamento de um ficheiro. A ABFS moveu a lógica do sistema de ficheiros Hadoop do lado do cliente para o lado do servidor. A API de Armazenamento de Lago de Dados Azure Gen2 agora funciona em paralelo com a API Blob. Esta migração melhora o desempenho porque agora as operações comuns do sistema de ficheiros Hadoop podem ser executadas com uma chamada de API REST.

Para mais informações, consulte [o controlador de ficheiros Azure Blob (ABFS): um controlador de armazenamento azure dedicado para Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Esquema URI para armazenamento de lagos de dados Azure Gen 2

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

> [!NOTE]
> O nome `hadoop-examples.jar` do ficheiro está nas versões HDInsight 2.1 e 1.6. Quando estiver a trabalhar com ficheiros fora do HDInsight, verá que a maioria dos utilitários não reconhece `example/jars/hadoop-mapreduce-examples.jar`o formato ABFS, mas espera um formato de caminho básico, como .

Para mais informações, consulte [Use o Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Armazenamento de Lagos De Dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Visão geral do Armazenamento do Lago Azure Data Gen1](./overview-data-lake-storage-gen1.md)