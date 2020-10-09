---
title: 'Tutorial: Criar um pipeline com o Assistente de Cópia '
description: Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Assistente de Cópia suportado pelo Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d1b17a3e4556f6a963f3ecacd31472ce3f75b0fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85248552"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Tutorial: Criar um pipeline com Atividade de Cópia com o Assistente de Cópia do Data Factory
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 


Este tutorial mostra-lhe como utilizar o **Copy Wizard** para copiar dados de um armazenamento de bolhas Azure para Azure SQL Database. 

O Assistente de Cópia do Azure Data Factorypermite criar rapidamente um pipeline de dados que copia dados de um arquivo de dados de origem suportado para um arquivo de dados de destino suportado. Consequentemente, recomendamos que o utilize como primeiro passo para criar um pipeline de exemplo para o cenário de movimento de dados. Para obter uma lista dos arquivos de dados suportados como origens e destinos, veja [Supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Arquivos de dados suportados).  

Este tutorial mostra-lhe como criar uma fábrica de dados do Azure, iniciar o Assistente de Cópia e passar por uma série de passos para fornecer os detalhes sobre o seu cenário de ingestão/movimento de dados. Quando termina os passos no assistente, o assistente cria automaticamente um oleoduto com uma Atividade de Cópia para copiar dados de um armazenamento de bolhas Azure para Azure SQL Database. Para obter mais informações sobre a Atividade de Cópia, veja [Data movement activities](data-factory-data-movement-activities.md) (Atividades de movimento de dados).

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir este tutorial, conclua os pré-requisitos listados na [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, irá utilizar o Portal do Azure para criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactory**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso** no canto superior esquerdo, clique em **Dados + análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. No painel **Nova fábrica de dados**:
   
   1. Introduza **ADFTutorialDataFactory** como **nome**.
       O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro `Data factory name “ADFTutorialDataFactory” is not available`, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactoryAAAAMMDD) e tente criá-la novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.  
      
       ![Nome do Data Factory não disponível](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Selecione a sua **subscrição Azure**.
   3. No Grupo de Recursos, siga um destes passos: 
      
      - Selecione **Utilizar existente** para selecionar um grupo de recursos já existente.
      - Selecione **Criar novo** para introduzir um nome para um grupo de recursos.
          
        Alguns dos passos deste tutorial pressupõe que utiliza o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../../azure-resource-manager/management/overview.md).
   4. Selecione uma **localização** para a fábrica de dados.
   5. Selecione a caixa de verificação **Afixar ao dashboard**, na parte inferior do painel.  
   6. Clique em **Criar**.
      
       ![Painel Nova fábrica de dados](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Após a criação estar concluída, vê a lâmina da **Fábrica de Dados** como mostrado na seguinte imagem:
   
   ![Home page da fábrica de dados](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Inicie o Assistente de Cópia
1. No painel do Data Factory, clique em **Copiar dados** para iniciar o **Assistente de Cópia**. 
   
   > [!NOTE]
   > Se vir que o browser bloqueia enquanto estiver a "A autorizar…", desative/desmarque a definição **Bloquear cookies de terceiros e dados do site** nas definições do browser (ou) mantenha-a ativada e crie uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar novamente o assistente.
2. Na página **Propriedades**:
   
   1. Introduza **CopyFromBlobToAzureSql** para **Nome da tarefa**
   2. Introduza **Descrição** (opcional).
   3. Altere a **Hora de data de início** e a **Hora de data de fim**, para que a data de fim esteja definida como a data de hoje e a de início como cinco dias antes.  
   4. Clique em **Seguinte**.  
      
      ![Página Ferramenta Copiar – Propriedades](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Na página **Arquivo de dados de origem**, clique no mosaico **Blob Storage do Azure**. Utilize esta página para especificar o arquivo de dados de origem para a tarefa de cópia. 
   
    ![Página Ferramenta Copiar – Arquivo de dados de origem](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de blobs do Azure**:
   
   1. Introduza **AzureStorageLinkedService** para **Nome do serviço ligado**.
   2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de contas**.
   3. Selecione a sua **subscrição Azure**.  
   4. Selecione uma **Conta de armazenamento do Azure** na lista das contas de armazenamento do Azure disponíveis na subscrição selecionada. Também pode optar por introduzir manualmente as definições da conta de armazenamento selecionando a opção **Introduzir manualmente** para **Método de seleção de contas** e, em seguida, clicar em **Seguinte**. 
      
      ![Ferramenta Copiar – Especificar a conta de armazenamento de blobs do Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. Na página **Escolher o ficheiro ou pasta de entrada**:
   
   1. Faça duplo clique em **adftutorial** (pasta).
   2. Selecione **emp.txt** e clique em **Escolher**
      
      ![Ferramenta Copiar – Escolha o ficheiro ou pasta de entrada](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. Na página **Escolher o ficheiro ou pasta de entrada**, clique em **Seguinte**. Não selecione **Cópia binária**. 
   
    ![Ferramenta Copiar – Escolha o ficheiro ou pasta de entrada](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. Na página **Definições do formato de ficheiro**, pode ver os delimitadores e o esquema que é detetado automaticamente pelo assistente ao analisar o ficheiro. Também pode introduzir os delimitadores manualmente para o Assistente de Cópia parar a deteção automática ou para substituir. Clique em **Seguinte** depois de rever os delimitadores e pré-visualizar os dados. 
   
    ![Ferramenta Copiar – Definições do formato de ficheiro](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Na página de Arquivo de dados de destino, selecione **Base de Dados SQL do Azure** e clique em **Seguinte**.
   
    ![Ferramenta Copiar - escolher arquivo de destino](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. Na página **Especificar a base de dados SQL do Azure**:
   
   1. Introduza **AzureSqlLinkedService** no campo **Nome da ligação**.
   2. Confirme que a opção **A partir de subscrições do Azure** está selecionada para o **Método de seleção de servidor/base de dados**.
   3. Selecione a sua **subscrição Azure**.  
   4. Selecione **Nome do servidor** e **Base de dados**.
   5. Introduza o **Nome de utilizador** e a **Palavra-passe**.
   6. Clique em **Seguinte**.  
      
      ![Ferramenta de Cópia - especifique a base de dados Azure SQL](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. Na página **Mapeamento de tabelas**, selecione **emp** para o campo **Destino** na lista pendente, clique na **seta para baixo** (opcional) para ver o esquema e pré-visualizar os dados.
    
     ![Ferramenta Copiar – Mapeamento da tabelas](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. Na página **Mapeamento de esquemas**, clique em **Seguinte**.
    
    ![Ferramenta Copiar - mapeamento de esquema](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. Na página **Definições de desempenho**, clique em **Seguinte**. 
    
    ![Ferramenta Copiar - definições de desempenho](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Reveja as informações na página **Resumo** e clique em **Concluir**. O assistente cria dois serviços ligados, dois conjuntos de dados (entrada e saída) e um pipeline na fábrica de dados (a partir da qual iniciou o Assistente de Cópia). 
    
    ![Ferramenta Copiar - definições de desempenho](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Iniciar o Monitor e Gerir a Aplicação
1. Na página **Implementação**, clique na ligação: `Click here to monitor copy pipeline`.
   
   ![Ferramenta Copiar – Implementação concluída com êxito](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. A aplicação de monitorização é iniciada num separador separado no browser.   
   
   ![Aplicação de Monitorização](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Para ver o estado mais recente dos setores por hora, clique no botão **Atualizar** na lista **JANELAS DE ATIVIDADE**, na parte inferior. Vê cinco janelas de atividade para cinco dias entre as horas de início e de fim do pipeline. A lista não é atualizada automaticamente, pelo que pode ter de clicar em Atualizar algumas vezes antes de ver todas as janelas de atividade no estado Pronto. 
4. Selecione uma janela de atividade na lista. Veja os detalhes sobre a mesma no **Explorador de Janelas de Atividade** à direita.

    ![Detalhes da janela de atividade](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Tenha em atenção que as datas 11, 12, 13, 14 e 15 são apresentadas a verde, o que significa que os setores de saída diários para essas datas já foram produzidos. Também pode ver esta codificação por cores no pipeline e o conjunto de dados de saída no modo de diagrama. No passo anterior, tenha em atenção que dois setores já foram produzidos, um deles está a ser processado atualmente e os outros dois estão a aguardar o processamento (com base na codificação por cores). 

    Para obter mais informações sobre como utilizar esta aplicação, veja o artigo [Monitorizar e gerir o pipeline com a Aplicação de Monitorização](data-factory-monitor-manage-app.md).

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, utilizou o armazenamento de blob Azure como uma loja de dados de origem e a Base de Dados Azure SQL como uma loja de dados de destino numa operação de cópia. A tabela seguinte disponibiliza uma lista dos arquivos de dados que a atividade de cópia suporta como origens e destinos: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para obter detalhes sobre os campos/propriedades que vê no assistente de cópia para um arquivo de dados, clique na ligação do arquivo de dados na tabela. 