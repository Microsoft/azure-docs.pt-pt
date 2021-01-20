---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98612923"
---
O Armazenamento de blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados. Dados não estruturados são dados que não aderem a um determinado modelo ou definição de dados, como texto ou dados binários.

## <a name="about-blob-storage"></a>Acerca do Armazenamento de Blobs do Azure

O Armazenamento de blobs foi concebido para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Escrever nos ficheiros de registo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os utilizadores ou aplicações de clientes podem aceder a objetos no armazenamento blob via HTTP/HTTPS, a partir de qualquer parte do mundo. Os objetos no armazenamento Blob são acessíveis através da [Azure Storage REST API,](/rest/api/storageservices/blob-service-rest-api) [Azure PowerShell,](/powershell/module/az.storage) [Azure CLI](/cli/azure/storage)ou uma biblioteca de clientes do Azure Storage. As bibliotecas de clientes estão disponíveis para diferentes idiomas, incluindo:

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Ir](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre o Azure Data Lake Storage Gen2

O Armazenamento de blobs suporta o Azure Data Lake Storage Gen2, a solução de análise de macrodados empresariais para a cloud da Microsoft. A Azure Data Lake Storage Gen2 oferece um sistema de ficheiros hierárquicos, bem como as vantagens do armazenamento blob, incluindo:

* Armazenamento de baixo custo e tiered
* Elevada disponibilidade
* Consistência forte
* Capacidades de recuperação de desastres

Para obter mais informações sobre data lake storage gen2, consulte [Introdução ao Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md).