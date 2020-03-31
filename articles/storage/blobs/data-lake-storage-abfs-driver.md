---
title: O condutor do sistema de ficheiros Azure Blob para o Azure Data Lake Storage Gen2
description: O controlador de ficheiros Hadoop ABFS
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3db039d39ef532ea51143dc9cbdb6bd5f29d6225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75970281"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>O controlador de ficheiros Azure Blob (ABFS): um controlador de armazenamento azure dedicado para Hadoop

Um dos principais métodos de acesso aos dados no Azure Data Lake Storage Gen2 é através do [Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Data Lake Storage Gen2 permite aos utilizadores do Armazenamento Azure Blob acesso a `ABFS`um novo controlador, o controlador do Sistema de Ficheiros Azure Blob ou . A ABFS faz parte da Apache Hadoop e está incluída em muitas das distribuições comerciais de Hadoop. Utilizando este controlador, muitas aplicações e estruturas podem aceder a dados no Armazenamento De Blob Azure sem qualquer código que refira explicitamente data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Capacidade prévia: O controlador Blob de Armazenamento Windows Azure

O controlador Windows Azure Storage Blob ou [o controlador WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) forneceram o suporte original para o Armazenamento De Blob Azure. Este controlador executou a complexa tarefa de mapear a semântica do sistema de ficheiros (conforme exigido pela interface Hadoop FileSystem) à interface do estilo da loja de objetos exposta pelo Armazenamento Azure Blob. Este controlador continua a apoiar este modelo, proporcionando acesso de alto desempenho aos dados armazenados em blobs, mas contém uma quantidade significativa de código que executa este mapeamento, dificultando a manutenção. Além disso, algumas operações como [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) e [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) quando aplicadas a diretórios exigem que o condutor realize um grande número de operações (devido à falta de suporte para diretórios) que muitas vezes leva a um desempenho degradado. O condutor da ABFS foi concebido para ultrapassar as deficiências inerentes do WASB.

## <a name="the-azure-blob-file-system-driver"></a>O controlador do sistema de ficheiros Azure Blob

A [interface Azure Data Lake Storage REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) foi concebida para suportar a semântica do sistema de ficheiros sobre o Armazenamento de Blob Azure. Dado que o Sistema de Ficheiros Hadoop também foi concebido para suportar a mesma semântica, não existe qualquer exigência para um mapeamento complexo no condutor. Assim, o controlador do Sistema de Ficheiros Azure Blob (ou ABFS) é um mero cliente para a API REST.

No entanto, existem algumas funções que o condutor deve ainda desempenhar:

### <a name="uri-scheme-to-reference-data"></a>Regime URI para dados de referência

Em consonância com outras implementações do Sistema de Ficheiros dentro do Hadoop, o condutor da ABFS define o seu próprio esquema URI para que os recursos (diretórios e ficheiros) possam ser claramente abordados. O regime URI está documentado na [Utilização do Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md). A estrutura do URI é:`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Utilizando o formato URI acima, as ferramentas e estruturas padrão hadoop podem ser usadas para fazer referência a estes recursos:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Internamente, o condutor da ABFS traduz os recursos(s) especificados no URI para ficheiros e diretórios e faz chamadas para a API de Armazenamento de Lagos de Dados Azure com essas referências.

### <a name="authentication"></a>Autenticação

O condutor da ABFS suporta duas formas de autenticação para que a aplicação Hadoop possa aceder de forma segura aos recursos contidos numa conta de Armazenamento de Data Lake Gen2 capaz. Todos os detalhes dos esquemas de autenticação disponíveis são fornecidos no guia de [segurança do Armazenamento Azure](security-recommendations.md). São:

- **Chave partilhada:** Isto permite aos utilizadores o acesso a TODOS os recursos da conta. A chave é encriptada e armazenada na configuração Hadoop.

- **Token do Porta-Estandarte Ativo Azure OAuth:** Os tokens do portador da AD Azure são adquiridos e atualizados pelo condutor utilizando a identidade do utilizador final ou um Diretor de Serviço configurado. Utilizando este modelo de autenticação, todo o acesso é autorizado por chamada utilizando a identidade associada ao símbolo fornecido e avaliada com a lista de controlo de acesso saqueada pela POSIX (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 suporta apenas pontos finais Azure AD v1.0.

### <a name="configuration"></a>Configuração

Toda a configuração do controlador ABFS está armazenada no <code>core-site.xml</code> ficheiro de configuração. Nas distribuições hadoop com [Ambari,](https://ambari.apache.org/)a configuração também pode ser gerida usando o portal web ou Ambari REST API.

Os detalhes de todas as entradas de configuração suportadas são especificados na documentação oficial do [Hadoop.](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

### <a name="hadoop-documentation"></a>Documentação de Hadoop

O condutor da ABFS está totalmente documentado na documentação oficial do [Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Passos seguintes

- [Criar um cluster de tijolos de dados Azure](./data-lake-storage-quickstart-create-databricks-account.md)
- [Utilizar o URI do Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md)
