---
title: 'Tutorial de fábrica de dados: Primeiro pipeline de dados | Documentos da Microsoft'
description: Este tutorial do Azure Data Factory mostra-lhe como criar e agendar uma fábrica de dados que processa dados através de script do Hive num cluster do Hadoop.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d9d9e68b7e74ba7725e97162d01e1a35314fdd0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544961"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Tutorial: Crie seu primeiro pipeline para transformar dados com o cluster do Hadoop
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [início rápido: Criar uma fábrica de dados com o Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, vai criar a primeira fábrica de dados do Azure com um pipeline de dados. O pipeline transforma dados de entrada ao executar o script do Hive num cluster do Azure HDInsight (Hadoop) para produzir dados de saída.  

Este artigo fornece a visão geral e pré-requisitos para o tutorial. Depois de concluir os pré-requisitos, pode fazer o tutorial, utilizando uma das ferramentas/SDKs seguintes: Portal do Azure, Visual Studio, PowerShell, o modelo do Resource Manager, REST API. Selecione uma das opções na lista suspensa no início (ou) ligações no final deste artigo para fazer o tutorial com uma destas opções.    

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, vai executar os seguintes passos:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines de dados que movimentam e transformam dados. 

    Neste tutorial, vai criar um pipeline na fábrica de dados. 
2. Criar uma **pipeline**. Um pipeline pode conter uma ou mais atividades (exemplos: Atividade de cópia, atividade do Hive do HDInsight). Este exemplo utiliza a atividade de Hive do HDInsight que executa um script do Hive num cluster HDInsight Hadoop. O script cria primeiro uma tabela que faz referência aos dados de registo de web não processados armazenados no armazenamento de Blobs do Azure e, em seguida, particiona os dados não processados por ano e mês.

    Neste tutorial, o pipeline utiliza a atividade do Hive para transformar dados executando uma consulta do Hive num cluster do Azure HDInsight Hadoop. 
3. Crie **serviços ligados**. Cria um serviço ligado para ligar um arquivo de dados ou um serviço de computação à fábrica de dados. Um arquivo de dados como o Armazenamento do Azure contém dados de entrada/saída de atividades no pipeline. Um serviço de computação, como cluster de Hadoop do HDInsight processa/transforma dados.

    Neste tutorial, vai criar dois serviços ligados: **O armazenamento do Azure** e **do Azure HDInsight**. O armazenamento do Azure serviço ligado liga uma conta de armazenamento do Azure que contém os dados de entrada/saída à fábrica de dados. O Azure HDInsight serviço ligado liga um cluster do HDInsight do Azure que é utilizado para transformar dados à fábrica de dados. 
3. Criar **conjuntos de dados** de entrada e de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade.

    Neste tutorial, os conjuntos de dados de entrada e saídos especificar localizações de dados de entrada e saídas no armazenamento de Blobs do Azure. O serviço ligado do armazenamento do Azure Especifica o que a conta de armazenamento do Azure é utilizado. Um conjunto de dados de entrada especifica onde se encontram os ficheiros de entrada e um conjunto de dados de saída especifica onde os ficheiros de saída são colocados. 


Ver [introdução ao Azure Data Factory](data-factory-introduction.md) artigo para uma visão geral detalhada do Azure Data Factory.
  
Aqui está o **vista de diagrama** da fábrica de dados de exemplo que criar neste tutorial. **MyFirstPipeline** tem uma atividade do tipo de Hive que consome **AzureBlobInput** conjunto de dados como entrada e produz **AzureBlobOutput** conjunto de dados como uma saída. 

![Vista de diagrama no tutorial do Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Neste tutorial, **inputdata** pasta da **adfgetstarted** contentor de Blobs do Azure contém um ficheiro com o nome Input. log. Este ficheiro de registo tem entradas de três meses: Janeiro, Fevereiro e Março de 2016. Seguem-se as linhas de exemplo para cada mês no ficheiro de entrada. 

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

De linhas de exemplo mostradas acima, aquela primeira (com 2016-01-01) é escrita no ficheiro 000000_0 na pasta Month=1. Do mesmo modo, a segunda é escrita no ficheiro na pasta month=2 e a terceira é escrita no ficheiro na pasta month=3.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

1. **Subscrição do Azure** - Se não tiver uma subscrição do Azure, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Veja o artigo [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) sobre como poderá obter uma conta de avaliação gratuita.
2. **Armazenamento do Azure** – Utilize uma conta de armazenamento do Azure para armazenar os dados deste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md). Depois de ter criado a conta de armazenamento, tome nota da **nome da conta** e **chave de acesso**. Veja [Ver, copiar e voltar a gerar chaves de acesso ao armazenamento](../../storage/common/storage-account-manage.md#access-keys).
3. Transfira e reveja o ficheiro de consulta do Hive (**HQL**) localizado em: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Esta consulta transforma dados de entrada para produzir dados de saída. 
4. Transfira e reveja o ficheiro de entrada de exemplo (**Input. log**) localizado em: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Criar um contentor de BLOBs denominado **adfgetstarted** no armazenamento de Blobs do Azure. 
6. Carregue **partitionweblogs. hql** do ficheiro para o **script** pasta no **adfgetstarted** contentor. Utilize ferramentas como [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/). 
7. Carregue **Input. log** do ficheiro para o **inputdata** pasta no **adfgetstarted** contentor. 

Depois de concluir os pré-requisitos, selecione uma das seguintes ferramentas/SDKs para fazer o tutorial: 

- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Portal do Azure e o Visual Studio fornecem a forma de GUI da criação de fábricas de dados. Ao passo que, opções de PowerShell, o modelo do Resource Manager e REST API fornece a forma de criação de scripts/programação da criação de fábricas de dados.

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Não copia dados de um de dados de origem para um arquivo de dados de destino. Para obter um tutorial sobre como copiar dados com o Azure Data Factory, veja [Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 





  
