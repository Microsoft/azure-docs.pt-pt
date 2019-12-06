---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cac6c40397e272d6d65931029c500bdcc2b774cc
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875407"
---
O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários.

## <a name="about-blob-storage"></a>Acerca do Armazenamento de Blobs do Azure

O Armazenamento de blobs foi concebido para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Escrever nos ficheiros de registo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os usuários ou aplicativos cliente podem acessar objetos no armazenamento de blob por meio de HTTP/HTTPS, de qualquer lugar do mundo. Os objetos no armazenamento de BLOBs podem ser acessados por meio da [API REST do armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [CLI do Azure](https://docs.microsoft.com/cli/azure/storage)ou uma biblioteca de cliente de armazenamento do Azure. As bibliotecas de cliente estão disponíveis para diferentes idiomas, incluindo:

* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre Azure Data Lake Storage Gen2

O Armazenamento de blobs suporta o Azure Data Lake Storage Gen2, a solução de análise de macrodados empresariais para a cloud da Microsoft. O Azure Data Lake Storage Gen2 oferece um sistema de arquivos hierárquico, bem como as vantagens do armazenamento de BLOBs, incluindo:

* Armazenamento em camadas de baixo custo
* Elevada disponibilidade
* Consistência forte
* Recursos de recuperação de desastre

Para obter mais informações sobre Data Lake Storage Gen2, consulte [introdução ao Azure data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
