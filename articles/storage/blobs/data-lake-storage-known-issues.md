---
title: Problemas conhecidos com geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Saiba mais sobre as limitações e problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 099dc723db44ba71fc4672c382d24ac93ffe742f
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689132"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake

Este artigo lista os recursos e as ferramentas que ainda não têm suporte ou que só têm suporte parcial com contas de armazenamento que têm um namespace hierárquico (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Problemas e limitações com o uso de APIs de BLOB

APIs de BLOB e APIs de Data Lake Storage Gen2 podem operar nos mesmos dados.

Esta seção descreve problemas e limitações com o uso de APIs de BLOB e Data Lake Storage Gen2 APIs para operar nos mesmos dados.

* Você não pode usar as APIs de BLOB e as APIs de Data Lake Storage para gravar na mesma instância de um arquivo. Se você gravar em um arquivo usando APIs Data Lake Storage Gen2, os blocos desse arquivo não ficarão visíveis para chamadas para a API de BLOB da [lista de blocos Get](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Você pode substituir um arquivo usando APIs Data Lake Storage Gen2 ou APIs de BLOB. Isso não afetará as propriedades do arquivo.

* Quando você usa a operação [listar BLOBs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sem especificar um delimitador, os resultados incluirão diretórios e blobs. Se você optar por usar um delimitador, use apenas uma barra (`/`). Esse é o único delimitador com suporte.

* Se você usar a API [excluir blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) para excluir um diretório, esse diretório será excluído somente se estiver vazio. Isso significa que você não pode usar a API de BLOB para excluir diretórios recursivamente.

Essas APIs REST de BLOB não têm suporte:

* [Colocar BLOB (página)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Colocar página](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Obter intervalos de página](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob de cópia incremental](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Colocar página da URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Colocar BLOB (acrescentar)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Bloco de acréscimo](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Anexar bloco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Não há suporte para discos de VM não gerenciados em contas que têm um namespace hierárquico. Se você quiser habilitar um namespace hierárquico em uma conta de armazenamento, coloque os discos de VM não gerenciados em uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

<a id="api-scope-data-lake-client-library" />

## <a name="filesystem-support-in-sdks"></a>Suporte do sistema de arquivos em SDKs

- O suporte para [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) e [python](data-lake-storage-directory-file-acl-python.md) está em visualização pública. Não há suporte para outros SDKs no momento.
- As operações obter e definir ACL não são recursivas no momento.

## <a name="filesystem-support-in-powershell-and-azure-cli"></a>Suporte do sistema de arquivos no PowerShell e CLI do Azure

- O [PowerShell](data-lake-storage-directory-file-acl-powershell.md) e o suporte do [CLI do Azure](data-lake-storage-directory-file-acl-cli.md) estão em visualização pública.
- As operações obter e definir ACL não são recursivas no momento.

## <a name="support-for-other-blob-storage-features"></a>Suporte para outros recursos de armazenamento de BLOBs

A tabela a seguir lista todos os outros recursos e ferramentas que ainda não têm suporte ou que só têm suporte parcial com contas de armazenamento que têm um namespace hierárquico (Azure Data Lake Storage Gen2).

| Recurso/ferramenta    | Mais informações    |
|--------|-----------|
| **Failover de conta** |Ainda não tem suporte|
| **AZCopy** | Suporte específico à versão <br><br>Use apenas a versão mais recente do AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Não há suporte para versões anteriores do AzCopy, como AzCopy v 8.1.|
| **Políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs** | As políticas de gerenciamento do ciclo de vida têm suporte (versão prévia).  Há suporte para todas as camadas de acesso. A camada de acesso de arquivamento está em visualização no momento. Ainda não há suporte para a exclusão de instantâneos de BLOB. <br><br> Atualmente, existem alguns bugs que afetam as políticas de gerenciamento do ciclo de vida e a camada de acesso de arquivamento.  Inscreva-se para visualizar as políticas de gerenciamento do ciclo de vida e arquivar a camada de acesso [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).   |
| **Rede de Entrega de Conteúdos do Azure (CDN)** | Ainda não tem suporte|
| **Azure Search** |Com suporte (visualização)|
| **Explorador do Armazenamento do Azure** | Suporte específico à versão. <br><br>Use somente versões `1.6.0` ou superior. <br> Atualmente, há um bug de armazenamento que afeta a versão `1.11.0` que pode resultar em erros de autenticação em determinados cenários. Uma correção para o bug de armazenamento está sendo distribuída, mas como uma solução alternativa, recomendamos que você use a versão `1.10.x`, que está disponível como um [Download gratuito](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` não é afetado pelo bug de armazenamento.|
| **ACLs de contêiner de BLOB** |Ainda não tem suporte|
| **Blobfuse** |Ainda não tem suporte|
| **Domínios personalizados** |Ainda não tem suporte|
| **Gerenciador de Armazenamento no portal do Azure** | Suporte limitado. As ACLs ainda não têm suporte. |
| **Registo de diagnósticos** |Os logs de diagnóstico têm suporte (versão prévia).<br><br>No momento, não há suporte para a habilitação de logs no portal do Azure. Aqui está um exemplo de como habilitar os logs usando o PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Certifique-se de especificar `Blob` como o valor do parâmetro `-ServiceType`, conforme mostrado neste exemplo. <br><br>No momento, Gerenciador de Armazenamento do Azure não pode ser usada para exibir os logs de diagnóstico. Para exibir os logs, use AzCopy ou SDKs.
| **Armazenamento imutável** |Ainda não tem suporte <br><br>O armazenamento imutável oferece a capacidade de armazenar dados em um estado de [worm (gravar uma vez, ler muitos)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Camadas de nível de objeto** |As camadas frias e de arquivo são suportadas. A camada de arquivo morto está em versão prévia. Todas as outras camadas de acesso ainda não têm suporte. <br><br> Atualmente, existem alguns bugs que afetam a camada de acesso de arquivamento.  Inscreva-se para a visualização da camada de acesso de arquivamento [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).|
| **Sites estáticos** |Ainda não tem suporte <br><br>Especificamente, a capacidade de fornecer arquivos para [sites estáticos](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplicativos de terceiros** | Suporte limitado <br><br>Aplicativos de terceiros que usam APIs REST para trabalhar continuarão a funcionar se você usá-los com Data Lake Storage Gen2. <br>Aplicativos que chamam APIs de blob provavelmente funcionarão.|
|**Eliminação de Forma Recuperável** |Ainda não tem suporte|
| **Recursos de controle de versão** |Ainda não tem suporte <br><br>Isso inclui [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)e outros recursos de controle de versão, como [instantâneos](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


