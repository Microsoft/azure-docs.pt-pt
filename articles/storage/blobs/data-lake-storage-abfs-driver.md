---
title: O driver de sistema de ficheiros do Azure Blob para a geração 2 de armazenamento do Azure Data Lake
description: O driver de sistema de ficheiros do ABFS Hadoop
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 83e2f6f42de5c729667f366a6e068f1c8bd71f02
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011065"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>O driver de sistema de ficheiros do Azure Blob (ABFS): Um driver de armazenamento do Azure dedicado para o Hadoop

Um dos métodos de acesso primária para os dados na geração 2 de armazenamento do Azure Data Lake é através da [sistema de ficheiros Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Geração 2 de armazenamento do Data Lake permite aos utilizadores de acesso de armazenamento de Blobs do Azure para um novo driver, o driver de sistema de ficheiros de Blob do Azure ou `ABFS`. ABFS faz parte do Apache Hadoop e está incluído em muitas das distribuições comerciais do Hadoop. Utilizar este controlador, muitos aplicativos e estruturas podem aceder a dados no armazenamento de Blobs do Azure sem qualquer código referenciar explicitamente a geração 2 de armazenamento do Data Lake.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Capacidade anterior: O driver do Windows Azure Storage Blob

O driver do Windows Azure Storage Blob ou [controlador WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) fornecido o suporte original para o armazenamento de Blobs do Azure. Este controlador efetuada a complexa tarefa de sistema de ficheiros de mapeamento semântica (conforme exigido pela interface do sistema de ficheiros do Hadoop) para que o objeto armazenar interface no estilo exposto pelo armazenamento de Blobs do Azure. Este controlador continua a suportar esse modelo, fornecendo acesso de alto desempenho para dados armazenados em Blobs, mas contém uma quantidade significativa de código realizar esse mapeamento, tornando difícil de manter. Além disso, algumas operações, tais como [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) e [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) quando aplicado aos diretórios requerem o driver para executar um grande número de operações (devido à ausência de arquivos de objeto suporte para diretórios) que, muitas vezes, leva a degradação do desempenho. O driver ABFS foi projetado para superar as deficiências inerentes de WASB.

## <a name="the-azure-blob-file-system-driver"></a>O driver de sistema de ficheiros de Blob do Azure

O [interface REST de armazenamento do Azure Data Lake](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) foi concebido para suportar a semântica de sistema de ficheiros do Azure no armazenamento de Blobs. Uma vez que o sistema de ficheiros Hadoop também foi concebido para suportar a mesma semântica não existe nenhum requisito para um mapeamento complexo no driver. Portanto, o driver de sistema de ficheiros de Blob do Azure (ou ABFS) é um shim meros de cliente para a API REST.

No entanto, existem algumas funções que o controlador deve executar:

### <a name="uri-scheme-to-reference-data"></a>Esquema URI para dados de referência

Consistente com outras implementações de sistema de ficheiros dentro do Hadoop, o driver ABFS define seu próprio esquema URI, de modo a que recursos (diretórios e arquivos) podem ser resolvidos de forma distinta. O esquema URI está documentado no [utilizam o URI de geração 2 de armazenamento do Azure Data Lake](./data-lake-storage-introduction-abfs-uri.md). A estrutura do URI é: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Usando o formato URI acima, padrão de ferramentas do Hadoop e estruturas podem ser utilizadas para fazer referência a esses recursos:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Internamente, o driver ABFS traduz-se o recurso ou recursos especificados no URI a arquivos e diretórios e faz chamadas para a API de REST de armazenamento do Azure Data Lake com essas referências.

### <a name="authentication"></a>Authentication

O driver ABFS suporta duas formas de autenticação para que a aplicação do Hadoop com segurança pode aceder aos recursos contidos dentro de uma conta com capacidade de geração 2 de armazenamento do Data Lake. Todos os detalhes de esquemas de autenticação disponíveis são fornecidos na [guia de segurança do armazenamento do Azure](../common/storage-security-guide.md). São:

- **Chave partilhada:** Isto permite aos utilizadores acesso a todos os recursos na conta. A chave é encriptada e armazenada na configuração do Hadoop.

- **Token de portador do OAuth do Azure Active Directory:** Os tokens de portador do Azure AD são obtidos e atualizados pelo driver usando a identidade do utilizador final ou um Principal de serviço configurado. Usando esse modelo de autenticação, todo o acesso está autorizado numa base por chamada usando a identidade associada ao token fornecido e avaliados em relação a atribuído POSIX controlo lista acesso (ACL).

### <a name="configuration"></a>Configuração

Toda a configuração para o controlador ABFS é armazenado no <code>core-site.xml</code> ficheiro de configuração. Nas distribuições Hadoop destacando [Ambari](https://ambari.apache.org/), a configuração também pode ser gerenciada com o web portal ou a API REST do Ambari.

Detalhes de todas as entradas de configuração suportados estão especificados na [documentação oficial Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Documentação do Hadoop

O driver ABFS é documentado na íntegra a [documentação oficial Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Passos Seguintes

- [Criar um Cluster do Databricks do Azure](./data-lake-storage-quickstart-create-databricks-account.md)
- [Utilize a geração 2 Lake armazenamento de dados do Azure URI](./data-lake-storage-introduction-abfs-uri.md)
