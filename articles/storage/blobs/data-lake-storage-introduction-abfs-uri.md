---
title: Usar o URI de Azure Data Lake Storage Gen2
description: Usar o URI de Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855564"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Usar o URI de Azure Data Lake Storage Gen2

O driver do [sistema de arquivos do Hadoop](https://www.aosabook.org/en/hdfs.html) que é compatível com Azure data Lake Storage Gen2 é conhecido `abfs` por seu identificador de esquema (sistema de arquivos de blob do Azure). Consistente com outros drivers de sistema de arquivos do Hadoop, o driver ABFS emprega um formato de URI para lidar com arquivos e diretórios em uma conta compatível com Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Sintaxe de URI

A sintaxe de URI para Data Lake Storage Gen2 depende se sua conta de armazenamento está ou não configurada para ter Data Lake Storage Gen2 como o sistema de arquivos padrão.

Se a conta compatível com Data Lake Storage Gen2 que você deseja endereçar **não estiver** definida como o sistema de arquivos padrão durante a criação da conta, a sintaxe do URI abreviado será:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador do esquema**: O `abfs` protocolo é usado como o identificador do esquema. Você tem a opção de conectar-se com ou sem uma conexão SSL (Secure Socket Layer). Use `abfss` para conectar-se com uma conexão de camada de soquete seguro.

2. **Sistema de arquivos**: O local pai que contém os arquivos e as pastas. Isso é o mesmo que contêineres no serviço de BLOBs do armazenamento do Azure.

3. **Nome da conta**: O nome dado à sua conta de armazenamento durante a criação.

4. **Caminhos**: Uma representação de barra inlimitada`/`() da estrutura de diretório.

5. **Nome do arquivo**: O nome do arquivo individual. Esse parâmetro será opcional se você estiver endereçando um diretório.

No entanto, se a conta que você deseja endereçar estiver definida como o sistema de arquivos padrão durante a criação da conta, a sintaxe do URI abreviado será:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Caminho**: Uma representação de barra inlimitada`/`() da estrutura de diretório.

2. **Nome do arquivo**: O nome do arquivo individual.


## <a name="next-steps"></a>Passos Seguintes

- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
