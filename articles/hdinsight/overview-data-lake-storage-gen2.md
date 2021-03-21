---
title: Visão geral do Azure Data Lake Storage Gen2 em HDInsight
description: Visão geral do Data Lake Storage Gen2 em HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e69838f18efc08d0f64dd9ea904f502617073a8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938859"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Visão geral do Azure Data Lake Storage Gen2 em HDInsight

A Azure Data Lake Storage Gen2 retira funcionalidades centrais do Azure Data Lake Storage Gen1 e integra-as no armazenamento do Azure Blob. Estas funcionalidades incluem um sistema de ficheiros compatível com Hadoop, Azure Ative Directory (Azure AD) e listas de controlo de acesso com base em POSIX (ACLs). Esta combinação permite-lhe aproveitar o desempenho da Azure Data Lake Storage Gen1. Ao mesmo tempo que utiliza a gestão do ciclo de vida de tiering e dados do armazenamento blob.

Para obter mais informações sobre a Azure Data Lake Storage Gen2, consulte [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade central do Azure Data Lake Storage Gen2

* **Acesso compatível com Hadoop:** No Azure Data Lake Storage Gen2, pode gerir e aceder aos dados tal como faria com um Sistema de Ficheiros Distribuídos Hadoop (HDFS). O controlador Azure Blob File System (ABFS) está disponível em todos os ambientes apache Hadoop, incluindo Azure HDInsight e Azure Databricks. Utilize o ABFS para aceder aos dados armazenados na Data Lake Storage Gen2.

* **Um superconjunto de permissões POSIX:** O modelo de segurança para data lake gen2 suporta permissões ACL e POSIX juntamente com alguma granularidade extra específica para Data Lake Storage Gen2. As configurações podem ser configuradas através de ferramentas de administração ou estruturas como Apache Hive e Apache Spark.

* **Eficácia dos custos:** Data Lake Storage Gen2 oferece capacidade de armazenamento de baixo custo e transações. Os ciclos de vida de armazenamento Azure Blob ajudam a reduzir os custos ajustando as taxas de faturação à medida que os dados se movem ao longo do seu ciclo de vida.

* **Compatibilidade com ferramentas de armazenamento Blob, quadros e aplicativos:** Data Lake Storage Gen2 continua a trabalhar com uma grande variedade de ferramentas, estruturas e aplicações para armazenamento blob.

* **Condutor otimizado:** O condutor da ABFS está otimizado especificamente para análise de big data. As APIs de REST correspondentes são surgidas através do ponto final do sistema de ficheiros distribuído (DFS), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Novidades para Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Identidades geridas para acesso seguro a ficheiros

Azure HDInsight utiliza identidades geridas para garantir o acesso do cluster a ficheiros em Azure Data Lake Storage Gen2. As identidades geridas são uma característica do Azure Ative Directory que fornece serviços Azure com um conjunto de credenciais geridas automaticamente. Estas credenciais podem ser usadas para autenticar qualquer serviço que suporte a autenticação do Ative Directory. A utilização de identidades geridas não requer que guarde credenciais em ficheiros de código ou de configuração.

Para obter mais informações, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Controlador do sistema de ficheiros Azure Blob

As aplicações Apache Hadoop esperam, nativamente, ler e escrever dados do armazenamento de discos locais. Um controlador de sistema de ficheiros Hadoop como o ABFS permite que as aplicações Hadoop funcionem com armazenamento em nuvem. Funciona através da emulação das operações regulares do sistema de ficheiros Hadoop. O controlador converte os comandos recebidos da aplicação em operações que a plataforma de armazenamento em nuvem compreende.

Anteriormente, o controlador do sistema de ficheiros Hadoop converteu todas as operações do sistema de ficheiros para chamadas Azure Storage REST API do lado do cliente. E, em seguida, invocou a API REST. Esta conversão do lado do cliente, no entanto, resultou em múltiplas chamadas de API REST para uma única operação do sistema de ficheiros como a renomeação de um ficheiro. A ABFS moveu a lógica do sistema de ficheiros Hadoop do lado do cliente para o lado do servidor. O Azure Data Lake Storage Gen2 API funciona agora em paralelo com a API Blob. Esta migração melhora o desempenho porque agora as operações comuns do sistema de ficheiros Hadoop podem ser executadas com uma chamada de API REST.

Para obter mais informações, consulte [o controlador de ficheiros Azure Blob (ABFS): Um controlador de armazenamento dedicado da Azure para a Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Esquema URI para Azure Data Lake Storage Gen 2

A Azure Data Lake Storage Gen2 usa um novo esquema URI para aceder a ficheiros em Azure Storage a partir de HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

O esquema URI fornece acesso encriptado SSL.

`<FILE_SYSTEM_NAME>` identifica o caminho do sistema de ficheiros Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifica o nome da conta Azure Storage. É necessário um nome de domínio completamente qualificado (FQDN).

`<PATH>` é o nome do caminho do ficheiro ou do diretório HDFS.

Se os valores `<FILE_SYSTEM_NAME>` forem e `<ACCOUNT_NAME>` não especificados, o sistema de ficheiros predefinido é utilizado. Para os ficheiros no sistema de ficheiros predefinidos, utilize um caminho relativo ou um caminho absoluto. Por exemplo, o `hadoop-mapreduce-examples.jar` ficheiro que vem com clusters HDInsight pode ser referido utilizando um dos seguintes caminhos:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> O nome do ficheiro está `hadoop-examples.jar` nas versões HDInsight 2.1 e 1.6. Quando estiver a trabalhar com ficheiros fora do HDInsight, verá que a maioria dos utilitários não reconhece o formato ABFS, mas em vez disso espera um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar` .

Para obter mais informações, [consulte o Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Descrição geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)