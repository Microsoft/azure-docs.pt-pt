---
title: Ferramentas de ingestão de dados de Máquina Virtual de ciência de dados - Azure | Documentos da Microsoft
description: Saiba mais sobre as ferramentas e os utilitários de ingestão de dados pré-instalados no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: vijetajo
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
ms.author: vijetaj
ms.openlocfilehash: 58bfab43d39f050e19687c30a61e05892fffc3f2
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060607"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de Máquina Virtual de ciência de dados

Como uma das primeiras etapas técnicas em um projeto de ciência de dados ou de ia, você deve identificar os conjuntos de dados a serem usados e colocá-los em seu ambiente de análise. O Máquina Virtual de Ciência de Dados (DSVM) fornece ferramentas e bibliotecas para trazer dados de diferentes fontes para o armazenamento de dados analíticos localmente no DSVM ou em uma plataforma de dados na nuvem ou no local.

Aqui estão algumas ferramentas de movimentação de dados que estão disponíveis no DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados do armazenamento de BLOBs do Azure para Azure Data Lake Store. Também pode copiar dados entre duas contas de Azure Data Lake Store.      |
| Versões do DSVM com suporte      | Windows      |
| Utilizações típicas      | Importando vários BLOBs do armazenamento de BLOBs do Azure para o Azure Data Lake Store.      |
|  Como utilizar / executá-lo?    |   Abra um prompt de comando e `adlcopy` digite para obter ajuda.    |
| Links para amostras      | [Utilizar o AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Ferramentas relacionadas no DSVM      | AzCopy, CLI do Azure     |

## <a name="azure-cli"></a>CLI do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gerenciamento para o Azure. Ele também contém verbos de comando para mover dados de plataformas de dados do Azure, como armazenamento de BLOBs do Azure e Azure Data Lake Store.     |
| Versões do DSVM com suporte      | Windows, Linux     |
| Utilizações típicas      | Importando e exportando dados de e para o armazenamento do Azure e Azure Data Lake Store.      |
|  Como utilizar / executá-lo?    |   Abra um prompt de comando e `az` digite para obter ajuda.    |
| Links para amostras      | [Utilizar a CLI do Azure](https://docs.microsoft.com/cli/azure)     |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de e para arquivos locais, armazenamento de BLOBs do Azure, arquivos e tabelas.      |
| Versões do DSVM com suporte      | Windows      |
| Utilizações típicas      | Copiar arquivos para o armazenamento de BLOBs do Azure e copiar BLOBs entre contas.      |
|  Como utilizar / executá-lo?    |   Abra um prompt de comando e `azcopy` digite para obter ajuda.    |
| Links para amostras      | [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas no DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados do Cosmos DB do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados de várias fontes para Azure Cosmos DB. Essas fontes incluem arquivos JSON, arquivos CSV, SQL, MongoDB, armazenamento de tabelas do Azure, Amazon DynamoDB e coleções de API do SQL Azure Cosmos DB.      |
| Versões do DSVM com suporte      | Windows      |
| Utilizações típicas      | Importando arquivos de uma VM para o CosmosDB, importando dados do armazenamento de tabelas do Azure para CosmosDB e importando dados de um banco de dado Microsoft SQL Server para o CosmosDB.     |
|  Como utilizar / executá-lo?    |   Para usar a versão de linha de comando, abra um prompt de comando `dt`e digite. Para usar a ferramenta GUI, abra um prompt de comando e `dtui`digite.    |
| Links para amostras      | [CosmosDB importar dados](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas no DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de SQL Server para copiar dados entre o SQL Server e um ficheiro de dados.      |
| Versões do DSVM com suporte      | Windows      |
| Utilizações típicas      | Importar um arquivo CSV para uma tabela SQL Server e exportar uma tabela SQL Server para um arquivo.      |
|  Como utilizar / executá-lo?    |   Abra um prompt de comando e `bcp` digite para obter ajuda.    |
| Links para amostras      | [utilitário bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Ferramentas relacionadas no DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para montar um contêiner de armazenamento de BLOBs do Azure no sistema de arquivos do Linux.      |
| Versões do DSVM com suporte      | Linux      |
| Utilizações típicas      | Leitura e gravação em BLOBs em um contêiner.      |
|  Como usá-lo e executá-lo?    |   Execute _blobfuse_ num terminal.    |
| Links para amostras      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas no DSVM      | CLI do Azure      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para se ligar no local origens de dados para a nuvem de serviços para consumo.      |
| Versões do DSVM com suporte      | Windows      |
| Utilizações típicas      | A ligar a uma VM a uma origem de dados no local.      |
|  Como utilizar / executá-lo?    |   Abra o gateway do Microsoft Gerenciamento de Dados no menu iniciar.    |
| Links para amostras      | [Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Ferramentas relacionadas no DSVM      | O AzCopy, AdlCopy, bcp    |
