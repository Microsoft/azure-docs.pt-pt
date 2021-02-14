---
title: 'Tutorial da Data Factory: Primeiro pipeline de dados '
description: Este tutorial da Azure Data Factory mostra-lhe como criar e agendar uma fábrica de dados que processa dados usando o script hive num cluster Hadoop.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7f1de53e20614ca66c91735ce462da5a194d1836
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377232"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Tutorial: Construa o seu primeiro oleoduto para transformar dados usando o cluster Hadoop
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja o [Início Rápido: criar uma fábrica de dados com o Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, constrói a sua primeira fábrica de dados Azure com um pipeline de dados. O oleoduto transforma dados de entrada executando o script da Hive num cluster Azure HDInsight (Hadoop) para produzir dados de saída.  

Este artigo fornece uma visão geral e pré-requisitos para o tutorial. Depois de completar os pré-requisitos, pode fazer o tutorial usando uma das seguintes ferramentas/SDKs: Visual Studio, PowerShell, Resource Manager, REST API. Selecione uma das opções na lista de drop-down no início (ou) links no final deste artigo para fazer o tutorial usando uma destas opções.    

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, vai executar os seguintes passos:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos de dados que se movem e transformam dados.

    Neste tutorial, cria-se um oleoduto na fábrica de dados.
2. Criar um **oleoduto**. Um pipeline pode ter uma ou mais atividades (exemplos: Atividade de Cópia, Atividade Hive do HDInsight). Esta amostra usa a atividade de Hive HDInsight que executa um script de Colmeia em um cluster HDInsight Hadoop. O script cria primeiro uma tabela que faz referência aos dados de registo sonoro armazenados no armazenamento de blob Azure e, em seguida, divisórias os dados brutos por ano e mês.

    Neste tutorial, o oleoduto utiliza a Atividade da Colmeia para transformar dados executando uma consulta de Hive num cluster Azure HDInsight Hadoop.
3. Criar **serviços ligados**. Cria um serviço ligado para ligar um arquivo de dados ou um serviço de computação à fábrica de dados. Um arquivo de dados como o Armazenamento do Azure contém dados de entrada/saída de atividades no pipeline. Um serviço de computação como o hdInsight Hadoop processa/transforma dados.

    Neste tutorial, cria dois serviços ligados: **Azure Storage** e **Azure HDInsight**. O serviço ligado ao Azure Storage liga uma Conta de Armazenamento Azure que detém os dados de entrada/saída para a fábrica de dados. O serviço ligado a Azure HDInsight liga um cluster Azure HDInsight que é usado para transformar dados na fábrica de dados.
3. Criar **conjuntos de dados** de entrada e de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade.

    Neste tutorial, os conjuntos de dados de entrada e saída especificam as localizações dos dados de entrada e saída no Azure Blob Storage. O serviço ligado ao Azure Storage especifica o que é utilizado na Conta de Armazenamento Azure. Um conjunto de dados de entrada especifica onde os ficheiros de entrada estão localizados e um conjunto de dados de saída especifica onde os ficheiros de saída são colocados.


Consulte o artigo [de Introdução à Fábrica de Dados Azure](data-factory-introduction.md) para uma visão detalhada da Azure Data Factory.

Aqui está a vista do **diagrama** da fábrica de dados de amostra que você constrói neste tutorial. **MyFirstPipeline** tem uma atividade do tipo Hive que consome o conjunto de dados **AzureBlobInput** como uma entrada e produz o conjunto de dados **AzureBlobOut como** uma saída.

![Vista de diagrama no tutorial do Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Neste tutorial, a pasta **inputdata** do recipiente **adfgetstarted** Azure blob contém um ficheiro nomeado entrada.log. Este ficheiro de registo tem entradas de três meses: janeiro, fevereiro e março de 2016. Seguem-se as linhas de exemplo para cada mês no ficheiro de entrada.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Quando o ficheiro é processado pelo pipeline com a Atividade Hive do HDInsight, a atividade executa um script Hive no cluster do HDInsight que cria partições dos dados de entrada por ano e mês. O script cria três pastas de saída que contêm um ficheiro com as entradas de cada mês.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Das linhas de amostra acima apresentadas, a primeira (com 2016-01-01) é escrita para o ficheiro 000000_0 na pasta mês=1. Do mesmo modo, a segunda é escrita no ficheiro na pasta month=2 e a terceira é escrita no ficheiro na pasta month=3.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

1. **Subscrição do Azure** - Se não tiver uma subscrição do Azure, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Veja o artigo [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) sobre como poderá obter uma conta de avaliação gratuita.
2. **Armazenamento do Azure** – Utilize uma conta de armazenamento do Azure para armazenar os dados deste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, anote o **nome** da conta e **a chave de acesso**. Para obter informações sobre como recuperar as chaves de acesso à conta de armazenamento, consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
3. Faça o download e reveja o ficheiro de consulta da Hive **(HQL)** localizado em: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) . Esta consulta transforma dados de entrada para produzir dados de saída.
4. Faça o download e reveja o ficheiro de entrada de amostra **(entrada.log)** localizado em: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Crie um recipiente blob chamado **adfgetstared** no seu Azure Blob Storage.
6. Faça o upload do ficheiro **partitionweblogs.hql** para a pasta de **scripts** no recipiente **adfgetstarted.** Utilize ferramentas como o [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Faça o **upload da entrada.log** ficheiro para a pasta **de dados de entrada** no recipiente **adfgetstarted.**

Depois de completar os pré-requisitos, selecione uma das seguintes ferramentas/SDKs para fazer o tutorial:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

O Visual Studio fornece uma forma GUI de construir as suas fábricas de dados. Enquanto que as opções powerShell, Resource Manager e REST API fornecem a forma de script/programação de construir as suas fábricas de dados.

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Não copia dados de um de dados de origem para um arquivo de dados de destino. Para um tutorial sobre como copiar dados com o Azure Data Factory, consulte [Tutorial: Copy data from Blob Storage to SQL Database (Tutorial: copiar dados do Armazenamento de Blobs para a Base de Dados SQL)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas.
