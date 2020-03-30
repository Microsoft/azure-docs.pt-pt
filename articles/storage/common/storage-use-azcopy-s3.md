---
title: Copiar dados da Amazon S3 para o Armazenamento Azure utilizando o AzCopy Microsoft Docs
description: Transferir dados com baldes AzCopy e Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941511"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Copiar dados da Amazon S3 para o Armazenamento Azure utilizando o AzCopy

O AzCopy é um utilitário de linha de comando que pode utilizar para copiar bolhas ou ficheiros de ou para uma conta de armazenamento. Este artigo ajuda-o a copiar objetos, diretórios e baldes da Amazon Web Services (AWS) S3 para o armazenamento de blob Azure utilizando o AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha como irá fornecer credenciais de autorização

* Para autorizar com o Armazenamento Azure, utilize o Diretório Ativo Azure (AD) ou um símbolo de assinatura de acesso partilhado (SAS).

* Para autorizar com a AWS S3, utilize uma chave de acesso AWS e uma chave de acesso secreta.

### <a name="authorize-with-azure-storage"></a>Autorizar com armazenamento Azure

Veja o [Artigo da AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e escolha como fornecerá credenciais de autorização ao serviço de armazenamento.

> [!NOTE]
> Os exemplos deste artigo assumem que autenticaram a `AzCopy login` sua identidade usando o comando. A AzCopy utiliza então a sua conta Azure AD para autorizar o acesso aos dados no armazenamento blob.
>
> Se preferir utilizar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy.
>
> Por exemplo: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizar com a AWS S3

Reúna a chave de acesso AWS e a chave de acesso secreta e, em seguida, detete as variáveis ambientais:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Copiar objetos, diretórios e baldes

O AzCopy utiliza o [Bloco de Colocação de URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, pelo que os dados são copiados diretamente entre a AWS S3 e os servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda da rede do seu computador.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. Se decidir remover os dados dos seus baldes S3 após uma operação de cópia, certifique-se de verificar se os dados foram devidamente copiados para a sua conta de armazenamento antes de remover os dados.

> [!TIP]
> Os exemplos nesta secção encerram argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

 Estes exemplos também funcionam com contas que têm um espaço de nome hierárquico. O [acesso multi-protocolo ao Armazenamento](../blobs/data-lake-storage-multi-protocol-access.md) de Data Lake permite-lhe utilizar a mesma sintaxe de URL ()`blob.core.windows.net`nessas contas. 

### <a name="copy-an-object"></a>Copiar um objeto

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Exemplos neste artigo usam URLs estilo caminho para baldes AWS S3 (por exemplo: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Também pode utilizar URLs de estilo virtual hospedado `http://bucket.s3.amazonaws.com`(por exemplo: ). 
>
> Para saber mais sobre o alojamento virtual de baldes,https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)consulte [Virtual Hosting of Buckets].

### <a name="copy-a-directory"></a>Copiar um diretório

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Copiar um balde

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copiar todos os baldes em todas as regiões

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copie todos os baldes numa região específica do S3

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Lidar com as diferenças nas regras de nomeação de objetos

A AWS S3 tem um conjunto diferente de convenções de nomeação para nomes de baldes em comparação com os contentores de blob Azure. Pode ler sobre eles [aqui.](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) Se optar por copiar um grupo de baldes para uma conta de armazenamento Azure, a operação de cópia pode falhar devido a diferenças de nomeação.

A AzCopy lida com duas das questões mais comuns que podem surgir; baldes que contenham períodos e baldes que contenham hífenes consecutivos. Os nomes de baldes AWS S3 podem conter períodos e hífenes consecutivos, mas um contentor em Azure não pode. A ZCopy substitui os períodos por hífenes e hífens consecutivos por um número `my----bucket` que `my-4-bucket`representa o número de hífenes consecutivos (por exemplo: um balde nomeado torna-se . 

Além disso, como a AzCopy copia ficheiros, verifica para nomear colisões e tenta resolvê-los. Por exemplo, se houver baldes `bucket-name` `bucket.name`com o nome e , `bucket.name` a `bucket-name` AzCopy resolve um balde nomeado primeiro para e depois para `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Lidar com diferenças nos metadados de objetos

AWS S3 e Azure permitem diferentes conjuntos de caracteres nos nomes das teclas de objeto. Pode ler sobre os caracteres que a AWS S3 usa [aqui](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Do lado do Azure, as teclas de objectoblo saem às regras de nomeação dos [identificadores C#](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Como parte de um `copy` comando AzCopy, pode `s2s-invalid-metadata-handle` fornecer um valor para opcional a bandeira que especifica como gostaria de lidar com ficheiros onde os metadados do ficheiro contêm nomes chave incompatíveis. A tabela seguinte descreve cada valor da bandeira.

| Valor da bandeira | Descrição  |
|--------|-----------|
| **ExcluirInválido** | (Opção por defeito) Os metadados não estão incluídos no objeto transferido. AAzCopy regista um aviso. |
| **FalhaIfInválido** | Os objetos não são copiados. A AzCopy regista um erro e inclui esse erro na contagem falhada que aparece no resumo da transferência.  |
| **Mudar o nomeIfIfválido**  | A AzCopy resolve a chave de metadados inválidos e copia o objeto para o Azure utilizando o par de valor-chave de metadados resolvidos. Para saber exatamente quais os passos que a AzCopy toma para mudar o nome das teclas de objeto, consulte a secção de teclas de objeto sinuosa como a [AzCopy renomea](#rename-logic) as teclas de objeto abaixo. Se a AzCopy não conseguir renomear a chave, então o objeto não será copiado. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Como a AzCopy renomea as teclas de objeto

A ZCopy executa estes passos:

1. Substitui caracteres inválidos por '_'.

2. Adiciona a `rename_` corda ao início de uma nova chave válida.

   Esta chave será utilizada para salvar o **valor**original dos metadados.

3. Adiciona a `rename_key_` corda ao início de uma nova chave válida.
   Esta chave será utilizada para salvar a **chave**inválida dos metadados originais .
   Pode utilizar esta chave para tentar recuperar os metadados do lado do Azure, uma vez que a chave de metadados é preservada como um valor no serviço de armazenamento Blob.

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com armazenamento azCopy e blob](storage-use-azcopy-blobs.md)

- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)

- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)