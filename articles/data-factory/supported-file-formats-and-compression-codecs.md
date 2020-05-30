---
title: Formatos de ficheiros suportados na Azure Data Factory
description: Este tópico descreve os formatos de ficheiros e os códigos de compressão que são suportados por conectores baseados em ficheiros na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 24edeb686136a1264d75e81fd9527feff3d17c48
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191095"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formatos de ficheiros suportados e codecs de compressão na Azure Data Factory
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

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
