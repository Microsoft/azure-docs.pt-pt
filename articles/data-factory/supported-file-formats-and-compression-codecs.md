---
title: Formatos de ficheiros suportados por atividade de cópia na Azure Data Factory
description: Este tópico descreve os formatos de ficheiros e os códigos de compressão que são suportados pela atividade de cópia na Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364618"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Formatos de ficheiros suportados e codecs de compressão por atividade de cópia na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Este artigo aplica-se aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Pode utilizar a [atividade Copy](copy-activity-overview.md) para copiar ficheiros em vez de duas lojas de dados baseadas em ficheiros, caso em que os dados são copiados de forma eficiente sem qualquer serialização ou deserialização. 

Além disso, também pode analisar ou gerar ficheiros de um determinado formato. Por exemplo, pode realizar o seguinte:

* Copie os dados de uma base de dados do SQL Server e escreva para Azure Data Lake Storage Gen2 em formato Parquet.
* Copie ficheiros em formato de texto (CSV) a partir de um sistema de ficheiros no local e escreva para o armazenamento de Azure Blob em formato Avro.
* Copie ficheiros fechados de um sistema de ficheiros no local, descomprimir-os no voo e escreva ficheiros extraídos para a Azure Data Lake Storage Gen2.
* Copie os dados no formato Gzip comprimed text (CSV) a partir do armazenamento Azure Blob e escreva-os para a Base de Dados Azure SQL.
* Muitas mais atividades que requerem serialização/deserialização ou compressão/descompressão.

## <a name="next-steps"></a>Passos seguintes

Consulte os outros artigos de Atividade de Cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
