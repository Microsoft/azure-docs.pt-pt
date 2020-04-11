---
title: Questões conhecidas com azure Data Lake Storage Gen2 [ Microsoft Docs
description: Conheça as limitações e as questões conhecidas do Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 204b5dd4661b34aae8b76d65505a65e20f293f0f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115330"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Questões conhecidas com Azure Data Lake Storage Gen2

Este artigo descreve limitações e questões conhecidas do Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Funcionalidades suportadas do Armazenamento de Blobs

Um número crescente de funcionalidades de armazenamento blob agora trabalham com contas que têm um espaço de nome hierárquico. Para obter uma lista completa, consulte [as funcionalidades de Armazenamento Blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviços Azure suportadas

O Azure Data Lake Storage Gen2 suporta vários serviços Azure que pode utilizar para ingerir dados, realizar análises e criar representações visuais. Para obter uma lista de serviços Azure suportados, consulte [os serviços Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Consulte [os serviços Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas open source suportadas

Várias plataformas de código aberto suportam data Lake Storage Gen2. Para obter uma lista completa, consulte [plataformas Open source que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Consulte [plataformas Open source que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>APIs de armazenamento de blob

As APIs blob e data Lake Storage Gen2 APIs podem operar com os mesmos dados.

Esta secção descreve problemas e limitações com a utilização de APIs blob e Data Lake Storage Gen2 APIs para operar nos mesmos dados.

* Não é possível utilizar apis de armazenamento de imagens blob e data lake para escrever na mesma instância de um ficheiro. Se escrever para um ficheiro usando data Lake Storage Gen2 APIs, então os blocos do ficheiro não serão visíveis para chamadas para a API blob Da [Lista de Bloqueio saem.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Pode substituir um ficheiro utilizando APIs de Armazenamento de Data Lake Gen2 ou APIs blob. Isto não afeta as propriedades dos ficheiros.

* Quando utilizar a operação [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sem especificar um delimitador, os resultados incluirão diretórios e blobs. Se optar por utilizar um delimitador, utilize`/`apenas um corte para a frente ( ). Este é o único delimitador apoiado.

* Se utilizar a API [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) para eliminar um diretório, esse diretório só será apagado se estiver vazio. Isto significa que não pode usar a API Blob apagar diretórios de forma recursiva.

Estas APIs de repouso blob não são suportadas:

* [Colocar Blob (Página)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Página de colocação](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Obtenha gamas de páginas](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob cópia incremental](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Colocar página a partir de URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Colocar Blob (apêndice)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Bloco de apêndice](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Bloco de apêndice de URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Os discos VM não geridos não são suportados em contas que tenham um espaço de nome hierárquico. Se pretender ativar um espaço de nome hierárquico numa conta de armazenamento, coloque discos VM não geridos numa conta de armazenamento que não tenha a funcionalidade hierárquica de espaço de nome ativada.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Suporte do sistema de ficheiros em SDKs

As operações de get e set ACL não são atualmente recursivas.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Suporte do sistema de ficheiros no PowerShell e no Azure CLI

- O suporte [powerShell](data-lake-storage-directory-file-acl-powershell.md) e [Azure CLI](data-lake-storage-directory-file-acl-cli.md) estão em pré-visualização pública.
- As operações de get e set ACL não são atualmente recursivas.

## <a name="lifecycle-management-policies"></a>Políticas de gestão do ciclo de vida

* A eliminação de imagens blob ainda não é suportada.  

## <a name="archive-tier"></a>Nível de Arquivo

Existe atualmente um bug que afeta o nível de acesso ao arquivo.


## <a name="blobfuse"></a>Blobfuse

Blobfuse não é apoiado.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Utilize apenas a versão mais recente do AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Versões anteriores da AzCopy, como AzCopy v8.1, não são suportadas.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Explorador do Storage do Azure

Utilize apenas `1.6.0` versões ou superiores.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Explorador de Armazenamento no portal Azure

Os ACLs ainda não são apoiados.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Candidaturas de terceiros

As aplicações de terceiros que usam APIs REST para trabalhar continuarão a funcionar se as utilizar com aplicações de Gen2 de Armazenamento de Data Lake que chamam de APIs blob provavelmente funcionarão.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listas de controlo de acesso (ACL) e acesso de leitura anónimo

Se o [acesso de leitura anónimo](storage-manage-access-to-resources.md) tiver sido concedido a um contentor, então os ACLs não têm qualquer efeito sobre esse recipiente ou os ficheiros nesse recipiente.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Condutor de Blob de Armazenamento Windows Azure (WASB) (sem suporte com Data Lake Storage Gen2)

Atualmente, o condutor do WASB, projetado para trabalhar apenas com a API Blob, encontra problemas em alguns cenários comuns. Especificamente, quando é cliente de uma conta de armazenamento hierárquica habilitada para o espaço de nome. O acesso multi-protocolo ao Armazenamento de Data Lake não vai atenuar estas questões. 

Por enquanto (e muito provavelmente o futuro previsível), não apoiaremos os clientes que usam o condutor wasb como cliente para uma conta de armazenamento hierárquica com nome sinuoso. Em vez disso, recomendamos que opte por utilizar o controlador do Sistema de [Ficheiros Azure Blob (ABFS)](data-lake-storage-abfs-driver.md) no seu ambiente Hadoop. Se está a tentar migrar de um ambiente Hadoop no local com uma versão mais cedo do que a filial Hadoop-3, por favor abra um bilhete de Suporte Azure para que possamos entrar em contacto consigo no caminho certo para si e para a sua organização.
