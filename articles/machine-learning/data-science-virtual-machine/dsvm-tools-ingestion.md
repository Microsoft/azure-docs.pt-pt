---
title: Ferramentas de ingestão de dados de Máquina Virtual de ciência de dados - Azure | Documentos da Microsoft
description: Saiba mais sobre as ferramentas de ingestão de dados e utilitários previamente instalados na máquina de Virtual de ciência de dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 92ff5d21fc30d8fcafe97a2b452ff157a2cd5f86
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997034"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de Máquina Virtual de ciência de dados

Uma das primeiras etapas técnicas num projeto de IA ou de ciência de dados é identificar os conjuntos de dados para ser utilizado e colocá-los no seu ambiente de análise. A Máquina Virtual de ciência de dados (DSVM) fornece ferramentas e bibliotecas para importar dados de diferentes fontes num armazenamento de dados analíticos localmente no DSVM ou numa plataforma de dados na cloud ou no local. 

Aqui estão algumas ferramentas de movimento de dados fornecidos no DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de blobs de armazenamento do Azure para o Azure Data Lake Store. Também pode copiar dados entre duas contas de Azure Data Lake Store.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações típicas      | Importar vários blobs de armazenamento do Azure para o Azure Data Lake Store.      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `adlcopy` para obter ajuda.    |
| Links para amostras      | [Utilizar o AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Ferramentas relacionadas na DSVM      | AzCopy, linha de comandos do Azure     |

## <a name="azure-command-line"></a>Linha de comandos do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gerenciamento para o Azure. Também contém verbos de comando para mover dados de plataformas de dados do Azure, como blobs de armazenamento do Azure, armazenamento do Azure Data Lake     |
| Versões suportadas DSVM      | Windows, Linux     |
| Utilizações típicas      | Importar, exportar dados para e do armazenamento do Azure, Azure Data Lake Store      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `az` para obter ajuda.    |
| Links para amostras      | [Utilizar a CLI do Azure](https://docs.microsoft.com/cli/azure)     |
| Ferramentas relacionadas na DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de e para ficheiros locais, blobs de armazenamento do Azure, ficheiros e tabelas.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações típicas      | Copiar ficheiros para o armazenamento de BLOBs, copiar blobs entre contas.      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `azcopy` para obter ajuda.    |
| Links para amostras      | [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas na DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados do Cosmos DB do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados de várias fontes, incluindo ficheiros JSON, ficheiros CSV, coleções de SQL, MongoDB, armazenamento tabelas do Azure, Amazon DynamoDB e API de SQL do Azure Cosmos DB para o Azure Cosmos DB.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações típicas      | Importar ficheiros a partir de uma VM para CosmosDB, importar dados do armazenamento de tabelas do Azure para CosmosDB ou importar dados a partir de uma base de dados do SQL Server para o cosmos DB.     |
|  Como utilizar / executá-lo?    |   Usar a linha de comando versão, abra um prompt de comando, em seguida, escreva `dt`. Para utilizar a ferramenta de GUI, abra um prompt de comando, em seguida, escreva `dtui`.    |
| Links para amostras      | [Dados de importação do cosmos DB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas na DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de SQL Server para copiar dados entre o SQL Server e um ficheiro de dados.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações típicas      | Importar um ficheiro CSV para uma tabela do SQL Server, a exportação de uma tabela do SQL Server para um arquivo.      |
|  Como utilizar / executá-lo?    |   Abra uma linha de comandos, em seguida, escreva `bcp` para obter ajuda.    |
| Links para amostras      | [Utilitário de cópia em massa](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Ferramentas relacionadas na DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para montar um contentor de Blobs do Azure no sistema de ficheiros de Linux.      |
| Versões suportadas DSVM      | Linux      |
| Utilizações típicas      | Leitura e escrita em blobs num contentor      |
|  Como utilizar / executá-lo?    |   Execute _blobfuse_ num terminal.    |
| Links para amostras      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas na DSVM      | Linha de comandos do Azure      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para se ligar no local origens de dados para a nuvem de serviços para consumo.      |
| Versões suportadas DSVM      | Windows      |
| Utilizações típicas      | A ligar a uma VM a uma origem de dados no local.      |
|  Como utilizar / executá-lo?    |   Inicie o "Microsoft Data Management Gateway" no Menu Iniciar.    |
| Links para amostras      | [Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Ferramentas relacionadas na DSVM      | O AzCopy, AdlCopy, bcp    |
