---
title: Problemas conhecidos com geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Saiba mais sobre as limitações e problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: normesta
ms.openlocfilehash: abc20d8ef51f94e25d0e46ec199ca715bf6cce22
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736792"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake

Este artigo contém problemas conhecidos e limitações temporárias com geração 2 de armazenamento do Data Lake.

## <a name="data-lake-storage-gen2-apis"></a>APIs de geração 2 do Data Lake Storage

APIs ainda não estão disponíveis para contas de geração 2 de armazenamento do Data Lake.

## <a name="blob-storage-apis"></a>APIs de armazenamento de BLOBs

APIs de armazenamento de blob ainda não estão disponíveis para contas de geração 2 de armazenamento do Data Lake.

Essas APIs estão desativadas para evitar problemas de acesso de dados acidentais que podem surgir como APIs de armazenamento de BLOBs não ainda são interoperáveis com APIs de geração 2 do Azure Data Lake.

Se utilizou estas APIs para carregar os dados antes de eles foram desativados, e tem um requisito de produção para aceder a esses dados, em seguida, entre em contato com Support da Microsoft com as seguintes informações:

* ID de subscrição (o GUID, não o nome do utilizador)

* Nome de conta de armazenamento (s)

* Se ativamente foi afetado em produção e em caso afirmativo, para as contas de armazenamento?

* Mesmo que não são a ativamente afetados na produção, diga-nos se estes dados para ser copiado para outra conta de armazenamento por algum motivo são necessários e em caso afirmativo, por que?

Nessas circunstâncias, podemos pode restaurar o acesso à API do Blob para um período de tempo limitado, de modo a que pode copiar esses dados para uma conta de armazenamento que não tem a funcionalidade de espaço de nomes hierárquico ativada.

Discos não geridos da Máquina Virtual (VM) dependem de APIs de armazenamento de BLOBs desativado, portanto, se pretender ativar um espaço de nomes hierárquico numa conta de armazenamento, considere colocar os discos VM não geridos para uma conta de armazenamento que não tem a funcionalidade de espaço de nomes hierárquico ativado.

## <a name="api-interoperability"></a>Interoperabilidade de API

APIs de armazenamento de BLOBs e APIs de geração 2 do Azure Data Lake não são interoperáveis entre si.

Se tiver ferramentas, aplicações, serviços ou scripts que utilizam APIs de Blob e pretende utilizá-los para trabalhar com todo o conteúdo que carregar para a sua conta, em seguida, não a ativar um espaço de nomes hierárquico na sua conta de armazenamento de BLOBs até que o tornam-se de APIs de Blob interoperável com APIs de geração 2 do Azure Data Lake. Com uma conta de armazenamento sem um espaço de nomes hierárquico significa que, em seguida, não tem acesso a recursos específicos de geração 2 de armazenamento do Data Lake, como o sistema de ficheiros e diretórios acessar listas de controlo.

## <a name="azure-storage-explorer"></a>Explorador do Storage do Azure

Para ver ou gerir contas de geração 2 de armazenamento do Data Lake ao utilizar o Explorador de armazenamento do Azure, tem de ter, pelo menos, versão `1.6.0` da ferramenta que está disponível como uma [gratuitamente para download](https://azure.microsoft.com/features/storage-explorer/).

Tenha em atenção que a versão do Explorador de armazenamento que está incorporada no Portal do Azure faz atualmente não suporta a visualização ou gerenciamento de contas de geração 2 de armazenamento do Data Lake com a funcionalidade de espaço de nomes hierárquico ativada.

## <a name="blob-viewing-tool"></a>Ferramenta de visualização de blob

Blob de visualização de ferramenta no portal do Azure tem apenas suporte limitado para geração 2 de armazenamento do Data Lake.

## <a name="third-party-applications"></a>Aplicações de terceiros

Aplicações de terceiros podem não suportar a geração 2 de armazenamento do Data Lake.

O suporte é ao critério de cada fornecedor de aplicações de terceiros. Atualmente, APIs de armazenamento de BLOBs e APIs de geração 2 de armazenamento do Data Lake não pode ser utilizadas para gerir o mesmo conteúdo. Pois estamos a trabalhar para permitir que a interoperabilidade, é possível que muitas ferramentas de terceiros serão automaticamente suporta a geração 2 de armazenamento do Data Lake.

## <a name="azcopy-support"></a>Suporte do AzCopy

AzCopy versão 8 não suporta a geração 2 de armazenamento do Data Lake.

Em alternativa, utilize a versão de pré-visualização mais recente do AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) por oferecer suporte a pontos finais de geração 2 de armazenamento do Data Lake.

## <a name="azure-event-grid"></a>Azure Event Grid

[O Azure Event Grid](https://azure.microsoft.com/services/event-grid/) não receber eventos de contas de geração 2 do Azure Data Lake, porque essas contas ainda não gerá-los.  

## <a name="soft-delete-and-snapshots"></a>Eliminação de forma recuperável e instantâneos

Eliminação de forma recuperável e instantâneos não estão disponíveis para contas de geração 2 de armazenamento do Data Lake.

Todas as funcionalidades de controlo de versões incluindo [instantâneos](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) e [eliminação de forma recuperável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) não ainda estão disponíveis para contas de armazenamento que têm a funcionalidade de espaço de nomes hierárquico ativada.

## <a name="object-level-storage-tiers"></a>Camadas de armazenamento ao nível do objeto

Camadas de armazenamento ao nível do objeto (frequente, esporádico e arquivo) ainda não estão disponíveis para contas do Azure Data Lake Storage Gen 2, mas estão disponíveis para contas de armazenamento que não têm a funcionalidade de espaço de nomes hierárquico ativada.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Políticas de gestão de ciclo de vida de armazenamento de Blobs do Azure

Políticas de gestão de ciclo de vida de armazenamento de Blobs do Azure ainda não estão disponíveis para contas de geração 2 de armazenamento do Data Lake.

Estas políticas estão disponíveis para contas de armazenamento que não têm a funcionalidade de espaço de nomes hierárquico ativada.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

Os registos de diagnóstico não estão disponíveis para contas de geração 2 de armazenamento do Data Lake.
