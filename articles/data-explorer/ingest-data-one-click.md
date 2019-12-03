---
title: Use a ingestão de um clique para ingerir dados no Azure Data Explorer
description: Saiba mais sobre como ingerir (carregar) dados no Azure Data Explorer simplesmente usando a ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: c4ee4ed81cd4cc443a8f412462a5a7f204c91898
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688198"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Use a ingestão de um clique para ingerir dados no Azure Data Explorer

Este artigo mostra como usar a ingestão de um clique para ingestão rápida de uma nova tabela em formatos JSON ou CSV do armazenamento ou do arquivo local em uma tabela existente ou em uma nova tabela no Azure Data Explorer. Use o assistente intuitivo e, em alguns minutos, seus dados são ingeridos, você pode editar a tabela e executar consultas usando a interface do usuário da Web.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Inicie sessão [na aplicação](https://dataexplorer.azure.com/).
* Criar [um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md)
* Entre na [interface do usuário da Web](https://dataexplorer.azure.com/) e [adicione uma conexão ao cluster](/azure/data-explorer/web-query-data#add-clusters)
* Fonte de dados no armazenamento do Azure.

## <a name="ingest-new-data"></a>Ingerir novos dados

1. Clique com o botão direito do mouse na linha do *banco de dados* ou da *tabela* no menu à esquerda da interface do usuário da Web e selecione **ingerir novos dados (visualização)**

    ![Selecione uma ingestão de clique na interface do usuário da Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Na janela **ingerir novos dados (versão prévia)** , na guia **origem** , preencha os **detalhes do projeto**:

    * **Tabela**: selecione o nome da tabela existente na lista suspensa ou selecione **criar novo** para fazer uma nova tabela.
    * Selecione o **tipo de ingestão** > **do armazenamento** ou **do arquivo**.
        * Se você tiver selecionado **em armazenamento**, digite **vincular ao armazenamento** para adicionar a URL ao armazenamento. Use a [URL SAS do blob](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) para contas de armazenamento privado. 
        * Se você selecionou **do arquivo**, selecione **procurar** e arraste o arquivo para a caixa.
    * Selecione **Editar esquema** para exibir e editar a configuração da coluna da tabela.
 
    ![detalhes da fonte de ingestão de um clique](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Se você selecionar **ingerir novos dados (visualização)** em uma linha de *tabela* , o nome da tabela selecionada será exibido nos **detalhes do projeto**.

1. Se você selecionou uma tabela existente, a janela **mapear colunas** será aberta para mapear colunas de dados de origem para colunas da tabela de destino. 
    * Use **omitir coluna** para remover uma coluna de destino da tabela. 
    * Use **nova coluna** para adicionar uma nova coluna à sua tabela. 

    ![Janela mapear colunas](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na guia **esquema** :

    * Selecione **tipo de compactação** na lista suspensa > **descompactado** ou **gzip**.
    * Selecione **formato de dados** na lista suspensa > **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**ou **PSV**. 
        * Ao selecionar o formato **JSON** , selecione **níveis JSON**: 1-10. Os níveis afetam a representação de dados da coluna da tabela. 
        * Se você selecionar um formato diferente de JSON:, marque a caixa de seleção **incluir nomes de coluna** para ignorar a linha de cabeçalho do arquivo.    
    * O **nome do mapeamento** é definido automaticamente, mas pode ser editado.
    * Se você selecionou uma tabela existente, poderá selecionar o botão **mapear colunas** para abrir a janela **mapear colunas** .

    ![um formato CSV de ingestão de clique esquema. png](media/ingest-data-one-click/one-click-csv-format.png)

1. No **Editor**, selecione **V** à direita para abrir o editor. No editor, você pode exibir e copiar as consultas automáticas geradas de suas entradas. 

1.  Na tabela: 
    * Clique com o botão direito do mouse em novos cabeçalhos de coluna para **alterar o tipo de dados**, **renomear coluna**, **Excluir coluna**, **classificar em ordem crescente**ou **classificar em ordem decrescente**. Em colunas existentes, somente a classificação de dados está disponível. 
    * Clique duas vezes no nome da nova coluna a ser editada.

1. Selecione **Iniciar ingestão** para criar tabela, criar mapeamento e ingestão de dados.

    ![esquema de formato JSON de ingestão de um clique](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Consultar dados

1. Na janela **ingestão de dados concluída** , todas as três etapas serão marcadas com marcas de seleção verdes se a ingestão de dados for concluída com êxito. 
 
    ![uma ingestão de dados de clique concluída](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecione **V** para abrir a consulta. Copie para a interface do usuário da Web para editar a consulta.

1. O menu à direita contém consultas e **ferramentas** **rápidas** . 

    * **Consultas rápidas** incluem links para a interface do usuário da Web com consultas de exemplo.
    * As **ferramentas** incluem link para a interface do usuário da Web com **comandos drop** que permitem solucionar problemas executando o comando `.drop` relevante.

    > [!TIP]
    > Os dados podem ser perdidos usando comandos `.drop`. Use-os com cuidado.

## <a name="next-steps"></a>Passos seguintes

* [Consultar dados no Azure Data Explorer interface do usuário da Web](web-query-data.md)
* [Escrever consultas para Data Explorer do Azure usando a linguagem de consulta Kusto](write-queries.md)
