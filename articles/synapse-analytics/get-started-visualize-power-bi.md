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
ms.date: 12/31/2020
ms.openlocfilehash: 952d69cccff86d1a0119391c400a40908c62ed69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98208969"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI

Neste tutorial, você aprenderá a criar um espaço de trabalho Power BI, ligar o seu espaço de trabalho Azure Synapse e criar um conjunto de dados Power BI que utiliza dados no seu espaço de trabalho Azure Synapse. 

> [!NOTE]
> Para completar este tutorial, [instale o Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="overview"></a>Descrição geral

A partir dos dados do Táxi de NYC, criámos conjuntos de dados agregados em duas tabelas:
- **nyctaxi.contagem de passageiros**
- **SQLDB1.dbo.PassengerCountStats**

Pode ligar um espaço de trabalho Power BI ao seu espaço de trabalho Azure Synapse. Esta capacidade permite-lhe obter facilmente dados no seu espaço de trabalho Power BI. Pode editar os seus relatórios power bi diretamente no seu espaço de trabalho Azure Synapse. 

### <a name="create-a-power-bi-workspace"></a>Criar um espaço de trabalho Power BI

1. Inscreva-se na [powerbi.microsoft.com.](https://powerbi.microsoft.com/)
1. Clique em **Espaços de Trabalho** e, em seguida, selecione Criar um espaço de **trabalho.** Crie um novo espaço de trabalho Power BI chamado **NYCTaxiWorkspace1** ou similar, uma vez que este nome deve ser único.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Ligue o seu espaço de trabalho Azure Synapse ao seu novo espaço de trabalho Power BI

1. No Synapse Studio, vá a **Manage**  >  **Linked Services**.
1. Selecione **New**  >  **Connect to Power BI**.
1. Definir **Nome** para **NYCTaxiWorkspace1**.
1. Deslo **sente o nome do espaço de** trabalho do Power BI que criou acima, semelhante ao **NYCTaxiWorkspace1**.
1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Crie um conjunto de dados Power BI que utilize dados no seu espaço de trabalho Azure Synapse

1. No Synapse Studio, vá ao **Develop**  >  **Power BI.**
1. Vá a conjuntos de dados do BI de energia **NYCTaxiWorkspace1**  >  e selecione o conjunto de **dados** do New Power **BI**. Clique em **Iniciar**.
1. Selecione a fonte de dados **SQLPOOL1,** clique em **Continuar**.
1. Clique em **Baixar** para descarregar o ficheiro .pbids para o seu ficheiro **NYCTaxiWorkspace1SQLPOOL1.pbids.** Clique em **Continue** (Continuar).
1. Abra o ficheiro **.pbids** descarregado. O Power BI Desktop abre e liga-se automaticamente ao **SQLDB1** no seu espaço de trabalho Azure Synapse.
1. Se vir uma caixa de diálogo aparecer chamada **base de dados SQL Server**:
    1. Selecione **a conta Microsoft**.
    1. Selecione **Iniciar sôms** e inscreva-se na sua conta.
    1. Selecione **Ligar**.
1. Depois de abrir a caixa de diálogo **Navigator,** verifique a tabela **PassengerCountStats** e selecione **Load**.
1. Depois de aparecer a caixa de diálogo **de definições de ligação,** selecione **DirectQuery**  >  **OK**.
1. Selecione o botão **Relatório** no lado esquerdo.
1. Em **Visualizações,** clique no ícone do gráfico de linha para adicionar um **gráfico de linha** ao seu relatório.
    1. Under **Fields**, arraste a coluna **PassengerCount** para o Eixo **visualizações.**  >  
    1. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Valores de Visualização**  >  .
1. No **separador Casa,** **selecione Publicar**.
1. Selecione **Guardar** para guardar as alterações.
1. Escolha o nome de ficheiro **PassengerAnalysis.pbix** e, em seguida, **selecione Save**.
1. Na janela **Publicar para Power BI,** em **Selecione um destino,** escolha o seu **NYCTaxiWorkspace1** e, em seguida, clique em **Select**.
1. Espere que a publicação termine. 

### <a name="configure-authentication-for-your-dataset"></a>Configure a autenticação para o seu conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e **inscreva-se**.
1. No lado esquerdo, em **Workspaces,** selecione o espaço de trabalho **NYCTaxiWorkspace1.**
1. Dentro desse espaço de trabalho, localize um conjunto de dados chamado **Análise de Passageiros** e um relatório chamado **Análise de Passageiros.**
1. Passe sobre o conjunto de **dados PassengerAnalysis,** selecione o botão elipse (...) e, em seguida, selecione **Definições**.
1. Nas **credenciais de origem de dados**, clique em **Editar,** descreva o **método de autenticação** para **OAuth2** e, em seguida, selecione **Iniciar sessão .**

### <a name="edit-a-report-in-synapse-studio"></a>Editar uma reportagem no Synapse Studio

1. Volte para o Synapse Studio e selecione **Close and refresh**.
1. Vá ao centro **de Desenvolvimento.**
1. À direita da camada **Power BI,** elipse (...) botão (...) e clique em **atualizar** para refrescar o nó **de relatórios power BI.**
1. Sob **o Power BI** deve ver:
    * Nos conjuntos de dados do BI de energia **NYCTaxiWorkspace1,** um novo conjunto de  >  **dados** chamado **PassengerAnalysis**.
    * Ao abrigo dos relatórios **NYCTaxiWorkspace1**  >  **Power BI**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis.** O relatório abre e pode editá-lo diretamente no Synapse Studio.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorizar](get-started-monitor.md)
                                 

