---
title: Problemas conhecidos com a Azure Data Lake Storage Gen2 Microsoft Docs
description: Conheça as limitações e questões conhecidas da Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 1c887093972507904b007c696214708eb0e2b039
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282197"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com Azure Data Lake Storage Gen2

Este artigo descreve limitações e questões conhecidas da Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Funcionalidades suportadas do Armazenamento de Blobs

Um número crescente de funcionalidades de armazenamento Blob agora funcionam com contas que têm um espaço hierárquico de nomes. Para obter uma lista completa, consulte [as funcionalidades de Blob Storage disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviços suportados aZure

A Azure Data Lake Storage Gen2 suporta vários serviços Azure que pode usar para ingerir dados, realizar análises e criar representações visuais. Para obter uma lista de serviços Azure suportados, consulte [os serviços Azure que suportam a Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Consulte [os serviços Azure que suportam a Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas open source suportadas

Várias plataformas de código aberto suportam data lake storage gen2. Para obter uma lista completa, consulte [plataformas Open source que suportam a Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Consulte [plataformas open source que suportam a Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>APIs de armazenamento de blob

As APIs blob e as APIs de armazenamento de dados podem operar nos mesmos dados.

Esta secção descreve problemas e limitações com a utilização de APIs blob e APIs de armazenamento de dados para operar nos mesmos dados.

* Não é possível utilizar as APIs de Armazenamento de API e Data Lake para escrever na mesma instância de um ficheiro. Se escrever para um ficheiro usando APIs de Armazenamento de Dados Lake Gen2, então os blocos desse ficheiro não serão visíveis para chamadas para a API [blob da Lista de Blocos de Obter.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) A única exceção é quando se usa em excesso. Pode substituir um ficheiro/bolha utilizando a API.

* Quando utilizar a operação [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sem especificar um delimiter, os resultados incluirão tanto diretórios como blobs. Se optar por utilizar um delimiter, utilize apenas um corte para a frente `/` (). Este é o único delimiter apoiado.

* Se utilizar a API [delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) para apagar um diretório, esse diretório só será apagado se estiver vazio. Isto significa que não pode usar os diretórios de exclusão da API blob novamente.

Estas APIs Blob REST não são suportadas:

* [Coloque Blob (Página)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Colocar página](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Obter Gamas de Páginas](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Bolha de cópia incremental](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Colocar página de URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Bloco de Apêndice de URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Os discos VM não geridos não são suportados em contas que tenham um espaço hierárquico de nomes. Se pretender ativar um espaço hierárquico numa conta de armazenamento, coloque discos VM não geridos numa conta de armazenamento que não tenha a funcionalidade de espaço hierárquico ativada.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Suporte para definir listas de controlo de acesso (ACLs) de forma recursiva

A capacidade de aplicar alterações ACL recursivamente do diretório dos pais para itens infantis está em [visualização pública](recursive-access-control-lists.md). Na versão atual desta capacidade, pode aplicar alterações ACL utilizando PowerShell, o .NET SDK e o Python SDK. O suporte ainda não está disponível para o Java SDK, Azure CLI, o portal Azure ou Azure Storage Explorer.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Utilize apenas a versão mais recente do AzCopy[(AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Versões anteriores de AzCopy, como AzCopy v8.1, não são suportadas.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Explorador do Storage do Azure

Utilize apenas versões  `1.6.0`   ou mais altas.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Explorador de Armazenamento no portal Azure

Os ACLs ainda não estão apoiados.

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>Aplicações de terceiros

As aplicações de terceiros que usam APIs REST para trabalhar continuarão a funcionar se as utilizar com aplicações gen2 de armazenamento de dados que chamam APIs blob provavelmente funcionarão.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listas de controlo de acesso (ACL) e acesso de leitura anónimo

Se o [acesso de leitura anónima](storage-manage-access-to-resources.md) tiver sido concedido a um recipiente, então os ACLs não têm qualquer efeito nesse recipiente ou nos ficheiros desse contentor.

### <a name="diagnostic-logs"></a>Registos de diagnósticos

A definição para dias de retenção ainda não está suportada, mas pode eliminar registos manualmente utilizando qualquer ferramenta suportada, como O Explorador de Armazenamento Azure, REST ou um SDK.

## <a name="issues-specific-to-premium-performance-blockblobstorage-storage-accounts"></a>Emissões específicas das contas de armazenamento blockBlobStorage de desempenho premium

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Os registos de diagnóstico ainda não podem ser ativados utilizando o portal Azure. Pode ative-los utilizando o PowerShell. Por exemplo:

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>Políticas de gestão do ciclo de vida

- As políticas de gestão do ciclo de vida são suportadas apenas nas contas v2 para fins gerais. Ainda não são suportados em contas de armazenamento premium BlockBlobStorage.
- Os dados não podem ser transferidos do nível premium para os níveis mais baixos.


### <a name="hdinsight-support"></a>Suporte HDInsight

Quando cria um cluster n HDInsight, ainda não é possível selecionar uma conta BlockBlobStorage que tenha a funcionalidade de espaço hierárquico ativada. No entanto, pode anexar a conta ao cluster depois de a ter criado.

### <a name="dremio-support"></a>Apoio dremio

O Dremio ainda não se conecta a uma conta BlockBlobStorage que tenha a funcionalidade hierárquica do espaço de identificação ativada. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Controlador do Windows Azure Storage Blob (WASB) (não suportado com data lake storage gen2)

Atualmente, o condutor do WASB, que foi projetado apenas para trabalhar apenas com a API Blob, encontra problemas em alguns cenários comuns. Especificamente, quando é cliente de uma conta de armazenamento hierárquica ativada pelo espaço de nome. O acesso a vários protocolos no Armazenamento do Lago de Dados não vai atenuar estes problemas. 

Por enquanto (e muito provavelmente o futuro previsível), não apoiaremos os clientes que usam o motorista WASB como cliente para uma conta de armazenamento hierárquica ativada pelo espaço de identificação. Em vez disso, recomendamos que opte por utilizar o controlador [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) no seu ambiente Hadoop. Se está a tentar migrar para fora de um ambiente Hadoop no local com uma versão mais cedo do que a filial Hadoop-3, então, por favor, abra um bilhete de Suporte Azure para que possamos entrar em contacto consigo no caminho certo para si e para a sua organização.
