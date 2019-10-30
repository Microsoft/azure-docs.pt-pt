---
title: Mover dados de armazenamento de BLOBs com conectores SSIS-processo de ciência de dados de equipe
description: Saiba como mover dados de ou para o armazenamento de BLOBs do Azure usando o SQL Server Integration Services Feature Pack para o Azure.
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
ms.openlocfilehash: 8fb802905070ab1ddc8af93e501e8e11e262cf4a
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053940"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover dados de ou para o armazenamento de BLOBs do Azure usando conectores SSIS
O [Feature Pack SQL Server Integration Services do Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornece componentes para se conectar ao Azure, transferir dados entre o Azure e fontes de dados locais e processar dados armazenados no Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Depois que os clientes movevam os dados locais para a nuvem, eles podem acessá-los de qualquer serviço do Azure para aproveitar todo o potencial do pacote de tecnologias do Azure. Ele pode ser usado, por exemplo, em Azure Machine Learning ou em um cluster HDInsight.

Normalmente, essa é a primeira etapa para o passo a passos do [SQL](sql-walkthrough.md) e do [HDInsight](hive-walkthrough.md) .

Para uma discussão sobre cenários canônicos que usam o SSIS para atender às necessidades de negócios comuns em cenários de integração de dados híbridos, consulte [fazendo mais com SQL Server Integration Services Feature Pack para o blog do Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) .

> [!NOTE]
> Para obter uma introdução completa ao armazenamento de BLOBs do Azure, consulte [noções básicas de blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para executar as tarefas descritas neste artigo, você deve ter uma assinatura do Azure e uma conta de armazenamento do Azure configurada. Você deve saber o nome da conta de armazenamento do Azure e a chave de conta para carregar ou baixar dados.

* Para configurar uma **assinatura do Azure**, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma **conta de armazenamento** e obter informações de conta e chave, consulte [sobre contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md).

Para usar os **conectores do SSIS**, você deve baixar:

* **SQL Server 2014 ou 2016 Standard (ou superior)** : a instalação inclui SQL Server Integration Services.
* **Microsoft SQL Server 2014 ou 2016 Integration Services Feature Pack para Azure**: eles podem ser baixados, respectivamente, das páginas [SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) e [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> O SSIS é instalado com o SQL Server, mas não está incluído na versão Express. Para obter informações sobre quais aplicativos estão incluídos em várias edições do SQL Server, consulte [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Para obter materiais de treinamento sobre o SSIS, consulte [treinamento prático para SSIS](https://www.microsoft.com/sql-server/training-certification)

Para obter informações sobre como começar a usar o SISS para criar pacotes ETL (extração, transformação e carregamento) simples, consulte [tutorial do SSIS: Criando um pacote ETL simples](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Baixar conjunto de NYC de táxi
O exemplo descrito aqui usa um conjunto de um DataSet disponível publicamente--o conjunto de [NYC de táxis](https://www.andresmh.com/nyctaxitrips/) . O conjunto de informações consiste em cerca de 173 milhões táxi corridas em NYC no ano 2013. Há dois tipos de dados: dados de informações de viagem e dados de Tarifa. Como há um arquivo para cada mês, temos 24 arquivos, cada um deles com aproximadamente 2 GB descompactados.

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados no armazenamento de BLOBs do Azure
Para mover dados usando o pacote de recursos do SSIS do local para o armazenamento de BLOBs do Azure, usamos uma instância da [**tarefa de upload de blob do Azure**](https://msdn.microsoft.com/library/mt146776.aspx), mostrada aqui:

![Configure-Data-Science-VM](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Os parâmetros que a tarefa usa são descritos aqui:

| Campo | Descrição |
| --- | --- |
| **AzureStorageConnection** |Especifica um Gerenciador de conexões de armazenamento do Azure existente ou cria um novo que se refere a uma conta de armazenamento do Azure que aponta para onde os arquivos de blob estão hospedados. |
| **BlobContainer** |Especifica o nome do contêiner de BLOB que contém os arquivos carregados como BLOBs. |
| **BlobDirectory** |Especifica o diretório de blob onde o arquivo carregado é armazenado como um blob de blocos. O diretório de blob é uma estrutura hierárquica virtual. Se o blob já existir, ia substituído. |
| **LocalDirectory** |Especifica o diretório local que contém os arquivos a serem carregados. |
| **Nome do arquivo** |Especifica um filtro de nome para selecionar arquivos com o padrão de nome especificado. Por exemplo, MySheet\*. xls\* inclui arquivos como MySheet001. xls e MySheetABC. xlsx |
| **TimeRangeFrom de intervalo** |Especifica um filtro de intervalo de tempo. Arquivos modificados após *TimeRangeFrom* e antes de *timerangeto* são incluídos. |

> [!NOTE]
> As credenciais do **AzureStorageConnection** precisam estar corretas e o **BlobContainer** deve existir antes da tentativa de transferência.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Baixar dados do armazenamento de BLOBs do Azure
Para baixar dados do armazenamento de BLOBs do Azure para o armazenamento local com o SSIS, use uma instância da [tarefa de download de blob do Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>SSIS mais avançados – cenários do Azure
O pacote de recursos do SSIS permite que fluxos mais complexos sejam manipulados por tarefas de empacotamento em conjunto. Por exemplo, os dados de blob podem ser alimentados diretamente em um cluster HDInsight, cuja saída pode ser baixada de volta para um blob e, em seguida, para o armazenamento local. O SSIS pode executar trabalhos Hive e Pig em um cluster HDInsight usando conectores SSIS adicionais:

* Para executar um script do hive em um cluster do Azure HDInsight com o SSIS, use a [tarefa do hive do Azure hdinsight](https://msdn.microsoft.com/library/mt146771.aspx).
* Para executar um script Pig em um cluster do Azure HDInsight com o SSIS, use a [tarefa Pig do Azure hdinsight](https://msdn.microsoft.com/library/mt146781.aspx).

