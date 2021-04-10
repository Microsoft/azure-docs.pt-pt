---
title: Copie os dados do Google Cloud Storage para o Azure Storage utilizando o AzCopy | Microsoft Docs
description: Utilize o AzCopy para copiar dados do Google Cloud Storage para o Azure Storage. O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c6a53acd63b6aa882674f6aa29e1f7152f5b0a30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728815"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Copie os dados do Google Cloud Storage para o Azure Storage utilizando o AzCopy (pré-visualização)

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo ajuda-o a copiar objetos, diretórios e baldes do Google Cloud Storage para O Azure Blob Storage utilizando o AzCopy.

> [!IMPORTANT]
> A cópia de dados do Google Cloud Storage para o Azure Storage encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolher como disponibilizar as credenciais de autorização

* Para autorizar com o Azure Storage, utilize o Azure Ative Directory (AD) ou um token de Assinatura de Acesso Partilhado (SAS).

* Para autorizar com o Google Cloud Storage, utilize uma chave de conta de serviço.

### <a name="authorize-with-azure-storage"></a>Autorizar com armazenamento Azure

Consulte o [artigo da AzCopy](storage-use-azcopy-v10.md) para descarregar o AzCopy e saiba como pode fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE] 
> Os exemplos deste artigo assumem que forneceu credenciais de autorização utilizando o Azure Ative Directory (Azure AD).
>
> Se preferir usar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autorizar com o Google Cloud Storage

Para autorizar com o Google Cloud Storage, utilizará uma chave de conta de serviço. Para obter informações sobre como criar uma chave de conta de serviço, consulte [criar e gerir as chaves da conta de serviço](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Depois de obter uma chave de serviço, deite a `GOOGLE_APPLICATION_CREDENTIALS` variável ambiente para o caminho absoluto para o ficheiro chave da conta de serviço:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Copiar objetos, diretórios e baldes

O AzCopy utiliza o [Bloco de Put From URL](/rest/api/storageservices/put-block-from-url) API, pelo que os dados são copiados diretamente entre o Google Cloud Storage e os servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda de rede do seu computador.

> [!TIP]
> Os exemplos desta secção encerram argumentos de percurso com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

 Estes exemplos também funcionam com contas que têm um espaço hierárquico de nomes. [O acesso multi-protocolo no Armazenamento do Lago de Dados](../blobs/data-lake-storage-multi-protocol-access.md) permite-lhe utilizar a mesma sintaxe URL `blob.core.windows.net` () nessas contas. 

### <a name="copy-an-object"></a>Copiar um objeto

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

| Sintaxe / exemplo  |  Código |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>Copiar um diretório

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

| Sintaxe / exemplo  |  Código |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> Este exemplo anexa a `--recursive` bandeira para copiar ficheiros em todos os sub-directórios.

### <a name="copy-the-contents-of-a-directory"></a>Copiar o conteúdo de um diretório

Pode copiar o conteúdo de um diretório sem copiar o próprio diretório contendo a utilização do símbolo wildcard (*).

| Sintaxe / exemplo  |  Código |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>Copie um balde de armazenamento em nuvem

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

| Sintaxe / exemplo  |  Código |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Copie todos os baldes num projeto do Google Cloud 

Primeiro, dedibra o `GOOGLE_CLOUD_PROJECT` projeto ID do projeto Google Cloud.

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

| Sintaxe / exemplo  |  Código |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Copie um subconjunto de baldes num projeto do Google Cloud 

Primeiro, dedibra o `GOOGLE_CLOUD_PROJECT` projeto ID do projeto Google Cloud.

Copie um subconjunto de baldes utilizando um símbolo wildcard (*) no nome do balde. Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

| Sintaxe / exemplo  |  Código |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>Lidar com diferenças nas regras de nomeação de baldes

O Google Cloud Storage tem um conjunto diferente de convenções de nomeação de nomes de baldes em comparação com os recipientes blob Azure. Pode ler sobre eles [aqui.](https://cloud.google.com/storage/docs/naming-buckets) Se optar por copiar um grupo de baldes para uma conta de armazenamento Azure, a operação de cópia pode falhar devido a diferenças de nomeação.

A AzCopy trata de três das questões mais comuns que podem surgir; baldes que contêm períodos, baldes que contêm hífenes consecutivos, e baldes que contêm sublinhados. Os nomes do balde do Google Cloud Storage podem conter períodos e hífens consecutivos, mas um contentor em Azure não pode. A AzCopy substitui períodos por hífens e hífens consecutivos por um número que representa o número de hífenes consecutivos (Por exemplo: um balde nomeado `my----bucket` torna-se `my-4-bucket` . Se o nome do balde tiver um sublinhado `_` (), então a AzCopy substitui o sublinhado por um hífen (Por exemplo: um balde nomeado `my_bucket` torna-se `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Lidar com diferenças nas regras de nomeação de objetos

O Google Cloud Storage tem um conjunto diferente de convenções de nomeação para nomes de objetos em comparação com as bolhas de Azure. Pode ler sobre eles [aqui.](https://cloud.google.com/storage/docs/naming-objects)

O Azure Storage não permite que nomes de objetos (ou qualquer segmento no percurso do diretório virtual) terminem com pontos de fuga (por `my-bucket...` exemplo). Os pontos de fuga são aparados quando a operação da cópia é executada. 

## <a name="handle-differences-in-object-metadata"></a>Lidar com diferenças nos metadados de objetos

O Google Cloud Storage e o Azure permitem diferentes conjuntos de caracteres nos nomes das teclas do objeto. Pode ler sobre metadados no Google Cloud Storage [aqui.](https://cloud.google.com/storage/docs/metadata) Do lado Azure, as teclas de objetos blob aderem às regras de nomeação para [identificadores C#](/dotnet/csharp/language-reference/).

Como parte de um comando AzCopy, `copy` pode fornecer um valor opcional para a bandeira que especifica como gostaria de lidar com `s2s-handle-invalid-metadata` ficheiros onde os metadados do ficheiro contêm nomes-chave incompatíveis. A tabela a seguir descreve cada valor da bandeira.

| Valor da bandeira | Description  |
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
