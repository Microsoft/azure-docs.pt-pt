---
title: Transferir dados para o armazenamento do Azure do Amazon S3 registos ao utilizar o AzCopy v10 | Documentos da Microsoft
description: Transferir dados com AzCopy e o Amazon S3 registos
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b18c4c039b615c7c88268b6e668df9f7fec9fabf
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687920"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Copiar dados de registos do Amazon S3 com o AzCopy

O AzCopy é um utilitário de linha de comandos que pode utilizar para copiar blobs ou ficheiros de ou para uma conta de armazenamento. Este artigo ajuda-o a copiar objetos, diretórios e registos do Amazon Web Services (AWS) S3 para o armazenamento de Blobs do Azure com o AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha a forma como irá fornecer credenciais de autorização

* Para autorizar com o armazenamento do Azure, utilize o Azure Active Directory (AD) ou um token de assinatura de acesso partilhado (SAS).

* Para autorizar com o AWS S3, utilize uma chave de acesso do AWS e uma chave de acesso a segredos.

### <a name="authorize-with-azure-storage"></a>Autorizar com o armazenamento do Azure

Consulte a [introdução ao AzCopy](storage-use-azcopy-v10.md) artigo para transferir o AzCopy e escolha a forma como irá fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE]
> Os exemplos neste artigo partem do princípio de que foi autenticado sua identidade através do `AzCopy login` comando. O AzCopy, em seguida, utiliza a conta do Azure AD para autorizar o acesso aos dados no armazenamento de Blobs.
>
> Se usaria um token SAS em vez disso, para autorizar o acesso aos dados de BLOBs, em seguida, pode acrescentar esse token para o URL de recurso em cada comando do AzCopy.
>
> Por exemplo: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizar com o AWS S3

Reúna a chave de acesso do AWS e a chave de acesso a segredos e, em seguida, defina esses variáveis de ambiente:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objetos de cópia, diretórios e registos

AzCopy utiliza a [colocar o bloco de URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, para que os dados são copiados diretamente entre AWS S3 e servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda de rede do seu computador.

### <a name="copy-an-object"></a>Copiar um objeto

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Exemplo** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Exemplos neste artigo utilizam os URLs de estilo de caminho para buckets de AWS S3 (por exemplo: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Também pode usar também os URLs de estilo alojado virtual (por exemplo: `http://bucket.s3.amazonaws.com`). 
>
> Para saber mais sobre o alojamento virtual de buckets, veja [Virtual de alojamento de registos]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Copiar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Exemplo** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Copiar um bucket

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Exemplo** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copiar todos os registos em todas as regiões

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Exemplo** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copiar todos os registos numa região específica S3

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Exemplo** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Lidar com diferenças nas regras de nomenclatura de objeto

AWS S3 tem um conjunto diferente de convenções de nomenclatura para nomes de bucket em comparação com os contentores de Blobs do Azure. Pode ler sobre as mesmas [aqui](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Se optar por copiar um grupo de registos para uma conta de armazenamento do Azure, a operação de cópia pode falhar devido a diferenças de nomenclatura.

AzCopy lida com dois dos problemas mais comuns que podem surgir; registos que contêm períodos e registos que contêm os hífenes consecutivos. Nomes de bucket de AWS S3 podem conter pontos e hífenes consecutivos, mas não de um contentor no Azure. AzCopy substitui períodos, hífenes e hífenes consecutivos com um número que representa o número de hífenes consecutivos (por exemplo: um registo com o nome `my----bucket` torna-se `my-4-bucket`. 

Além disso, como o AzCopy copia ficheiros, ele verifica a existência de conflitos de nomenclatura e tenta resolvê-los. Por exemplo, se existirem registos com o nome `bucket-name` e `bucket.name`, o AzCopy é um registo com o nome resolvido `bucket.name` primeiro para `bucket-name` e, em seguida, para `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Lidar com diferenças nos metadados do objeto

AWS S3 e o Azure permitem diferentes conjuntos de caracteres nos nomes de chaves do objeto. Pode ler sobre os carateres que o AWS S3 usa [aqui](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). No lado do Azure, as chaves do objeto de blob respeitar as regras de nomenclatura para [ C# identificadores](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Como parte de um AzCopy `copy` comando, pode fornecer um valor para opcional a `s2s-invalid-metadata-handle` sinalizador que especifica como pretende processar ficheiros onde os metadados do ficheiro contém os nomes de chave incompatíveis. A tabela seguinte descreve cada valor de sinalizador.

| Valor de sinalizador | Descrição  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opção predefinida) Os metadados não está incluído no objeto transferido. AzCopy regista um aviso. |
| **FailIfInvalid** | Objetos não são copiados. AzCopy registra um erro e inclui esse erro na contagem de falhas que aparece no resumo de transferência.  |
| **RenameIfInvalid**  | AzCopy resolve a chave de metadados inválidos e copia o objeto para o Azure com o par de chave-valor de metadados resolvido. Para saber exatamente o que leva o AzCopy para mudar o nome de chaves do objeto os passos, consulte a [AzCopy como muda o nome de chaves do objeto](#rename-logic) secção abaixo. Se AzCopy não é possível mudar o nome da chave, em seguida, o objeto não será copiado. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Como AzCopy muda o nome de chaves do objeto

AzCopy executa estes passos:

1. Substitui a carateres inválidos com "_".

2. Adiciona a cadeia de caracteres `rename_` para o início de uma nova chave válido.

   Esta chave será utilizada para guardar os metadados originais **valor**.

3. Adiciona a cadeia de caracteres `rename_key_` para o início de uma nova chave válido.
   Esta chave será utilizada para guardar os metadados originais inválido **chave**.
   Pode utilizar esta chave para tentar recuperar os metadados no lado do Azure, uma vez que a chave de metadados é preservado como um valor no serviço de armazenamento de Blobs.

## <a name="next-steps"></a>Passos Seguintes

Encontre mais exemplos em qualquer um dos seguintes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com AzCopy e armazenamento de BLOBs](storage-use-azcopy-blobs.md)

- [Transferir dados com AzCopy e o ficheiro de armazenamento](storage-use-azcopy-files.md)

- [Configurar, otimizar e resolver problemas relacionados com o AzCopy](storage-use-azcopy-configure.md)