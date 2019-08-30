---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ca5830a3d02e4127f65e580ade7727ae92cd5c15
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166762"
---
O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários.

## <a name="about-blob-storage"></a>Sobre o armazenamento de BLOBs

O Armazenamento de blobs foi concebido para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Escrever nos ficheiros de registo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os usuários ou aplicativos cliente podem acessar objetos no armazenamento de blob por meio de HTTP/HTTPS, de qualquer lugar do mundo. Os objetos no armazenamento de BLOBs podem ser acessados por meio da [API REST do armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [CLI do Azure](https://docs.microsoft.com/cli/azure/storage)ou uma biblioteca de cliente de armazenamento do Azure. As bibliotecas de cliente estão disponíveis para uma variedade de linguagens, incluindo [.net](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [node. js](https://azure.github.io/azure-storage-node), [Python](/azure/python/), [go](https://github.com/azure/azure-storage-blob-go/), [php](https://azure.github.io/azure-storage-php/)e [Ruby](https://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre Azure Data Lake Storage Gen2

O Armazenamento de blobs suporta o Azure Data Lake Storage Gen2, a solução de análise de macrodados empresariais para a cloud da Microsoft. O Azure Data Lake Storage Gen2 oferece um sistema de ficheiros hierarquizado, assim como as vantagens do Armazenamento de blobs, incluindo o armazenamento em camadas económico; a levada disponibilidade; a forte consistência e as funcionalidades de recuperação após desastre.

Para obter mais informações sobre Data Lake Storage Gen2, consulte [introdução ao Azure data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
