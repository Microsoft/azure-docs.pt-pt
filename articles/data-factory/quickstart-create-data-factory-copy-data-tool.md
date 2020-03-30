---
title: Copiar dados utilizando a ferramenta Dados de Cópia Azure
description: Crie uma fábrica de dados do Azure e utilize a ferramenta Copiar Dados para copiar dados de uma localização no armazenamento de Blobs do Azure para outra localização.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 03/18/2020
ms.openlocfilehash: 26169755fbe252a4be2626dae50d40c005c7c6db
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130938"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Quickstart: Utilize a ferramenta Copy Data para copiar dados

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-copy-data-tool.md)

Neste início rápido, irá utilizar o portal do Azure para criar uma fábrica de dados. Utilize a ferramenta Copiar Dados para criar um pipeline que copia dados de uma pasta num armazenamento de Blobs do Azure para outra pasta. 

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de começar o início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Vá ao [portal Azure.](https://portal.azure.com) 
1. A partir do menu do portal Azure, selecione Criar uma fábrica de**dados****analíticos** > de **recursos:** > 

    ![Criação de nova fábrica de dados](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** em **Nome**. 
 
   O nome da fábrica de dados Azure deve ser *globalmente único.* Se vir o seguinte erro, altere o nome da fábrica de dados (por exemplo, ** &lt;o seu nome&gt;ADFTutorialDataFactory**) e tente criar novamente. Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/doc-common-process/name-not-available-error.png)
1. Em **Subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
1. Em **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
   Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados.

   A lista mostra apenas as localizações suportadas pelo Data Factory e onde serão armazenados os seus metadados do Azure Data Factory. As lojas de dados associadas (como o Azure Storage e o Azure SQL Database) e os cálculos (como o Azure HDInsight) que a Data Factory utiliza podem ser executados noutras regiões.

1. Selecione **Criar**.

1. Após a criação estar concluída, vê a página **data Factory.** Selecione o mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte.
   
   ![Home page da fábrica de dados, com o mosaico "Criar e Monitorizar"](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Iniciar a ferramenta Copiar Dados

1. Na página **Vamos começar**, selecione o mosaico **Copiar Dados** para iniciar a ferramenta Copiar Dados. 

   ![Mosaico "Copiar Dados"](./media/doc-common-process/get-started-page.png)

1. Na página **Propriedades** da ferramenta Copiar Dados, pode especificar um nome para o pipeline e a respetiva descrição e, em seguida, selecionar **Seguinte**. 

   ![Página "Propriedades"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na página **Arquivo de dados de origem**, conclua os seguintes passos:

    a. Clique **+ Criar uma nova ligação** para adicionar uma ligação.

    b. Selecione o tipo de serviço ligado que pretende criar para a ligação de origem. Neste tutorial, utilizamos **o Armazenamento Azure Blob.** Selecione-o na galeria e, em seguida, selecione **Continuar**.
    
    ![Selecione Blob](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na página **New Linked Service (Azure Blob Storage),** especifique um nome para o seu serviço ligado. Selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento,** ligação de teste e, em seguida, selecione **Criar**. 

    ![Configurar a conta de armazenamento de Blobs do Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Selecione o serviço ligado recém-criado como fonte e, em seguida, clique em **Next**.


1. Na página **Escolher o ficheiro ou pasta de entrada**, complete os seguintes passos:

   a. Clique em **Navegar** para navegar na pasta **adftutorial/entrada,** selecione o ficheiro **emp.txt** e, em seguida, clique **em Escolher**. 

   d. Selecione a caixa de verificação **de cópias Binárias** para copiar o ficheiro como está e, em seguida, selecione **Next**. 

   ![Página "Escolher o ficheiro ou pasta de entrada"](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na página da loja de **dados Destination,** selecione o serviço ligado ao **Armazenamento Azure Blob** que criou e, em seguida, selecione **Next**. 

1. Na página **escolha do ficheiro de saída ou da página de pasta,** introduza **adftutorial/saída** para o caminho da pasta e, em seguida, selecione **Seguinte**. 

   ![Página "Escolher ficheiro ou pasta de saída"](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na página **Definições**, selecione **Seguinte** para utilizar as configurações predefinidas. 

1. Reveja todas as definições na página **Resumo** e selecione **Seguinte**. 

1. Na página **Implementação concluída**, selecione **Monitorização** para monitorizar o pipeline que criou. 

    ![Página "Implementação concluída"](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. A aplicação muda para o separador **Monitor.** Consulte o estado do gasoduto neste separador. Selecione **Refresh** para refrescar a lista. Clique no link em **PIPELINE NAME** para ver os detalhes de execução da atividade ou reexecutar o gasoduto. 
   
    ![Gasoduto de reciclagem](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Na página 'Atividade', selecione o link **Detalhes** (ícone de óculos) na coluna **'NAME'** para obter mais detalhes sobre o funcionamento da cópia. Para obter os detalhes das propriedades, veja [Copy Activity overview](copy-activity-overview.md) (Descrição geral da Atividade Copy). 

1. Para voltar à vista Pipeline Runs, selecione o **link ALL pipeline executa** o menu de migalhas de pão. Para atualizar a vista, selecione **Atualizar**. 

1. Verifique se é criado um ficheiro **emp.txt** na pasta **saída** do contentor **adftutorial**. Se a pasta de saída não existir, o serviço Data Factory cria-a automaticamente. 

1. Mude para o separador **Criar** acima do **Monitor** no painel esquerdo, para que possa editar os serviços ligados, conjuntos de dados e pipelines. Para saber mais sobre a edição dos mesmos na IU do Data Factory, consulte [Criar uma fábrica de dados através do portal do Azure](quickstart-create-data-factory-portal.md).

    ![Selecione separador Autor](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. Para saber como utilizar o Data Factory em mais cenários, aceda aos [tutoriais](tutorial-copy-data-portal.md). 
