---
title: O driver do sistema de arquivos de blob do Azure para Azure Data Lake Storage Gen2
description: O driver do sistema de arquivos Hadoop ABFS
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6e74830a3a62ea54c5d8e7f9815fe2ba6eed6d58
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166499"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>O driver do sistema de arquivos de blob do Azure (ABFS): Um driver de armazenamento do Azure dedicado para Hadoop

Um dos principais métodos de acesso para dados no Azure Data Lake Storage Gen2 é por meio do [sistema de arquivos do Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Data Lake Storage Gen2 permite que os usuários do armazenamento de BLOBs do Azure acessem um novo driver, o driver do sistema de arquivos do blob do Azure ou o `ABFS`. O ABFS faz parte do Apache Hadoop e está incluído em muitas das distribuições comerciais do Hadoop. Usando esse driver, muitos aplicativos e estruturas podem acessar dados no armazenamento de BLOBs do Azure sem qualquer código explicitamente referenciando Data Lake Storage Gen2. 

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Funcionalidade anterior: O driver de Azure Storage Blob do Windows

O driver do Windows Azure Storage Blob ou [Driver WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) forneceu o suporte original para o armazenamento de BLOBs do Azure. Esse driver realizou a tarefa complexa de mapear a semântica do sistema de arquivos (conforme exigido pela interface do sistema de arquivos do Hadoop) para a interface do estilo de armazenamento de objetos exposta pelo armazenamento de BLOBs do Azure. Esse driver continua a oferecer suporte a esse modelo, fornecendo acesso de alto desempenho aos dados armazenados em BLOBs, mas contém uma quantidade significativa de código que executa esse mapeamento, dificultando a manutenção. Além disso, algumas operações como [FileSystem. Rename ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) e [FileSystem. Delete ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) quando aplicadas aos diretórios exigem que o driver execute um grande número de operações (devido à falta de suporte para diretórios de armazenamento de objetos) que geralmente leva para degradar o desempenho. O driver ABFS foi projetado para superar as deficiências inerentes de WASB.

## <a name="the-azure-blob-file-system-driver"></a>O driver do sistema de arquivos do blob do Azure

A [interface REST do Azure data Lake Storage](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) foi projetada para dar suporte à semântica do sistema de arquivos no armazenamento de BLOBs do Azure. Considerando que o sistema de arquivos do Hadoop também foi projetado para dar suporte à mesma semântica, não há nenhum requisito para um mapeamento complexo no driver. Assim, o driver do sistema de arquivos do blob do Azure (ou ABFS) é um mero Shim do cliente para a API REST.

No entanto, há algumas funções que o driver ainda deve executar:

### <a name="uri-scheme-to-reference-data"></a>Esquema de URI para dados de referência

Consistente com outras implementações de sistema de arquivos no Hadoop, o driver ABFS define seu próprio esquema de URI para que os recursos (diretórios e arquivos) possam ser endereçados de forma distinta. O esquema de URI está documentado em [usar o URI de Azure data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md). A estrutura do URI é: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Usando o formato de URI acima, as ferramentas e estruturas padrão do Hadoop podem ser usadas para fazer referência a esses recursos:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Internamente, o driver ABFS converte os recursos especificados no URI para arquivos e diretórios e faz chamadas para a API REST do Azure Data Lake Storage com essas referências.

### <a name="authentication"></a>Authentication

O driver ABFS dá suporte a duas formas de autenticação para que o aplicativo Hadoop possa acessar com segurança os recursos contidos em uma conta compatível com Data Lake Storage Gen2. Os detalhes completos dos esquemas de autenticação disponíveis são fornecidos no [Guia de segurança do armazenamento do Azure](../common/storage-security-guide.md). São:

- **Chave compartilhada:** Isso permite que os usuários acessem todos os recursos na conta. A chave é criptografada e armazenada na configuração do Hadoop.

- **Azure Active Directory token de portador OAuth:** Os tokens de portador do Azure AD são adquiridos e atualizados pelo driver usando a identidade do usuário final ou uma entidade de serviço configurada. Usando esse modelo de autenticação, todo o acesso é autorizado por chamada usando a identidade associada ao token fornecido e avaliado com base na ACL (lista de controle de acesso) do POSIX atribuída.

### <a name="configuration"></a>Configuração

Todas as configurações do driver ABFS são armazenadas no arquivo de configuração <code>core-site.xml</code>. Em distribuições do Hadoop com [Ambari](https://ambari.apache.org/), a configuração também pode ser gerenciada usando o portal da Web ou a API REST do Ambari.

Detalhes de todas as entradas de configuração com suporte são especificados na [documentação oficial do Hadoop](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Documentação do Hadoop

O driver ABFS está totalmente documentado na [documentação oficial do Hadoop](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Passos seguintes

- [Criar um cluster Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)
- [Utilizar o URI do Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md)
