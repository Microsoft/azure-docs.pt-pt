---
title: Use a ingestão de um clique para ingerir dados no Azure Data Explorer
description: Saiba mais sobre como ingerir (carregar) dados no Azure Data Explorer simplesmente usando a ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520052"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Use a ingestão de um clique para ingerir dados no Azure Data Explorer

Este artigo mostra como usar a ingestão de um clique para ingestão rápida de uma nova tabela em formatos JSON ou CSV do armazenamento para o Azure Data Explorer. Depois que os dados são ingeridos, você pode editar a tabela e executar consultas usando a interface do usuário da Web.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Inicie sessão [na aplicação](https://dataexplorer.azure.com/).
* Criar [um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md)
* Fonte de dados no armazenamento do Azure.

## <a name="ingest-new-data"></a>Ingerir novos dados

1. Clique com o botão direito do mouse no *nome do banco* de dados e selecione **ingerir novo dado (visualização)**

    ![Selecione uma ingestão de clique na interface do usuário da Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Na janela **ingestão de dados (visualização)** , na guia **origem** , preencha os **detalhes do projeto**:

    * Insira o novo **nome da tabela**. 
    * Selecione o **tipo** > de ingestão**do armazenamento**.
    * Digite **link para armazenamento** Adicionar URL ao armazenamento. Use a URL SAS do blob para contas de armazenamento privado. 
    * Selecione **Editar esquema**
 
    ![detalhes da fonte de ingestão de um clique](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. Na guia **esquema** , selecione **formato de dados** na lista suspensa > **JSON** ou **CSV**. 
   
   Se selecionar **CSV**:
    * Marque a caixa de seleção **ignorar título** para ignorar a linha de cabeçalho do arquivo CSV.    
    * O **nome do mapeamento** é definido automaticamente, mas pode ser editado.

    ![um formato CSV de ingestão de clique esquema. png](media/ingest-data-one-click/one-click-csv-format.png)

   Se selecionar **JSON**:
    * Selecione **os níveis JSON**: 1-10 da lista suspensa. Os níveis no arquivo JSON são mostrados na tabela no canto inferior direito. 
    * O **nome do mapeamento** é definido automaticamente, mas pode ser editado.

    ![esquema de formato JSON de ingestão de um clique](media/ingest-data-one-click/one-click-json-format.png)  

1. No **Editor**, selecione **V** à direita para abrir o editor. No editor, você pode exibir e copiar as consultas automáticas geradas de suas entradas. 

1.  Na tabela no canto inferior direito: 
    * Selecione **V** à direita da coluna para **renomear coluna**, **Excluir coluna**, **classificar em ordem crescente**ou **classificar** em ordem decrescente
    * Clique duas vezes no nome da coluna para editar.
    * Selecione o ícone à esquerda do nome da coluna para alterar o tipo de dados. 

1. Selecione **Iniciar ingestão** para criar tabela, criar mapeamento e ingestão de dados.
 
## <a name="query-data"></a>Consultar dados

1. Na janela **ingestão de dados concluída** , todas as três etapas serão marcadas com marcas de seleção verdes se a ingestão de dados for concluída com êxito. 
 
    ![uma ingestão de dados de clique concluída](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecione **V** para abrir a consulta. Copie para a interface do usuário da Web para editar a consulta.

1. O menu à direita contém consultas e **ferramentas** **rápidas** . 

    * **Consultas rápidas** incluem links para a interface do usuário da Web com consultas de exemplo.
    * As **ferramentas** incluem link para a interface do usuário da Web com **comandos drop** que permitem solucionar problemas `.drop` executando o comando relevante.

    > [!TIP]
    > Os dados podem ser perdidos `.drop` usando comandos. Use-os com cuidado.

## <a name="next-steps"></a>Passos seguintes

* [Consultar dados no Azure Data Explorer interface do usuário da Web](web-query-data.md)
* [Escrever consultas para Data Explorer do Azure usando a linguagem de consulta Kusto](write-queries.md)
