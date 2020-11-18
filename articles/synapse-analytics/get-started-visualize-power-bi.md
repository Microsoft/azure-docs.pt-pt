---
title: 'Tutorial: Começar com a Azure Synapse Analytics - visualizar dados do espaço de trabalho com Power BI'
description: Neste tutorial, você aprenderá a criar um espaço de trabalho Power BI, ligar o seu espaço de trabalho Azure Synapse e criar um conjunto de dados Power BI que utiliza dados no espaço de trabalho Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b72d083216b7cd0ae7a588bfd721d8981829bfc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844374"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

Neste tutorial, você aprenderá a criar um espaço de trabalho Power BI, ligar o seu espaço de trabalho Azure Synapse e criar um conjunto de dados Power BI que utiliza dados no seu espaço de trabalho Azure Synapse. 

## <a name="overview"></a>Descrição Geral

A partir dos dados do Táxi de NYC, criámos conjuntos de dados agregados em duas tabelas:
- **nyctaxi.contagem de passageiros**
- **SQLDB1.dbo.PassengerCountStats**

Pode ligar um espaço de trabalho Power BI ao seu espaço de trabalho Azure Synapse. Esta capacidade permite-lhe obter facilmente dados no seu espaço de trabalho Power BI. Pode editar os seus relatórios power bi diretamente no seu espaço de trabalho Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Criar um espaço de trabalho Power BI

1. Inscreva-se na [powerbi.microsoft.com.](https://powerbi.microsoft.com/)
1. Criar um novo espaço de trabalho Power BI chamado **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Ligue o seu espaço de trabalho Azure Synapse ao seu novo espaço de trabalho Power BI

1. No Synapse Studio, vá a **Manage**  >  **Linked Services**.
1. Selecione **Nova**  >  **Ligação ao Power BI**
1. Definir **Nome** para **NYCTaxiWorkspace1**
1. Definir **nome do espaço de** trabalho para **NYCTaxiWorkspace1**
1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Crie um conjunto de dados Power BI que utilize dados no seu espaço de trabalho Azure Synapse

1. No Synapse Studio, vá ao **Develop**  >  **Power BI.**
1. Vá a conjuntos de dados do BI de energia **NYCTaxiWorkspace1**  >  e selecione o conjunto de **dados** do New Power **BI**.
1. Passe sobre a base de dados **SQLDB1** e selecione **Download .pbids file**.
1. Abra o ficheiro **.pbids** descarregado. O ambiente de trabalho power BI abre e liga-se automaticamente ao **SQLDB1** no seu espaço de trabalho Azure Synapse.
1. Se vir uma caixa de diálogo aparecer chamada **base de dados de servidor SQL:**
    1. Selecione **a conta Microsoft**.
    1. Selecione **Iniciar sôms** e inscreva-se na sua conta.
    1. Selecione **Connect** (Ligar).
1. Depois de abrir a caixa de diálogo **Navigator,** verifique a tabela **PassengerCountStats** e selecione **Load**.
1. Depois de aparecer a caixa de diálogo **de definições de ligação,** selecione **DirectQuery**  >  **OK**.
1. Selecione o botão **Relatório** no lado esquerdo.
1. Adicione **gráfico de linha** ao seu relatório.
    1. Arraste a coluna **PassengerCount** para o Eixo **visualizações.**  >  **Axis**
    1. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Valores de Visualização**  >  **Values**.
1. No **separador Casa,** **selecione Publicar**.
1. Selecione **Guardar** para guardar as alterações.
1. Escolha o nome de ficheiro **PassengerAnalysis.pbix** e, em seguida, **selecione Save**.
1. Em **Selecionar um destino,** escolha **NYCTaxiWorkspace1** e, em seguida, clique em **Select**.
1. Espere que a publicação termine.

### <a name="configure-authentication-for-your-dataset"></a>Configure a autenticação para o seu conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e **inscreva-se**.
1. No lado esquerdo, em **Workspaces,** selecione o espaço de trabalho **NYCTaxiWorkspace1.**
1. Dentro desse espaço de trabalho, localize um conjunto de dados chamado **Análise de Passageiros** e um relatório chamado **Análise de Passageiros.**
1. Passe sobre o conjunto de **dados PassengerAnalysis,** selecione o botão elipse (...) e, em seguida, selecione **Definições**.
1. Nas **credenciais de origem de dados**, descreva o **método de autenticação** para **OAuth2** e, em seguida, selecione **Iniciar sposição em**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar uma reportagem no Synapse Studio

1. Volte para o Synapse Studio e selecione **Close and refresh**.
1. Vá ao centro **de Desenvolvimento.**
1. Passe sobre **o Power BI** e selecione o nó de **relatórios Power BI.**
1. Sob **o Power BI** deve ver:
    * Sob os conjuntos de dados **do NYCTaxiWorkspace1** Power BI , um novo conjunto de  >  **dados** chamado **PassengerAnalysis**.
    * Ao abrigo dos relatórios **NYCTaxiWorkspace1**  >  **Power BI**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis.** O relatório abre e pode editá-lo diretamente no Synapse Studio.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorizar](get-started-monitor.md)
                                 

