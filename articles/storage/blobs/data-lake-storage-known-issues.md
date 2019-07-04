---
title: Problemas conhecidos com geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Saiba mais sobre as limitações e problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: daf9199104047f714d568bd2796490b836243952
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443237"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos relacionados com a geração 2 de armazenamento do Azure Data Lake

Este artigo lista as funcionalidades e ferramentas que ainda não são suportadas ou apenas parcialmente suportadas com contas de armazenamento que têm um espaço de nomes hierárquico (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>APIs de armazenamento de BLOBs

APIs de armazenamento de BLOBs estão desativados para evitar problemas de capacidade de operação do recurso que podem surgir como APIs de armazenamento de BLOBs não ainda são interoperáveis com APIs de geração 2 do Azure Data Lake.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>O que fazer com as ferramentas existentes, aplicações e serviços

Se qualquer um destes uso de APIs de Blob e deseja usá-las a trabalhar com todo o conteúdo que carregar para a sua conta, em seguida, não ative um espaço de nomes hierárquico na sua conta de armazenamento de BLOBs até que as APIs de BLOBs tornam-se interoperável com APIs de geração 2 do Azure Data Lake.

Utilizar uma conta de armazenamento sem um espaço de nomes hierárquico significa que, em seguida, não tem acesso a recursos específicos de geração 2 de armazenamento do Data Lake, como o diretório e arquivo listas de controlo de acesso de sistema.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>O que fazer com discos não geridos da Máquina Virtual (VM)

Estes dependem de APIs de armazenamento de BLOBs desativado, então, se pretender ativar um espaço de nomes hierárquico numa conta de armazenamento, considere colocá-los para uma conta de armazenamento que não tem a funcionalidade de espaço de nomes hierárquico ativada.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>O que fazer se tiver utilizado APIs de BLOBs para carregar os dados antes de APIs de BLOBs foram desativadas

Se utilizou estas APIs para carregar os dados antes de eles foram desativados, e tem um requisito de produção para aceder a esses dados, em seguida, entre em contato com Support da Microsoft com as seguintes informações:

> [!div class="checklist"]
> * ID de subscrição (o GUID, não o nome do utilizador).
> * Nomes de conta de armazenamento.
> * Se ativamente foi afetado em produção e em caso afirmativo, para as contas de armazenamento?.
> * Mesmo que não são a ativamente afetados na produção, diga-nos se estes dados para ser copiado para outra conta de armazenamento por algum motivo são necessários e em caso afirmativo, por que?

Nessas circunstâncias, podemos pode restaurar o acesso à API do Blob para um período de tempo limitado, de modo a que pode copiar esses dados para uma conta de armazenamento que não tem a funcionalidade de espaço de nomes hierárquico ativada.

## <a name="all-other-features-and-tools"></a>Todas as outras funcionalidades e ferramentas

A tabela seguinte lista todas as outras funcionalidades e ferramentas que ainda não são suportadas ou apenas parcialmente suportadas com contas de armazenamento que têm um espaço de nomes hierárquico (Azure Data Lake Storage Gen2).

| Funcionalidade / ferramenta    | Mais informações    |
|--------|-----------|
| **APIs para contas de armazenamento de geração 2 de armazenamento do Data Lake** | Parcialmente suportada <br><br>Pode usar a geração 2 de armazenamento do Data Lake **REST** APIs, mas APIs nos outros SDKs de BLOBs, como os SDKs do .NET, Java, Python ainda não estão disponíveis.|
| **AZCopy** | Suporte específico da versão <br><br>Utilizar a versão mais recente do AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Versões anteriores do AzCopy como AzCopy v8.1, não são suportadas.|
| **Azure políticas de gestão do ciclo de vida de armazenamento de BLOBs** | Ainda não é suportado |
| **Rede de entrega de conteúdos do Azure (CDN)** | Ainda não é suportado|
| **O Azure search** |Ainda não é suportado|
| **Explorador do Armazenamento do Azure** | Suporte específico da versão <br><br>Utilize apenas a versão `1.6.0` ou superior. <br>Versão `1.6.0` está disponível como uma [gratuitamente para download](https://azure.microsoft.com/features/storage-explorer/).|
| **ACLs do contentor de BLOBs** |Ainda não é suportado|
| **Blobfuse** |Ainda não é suportado|
| **Domínios personalizados** |Ainda não é suportado|
| **Registos de diagnóstico** |Ainda não é suportado|
| **Explorador de sistema de ficheiros** | Suporte limitado |
| **Armazenamento imutável** |Ainda não é suportado <br><br>Armazenamento imutável dá a capacidade de armazenar dados num [WORM (escrever uma vez, ler vários)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) estado.|
| **Camadas ao nível do objeto** |Ainda não é suportado <br><br>Por exemplo: Premium, as camadas frequente, esporádico e arquivo.|
| **Suporte do PowerShell e CLI** | Funcionalidade limitada <br><br>Pode criar uma conta com o Powershell ou a CLI. Não é possível realizar operações ou definir listas de controle de acesso em sistemas de arquivos, diretórios e ficheiros.|
| **Web sites estáticos** |Ainda não é suportado <br><br>Especificamente, a capacidade para processar ficheiros [Web sites estáticos](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplicações de terceiros** | Suporte limitado <br><br>Aplicações de terceiros que utilizar REST APIs para trabalhar irão continuar a funcionar se usá-los com geração 2 de armazenamento do Data Lake. <br>Se tiver um aplicativo que usa APIs de BLOBs, esse aplicativo provavelmente terá problemas se usar esse aplicativo com geração 2 de armazenamento do Data Lake. Para obter mais informações, consulte a [APIs estão desativadas para contas de armazenamento de geração 2 de armazenamento do Data Lake do armazenamento de BLOBs](#blob-apis-disabled) seção deste artigo.|
| **Recursos de controle de versão** |Ainda não é suportado <br><br>Isto inclui [instantâneos](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) e [eliminação de forma recuperável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

