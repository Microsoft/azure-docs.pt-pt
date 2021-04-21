---
title: 'Quickstart: Get start - crie um espaço de trabalho synapse'
description: Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL dedicada e uma piscina Apache Spark sem servidor.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 4b7251be220c012ca51970863ac2eed55d46d711
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751153"
---
# <a name="creating-a-synapse-workspace"></a>Criação de um espaço de trabalho sinapse

Neste tutorial, você aprenderá a criar um espaço de trabalho Synapse, uma piscina SQL dedicada e uma piscina Apache Spark sem servidor. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste tutorial, precisa de ter acesso a um grupo de recursos para o qual lhe é atribuída a função **Proprietário.** Crie o espaço de trabalho synapse neste grupo de recursos.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho sinapse no portal Azure

### <a name="start-the-process"></a>Inicie o processo
1. Abra o [portal Azure,](https://portal.azure.com)na barra de pesquisa entre em **Sinapse** sem bater na entrada.
1. Nos resultados da pesquisa, em **Serviços,** selecione **Azure Synapse Analytics**.
1. **Selecione Adicionar** para criar um espaço de trabalho.

## <a name="basics-tab--project-details"></a>Detalhes básicos do separador > projeto
Preencha os seguintes campos:

1. **Subscrição** - Escolha qualquer subscrição.
1. **Grupo de recursos** - Utilize qualquer grupo de recursos.
1. **Grupo de Recursos Geridos** - Deixe este em branco.

## <a name="basics-tab--workspace-details"></a>Detalhes básicos do separador > do espaço de trabalho
Preencha os seguintes campos:

1. **Nome do espaço de** trabalho - Escolha qualquer nome globalmente único. Neste tutorial, usaremos **o meu espaço de trabalho.**
1. **Região** - Escolha a região onde colocou as aplicações/serviços do seu cliente (por exemplo, Azure VM, Power BI, Azure Analysis Service) e armazenamentos que contenham dados (por exemplo, armazenamento analítico Azure Data Lake, armazenamento analítico Azure Cosmos DB).

> [!NOTE]
> Um espaço de trabalho que não esteja equipado com as aplicações do cliente ou armazenamento pode ser a causa principal de muitos problemas de desempenho. Se você dados ou os clientes forem colocados em várias regiões, você pode criar espaços de trabalho separados em diferentes regiões com os seus dados e clientes.

Em **Data Data Desemar o Grupo de Armazenamento do Lago 2:**

1. Por **nome de conta,** selecione **Create New** e nomeie a nova conta de armazenamento **contosolake** ou similar como o nome deve ser único.
1. Por **nome do sistema de ficheiros,** selecione Criar **Novo** e nomeie **os utilizadores**. Isto criará um recipiente de armazenamento chamado **utilizadores.** O espaço de trabalho utilizará esta conta de armazenamento como a conta de armazenamento "primária" para as tabelas Spark e os registos de aplicações Spark.
1. Verifique a função "Atribua-me a função de contribuinte de dados de armazenamento na conta Gen2 de armazenamento de dados". 

## <a name="completing-the-process"></a>Completar o processo
Selecione **Rever + criar** > **Criar**. O seu espaço de trabalho está pronto em poucos minutos.

> [!NOTE]
> Para permitir funcionalidades de espaço de trabalho a partir de uma piscina SQL dedicada existente (anteriormente SQL DW) consulte como [ativar um espaço de trabalho para a sua piscina DE SQL dedicada (anteriormente SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Open Synapse Studio

Depois de criar o seu espaço de trabalho Azure Synapse, tem duas formas de abrir o Synapse Studio:

* Abra o seu espaço de trabalho sinapse no [portal Azure,](https://portal.azure.com)na secção **visão geral** do espaço de trabalho synapse, selecione **Open** na caixa Open Synapse Studio.
* Vá ao `https://web.azuresynapse.net` e inscreva-se no seu espaço de trabalho.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Coloque os dados da amostra na conta de armazenamento primário
Vamos usar um pequeno conjunto de dados de amostras de 100K linha de dados da NYX Taxi Cab para muitos exemplos neste guia de arranque. Começamos por colocá-lo na conta de armazenamento primário que criou para o espaço de trabalho.

* Descarregue este ficheiro para o seu computador: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* No Synapse Studio, navegue para o Data Hub. 
* Selecione **Linked**.
* Na categoria **Azure Data Lake Storage Gen2** você verá um item com um nome como **myworkspace (Primary - contosolake )**.
* Selecione os **utilizadores nomeados para** o recipiente (Primário) .
* Selecione **Upload** e selecione o `NYCTripSmall.parquet` ficheiro que descarregou.

Um dos ficheiros parquet é carregado, está disponível através de dois URIs equivalentes:
* `https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet` 
* `abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet`

Nos exemplos que se seguem neste tutorial, certifique-se de substituir o **contosolake** na UI pelo nome da conta de armazenamento primário que selecionou para o seu espaço de trabalho.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise usando uma piscina SQL sem servidor](get-started-analyze-sql-on-demand.md)
