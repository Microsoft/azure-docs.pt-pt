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
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437136"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Utilizar o URI do Azure Data Lake Storage Gen2

O controlador [hadoop Filesystem](https://www.aosabook.org/en/hdfs.html) compatível com o Azure Data Lake Storage `abfs` Gen2 é conhecido pelo seu identificador de esquema (Sistema de Ficheiros Azure Blob). Consistente com outros controladores do Sistema de Ficheiros Hadoop, o condutor da ABFS emprega um formato URI para endereçar ficheiros e diretórios dentro de uma conta capaz de Armazenamento de Data Lake Gen2.

## <a name="uri-syntax"></a>Sintaxe URI

A sintaxe URI para Data Lake Storage Gen2 depende se a sua conta de armazenamento está ou não configurada para ter data Lake Storage Gen2 como o sistema de ficheiros padrão.

Se a conta de Armazenamento de Data Lake Gen2 que deseja endereçar **não é** definida como o sistema de ficheiros predefinido durante a criação de conta, então a sintaxe URI abreviada é:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador**do `abfs` regime : O protocolo é utilizado como identificador do regime. Tem a opção de se conectar com ou sem uma Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL), ligação. Utilize `abfss` para se ligar a uma ligação TLS.

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
