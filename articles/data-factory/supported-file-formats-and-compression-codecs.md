---
title: Formatos de ficheiros suportados na Azure Data Factory
description: Este tópico descreve os formatos de ficheiros e códigos de compressão que são suportados por conectores baseados em ficheiros na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439578"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formatos de ficheiros suportados e codecs de compressão na Azure Data Factory

*Este artigo aplica-se aos seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Pode utilizar a [atividade Copy](copy-activity-overview.md) para copiar ficheiros como está entre duas lojas de dados baseadas em ficheiros, caso em que os dados são copiados de forma eficiente sem qualquer serialização ou desserialização. 

Além disso, também pode analisar ou gerar ficheiros de um determinado formato. Por exemplo, pode executar o seguinte:

* Copie os dados de uma base de dados do SQL Server no local e escreva para O Armazenamento de Lago de Dados Azure Gen2 em formato Parquet.
* Copiar ficheiros em formato de texto (CSV) a partir de um sistema de ficheiros no local e escrever para o armazenamento Azure Blob em formato Avro.
* Copie ficheiros com fecho de um sistema de ficheiros no local, descomprima-os no voo e escreva ficheiros extraídos para o Azure Data Lake Storage Gen2.
* Copie os dados no formato gzip de texto comprimido (CSV) do armazenamento da Blob Azure e escreva-os na Base de Dados Azure SQL.
* Muitas mais atividades que requerem serialização/desserialização ou compressão/descompressão.

## <a name="next-steps"></a>Passos seguintes

Consulte os outros artigos da Atividade cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Copiar desempenho da atividade](copy-activity-performance.md)
