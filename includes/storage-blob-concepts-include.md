---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cb4cac18f887a72c05fe520d6103dc00399cc05e
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828909"
---
O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados. Dados não estruturados são dados que não aderem a um determinado modelo ou definição de dados, como texto ou dados binários.

## <a name="about-blob-storage"></a>Acerca do Armazenamento de Blobs do Azure

O Armazenamento de blobs foi concebido para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Escrever nos ficheiros de registo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os utilizadores ou aplicações de clientes podem aceder a objetos no armazenamento blob via HTTP/HTTPS, a partir de qualquer parte do mundo. Os objetos no armazenamento Blob são acessíveis através da [Azure Storage REST API,](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.storage) [Azure CLI](https://docs.microsoft.com/cli/azure/storage)ou uma biblioteca de clientes do Azure Storage. As bibliotecas de clientes estão disponíveis para diferentes idiomas, incluindo:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Ir](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre o Azure Data Lake Storage Gen2

O Armazenamento de blobs suporta o Azure Data Lake Storage Gen2, a solução de análise de macrodados empresariais para a cloud da Microsoft. A Azure Data Lake Storage Gen2 oferece um sistema de ficheiros hierárquicos, bem como as vantagens do armazenamento blob, incluindo:

* Armazenamento de baixo custo e tiered
* Elevada disponibilidade
* Consistência forte
* Capacidades de recuperação de desastres

Para obter mais informações sobre data lake storage gen2, consulte [Introdução ao Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
