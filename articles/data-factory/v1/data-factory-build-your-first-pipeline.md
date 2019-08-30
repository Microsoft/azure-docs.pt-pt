---
title: 'Tutorial de Data Factory: Primeiro pipeline de dados | Microsoft Docs'
description: Este tutorial de Azure Data Factory mostra como criar e agendar um data factory que processa dados usando o script do hive em um cluster Hadoop.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 2dd2edfabff51c749890fe20d47a29c1ec39947c
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140388"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Tutorial: Crie seu primeiro pipeline para transformar dados usando o cluster Hadoop
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de data Factory, consulte [início rápido: Crie um data factory usando Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, você criará seu primeiro data factory do Azure com um pipeline de dados. O pipeline transforma os dados de entrada executando o script do hive em um cluster do Azure HDInsight (Hadoop) para produzir dados de saída.  

Este artigo fornece uma visão geral e pré-requisitos para o tutorial. Depois de concluir os pré-requisitos, você pode fazer o tutorial usando uma das seguintes ferramentas/SDKs: Visual Studio, PowerShell, modelo do Resource Manager, API REST. Selecione uma das opções na lista suspensa no início (ou) links no final deste artigo para fazer o tutorial usando uma das opções a seguir.    

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, vai executar os seguintes passos:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines de dados que movem e transformam dados. 

    Neste tutorial, você criará um pipeline no data factory. 
2. Crie um **pipeline**. Um pipeline pode ter uma ou mais atividades (exemplos: Atividade de cópia, atividade de hive do HDInsight). Este exemplo usa a atividade do hive do HDInsight que executa um script do hive em um cluster Hadoop do HDInsight. O script cria primeiro uma tabela que faz referência aos dados brutos de log da Web armazenados no armazenamento de BLOBs do Azure e, em seguida, particiona os dados brutos por ano e mês.

    Neste tutorial, o pipeline usa a atividade do hive para transformar dados executando uma consulta de Hive em um cluster Azure HDInsight Hadoop. 
3. Criar **Serviços vinculados**. Cria um serviço ligado para ligar um arquivo de dados ou um serviço de computação à fábrica de dados. Um arquivo de dados como o Armazenamento do Azure contém dados de entrada/saída de atividades no pipeline. Um serviço de computação, como o cluster Hadoop do HDInsight, processa/transforma dados.

    Neste tutorial, você criará dois serviços vinculados: **Armazenamento do Azure** e **Azure HDInsight**. O serviço vinculado do armazenamento do Azure vincula uma conta de armazenamento do Azure que contém os dados de entrada/saída ao data factory. O serviço vinculado do Azure HDInsight vincula um cluster do Azure HDInsight que é usado para transformar dados para o data factory. 
3. Criar **conjuntos de dados** de entrada e de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade.

    Neste tutorial, os conjuntos de dados de entrada e saída especificam os locais de entrada e de saída do armazenamento de BLOBs do Azure. O serviço vinculado do armazenamento do Azure especifica qual conta de armazenamento do Azure é usada. Um conjunto de dados de entrada especifica onde os arquivos de entrada estão localizados e um conjunto de dados de saída especifica onde os arquivos de saída são colocados. 


Consulte [introdução ao Azure data Factory](data-factory-introduction.md) artigo para obter uma visão geral detalhada do Azure data Factory.
  
Este é o **modo de exibição de diagrama** do data Factory de exemplo que você cria neste tutorial. O **MyFirstPipeline** tem uma atividade do tipo Hive que consome o conjunto de dados **AzureBlobInput** como uma entrada e produz o **AzureBlobOutput** como uma saída. 

![Vista de diagrama no tutorial do Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Neste tutorial, a pasta **inputdata** do contêiner de BLOBs do Azure **adfgetstarted** contém um arquivo chamado Input. log. Este arquivo de log tem entradas de três meses: Janeiro, fevereiro e março de 2016. Seguem-se as linhas de exemplo para cada mês no ficheiro de entrada. 

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

Nas linhas de exemplo mostradas acima, a primeira (com 2016-01-01) é gravada no arquivo 000000_0 na pasta month = 1. Do mesmo modo, a segunda é escrita no ficheiro na pasta month=2 e a terceira é escrita no ficheiro na pasta month=3.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

1. **Subscrição do Azure** - Se não tiver uma subscrição do Azure, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Veja o artigo [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) sobre como poderá obter uma conta de avaliação gratuita.
2. **Armazenamento do Azure** – Utilize uma conta de armazenamento do Azure para armazenar os dados deste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md). Depois de criar a conta de armazenamento, anote o **nome da conta** e a **chave de acesso**. Veja [Ver, copiar e voltar a gerar chaves de acesso ao armazenamento](../../storage/common/storage-account-manage.md#access-keys).
3. Baixe e examine o arquivo de consulta do hive (**HQL**) localizado [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql)em:. Essa consulta transforma os dados de entrada para produzir dados de saída. 
4. Baixe e examine o arquivo de entrada de exemplo (**Input. log**) localizado em:[https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Crie um contêiner de blob chamado **adfgetstarted** no armazenamento de BLOBs do Azure. 
6. Carregue o arquivo **partitionweblogs. HQL** na pasta de **script** no contêiner **adfgetstarted** . Use ferramentas como [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com/). 
7. Carregue o arquivo **Input. log** na pasta **inputdata** no contêiner **adfgetstarted** . 

Depois de concluir os pré-requisitos, selecione uma das seguintes ferramentas/SDKs para fazer o tutorial: 

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

O Visual Studio fornece uma maneira de GUI da criação de fábricas de dados. Enquanto, o PowerShell, o modelo do Resource Manager e as opções da API REST fornecem uma maneira de criação de scripts/programação de criar suas fábricas de dados.

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Não copia dados de um de dados de origem para um arquivo de dados de destino. Para obter um tutorial sobre como copiar dados usando Azure data Factory, consulte [o tutorial: Copiar dados do armazenamento de BLOBs para](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)o banco de dados SQL.
> 
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 





  
