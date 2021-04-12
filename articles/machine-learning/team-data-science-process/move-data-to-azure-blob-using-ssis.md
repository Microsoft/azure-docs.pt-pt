---
title: Mover dados de armazenamento blob com conectores SSIS - Processo de Ciência de Dados de Equipa
description: Saiba como mover dados para ou a partir do Azure Blob Storage usando o SqL Server Integration Services Feature Feature Pack for Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9572d612e7ee8e2fd72850ba14447e8449f0f371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322004"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover dados para ou de Azure Blob Storage usando conectores SSIS
O [SQL Server Integration Services Feature Feature Pack for Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) fornece componentes para ligar ao Azure, transferir dados entre fontes de dados do Azure e no local e processar dados armazenados no Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Uma vez que os clientes tenham movido dados no local para a nuvem, podem aceder aos seus dados a partir de qualquer serviço Azure para aproveitar a potência total do conjunto de tecnologias Azure. Os dados podem ser posteriormente utilizados, por exemplo, em Azure Machine Learning ou num cluster HDInsight.

Exemplos para a utilização destes recursos Azure estão nas caminhadas [SQL](sql-walkthrough.md) e [HDInsight.](hive-walkthrough.md)

Para uma discussão de cenários canónicos que usam o SSIS para realizar necessidades de negócio comuns em cenários de integração de dados híbridos, consulte fazer mais com o SQL Server Integration Services Feature Pack para o blog [Azure.](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238)

> [!NOTE]
> Para uma introdução completa ao armazenamento de blob Azure, consulte [a Azure Blob Basics](../../storage/blobs/storage-quickstart-blobs-dotnet.md) e o [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para executar as tarefas descritas neste artigo, tem de ter uma subscrição Azure e uma conta Azure Storage configurada. Precisa do nome da conta e da conta do Azure Storage para carregar ou transferir dados.

* Para configurar uma **subscrição Azure,** consulte [o teste gratuito de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre a criação de uma **conta de armazenamento** e para obter informações de conta e informações fundamentais, consulte as contas de Armazenamento [Azure](../../storage/common/storage-account-create.md).

Para utilizar os **conectores SSIS,** tem de baixar:

* **SQL Server 2014 ou 2016 Standard (ou acima)**: A instalação inclui serviços de integração de servidores SQL.
* **Microsoft SQL Server 2014 ou 2016 Integration Services Feature Pack for Azure**: Estes conectores podem ser descarregados, respectivamente, a partir dos Serviços de [Integração do SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=47366) e das páginas [dos Serviços de Integração do SQL Server 2016.](https://www.microsoft.com/download/details.aspx?id=49492)

> [!NOTE]
> O SSIS está instalado com o SQL Server, mas não está incluído na versão Express. Para obter informações sobre quais aplicações estão incluídas em várias edições do SQL Server, consulte [edições de servidor SQL](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Para materiais de formação em SSIS, consulte [Hands On Training for SSIS](https://www.microsoft.com/sql-server/training-certification)

Para obter informações sobre como se levantar e funcionar utilizando o SISS para construir pacotes simples de extração, transformação e carga (ETL), consulte [O Tutorial SSIS: Criação de um pacote ETL simples](/sql/integration-services/ssis-how-to-create-an-etl-package).

## <a name="download-nyc-taxi-dataset"></a>Baixar conjunto de dados de táxi nyc
O exemplo descrito aqui usa um conjunto de dados publicamente disponível - o conjunto de dados [de viagens de táxi de NYC.](https://www.andresmh.com/nyctaxitrips/) O conjunto de dados consiste em cerca de 173 milhões de passeios de táxi em NYC no ano de 2013. Existem dois tipos de dados: dados de detalhes de viagem e dados de tarifas. Como há um ficheiro para cada mês, temos 24 ficheiros, cada um dos quais é cerca de 2 GB descomprimidos.

## <a name="upload-data-to-azure-blob-storage"></a>Faça upload de dados para o armazenamento de blob Azure
Para mover dados usando o pacote de funcionalidades SSIS de instalações para o armazenamento de blob Azure, usamos uma instância da [**Tarefa de Upload Azure Blob**](/sql/integration-services/control-flow/azure-blob-upload-task), mostrada aqui:

![configuração-dado-ciência-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Os parâmetros que a tarefa utiliza são descritos aqui:

| Campo | Descrição |
| --- | --- |
| **AzurestorageConnection** |Especifica um gestor de conexão de armazenamento Azure existente ou cria um novo que se refere a uma conta de Armazenamento Azure que aponta para onde os ficheiros blob estão hospedados. |
| **BlobContainer** |Especifica o nome do recipiente blob que contém os ficheiros carregados como bolhas. |
| **BlobDirect** |Especifica o diretório blob onde o ficheiro carregado é armazenado como uma bolha de bloco. O diretório blob é uma estrutura hierárquica virtual. Se a bolha já existe, foi substituída. |
| **Diretório Local** |Especifica o diretório local que contém os ficheiros a serem carregados. |
| **Nome de arquivo** |Especifica um filtro de nome para selecionar ficheiros com o padrão de nome especificado. Por exemplo, o MySheet \* .xls \* inclui ficheiros como MySheet001.xls e MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Especifica um filtro de intervalo de tempo. Os ficheiros modificados após *o TimeRangeFrom* e antes *do TimeRangeTo* serem incluídos. |

> [!NOTE]
> As credenciais **AzureStorageConnection** têm de ser corretas e o **BlobContainer** deve existir antes da tentativa de transferência.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Baixar dados do armazenamento de blob Azure
Para transferir dados do armazenamento de blob Azure para o armazenamento no local com sSIS, utilize uma instância da Tarefa de Download da [Blob Azure](/sql/integration-services/control-flow/azure-blob-download-task).

## <a name="more-advanced-ssis-azure-scenarios"></a>Cenários de SSIS-Azure mais avançados
O pacote de funcionalidades SSIS permite que fluxos mais complexos sejam manuseados em conjunto por tarefas de embalagem. Por exemplo, os dados do blob podem alimentar-se diretamente num cluster HDInsight, cuja saída poderia ser descarregada de volta para uma bolha e, em seguida, para armazenamento no local. O SSIS pode executar trabalhos de Hive e Pig num cluster HDInsight utilizando conectores SSIS adicionais:

* Para executar um script hive em um cluster Azure HDInsight com SSIS, use [Azure HDInsight Hive Task](/sql/integration-services/control-flow/azure-hdinsight-hive-task).
* Para executar um script pig num cluster Azure HDInsight com SSIS, utilize [a Azure HDInsight Pig Task](/sql/integration-services/control-flow/azure-hdinsight-pig-task).