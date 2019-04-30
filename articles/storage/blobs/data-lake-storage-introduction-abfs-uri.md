---
title: Utilize a geração 2 Lake armazenamento de dados do Azure URI
description: Utilize a geração 2 Lake armazenamento de dados do Azure URI
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: e4f148ac6f66cfbd7d89eff3623418197c2e0a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708486"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Utilize a geração 2 Lake armazenamento de dados do Azure URI

O [sistema de ficheiros do Hadoop](https://www.aosabook.org/en/hdfs.html) controladores que seja compatível com a geração 2 de armazenamento do Azure Data Lake é conhecido pelo respetivo identificador de esquema `abfs` (sistema de ficheiros de Blob do Azure). Consistente com outros controladores de sistema de ficheiros do Hadoop, o driver ABFS emprega um formato de URI para endereçar ficheiros e diretórios dentro de uma conta com capacidade de geração 2 de armazenamento do Data Lake.

## <a name="uri-syntax"></a>Sintaxe de URI

A sintaxe URI de geração 2 de armazenamento do Data Lake é depende se pretende ou não a conta de armazenamento está configurada para ter a geração 2 de armazenamento do Data Lake como o sistema de ficheiros predefinido.

Se a conta com capacidade de geração 2 de armazenamento do Data Lake desejo abordar **není** definido durante a criação de conta, como o sistema de ficheiros predefinido, em seguida, é a abreviatura de sintaxe de URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador do esquema**: O `abfs` protocolo é utilizado como o identificador do esquema. Tem a opção para ligar com ou sem uma ligação do secure sockets layer (SSL). Utilize `abfss` para estabelecer ligação com uma ligação de camada de soquete seguro.

2. **Sistema de ficheiros**: A localização principal que contém os ficheiros e pastas. Este é o mesmo que contentores no serviço de Blobs de armazenamento do Azure.

3. **Nome da conta**: O nome dado à sua conta de armazenamento durante a criação.

4. **Caminhos**: Uma barra delimitados por (`/`) representação da estrutura de diretório.

5. **Nome de ficheiro**: O nome do ficheiro individual. Este parâmetro é opcional, se esteja Solucionando um diretório.

No entanto, se a conta que desejo abordar é definida como o sistema de ficheiros predefinido durante a criação da conta, a forma abreviada sintaxe de URI é:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Caminho**: Uma barra delimitados por (`/`) representação da estrutura de diretório.

2. **Nome do ficheiro**: O nome do ficheiro individual.


## <a name="next-steps"></a>Passos Seguintes

- [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
