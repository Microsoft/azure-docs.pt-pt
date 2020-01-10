---
title: Formatos de arquivo com suporte no Azure Data Factory
description: Este tópico descreve os formatos de ficheiro e códigos de compressão suportados pelo conectores baseados em ficheiros no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439578"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formatos de ficheiro suportados e codecs de compactação no Azure Data Factory

*Este artigo se aplica aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Você pode usar a [atividade de cópia](copy-activity-overview.md) para copiar arquivos no estado em que se encontram entre dois armazenamentos de dados baseados em arquivo; nesse caso, os dados são copiados com eficiência sem nenhuma serialização ou desserialização. 

Além disso, você também pode analisar ou gerar arquivos de um determinado formato. Por exemplo, você pode executar o seguinte:

* Copie dados de um banco de dado SQL Server local e grave em Azure Data Lake Storage Gen2 no formato parquet.
* Copiar arquivos no formato de texto (CSV) de um sistema de arquivos local e gravar no armazenamento de BLOBs do Azure no formato Avro.
* Copie arquivos compactados de um sistema de arquivos local, descompacte-os imediatamente e grave arquivos extraídos em Azure Data Lake Storage Gen2.
* Copie dados no formato de texto compactado gzip (CSV) do armazenamento de BLOBs do Azure e grave-os no banco de dados SQL do Azure.
* Muitas outras atividades que exigem serialização/desserialização ou compactação/descompactação.

## <a name="next-steps"></a>Passos seguintes

Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
