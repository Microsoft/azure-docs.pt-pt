---
title: Ferramentas de ingestão de dados
titleSuffix: Azure Data Science Virtual Machine
description: Conheça as ferramentas e utilitários de ingestão de dados que estão pré-instalados na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519376"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de máquina virtual de ciência de dados de dados

Como um dos primeiros passos técnicos de um projeto de ciência de dados ou IA, deve identificar os conjuntos de dados a serem utilizados e trazê-los para o seu ambiente de análise. A Máquina Virtual de Ciência de Dados (DSVM) fornece ferramentas e bibliotecas para trazer dados de diferentes fontes para o armazenamento de dados analíticos localmente no DSVM, ou para uma plataforma de dados na nuvem ou no local.

Aqui estão algumas ferramentas de movimento de dados que estão disponíveis no DSVM.

## <a name="adlcopy"></a>AdlCopy

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Uma ferramenta para copiar dados do armazenamento da Azure Blob na Azure Data Lake Store. Também pode copiar dados entre duas contas Azure Data Lake Store.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importando várias bolhas do armazenamento Azure Blob para a Azure Data Lake Store.      |
|  Como usá-lo/ executá-lo?    |   Abra uma solicitação de comando e `adlcopy` escreva para obter ajuda.    |
| Ligações a amostras      | [Utilizar o AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| Ferramentas relacionadas no DSVM      | AzCopy     |

## <a name="azure-cli"></a>CLI do Azure

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Uma ferramenta de gestão para o Azure. Também contém verbos de comando para mover dados de plataformas de dados Azure, como o armazenamento de Azure Blob e a Azure Data Lake Store.     |
| Versões DSVM suportadas      | Windows, Linux     |
| Usos típicos      | Importar e exportar dados de e para a Azure Storage e Azure Data Lake Store.      |
|  Como usá-lo/ executá-lo?    |   Abra uma solicitação de comando e `az` escreva para obter ajuda.    |
| Ligações a amostras      | [Utilizar a CLI do Azure](/cli/azure)     |
| Ferramentas relacionadas no DSVM      | AzCopy      |


## <a name="azcopy"></a>AzCopy

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Uma ferramenta para copiar dados de e para ficheiros locais, armazenamento, ficheiros e tabelas Azure Blob.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Copiar ficheiros para o armazenamento da Azure Blob e copiar bolhas entre contas.      |
|  Como usá-lo/ executá-lo?    |   Abra uma solicitação de comando e `azcopy` escreva para obter ajuda.    |
| Ligações a amostras      | [AzCopy no Windows](../../storage/common/storage-use-azcopy-v10.md)      |
| Ferramentas relacionadas no DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados Azure Cosmos DB

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Ferramenta para importar dados de várias fontes para Azure Cosmos DB, uma base de dados NoSQL na nuvem. Estas fontes incluem ficheiros JSON, ficheiros CSV, SQL, MongoDB, armazenamento de mesa Azure, Amazon DynamoDB e Azure Cosmos DB SQL API coleções.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importar ficheiros de um VM para CosmosDB, importar dados do armazenamento de mesas Azure para cosmosDB, e importar dados de uma base de dados do Microsoft SQL Server para CosmosDB.     |
|  Como usá-lo/ executá-lo?    |   Para utilizar a versão da linha de comando, abra um pedido de comando e escreva `dt` . Para utilizar a ferramenta GUI, abra uma solicitação de comando e escreva `dtui` .    |
| Ligações a amostras      | [Dados de importação do CosmosDB](../../cosmos-db/import-data.md)      |
| Ferramentas relacionadas no DSVM      | AzCopy      |

## <a name="azure-storage-explorer"></a>Explorador de Armazenamento do Azure

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Interface gráfica do utilizador para interagir com ficheiros armazenados na nuvem Azure. |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Dados de importação e exportação do DSVM.    |
|  Como usá-lo/ executá-lo?    | Procure "Azure Storage Explorer" no menu Iniciar. |
| Ligações a amostras      | [Explorador do Storage do Azure](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Ferramenta SQL Server para copiar dados entre o SQL Server e um ficheiro de dados.      |
| Versões DSVM suportadas      | Windows      |
| Usos típicos      | Importar um ficheiro CSV numa tabela do SQL Server e exportar uma tabela sql Server para um ficheiro.      |
|  Como usá-lo/ executá-lo?    |   Abra uma solicitação de comando e `bcp` escreva para obter ajuda.    |
| Ligações a amostras      | [utilidade bcp](/sql/tools/bcp-utility)      |
| Ferramentas relacionadas no DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| Categoria | Valor |
| ------------- | ------------- |
| Que passa?   | Uma ferramenta para montar um recipiente de armazenamento Azure Blob no sistema de ficheiros Linux.      |
| Versões DSVM suportadas      | Linux      |
| Usos típicos      | Ler e escrever para bolhas num recipiente.      |
|  Como usá-lo e executá-lo?    |   Executar _blobfuse_ em um terminal.    |
| Ligações a amostras      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas no DSVM      | CLI do Azure      |