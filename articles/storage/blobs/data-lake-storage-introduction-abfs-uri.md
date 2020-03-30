---
title: Utilizar o URI do Azure Data Lake Storage Gen2
description: Utilizar o URI do Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855564"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Utilizar o URI do Azure Data Lake Storage Gen2

O controlador [hadoop Filesystem](https://www.aosabook.org/en/hdfs.html) compatível com o Azure Data Lake Storage `abfs` Gen2 é conhecido pelo seu identificador de esquema (Sistema de Ficheiros Azure Blob). Consistente com outros controladores do Sistema de Ficheiros Hadoop, o condutor da ABFS emprega um formato URI para endereçar ficheiros e diretórios dentro de uma conta capaz de Armazenamento de Data Lake Gen2.

## <a name="uri-syntax"></a>Sintaxe URI

A sintaxe URI para Data Lake Storage Gen2 depende se a sua conta de armazenamento está ou não configurada para ter data Lake Storage Gen2 como o sistema de ficheiros padrão.

Se a conta de Armazenamento de Data Lake Gen2 que deseja endereçar **não é** definida como o sistema de ficheiros predefinido durante a criação de conta, então a sintaxe URI abreviada é:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador**do `abfs` regime : O protocolo é utilizado como identificador do regime. Tem a opção de se ligar ou sem uma ligação segura à camada de tomada (SSL). Utilize `abfss` para se ligar a uma ligação de camada de tomada segura.

2. **Sistema de ficheiros**: A localização dos pais que detém os ficheiros e pastas. Este é o mesmo que os Contentores no serviço Azure Storage Blobs.

3. **Nome**da conta : O nome dado à sua conta de armazenamento durante a criação.

4. **Caminhos**: Um corte`/`avançado delimitado ( ) representação da estrutura do diretório.

5. **Nome**do ficheiro : O nome do ficheiro individual. Este parâmetro é opcional se estiver a dirigir-se a um diretório.

No entanto, se a conta que pretende endereçar é definida como o sistema de ficheiros predefinido durante a criação de conta, então a sintaxe URI abreviada é:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Caminho**: Um corte`/`avançado delimitado ( ) representação da estrutura do diretório.

2. **Nome**do ficheiro : O nome do ficheiro individual.


## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
