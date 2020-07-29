---
title: Ingerir no Azure Data Lake Storage Gen2
description: Saiba como ingerir dados no Azure Data Lake Storage Gen2 em Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 7b844bcf45417fefc7dd78a26d5dae0b2ce03249
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82982909"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Ingerir dados no Azure Data Lake Storage Gen2 

Neste artigo, você aprenderá a ingerir dados de um local para outro numa conta de armazenamento Azure Data Lake Gen 2 (Azure Data Lake Gen 2) usando a Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição Azure**: Se não tiver uma subscrição do Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta Azure Storage**: Utiliza o Azure Data Lake Gen 2 como uma loja de dados *de origem.* Se não tiver uma conta de armazenamento, consulte [criar uma conta de Armazenamento Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter passos para criar uma.

## <a name="create-linked-services"></a>Criar serviços ligados

No Azure Synapse Analytics, um serviço ligado é onde define as suas informações de ligação a outros serviços. Nesta secção, você adicionará Azure Synapse Analytics e Azure Data Lake Gen 2 como serviços ligados.

1. Abra o Azure Synapse Analytics UX e vá ao **separador Gerir.**
1. Em **ligações externas**, selecione **serviços Linked**.
1. Para adicionar um serviço ligado, clique em **New**.
1. Selecione o azulejo Azure Data Lake Storage Gen2 da lista e clique em **Continuar**.
1. Insira as suas credenciais de autenticação. A chave de conta, o principal do serviço e a identidade gerida são atualmente tipos de autenticação suportados. Clique na ligação de teste para verificar se as suas credenciais estão corretas. 
1. Clique em **Criar** quando terminar.

## <a name="create-pipeline"></a>Criar pipeline

Um oleoduto contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta secção, você vai criar um pipeline contendo uma atividade de cópia que ingere dados do Azure Data Lake Gen 2 em uma piscina SQL.

1. Vá ao **separador Orquestrar.** Clique no ícone mais ao lado do cabeçalho dos gasodutos e selecione **Pipeline**.
1. Em **Movimento e Transforme** no painel de atividades, **arraste os dados do Copy** para a tela do gasoduto.
1. Clique na atividade da cópia e vá ao separador **'Fonte.'** Clique **em Novo** para criar um novo conjunto de dados de origem.
1. Selecione Azure Data Lake Storage Gen2 como a sua loja de dados e clique em continuar.
1. Selecione DelimitedText como o seu formato e clique em continuar.
1. No painel de propriedades definidas, selecione o serviço ligado ADLS que criou. Especifique o caminho do ficheiro dos seus dados de origem e especifique se a primeira linha tem um cabeçalho. Pode importar o esquema da loja de ficheiros ou de um ficheiro de amostra. Clique em OK quando terminar.
1. Vá ao **separador Sink.** Clique em **Novo** para criar um novo conjunto de dados da pia.
1. Selecione Azure Data Lake Storage gen2 como a sua loja de dados e clique em continuar.
1. Selecione DelimitedText como o seu formato e clique em continuar.
1. No painel de propriedades definidas, selecione o serviço ligado ADLS que criou. Especifique o caminho da pasta onde deseja escrever dados. Clique em OK quando terminar.

## <a name="debug-and-publish-pipeline"></a>Debug e publicar pipeline

Uma vez terminada a configuração do seu oleoduto, pode executar uma corrida de depuragem antes de publicar os seus artefactos para verificar se está tudo correto.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Verá o estado da execução do pipeline no separador **Saída**, na parte inferior da janela. 
1. Uma vez que o gasoduto possa funcionar com sucesso, na barra de ferramentas superior, **selecione Publicar Tudo**. Esta ação publica entidades (conjuntos de dados e oleodutos) que criou para o serviço Synapse Analytics.
1. Aguarde até ver a mensagem **Publicação com êxito**. Para ver as mensagens de notificação, clique no botão de campainha no topo direito. 


## <a name="trigger-and-monitor-the-pipeline"></a>Dispare e monitorize o gasoduto

Neste passo, aciona manualmente o gasoduto publicado no passo anterior. 

1. **Selecione Adicionar Gatilho** na barra de ferramentas e, em seguida, selecione Trigger **Now**. Na página **Executar Pipeline**, selecione **Concluir**.  
1. Vá ao **separador Monitor** localizado na barra lateral esquerda. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar links na coluna **Ações** para visualizar detalhes da atividade e para refazer o pipeline.
1. Para ver as execuções de atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna **Ações**. Neste exemplo, há apenas uma atividade, por isso só se vê uma entrada na lista. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna **Ações**. Selecione **Pipeline Runs** na parte superior para voltar à vista Pipeline Runs. Para atualizar a vista, selecione **Atualizar**.
1. Verifique se os seus dados estão corretamente escritos na piscina SQL.


## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a integração de dados para a Synapse Analytics, consulte os [dados de Ingeste num artigo de piscina SQL.](data-integration-sql-pool.md)
