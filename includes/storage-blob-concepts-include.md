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
ms.openlocfilehash: 8a85a21d47733bb031ea8ca16f776ea8c2df6da2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158680"
---
O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. Armazenamento de Blobs está otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são os dados que não cumprem a um modelo de dados específica ou a definição, como texto ou dados binários.

## <a name="about-blob-storage"></a>Sobre o armazenamento de BLOBs

Armazenamento de BLOBs é projetado para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Escrever nos ficheiros de registo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Utilizadores ou aplicações de cliente podem acessar objetos no armazenamento de BLOBs através de HTTP/HTTPS, de qualquer lugar do mundo. Objetos no armazenamento de BLOBs são acessíveis através da [API de REST de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [da CLI do Azure](https://docs.microsoft.com/cli/azure/storage), ou uma biblioteca de cliente de armazenamento do Azure. Bibliotecas de cliente estão disponíveis para uma variedade de linguagens, incluindo [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [node. js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [ir ](https://github.com/azure/azure-storage-blob-go/), [PHP](http://azure.github.io/azure-storage-php/), e [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre o Azure Data Lake Storage Gen2

Armazenamento de BLOBs suporta Gen2 de armazenamento do Azure Data Lake, solução de análise do Microsoft enterprise grandes quantidades de dados para a nuvem. Ficheiro de ofertas de geração 2 de armazenamento do Azure Data Lake hierárquica um sistema, bem como as vantagens do armazenamento de BLOBs, incluindo o armazenamento em camadas e de baixo custo; elevada disponibilidade; consistência forte; e capacidades de recuperação após desastre.

Para obter mais informações sobre a geração 2 de armazenamento do Data Lake, veja [introdução ao Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).