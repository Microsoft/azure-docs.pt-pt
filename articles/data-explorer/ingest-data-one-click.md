---
title: Use a ingestão de um clique para ingerir dados no Azure Data Explorer
description: Saiba mais sobre como ingerir (carregar) dados no Azure Data Explorer simplesmente usando a ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444560"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Use a ingestão de um clique para ingerir dados no Azure Data Explorer

Este artigo mostra como usar a ingestão de um clique para ingestão rápida de uma nova tabela nos formatos JSON ou CSV. Os dados podem ser ingeridos do armazenamento ou de um arquivo local em uma tabela existente ou em uma nova tabela. Use o assistente de um único clique intuitivo e seus dados são ingeridos em alguns minutos. Em seguida, você pode editar a tabela e executar consultas usando a interface do usuário da Web do Azure Data Explorer.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Inicie sessão [na aplicação](https://dataexplorer.azure.com/).
* Crie [um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md).
* Entre na [interface do usuário da Web](https://dataexplorer.azure.com/) e [adicione uma conexão ao cluster](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Ingerir novos dados

1. Clique com o botão direito do mouse na linha de *banco de dados* ou *tabela* no menu à esquerda da interface do usuário da Web e selecione **ingerir novos dados (versão prévia)** .

    ![Selecione a ingestão de um clique na interface do usuário da Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Na janela **ingerir novos dados (versão prévia)** , selecione a guia **origem** e preencha os **detalhes do projeto**:

    * Para **tabela**, selecione um nome de tabela existente no menu suspenso ou selecione **criar novo** para fazer uma nova tabela.
    * Para **tipo de ingestão**, selecione **de armazenamento** ou **de arquivo**.
      * Se você tiver selecionado **em armazenamento**, selecione **vincular ao armazenamento** para adicionar a URL. Use a [URL SAS do blob](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) para contas de armazenamento privado. 
      * Se você selecionou **do arquivo**, selecione **procurar** e arraste o arquivo para a caixa.
    * Selecione **Editar esquema** para exibir e editar a configuração da coluna da tabela.
 
    ![Detalhes da fonte de ingestão com um clique](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Se você selecionar **ingerir novos dados (visualização)** em uma linha de *tabela* , o nome da tabela selecionada será exibido nos **detalhes do projeto**.

1. Se você selecionou uma tabela existente, a janela **mapear colunas** será aberta para mapear colunas de dados de origem para colunas da tabela de destino. 
    * Use **omitir coluna** para remover uma coluna de destino da tabela.
    * Use **nova coluna** para adicionar uma nova coluna à tabela.

    ![Janela mapear colunas](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na guia **esquema** :

    * Selecione **tipo de compactação** no menu suspenso e, em seguida, selecione **descompactado** ou **gzip**.
    * Selecione **formato de dados** no menu suspenso e, em seguida, selecione **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**ou **PSV**. 
        * Ao selecionar o formato **JSON** , você também deve selecionar **níveis JSON**, de 1 a 10. Os níveis afetam a representação de dados da coluna da tabela. 
        * Se você selecionar um formato diferente de JSON, deverá marcar a caixa de seleção **incluir nomes de coluna** para ignorar a linha de cabeçalho do arquivo.
    * O **nome do mapeamento** é definido automaticamente, mas pode ser editado.
    * Se você selecionou uma tabela existente, poderá selecionar **mapear colunas** para abrir a janela **mapear colunas** .

    ![Esquema de formato CSV de ingestão com um clique](media/ingest-data-one-click/one-click-csv-format.png)

1. Acima do painel do **Editor** , selecione o botão **v** para abrir o editor. No editor, você pode exibir e copiar as consultas automáticas geradas de suas entradas. 

1. Na tabela: 
    * Clique com o botão direito do mouse em novos cabeçalhos de coluna para **alterar tipo de dados**, **renomear coluna**, **Excluir coluna**, **classificar em ordem crescente**ou classificar em ordem **decrescente**. Nas colunas existentes, somente a classificação de dados está disponível. 
    * Clique duas vezes no nome da nova coluna a ser editada.

1. Selecione **Iniciar ingestão** para criar uma tabela e um mapeamento e para iniciar a ingestão de dados.

    ![Esquema de formato JSON de ingestão com um clique](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Consultar dados

1. Na janela **ingestão de dados concluída** , todas as três etapas serão marcadas com marcas de seleção verdes se a ingestão de dados for concluída com êxito.
 
    ![Ingestão de dados com um clique concluída](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecione o botão **v** para abrir a consulta. Copie para a interface do usuário da Web para editar a consulta.

1. O menu à direita contém **consultas rápidas** e opções de **ferramentas** . 

    * **Consultas rápidas** incluem links para a interface do usuário da Web com consultas de exemplo.
    * As **ferramentas** incluem um link para **descartar comandos** na interface do usuário da Web, o que permite solucionar problemas executando os comandos `.drop` relevantes.

    > [!TIP]
    > Você pode perder dados ao usar comandos de `.drop`. Use-os com cuidado.

## <a name="next-steps"></a>Passos seguintes

* [Consultar dados no Azure Data Explorer interface do usuário da Web](web-query-data.md)
* [Escrever consultas para Data Explorer do Azure usando a linguagem de consulta Kusto](write-queries.md)
