---
title: Mova os dados de armazenamento blob com conectores SSIS - Processo de Ciência de Dados da Equipa
description: Saiba como mover dados de ou para o Armazenamento De Blob Azure utilizando o Pacote de Funcionalidades de Integração de Servidores SQL para o Azure.
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
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720876"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover dados de ou para o Armazenamento De Blob Azure utilizando conectores SSIS
O Pacote de Funcionalidades de Integração de [Servidores SQL para o Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornece componentes para ligar ao Azure, transferir dados entre as fontes de dados do Azure e no local e processar os dados armazenados no Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Uma vez que os clientes tenham mudado os dados no local para a nuvem, podem aceder aos seus dados a partir de qualquer serviço Azure para alavancar toda a potência do conjunto de tecnologias Azure. Os dados podem ser posteriormente utilizados, por exemplo, no Azure Machine Learning ou num cluster HDInsight.

Exemplos para a utilização destes recursos Azure estão nos walkthroughs [SQL](sql-walkthrough.md) e [HDInsight.](hive-walkthrough.md)

Para uma discussão de cenários canónicos que usam o SSIS para realizar necessidades empresariais comuns em cenários de integração de dados híbridos, consulte [Fazer mais com o SQL Server Integration Services Feature Pack para](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) o blog Azure.

> [!NOTE]
> Para uma introdução completa ao armazenamento de blob Azure, consulte [o Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e o [Serviço Blob Azure.](https://msdn.microsoft.com/library/azure/dd179376.aspx)
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para executar as tarefas descritas neste artigo, deve ter uma subscrição Azure e uma conta De Armazenamento Azure configurada. Precisa do nome da conta Azure Storage e da chave de conta para fazer upload ou descarregamento de dados.

* Para configurar uma **subscrição Azure,** consulte o [teste gratuito de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Para obter instruções sobre a criação de uma conta de **armazenamento** e para obter informações de conta e chave, consulte as contas de [Armazenamento Azure](../../storage/common/storage-create-storage-account.md).

Para utilizar os **conectores SSIS,** tem de descarregar:

* **SQL Server 2014 ou 2016 Standard (ou acima)**: A instalação inclui os Serviços de Integração do Servidor SQL.
* **Microsoft SQL Server 2014 ou 2016 Integration Services Pack para Azure**: Estes conectores podem ser descarregados, respectivamente, a partir das páginas [sQL Server 2014 integration Services](https://www.microsoft.com/download/details.aspx?id=47366) e [SQL Server 2016 Integration Services.](https://www.microsoft.com/download/details.aspx?id=49492)

> [!NOTE]
> O SSIS está instalado com o SQL Server, mas não está incluído na versão Express. Para obter informações sobre quais as aplicações incluídas em várias edições do SQL Server, consulte [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Para materiais de formação no SSIS, consulte [Hands On Training for SSIS](https://www.microsoft.com/sql-server/training-certification)

Para obter informações sobre como obter up-and-running usando SISS para construir pacotes simples de extração, transformação e carga (ETL), consulte [SSIS Tutorial: Criar um pacote Simples ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Baixar conjunto de dados do táxi de NYC
O exemplo aqui descrito usa um conjunto de dados disponível publicamente- o conjunto de dados de Viagens de Táxi de [NYC.](https://www.andresmh.com/nyctaxitrips/) O conjunto de dados é composto por cerca de 173 milhões de passeios de táxi em NYC no ano de 2013. Existem dois tipos de dados: dados de dados da viagem e dados de tarifas. Como há um ficheiro para cada mês, temos 24 ficheiros, cada um dos quais é cerca de 2 GB descomprimido.

## <a name="upload-data-to-azure-blob-storage"></a>Enviar dados para armazenamento de blob Azure
Para mover dados utilizando o pacote de funcionalidades SSIS do local para o armazenamento de blob Azure, utilizamos uma instância da Tarefa de Upload de [**Blob Azure,**](https://msdn.microsoft.com/library/mt146776.aspx)mostrada aqui:

![configurar-dados-ciência-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Os parâmetros que a tarefa utiliza são descritos aqui:

| Campo | Descrição |
| --- | --- |
| **Conexão AzureStorage** |Especifica um gestor de conexão de armazenamento azure existente ou cria um novo que se refere a uma conta de Armazenamento Azure que aponta para onde os ficheiros blob estão hospedados. |
| **BlobContainer** |Especifica o nome do recipiente blob que contém os ficheiros carregados como bolhas. |
| **BlobDirectory** |Especifica o diretório blob onde o ficheiro carregado é armazenado como uma bolha de bloco. O diretório blob é uma estrutura hierárquica virtual. Se a bolha já existe, é substituída. |
| **Diretório Local** |Especifica o diretório local que contém os ficheiros a serem carregados. |
| **Nome de ficheiro** |Especifica um filtro de nome para selecionar ficheiros com o padrão de nome especificado. Por exemplo,\*mySheet\* .xls inclui ficheiros como MySheet001.xls e MySheetABC.xlsx |
| **Intervalos de tempo/intervalos de tempo** |Especifica um filtro de intervalo de tempo. Ficheiros modificados após *timeRangeFrom* e antes de *timeRangeTo* serem incluídos. |

> [!NOTE]
> As credenciais **AzureStorageConnection** têm de estar corretas e o **BlobContainer** tem de existir antes da transferência ser tentada.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Descarregue dados do armazenamento de blob Azure
Para descarregar dados do armazenamento de blob azure para o armazenamento no local com o SSIS, utilize uma instância da Tarefa de [Descarregamento de Blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Cenários mais avançados do SSIS-Azure
O pacote de funcionalidades SSIS permite que fluxos mais complexos sejam tratados em conjunto com tarefas de embalagem. Por exemplo, os dados blob poderiam alimentar-se diretamente num cluster HDInsight, cuja saída poderia ser descarregada de volta para uma bolha e, em seguida, para o armazenamento no local. O SSIS pode executar trabalhos de Hive e Pig num cluster HDInsight utilizando conectores SSIS adicionais:

* Para executar um script de Colmeia num cluster Azure HDInsight com SSIS, utilize a Tarefa de [Colmeia Azure HDInsight](https://msdn.microsoft.com/library/mt146771.aspx).
* Para executar um script pig em um cluster Azure HDInsight com SSIS, use [Azure HDInsight Pig Task](https://msdn.microsoft.com/library/mt146781.aspx).

