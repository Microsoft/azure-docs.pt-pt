---
title: Utilize uma ingestão de um clique para ingerir dados no Azure Data Explorer
description: Saiba como ingerir (carregar) dados no Azure Data Explorer simplesmente usando uma ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444560"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Utilize uma ingestão de um clique para ingerir dados no Azure Data Explorer

Este artigo mostra-lhe como usar uma ingestão de um clique para uma ingestão rápida de uma nova tabela em formatos JSON ou CSV. Os dados podem ser ingeridos a partir do armazenamento ou de um ficheiro local numa tabela existente ou numa nova tabela. Utilize o intuitivo assistente de um clique e os seus dados ingerirem em poucos minutos. Em seguida, pode editar a tabela e executar consultas utilizando o Azure Data Explorer Web UI.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Inscreva-se [na inscrição](https://dataexplorer.azure.com/).
* Criar [um cluster azure Data Explorer e base de dados.](create-cluster-database-portal.md)
* Inscreva-se na [UI web](https://dataexplorer.azure.com/) e [adicione uma ligação ao seu cluster](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Ingestão de novos dados

1. Clique na *base* de dados ou na linha de *mesa* no menu esquerdo da UI Web e selecione **Ingest novos dados (Pré-visualização)**.

    ![Selecione ingestão de um clique na UI Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Na janela **Ingest new data (Preview),** selecione o separador **Fonte** e complete os Detalhes do **Projeto:**

    * Para **Tabela**, selecione um nome de mesa existente no menu suspenso ou selecione **Criar novo** para fazer uma nova tabela.
    * Para **o tipo de ingestão,** selecione a partir do **armazenamento** ou **do ficheiro**.
      * Se selecionar **a partir do armazenamento,** selecione Link para o **armazenamento** para adicionar o URL. Utilize [o URL Blob SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) para contas de armazenamento privadas. 
      * Se selecionar **a partir do ficheiro,** selecione **'Navegar'** e arraste o ficheiro para a caixa.
    * **Selecione Editar esquemapara** visualizar e editar a configuração da coluna de tabela.
 
    ![Detalhes da fonte de ingestão de um clique](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Se selecionar **novos dados da Ingest (Pré-visualização)** numa linha de *tabela,* o nome da tabela selecionado aparecerá nos Detalhes do **Projeto**.

1. Se selecionar uma tabela existente, a janela das **colunas** mapeia abre-se para mapear colunas de dados de origem para colunas de tabelas-alvo. 
    * Utilize a **coluna Omit** para remover uma coluna de alvo da tabela.
    * Utilize a **nova coluna** para adicionar uma nova coluna à mesa.

    ![Janela de colunas de mapas](media/ingest-data-one-click/one-click-map-columns-window.png)

1. No separador **Schema:**

    * Selecione o tipo de **compressão** a partir do menu suspenso e, em seguida, selecione-os **descomprimidos** ou **GZip**.
    * Selecione **o formato Data** a partir do menu suspenso e, em seguida, selecione **JSON,** **CSV,** **TSV,** **SCSV,** **SOHSV,** **TSVE**ou **PSV**. 
        * Quando selecionar o formato **JSON,** deve também selecionar **os níveis JSON**, de 1 a 10. Os níveis afetam a representação de dados da coluna de tabela. 
        * Se selecionar um formato diferente do JSON, deve selecionar a caixa de verificação **Incluir nomes** de colunas para ignorar a linha de título do ficheiro.
    * **O nome do mapeamento** é definido automaticamente, mas pode ser editado.
    * Se selecionar uma tabela existente, pode selecionar **colunas do Mapa** para abrir a janela das **colunas do Mapa.**

    ![Esquema de formato CSV de ingestão de um clique](media/ingest-data-one-click/one-click-csv-format.png)

1. Acima do painel do **Editor,** selecione o botão **v** para abrir o editor. No editor, pode ver e copiar as consultas automáticas geradas a partir das suas inputs. 

1. Na mesa: 
    * Clique na direita nos cabeçalhos da coluna para alterar o tipo de **dados,** **coluna De Nome de renome,** apagar **coluna,** **ordenar ascendente**, ou ordenar **a descida**. Nas colunas existentes, apenas está disponível a triagem de dados. 
    * Clique duas vezes no novo nome da coluna para editar.

1. **Selecione Iniciar a ingestão** para criar uma tabela e mapeamento e iniciar a ingestão de dados.

    ![Esquema de formato JSON de ingestão de um clique](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Consultar dados

1. Na janela concluída de **ingestão** de dados, os três passos serão marcados com marcas de verificação verde se a ingestão de dados terminar com sucesso.
 
    ![Ingestão de dados de um clique completa](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecione o botão **v** para abrir a consulta. Copiar para a Web UI para editar a consulta.

1. O menu à direita contém **consultas rápidas** e opções **de ferramentas.** 

    * **Consultas rápidas** incluem links para a Web UI com consultas de exemplo.
    * **As ferramentas** incluem um link para **comandos Drop** na Web UI, `.drop` que lhe permitem resolver problemas executando os comandos relevantes.

    > [!TIP]
    > Pode perder dados quando `.drop` utilizar comandos. Use-os com cuidado.

## <a name="next-steps"></a>Passos seguintes

* [Dados de consulta na Web UI do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web](web-query-data.md)
* [Escreva consultas para O Explorador de Dados Azure usando a linguagem da consulta de Kusto](write-queries.md)
