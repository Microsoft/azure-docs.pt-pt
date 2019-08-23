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
ms.openlocfilehash: 1950e55cf669ea7502e8523d7f8fe429c2caeb49
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903851"
---
O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O armazenamento de BLOBs é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários.

## <a name="about-blob-storage"></a>Sobre o armazenamento de BLOBs

O armazenamento de BLOBs foi projetado para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Escrever nos ficheiros de registo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os usuários ou aplicativos cliente podem acessar objetos no armazenamento de blob por meio de HTTP/HTTPS, de qualquer lugar do mundo. Os objetos no armazenamento de BLOBs podem ser acessados por meio da [API REST do armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [CLI do Azure](https://docs.microsoft.com/cli/azure/storage)ou uma biblioteca de cliente de armazenamento do Azure. As bibliotecas de cliente estão disponíveis para uma variedade de linguagens, incluindo [.net](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [node. js](https://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [go](https://github.com/azure/azure-storage-blob-go/), [php](https://azure.github.io/azure-storage-php/)e [Ruby](https://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Sobre Azure Data Lake Storage Gen2

O armazenamento de BLOBs dá suporte à Azure Data Lake Storage Gen2, a solução de análise de Big Data empresarial da Microsoft para a nuvem. O Azure Data Lake Storage Gen2 oferece um sistema de arquivos hierárquico, bem como as vantagens do armazenamento de BLOBs, incluindo armazenamento em camadas de baixo custo; alta disponibilidade; consistência forte; e recursos de recuperação de desastres.

Para obter mais informações sobre Data Lake Storage Gen2, consulte [introdução ao Azure data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
