---
title: Ingerir em piscina SQL em Azure Synapse Analytics
description: Saiba como ingerir dados numa piscina SQL no Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: ab25b2e300f9fe9b0b1d077a42538c69fa543af2
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982705"
---
# <a name="ingesting-data-into-a-sql-pool"></a>Ingerir dados num pool SQL

Neste artigo você aprenderá a ingerir dados de uma conta de armazenamento Do Lago De Dados Azure Gen 2 em uma piscina SQL usando Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição Azure**: Se não tiver uma subscrição Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure:** Utiliza o Azure Data Lake Storage Gen 2 como uma loja de dados *de origem.* Se não tiver uma conta de armazenamento, consulte [Criar uma conta de Armazenamento Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter passos para criar uma.
* **Azure Synapse Analytics:** Utiliza uma piscina SQL como uma loja de dados *de sumidouro.* Se não tiver um exemplo de Análise Synapse Azure, consulte [Criar uma piscina SQL](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para dar passos para criar uma.

## <a name="create-linked-services"></a>Criar serviços ligados

No Azure Synapse Analytics, um serviço ligado é onde define as suas informações de ligação a outros serviços. Nesta secção, você adicionará um serviço azure Synapse Analytics e Azure Data Lake Storage Gen2 ligado.

1. Abra o Azure Synapse Analytics UX e vá ao separador **Manage.**
1. Sob **ligações externas,** selecione **serviços Linked**.
1. Para adicionar um serviço ligado, clique em **New**.
1. Selecione o azulejo Azure Data Lake Storage Gen2 da lista e clique **em Continuar**.
1. Introduza as suas credenciais de autenticação. A chave da conta, o diretor de serviço e a identidade gerida são atualmente tipos de autenticação suportados. Clique na ligação de teste para verificar se as suas credenciais estão corretas. Clique em **Criar** quando terminar.
1. Repita os passos 3-5, mas em vez de Azure Data Lake Storage Gen2, selecione o azulejo Azure Synapse Analytics e introduza as credenciais de ligação correspondentes. Para a Azure Synapse Analytics, a autenticação SQL, identidade gerida e diretor de serviço são atualmente suportados.

## <a name="create-pipeline"></a>Criar pipeline

Um oleoduto contém o fluxo lógico para a execução de um conjunto de atividades. Nesta secção, você vai criar um pipeline contendo uma atividade de cópia que ingere dados da ADLS Gen2 em um pool SQL.

1. Vá ao **separador Orchestrate.** Clique no ícone plus ao lado do cabeçalho dos gasodutos e selecione **Pipeline**.
1. Em **Movimento e Transforme** no painel de atividades, arraste **os dados da Cópia** para a tela do gasoduto.
1. Clique na atividade da cópia e vá ao separador **Fonte.** Clique em **Novo** para criar um novo conjunto de dados de origem.
1. Selecione Azure Data Lake Storage gen2 como a sua loja de dados e clique em continuar.
1. Selecione DelimitedText à medida que o seu formato e clique continue.
1. No painel de propriedades do conjunto, selecione o serviço ligado a ADLS que criou. Especifique o caminho de ficheiro dos seus dados de origem e especifique se a primeira linha tem um cabeçalho. Pode importar o esquema da loja de ficheiros ou de um ficheiro de amostra. Clique ok quando terminar.
1. Vá ao **separador Sink.** Clique em **Novo** para criar um novo conjunto de dados de afundar.
1. Selecione Azure Synapse Analytics à medida que a sua loja de dados e clique em continuar.
1. No painel de propriedades do conjunto, selecione o serviço ligado azure Synapse Analytics que criou. Se estiver escrevendo para uma mesa existente, selecione-a a partir do dropdown. Caso contrário, consulte **editar** e insira o seu novo nome de mesa. Clique OK quando terminar
1. Se estiver a criar uma tabela, ative a **criação de automaticamente** no campo de opções da tabela.

## <a name="debug-and-publish-pipeline"></a>Depuração e publicar oleoduto

Uma vez concluída a configuração do seu pipeline, pode executar uma execução de depuração antes de publicar os seus artefactos para verificar se está tudo correto.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Verá o estado da execução do pipeline no separador **Saída**, na parte inferior da janela. 
1. Uma vez que o gasoduto possa funcionar com sucesso, na barra de ferramentas superior, **selecione Publicar Tudo**. Esta ação publica entidades (conjuntos de dados e oleodutos) que criou para o serviço Synapse Analytics.
1. Aguarde até ver a mensagem **Publicação com êxito**. Para ver mensagens de notificação, clique no botão do sino no canto superior direito. 


## <a name="trigger-and-monitor-the-pipeline"></a>Desencadear e monitorizar o gasoduto

Neste passo, aciona manualmente o gasoduto publicado no passo anterior. 

1. **Selecione Adicionar gatilho** na barra de ferramentas e, em seguida, selecione **'Gatilho' agora**. Na página **Executar Pipeline**, selecione **Concluir**.  
1. Vá ao separador **Monitor** localizado na barra lateral esquerda. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar links na coluna **Ações** para visualizar detalhes de atividade e reexecutar o gasoduto.
1. Para ver as execuções de atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna **Ações**. Neste exemplo, só há uma atividade, por isso só se vê uma entrada na lista. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna **Ações**. Selecione **Pipeline Runs** na parte superior para voltar à vista Pipeline Runs. Para atualizar a vista, selecione **Atualizar**.
1. Verifique se os seus dados estão corretamente escritos na piscina SQL.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a integração de dados para a Synapse Analytics, consulte os dados de [Ingesting no artigo da Azure Data Lake Storage Gen2.](data-integration-data-lake.md)
