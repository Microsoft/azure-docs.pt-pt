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
ms.openlocfilehash: e90e750bf248bdcc8e50c6ddc6e9fa0273660195
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136023"
---
O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados. Os dados não estruturados são dados que não seguem uma definição ou um modelo de dados particular, como texto ou dados binários.

## <a name="about-blob-storage"></a>Acerca do Armazenamento de Blobs do Azure

O Armazenamento de blobs foi concebido para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Escrever nos ficheiros de registo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os utilizadores ou aplicações cliente podem aceder aos objetos no armazenamento de Blobs por HTTP/HTTPS, em qualquer parte do mundo. Os objetos no armazenamento de Blobs são acessíveis através da [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), do [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), da [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou de uma biblioteca de cliente do Armazenamento do Azure. As bibliotecas de cliente estão disponíveis para diferentes linguagens, incluindo:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Acerca do Azure Data Lake Storage Gen2

O Armazenamento de blobs suporta o Azure Data Lake Storage Gen2, a solução de análise de macrodados empresariais para a cloud da Microsoft. O Azure Data Lake Storage Gen2 oferece um sistema de ficheiros hierárquico, bem como as vantagens do armazenamento de Blobs, incluindo:

* Armazenamento em camadas de baixo custo
* Elevada disponibilidade
* Consistência forte
* Capacidades de recuperação após desastre

Para obter mais informações sobre o Data Lake Storage Gen2, veja [Introduction to Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md) (Introdução ao Azure Data Lake Storage Gen2).
