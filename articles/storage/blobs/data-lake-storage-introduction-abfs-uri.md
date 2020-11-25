---
title: Utilizar o URI do Azure Data Lake Storage Gen2
description: Aprenda a sintaxe URI para o identificador do esquema Abfs, que representa o controlador do Sistema de Ficheiros Azure Blob (controlador hadoop filesystem para Azure Data Lake Storage Gen2).
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 948b5aa0ad015f9f3c693e13219ec034724687c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913169"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Utilizar o URI do Azure Data Lake Storage Gen2

O controlador [hadoop filesystem](https://www.aosabook.org/en/hdfs.html) que é compatível com a Azure Data Lake Storage Gen2 é conhecido pelo seu identificador de `abfs` esquemas (Azure Blob File System). Em consonância com outros controladores do Sistema de Ficheiros Hadoop, o controlador ABFS emprega um formato URI para abordar ficheiros e diretórios dentro de uma conta de Data Lake Storage Gen2 capaz.

## <a name="uri-syntax"></a>Sintaxe URI

A sintaxe URI para data lake storage gen2 depende se a sua conta de armazenamento está ou não configurada para ter data lake storage Gen2 como o sistema de ficheiros padrão.

Se a conta de armazenamento de dados gen2 que deseja endereçar **não for** definida como o sistema de ficheiros predefinido durante a criação de conta, então a sintaxe URI de abreviatura é:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador de esquema**: O `abfs` protocolo é utilizado como identificador de esquemas. Tem a opção de se conectar com ou sem uma segurança da camada de transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL), ligação. Utilize `abfss` para ligar com uma ligação TLS.

2. **Sistema de ficheiros**: A localização dos pais que contém os ficheiros e pastas. Isto é o mesmo que os contentores do serviço Azure Storage Blobs.

3. **Nome da conta**: O nome dado à sua conta de armazenamento durante a criação.

4. **Caminhos**: Uma representação delimitada da `/` estrutura do diretório.

5. **Nome do ficheiro**: O nome do ficheiro individual. Este parâmetro é opcional se estiver a dirigir-se a um diretório.

No entanto, se a conta que pretende endereçar for definida como o sistema de ficheiros predefinidos durante a criação de conta, então a sintaxe URI abreviada é:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Caminho**: Uma representação delimitada da `/` estrutura do diretório.

2. **Nome do ficheiro**: O nome do ficheiro individual.


## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)