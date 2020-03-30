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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136023"
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

Os utilizadores ou aplicações de clientes podem aceder a objetos no armazenamento Blob via HTTP/HTTPS, de qualquer parte do mundo. Os objetos em armazenamento Blob são acessíveis através da [API, Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) [PowerShell, Azure](https://docs.microsoft.com/powershell/module/azure.storage) [CLI](https://docs.microsoft.com/cli/azure/storage)ou de uma biblioteca de clientes Azure Storage. As bibliotecas de clientes estão disponíveis para diferentes idiomas, incluindo:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Nó.js](https://azure.github.io/azure-storage-node) 
* [Pitão](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Ir](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre o Armazenamento do Lago De Dados Azure Gen2

O Armazenamento de blobs suporta o Azure Data Lake Storage Gen2, a solução de análise de macrodados empresariais para a cloud da Microsoft. O Azure Data Lake Storage Gen2 oferece um sistema de ficheiros hierárquico, bem como as vantagens do armazenamento blob, incluindo:

* Armazenamento a baixo custo e hierárquico
* Elevada disponibilidade
* Consistência forte
* Capacidades de recuperação de desastres

Para mais informações sobre data Lake Storage Gen2, consulte Introdução ao Armazenamento de [Lagos De dados Azure Gen2](../articles/storage/data-lake-storage/introduction.md).
