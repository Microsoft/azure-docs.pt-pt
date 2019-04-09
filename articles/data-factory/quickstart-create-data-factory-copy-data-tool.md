---
title: Copiar dados utilizando a ferramenta Copiar Dados do Azure | Microsoft Docs
description: Crie uma fábrica de dados do Azure e utilize a ferramenta Copiar Dados para copiar dados de uma localização no armazenamento de Blobs do Azure para outra localização.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: yexu
ms.openlocfilehash: a77041b063f043c065df9a2955e9d07b5692f592
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256265"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Início rápido: Utilizar a ferramenta Copiar Dados para copiar dados

> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-copy-data-tool.md)

Neste início rápido, irá utilizar o portal do Azure para criar uma fábrica de dados. Utilize a ferramenta Copiar Dados para criar um pipeline que copia dados de uma pasta num armazenamento de Blobs do Azure para outra pasta. 

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de começar o início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Selecione **Novo** no menu da esquerda, selecione **Dados + Análise** e, em seguida, selecione **Data Factory**. 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** em **Nome**. 
      
   ![Página "Nova fábrica de dados"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, **&lt;oseunome&gt;ADFTutorialDataFactory**) e tente criá-la novamente. Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
1. Em **Subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
1. Em **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
   Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados. 

   A lista mostra apenas as localizações suportadas pelo Data Factory e onde serão armazenados os seus metadados do Azure Data Factory. Tenha em atenção que os arquivos de dados associados (como o Armazenamento do Microsoft Azure e a Base de Dados SQL do Azure) e as computações (como o Azure HDInsight) utilizados pelo Data Factory podem ser executados noutras regiões.

1. Selecione **Criar**.
1. Após concluir a criação, verá a página **Data Factory**. Selecione o mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte.
   
   ![Home page da fábrica de dados, com o mosaico "Criar e Monitorizar"](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Iniciar a ferramenta Copiar Dados

1. Na página **Vamos começar**, selecione o mosaico **Copiar Dados** para iniciar a ferramenta Copiar Dados. 

   ![Mosaico "Copiar Dados"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)

1. Na página **Propriedades** da ferramenta Copiar Dados, pode especificar um nome para o pipeline e a respetiva descrição e, em seguida, selecionar **Seguinte**. 

   ![Página "Propriedades"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique em **+ Criar nova ligação** para adicionar uma ligação.

    ![Página "Arquivo de dados de origem"](./media/quickstart-create-data-factory-copy-data-tool/new-source-linked-service.png)

    b. Selecione **Armazenamento de Blobs do Azure** na galeria e, em seguida, selecione **Seguinte**.

    ![Selecione o armazenamento de blobs na galeria](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na página **Especificar a conta de armazenamento de Blobs do Azure**, selecione a sua conta de armazenamento da lista **Nome da conta de armazenamento** e, em seguida, selecione **Concluir**. 

   ![Configurar a conta de armazenamento de Blobs do Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

   d. Selecione o serviço ligado criado recentemente como origem e, em seguida, clique em **Seguinte**.

   ![Selecionar serviço ligado de origem](./media/quickstart-create-data-factory-copy-data-tool/select-source-linked-service.png)


1. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:

   a. Clique em **Navegar** para navegar para a pasta **adftutorial/input**, selecione o ficheiro **emp.txt** e clique em **Escolher**. 

   ![Página "Escolher o ficheiro ou pasta de entrada"](./media/quickstart-create-data-factory-copy-data-tool/configure-source-path.png)

   d. Verifique a opção **Cópia binária** para copiar o ficheiro conforme está e, em seguida, selecione **Seguinte**. 

   ![Página "Escolher o ficheiro ou pasta de entrada"](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na página **Arquivo de dados de destino**, selecione o serviço ligado **Armazenamento de Blobs do Azure** que acabou de criar e, em seguida, selecione **Seguinte**. 

   ![Página "Arquivo de dados de destino"](./media/quickstart-create-data-factory-copy-data-tool/select-sink-linked-service.png)

1. Na página **Escolher ficheiro ou pasta de saída**, introduza **adftutorial/output**, no caminho da pasta e, em seguida, clique em **Seguinte**. 

   ![Página "Escolher ficheiro ou pasta de saída"](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na página **Definições**, selecione **Seguinte** para utilizar as configurações predefinidas. 

1. Reveja todas as definições na página **Resumo** e selecione **Seguinte**. 

    ![Página "Resumo"](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)

1. Na página **Implementação concluída**, selecione **Monitorização** para monitorizar o pipeline que criou. 

    ![Página "Implementação concluída"](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. A aplicação muda para o separador **Monitorização**. Verá o estado do pipeline neste separador. Selecione **Atualizar** para atualizar a lista. 
    
    ![Monitorizar execução do pipeline](./media/quickstart-create-data-factory-copy-data-tool/pipeline-monitoring.png)

1. Selecione a ligação **Ver Execuções de Atividades** na coluna **Ações**. O pipeline apenas tem uma atividade do tipo **Cópia**. 

    ![Monitorizar execução de atividade](./media/quickstart-create-data-factory-copy-data-tool/activity-monitoring.png)
    
1. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (imagem de óculos), na coluna **Ações**. Para obter os detalhes das propriedades, veja [Copy Activity overview](copy-activity-overview.md) (Descrição geral da Atividade Copy).

    ![Detalhes da operação de cópia](./media/quickstart-create-data-factory-copy-data-tool/activity-execution-details.png)

1. Verifique se é criado um ficheiro **emp.txt** na pasta **saída** do contentor **adftutorial**. Se a pasta de saída não existir, o serviço Data Factory cria-a automaticamente. 

1. Mude para o separador **Criar** acima do **Monitor** no painel esquerdo, para que possa editar os serviços ligados, conjuntos de dados e pipelines. Para saber mais sobre a edição dos mesmos na IU do Data Factory, consulte [Criar uma fábrica de dados através do portal do Azure](quickstart-create-data-factory-portal.md).

## <a name="next-steps"></a>Passos Seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. Para saber como utilizar o Data Factory em mais cenários, aceda aos [tutoriais](tutorial-copy-data-portal.md). 