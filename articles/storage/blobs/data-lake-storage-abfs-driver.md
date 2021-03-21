---
title: O controlador de files Azure Blob para Azure Data Lake Storage Gen2
description: Saiba mais sobre o controlador de files Azure Blob (ABFS), um controlador dedicado de armazenamento Azure para Hadoop. Aceder a dados em Azure Data Lake Storage Gen2 utilizando este motorista.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 54c6245ea5290e2cab0efcd52f2208ba874c0848
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720448"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>O controlador de files Azure Blob (ABFS): Um controlador dedicado de armazenamento Azure para Hadoop

Um dos principais métodos de acesso aos dados na Azure Data Lake Storage Gen2 é através do [Hadoop FileSystem.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html) Data Lake Storage Gen2 permite aos utilizadores de Azure Blob Storage acesso a um novo controlador, o controlador do Sistema de Ficheiros Azure Blob ou `ABFS` . A ABFS faz parte da Apache Hadoop e está incluída em muitas das distribuições comerciais da Hadoop. Utilizando este controlador, muitas aplicações e estruturas podem aceder a dados no Azure Blob Storage sem qualquer código que se refere explicitamente à Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Capacidade prévia: O controlador blob de armazenamento do Windows Azure

O controlador windows Azure Storage Blob ou [o controlador WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) forneceu o suporte original para o Armazenamento Azure Blob. Este controlador executou a complexa tarefa de mapeamento da semântica do sistema de ficheiros (conforme exigido pela interface Hadoop FileSystem) à interface do estilo de loja de objetos exposta pelo Azure Blob Storage. Este controlador continua a apoiar este modelo, proporcionando acesso de alto desempenho aos dados armazenados em blobs, mas contém uma quantidade significativa de código que executa este mapeamento, dificultando a manutenção. Além disso, algumas operações como [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) e [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) quando aplicadas a diretórios exigem que o condutor execute um grande número de operações (devido à falta de suporte de lojas de objetos de oposição para diretórios) o que muitas vezes leva a um desempenho degradado. O condutor da ABFS foi concebido para superar as deficiências inerentes ao WASB.

## <a name="the-azure-blob-file-system-driver"></a>O controlador do sistema de ficheiros Azure Blob

A [interface Azure Data Lake Storage REST](/rest/api/storageservices/data-lake-storage-gen2) foi concebida para suportar a semântica do sistema de ficheiros sobre o armazenamento de Azure Blob. Dado que o Hadoop FileSystem também foi concebido para suportar a mesma semântica, não existe qualquer requisito para um mapeamento complexo no controlador. Assim, o controlador do Sistema de Ficheiros Azure Blob (ou ABFS) é um mero shim cliente para a API REST.

No entanto, existem algumas funções que o condutor ainda deve desempenhar:

### <a name="uri-scheme-to-reference-data"></a>Esquema URI para dados de referência

Em consonância com outras implementações do FileSystem dentro da Hadoop, o controlador ABFS define o seu próprio esquema URI para que os recursos (diretórios e ficheiros) possam ser claramente abordados. O regime URI é documentado na [Utilização do Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md). A estrutura do URI é: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Utilizando o formato URI acima, podem ser utilizadas ferramentas e quadros padrão hadoop para fazer referência a estes recursos:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Internamente, o controlador ABFS traduz os recursos especificados no URI para ficheiros e diretórios e faz chamadas para a API de Armazenamento de Lagos de Dados Azure com essas referências.

### <a name="authentication"></a>Autenticação

O condutor da ABFS suporta duas formas de autenticação para que a aplicação Hadoop possa aceder de forma segura aos recursos contidos numa conta de Data Lake Storage Gen2 capaz. Todos os detalhes dos esquemas de autenticação disponíveis são fornecidos no guia de [segurança do Azure Storage](security-recommendations.md). A saber:

- **Chave partilhada:** Isto permite aos utilizadores aceder a TODOS os recursos na conta. A chave é encriptada e armazenada na configuração de Hadoop.

- **Azure Ative Directory OAuth Bearer Token:** As fichas portadoras de Azure são adquiridas e refrescadas pelo condutor utilizando a identidade do utilizador final ou um diretor de serviço configurado. Utilizando este modelo de autenticação, todo o acesso é autorizado por chamada utilizando a identidade associada ao token fornecido e avaliado contra a lista de controlo de acesso posix atribuída (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 suporta apenas pontos finais Azure AD v1.0.

### <a name="configuration"></a>Configuração

Toda a configuração do controlador ABFS é armazenada no <code>core-site.xml</code> ficheiro de configuração. Nas distribuições de Hadoop com [Ambari,](https://ambari.apache.org/)a configuração também pode ser gerida através do portal web ou da AMBAri REST API.

Os detalhes de todas as entradas de configuração suportadas estão especificados na documentação oficial do [Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Documentação hadoop

O motorista da ABFS está totalmente documentado na documentação oficial do [Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Passos seguintes

- [Criar um cluster de databricks Azure](./data-lake-storage-use-databricks-spark.md)
- [Utilizar o URI do Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md)