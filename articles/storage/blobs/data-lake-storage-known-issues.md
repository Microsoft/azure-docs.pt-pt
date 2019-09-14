---
title: Problemas conhecidos com geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Saiba mais sobre as limitações e problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: fb333db693c0f42b66e9fd45f5eb3c879787875d
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959119"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake

Este artigo lista os recursos e as ferramentas que ainda não têm suporte ou que só têm suporte parcial com contas de armazenamento que têm um namespace hierárquico (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>APIs de armazenamento de BLOBs

As APIs de armazenamento de BLOBs são desabilitadas para evitar problemas de operabilidade de recursos que podem surgir porque as APIs de armazenamento de BLOBs ainda não são interoperáveis com Azure Data Lake APIs

> [!NOTE]
> Se você se registrar na visualização pública de acesso de vários protocolos em Data Lake Storage, as APIs de BLOB e Data Lake Storage Gen2 APIs poderão operar nos mesmos dados. Para saber mais, consulte [acesso de vários protocolos em data Lake Storage](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>O que fazer com ferramentas, aplicativos e serviços existentes

Se qualquer um deles usar APIs de BLOB e você quiser usá-las para trabalhar com todo o conteúdo carregado em sua conta, você terá duas opções.

* **Opção 1**: Não habilite um namespace hierárquico em sua conta de armazenamento de BLOBs até que as APIs de BLOB se tornem interoperáveis com Azure Data Lake APIs Gen2. O uso de uma conta de armazenamento sem um namespace hierárquico significa que você não tem acesso a Data Lake Storage Gen2 recursos específicos, como listas de controle de acesso de diretório e contêiner.

* **Opção 2**: Registre-se na visualização pública de [acesso de vários protocolos em data Lake Storage](data-lake-storage-multi-protocol-access.md). Ferramentas e aplicativos que chamam APIs de BLOB, bem como recursos de armazenamento de BLOBs, como logs de diagnóstico, podem trabalhar com contas que têm um namespace hierárquico.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>O que fazer se você usou APIs de BLOB para carregar dados antes de as APIs de blob serem desabilitadas

Se utilizou estas APIs para carregar os dados antes de eles foram desativados, e tem um requisito de produção para aceder a esses dados, em seguida, entre em contato com Support da Microsoft com as seguintes informações:

> [!div class="checklist"]
> * ID da assinatura (o GUID, não o nome).
> * Nome (s) da conta de armazenamento.
> * Se você está ativamente impactado em produção e, em caso afirmativo, para quais contas de armazenamento?.
> * Mesmo que não são a ativamente afetados na produção, diga-nos se estes dados para ser copiado para outra conta de armazenamento por algum motivo são necessários e em caso afirmativo, por que?

Sob essas circunstâncias, podemos restaurar o acesso à API de blob por um período de tempo limitado para que você possa copiar esses dados em uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problemas e limitações com o uso de APIs de BLOB em contas que têm um namespace hierárquico

Se você se registrar na visualização pública de acesso de vários protocolos em Data Lake Storage, as APIs de BLOB e Data Lake Storage Gen2 APIs poderão operar nos mesmos dados.

Esta seção descreve problemas e limitações com o uso de APIs de BLOB e Data Lake Storage Gen2 APIs para operar nos mesmos dados.

* Você não pode usar as APIs de BLOB e as APIs de Data Lake Storage para gravar na mesma instância de um arquivo.

* Se você gravar em um arquivo usando APIs Data Lake Storage Gen2, os blocos desse arquivo não ficarão visíveis para chamadas para a API de BLOB da [lista de blocos Get](https://docs.microsoft.com/rest/api/storageservices/get-block-list) .

* Você pode substituir um arquivo usando APIs Data Lake Storage Gen2 ou APIs de BLOB. Isso não afetará as propriedades do arquivo.

* Quando você usa a [](https://docs.microsoft.com/rest/api/storageservices/list-blobs) operação listar BLOBs sem especificar um delimitador, os resultados incluirão diretórios e blobs.

  Se você optar por usar um delimitador, use apenas uma barra`/`(). Esse é o único delimitador com suporte.

* Se você usar a API [excluir blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) para excluir um diretório, esse diretório será excluído somente se estiver vazio.

  Isso significa que você não pode usar a API de BLOB para excluir diretórios recursivamente.

Essas APIs REST de BLOB não têm suporte:

* [Colocar BLOB (página)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Colocar página](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Obter intervalos de página](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob de cópia incremental](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Colocar página da URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Colocar BLOB (acrescentar)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Bloco de acréscimo](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Anexar bloco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Problemas com discos de VM (máquina virtual) não gerenciados

Não há suporte para discos de VM não gerenciados em contas que têm um namespace hierárquico. Se você quiser habilitar um namespace hierárquico em uma conta de armazenamento, coloque os discos de VM não gerenciados em uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.


## <a name="support-for-other-blob-storage-features"></a>Suporte para outros recursos de armazenamento de BLOBs

A tabela a seguir lista todos os outros recursos e ferramentas que ainda não têm suporte ou que só têm suporte parcial com contas de armazenamento que têm um namespace hierárquico (Azure Data Lake Storage Gen2).

| Recurso/ferramenta    | Mais informações    |
|--------|-----------|
| **APIs para contas de armazenamento Data Lake Storage Gen2** | Com suporte parcial <br><br>o acesso de vários protocolos no Data Lake Storage está atualmente em visualização pública. Essa visualização permite que você use APIs de blob nos SDKs .NET, Java e Python com contas que têm um namespace hierárquico.  Os SDKs ainda não contêm APIs que permitem interagir com diretórios ou definir ACLs (listas de controle de acesso). Para executar essas funções, você pode usar Data Lake Storage Gen2 APIs **REST** . |
| **AZCopy** | Suporte específico à versão <br><br>Use apenas a versão mais recente do AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Não há suporte para versões anteriores do AzCopy, como AzCopy v 8.1.|
| **Políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs** | Com suporte apenas se você se registrar no [acesso de vários protocolos na](data-lake-storage-multi-protocol-access.md) versão prévia data Lake Storage. As camadas de acesso fria e de arquivo são suportadas apenas pela visualização. Ainda não há suporte para a exclusão de instantâneos de BLOB. |
| **CDN (rede de distribuição de conteúdo) do Azure** | Ainda não tem suporte|
| **Azure Search** |Com suporte apenas se você se registrar no [acesso de vários protocolos na](data-lake-storage-multi-protocol-access.md) versão prévia data Lake Storage.|
| **Explorador do Armazenamento do Azure** | Suporte específico à versão <br><br>Use somente a `1.6.0` versão ou superior. <br>A `1.6.0` versão está disponível como um [Download gratuito](https://azure.microsoft.com/features/storage-explorer/).|
| **ACLs de contêiner de BLOB** |Ainda não tem suporte|
| **Blobfuse** |Ainda não tem suporte|
| **Domínios personalizados** |Ainda não tem suporte|
| **Explorador do sistema de arquivos** | Suporte limitado |
| **Registo de diagnósticos** |Os logs de diagnóstico só têm suporte se você se registrar no [acesso de vários protocolos na](data-lake-storage-multi-protocol-access.md) versão prévia do data Lake Storage. <br><br>No momento, não há suporte para a habilitação de logs no portal do Azure. Aqui está um exemplo de como habilitar os logs usando o PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Certifique-se de `Blob` especificar como o valor `-ServiceType` do parâmetro, conforme mostrado neste exemplo. <br><br>No momento, Gerenciador de Armazenamento do Azure não pode ser usada para exibir os logs de diagnóstico. Para exibir os logs, use AzCopy ou SDKs.
| **Armazenamento imutável** |Ainda não tem suporte <br><br>O armazenamento imutável oferece a capacidade de armazenar dados em um estado de [worm (gravar uma vez, ler muitos)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Camadas de nível de objeto** |As camadas frias e de arquivo só têm suporte se você se registrar no [acesso de vários protocolos na versão prévia do data Lake Storage](data-lake-storage-multi-protocol-access.md) . <br><br> Todas as outras camadas de acesso ainda não têm suporte.|
| **Suporte ao PowerShell e à CLI** | Funcionalidade limitada <br><br>As operações de gerenciamento, como a criação de uma conta, têm suporte. As operações do plano de dados, como carregar e baixar arquivos, estão em visualização pública como parte do [acesso de vários protocolos em data Lake Storage](data-lake-storage-multi-protocol-access.md). O trabalho com diretórios e a configuração de listas de controle de acesso (ACLs) ainda não têm suporte. |
| **Sites estáticos** |Ainda não tem suporte <br><br>Especificamente, a capacidade de fornecer arquivos para [sites estáticos](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplicativos de terceiros** | Suporte limitado <br><br>Aplicativos de terceiros que usam APIs REST para trabalhar continuarão a funcionar se você usá-los com Data Lake Storage Gen2. <br>Aplicativos que chamam APIs de blob provavelmente funcionarão se você se registrar na visualização pública de [acesso de vários protocolos em data Lake Storage](data-lake-storage-multi-protocol-access.md). 
| **Recursos de controle de versão** |Ainda não tem suporte <br><br>Isso inclui [instantâneos](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) e [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


