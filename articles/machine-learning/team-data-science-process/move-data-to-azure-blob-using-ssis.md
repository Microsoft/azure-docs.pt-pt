---
title: Mover dados de armazenamento de Blobs com conectores SSIS - Team Data Science Process
description: Mova dados de ou para armazenamento de Blobs do Azure com conectores SSIS.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 688ef059387dc4c312873051c7c9d1eae259b1ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57844788"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover dados de ou para armazenamento de Blobs do Azure com conectores SSIS
O [SQL Server Integration Services Feature Pack para o Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornece componentes para ligar ao Azure, transferir dados entre origens de dados do Azure e no local e processar os dados armazenados no Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Assim que os clientes terem sido movidos dados no local para a cloud, eles podem aceder a partir de qualquer serviço do Azure para aproveitar todo o potencial do conjunto de tecnologias do Azure. Ele pode ser usado, por exemplo, no Azure Machine Learning ou num cluster do HDInsight.

Isso costuma ser o primeiro passo para o [SQL](sql-walkthrough.md) e [HDInsight](hive-walkthrough.md) orientações passo a passo.

Para uma discussão sobre cenários canônicos que usar o SSIS para realizar a necessidades comerciais comuns em cenários de integração de dados híbridos, consulte [fazer mais com o SQL Server Integration Services Feature Pack para o Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog.

> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blobs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e, a [serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para executar as tarefas descritas neste artigo, tem de ter uma subscrição do Azure e uma conta de armazenamento do Azure configurada. Deve saber a sua chave de conta e o nome conta de armazenamento do Azure para carregar ou transferir dados.

* Para configurar uma **subscrição do Azure**, consulte [durante um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma **conta de armazenamento** e, para obter a conta e informações da chave, veja [sobre as contas de armazenamento](../../storage/common/storage-create-storage-account.md).

Para utilizar o **conectores SSIS**, tem de transferir:

* **SQL Server 2014 ou 2016 Standard (ou superior)**: Instalação inclui o SQL Server Integration Services.
* **Feature Pack para o Azure dos serviços de integração do Microsoft SQL Server 2014 ou 2016**: Estes podem ser transferidos, respectivamente, a partir da [serviços de integração do SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=47366) e [serviços de integração do SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=49492) páginas.

> [!NOTE]
> SSIS é instalado com o SQL Server, mas não está incluído na versão Express. Para obter informações sobre quais aplicativos estão incluídos nas várias edições do SQL Server, consulte [edições do SQL Server](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Para obter materiais de treinamento sobre o SSIS, veja [mãos no treinamento para SSIS](https://www.microsoft.com/sql-server/training-certification)

Para obter informações sobre como obter a cópia de segurança operacional com SISS para criar simple extração, transformação e carregamento (ETL) de pacotes, consulte [Tutorial do SSIS: Criando um pacote ETL simples](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Transferir o conjunto de dados de táxis de NYC
O exemplo descrito aqui usar um conjunto de dados publicamente disponível – a [NYC táxis viagens](https://www.andresmh.com/nyctaxitrips/) conjunto de dados. O conjunto de dados consiste em cerca de 173 milhões táxis passes em NYC no ano de 2013. Existem dois tipos de dados: viagem fornece detalhes sobre os dados de dados e Europeia. Como há um arquivo para todos os meses, temos 24 ficheiros em todos, cada uma delas é aproximadamente 2GB descomprimido.

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o armazenamento de Blobs do Azure
Para mover dados com o SSIS feature pack no local para o armazenamento de Blobs do Azure, podemos usar uma instância do [ **tarefa de carregamento de Blobs do Azure**](https://msdn.microsoft.com/library/mt146776.aspx), mostrado aqui:

![Configurar-data--vm de ciência](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Os parâmetros que utiliza a tarefa são descritos aqui:

| Campo | Descrição |
| --- | --- |
| **AzureStorageConnection** |Especifica um Gerenciador de conexões de armazenamento do Azure existente ou cria um novo, o que se refere a uma conta de armazenamento do Azure que aponta para onde os ficheiros de blob estão alojados. |
| **BlobContainer** |Especifica o nome do contentor de BLOBs que contêm os ficheiros carregados como blobs. |
| **BlobDirectory** |Especifica o diretório de BLOBs onde o ficheiro carregado é armazenado como um blob de blocos. O diretório de BLOBs é uma estrutura hierárquica virtual. Se o blob já existir, it ia substituído. |
| **LocalDirectory** |Especifica o diretório de local que contém os ficheiros a carregar. |
| **FileName** |Especifica um filtro de nomes para selecionar os ficheiros com o padrão de nome especificado. Por exemplo, MySheet\*. xls\* inclui arquivos como MySheet001.xls e MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Especifica um filtro de intervalo de tempo. Ficheiros modificados após *TimeRangeFrom* e antes *TimeRangeTo* estão incluídos. |

> [!NOTE]
> O **AzureStorageConnection** as credenciais têm de estar correto e o **BlobContainer** tem de existir antes da transferência é tentada.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Transferir dados do armazenamento de Blobs do Azure
Para transferir dados do armazenamento de Blobs do Azure para armazenamento no local com o SSIS, utilize uma instância do [a tarefa de transferir o Blob do Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Cenários mais avançados do SSIS-Azure
O feature pack do SSIS permite fluxos mais complexos ser tratada por tarefas de empacotamento em conjunto. Por exemplo, os dados de blob poderá alimentar diretamente para um cluster do HDInsight, cuja saída pode ser transferida para um blob e, em seguida, para armazenamento no local. SSIS pode executar tarefas do Pig e Hive num cluster HDInsight utilizando a conectores adicionais do SSIS:

* Para executar um script do Hive num cluster HDInsight do Azure com o SSIS, utilize [do Azure HDInsight Hive tarefas](https://msdn.microsoft.com/library/mt146771.aspx).
* Para executar um script Pig num cluster HDInsight do Azure com o SSIS, utilize [do Azure HDInsight Pig tarefas](https://msdn.microsoft.com/library/mt146781.aspx).

