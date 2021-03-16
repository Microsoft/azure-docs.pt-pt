---
title: Copie os dados do Amazon S3 para o Azure Storage utilizando o AzCopy | Microsoft Docs
description: Utilize o AzCopy para copiar dados do Amazon S3 para o Azure Storage. O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 34f54bb30e959ecc2fa27fba5ab7392b9eddc68e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494517"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Copiar dados do Amazon S3 para o Azure Storage utilizando o AzCopy

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo ajuda-o a copiar objetos, diretórios e baldes da Amazon Web Services (AWS) S3 para Azure Blob Storage utilizando a AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolher como disponibilizar as credenciais de autorização

* Para autorizar com o Azure Storage, utilize o Azure Ative Directory (AD) ou um token de Assinatura de Acesso Partilhado (SAS).

* Para autorizar com a AWS S3, utilize uma chave de acesso AWS e uma chave de acesso secreta.

### <a name="authorize-with-azure-storage"></a>Autorizar com armazenamento Azure

Consulte o [artigo da AzCopy](storage-use-azcopy-v10.md) para descarregar o AzCopy e escolha como irá fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE]
> Os exemplos deste artigo assumem que autenticaste a tua identidade usando o `AzCopy login` comando. A AzCopy utiliza então a sua conta Azure AD para autorizar o acesso aos dados no armazenamento blob.
>
> Se preferir usar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy.
>
> Por exemplo: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizar com a AWS S3

Recolha a sua chave de acesso AWS e chave de acesso secreto e, em seguida, descreva estas variáveis ambientais:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Copiar objetos, diretórios e baldes

O AzCopy utiliza o [Bloco de Put From URL](/rest/api/storageservices/put-block-from-url) API, pelo que os dados são copiados diretamente entre a AWS S3 e os servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda de rede do seu computador.

> [!TIP]
> Os exemplos desta secção encerram argumentos de percurso com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

 Estes exemplos também funcionam com contas que têm um espaço hierárquico de nomes. [O acesso multi-protocolo no Armazenamento do Lago de Dados](../blobs/data-lake-storage-multi-protocol-access.md) permite-lhe utilizar a mesma sintaxe URL `blob.core.windows.net` () nessas contas. 

### <a name="copy-an-object"></a>Copiar um objeto

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Exemplos neste artigo utilizam URLs estilo caminho para baldes AWS S3 (por exemplo: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Também pode utilizar URLs de estilo hospedeiro virtual (por exemplo: `http://bucket.s3.amazonaws.com` ). 
>
> Para saber mais sobre hospedagem virtual de baldes, consulte [Virtual Hosting of Buckets.](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)

### <a name="copy-a-directory"></a>Copiar um diretório

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> Este exemplo anexa a `--recursive` bandeira para copiar ficheiros em todos os sub-directórios.

### <a name="copy-the-contents-of-a-directory"></a>Copiar o conteúdo de um diretório

Pode copiar o conteúdo de um diretório sem copiar o próprio diretório contendo a utilização do símbolo wildcard (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Copiar um balde

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copiar todos os baldes em todas as regiões

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copie todos os baldes numa região específica do S3

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Lidar com diferenças nas regras de nomeação de objetos

AWS S3 tem um conjunto diferente de convenções de nomeação para nomes de baldes em comparação com os recipientes de bolhas Azure. Pode ler sobre eles [aqui.](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) Se optar por copiar um grupo de baldes para uma conta de armazenamento Azure, a operação de cópia pode falhar devido a diferenças de nomeação.

A AzCopy trata de duas das questões mais comuns que podem surgir; baldes que contêm períodos e baldes que contêm hífenes consecutivos. Os nomes dos baldes AWS S3 podem conter períodos e hífens consecutivos, mas um recipiente em Azure não pode. A AzCopy substitui períodos por hífens e hífens consecutivos por um número que representa o número de hífenes consecutivos (Por exemplo: um balde nomeado `my----bucket` torna-se `my-4-bucket` . 

Além disso, à medida que o AzCopy copia sobre ficheiros, verifica se dá nome a colisões e tenta resolvê-las. Por exemplo, se houver baldes com o nome `bucket-name` `bucket.name` e, a AzCopy resolve um balde com o nome `bucket.name` primeiro para e depois para `bucket-name` `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Lidar com diferenças nos metadados de objetos

AWS S3 e Azure permitem diferentes conjuntos de caracteres nos nomes das teclas do objeto. Pode ler sobre os caracteres que a AWS S3 utiliza [aqui.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Do lado Azure, as teclas de objetos blob aderem às regras de nomeação para [identificadores C#](/dotnet/csharp/language-reference/).

Como parte de um comando AzCopy, `copy` pode fornecer um valor opcional para a bandeira que especifica como gostaria de lidar com `s2s-handle-invalid-metadata` ficheiros onde os metadados do ficheiro contêm nomes-chave incompatíveis. A tabela a seguir descreve cada valor da bandeira.

| Valor da bandeira | Descrição  |
|--------|-----------|
| **Excluir Não-Izaride** | (Opção predefinição) Os metadados não estão incluídos no objeto transferido. A azCopy regista um aviso. |
| **FailIfInvalid** | Os objetos não são copiados. O AzCopy regista um erro e inclui esse erro na contagem falhada que aparece no resumo da transferência.  |
| **RenomeIInvalid**  | O AzCopy resolve a chave de metadados inválidos e copia o objeto para Azure usando o par de valor chave de metadados resolvido. Para saber exatamente que passos a AzCopy toma para renomear as teclas de objetos, consulte a secção [de teclas de objetos do AzCopy](#rename-logic) abaixo. Se a AzCopy não conseguir mudar o nome da chave, o objeto não será copiado. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Como a AzCopy renomea as teclas de objeto

A AzCopy realiza estes passos:

1. Substitui caracteres inválidos por '_'.

2. Adiciona a cadeia `rename_` ao início de uma nova chave válida.

   Esta chave será utilizada para guardar o **valor** original dos metadados.

3. Adiciona a cadeia `rename_key_` ao início de uma nova chave válida.
   Esta chave será utilizada para guardar a chave inválida dos metadados **originais**.
   Pode utilizar esta chave para tentar recuperar os metadados do lado Azure, uma vez que a tecla de metadados é preservada como um valor no serviço de armazenamento Blob.

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados](storage-use-azcopy-v10.md#transfer-data)

- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)
