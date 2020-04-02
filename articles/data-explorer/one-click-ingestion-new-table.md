---
title: Utilize uma ingestão de um clique para ingerir dados numa nova tabela do Azure Data Explorer
description: Ingeque (loading) dados numa nova tabela do Azure Data Explorer simplesmente, utilizando uma ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 1e337a46d1d8ddda1b07bde6f12d4c1f7feda42f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549223"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-a-new-table-in-azure-data-explorer"></a>Utilize uma ingestão de um clique para ingerir dados para uma nova tabela no Azure Data Explorer

A ingestão de um clique permite-lhe ingerir rapidamente dados em JSON, CSV e outros formatos numa tabela. Utilizando o Azure Data Explorer Web UI, pode ingerir dados a partir do armazenamento, a partir de um ficheiro local ou de um recipiente. 

Este documento descreve a utilização do intuitivo assistente de um clique para ingerir dados csv de um recipiente para uma nova tabela. Em seguida, pode editar a tabela e executar consultas com o Azure Data Explorer Web UI. Também pode configurar a ingestão contínua para ingerir automaticamente dados na tabela quando os dados de origem atualizarem.

A ingestão de um clique é particularmente útil quando ingerir dados pela primeira vez, ou quando o esquema dos seus dados não lhe é familiar. 

Para uma visão geral da ingestão de um clique e de uma lista de pré-requisitos, consulte a [ingestão de um clique](ingest-data-one-click.md).
Para obter informações sobre a ingestão de dados numa tabela existente no Azure Data Explorer, consulte a [ingestão de um clique numa tabela existente](one-click-ingestion-existing-table.md)

## <a name="ingest-new-data"></a>Ingestão de novos dados

1. No menu esquerdo da UI Web, clique na direita numa base de *dados* e selecione **Novos dados da Ingest (Pré-visualização)**.

    ![Selecione ingestão de um clique na UI Web](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. Na janela **Ingest new data (Preview),** o separador **Fonte** é automaticamente selecionado. 

1. Selecione **Criar uma nova tabela** e introduza um nome para a nova tabela. Pode usar alfanuméricos, hífenes e sublinhados. Personagens especiais não são suportados.

![Criar uma nova tabela](media/one-click-ingestion-new-table/create-new-table.png) 

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]

**Selecione Editar esquemapara** visualizar e editar a configuração da coluna de tabela. O sistema selecionará uma das bolhas aleatoriamente e o esquema será gerado com base nessa bolha. Olhando para o nome da fonte, o serviço identifica automaticamente se está comprimido ou não.

## <a name="edit-the-schema"></a>Editar o esquema

1. No separador **Schema:**

    1. Selecione **o formato Data:**

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]

    1. Se selecionar **JSON,** também deve selecionar **os níveis JSON,** de 1 a 10. Os níveis afetam a representação de dados da coluna de tabela. 

    ![Selecione os níveis JSON](media/one-click-ingestion-new-table/json-levels.png)

    * Se selecionar um formato diferente do JSON, pode selecionar a caixa de verificação **Incluir nomes** de colunas para ignorar a linha de título do ficheiro.

        ![Selecione Incluir nomes de colunas](media/one-click-ingestion-new-table/non-json-format.png)
        
1. No campo de **nome de Mapeamento,** introduza um nome de mapeamento. Pode usar caracteres alfanuméricos e sublinhados. Espaços, personagens especiais e hífenes não são suportados.
    
    ![Nome de mapeamento de mesa](media/one-click-ingestion-new-table/table-mapping.png)

## <a name="copy-and-paste-queries"></a>Consultas de cópia e pasta

1. Acima do painel do **Editor,** selecione o botão **v** para abrir o editor. No editor, pode ver e copiar as consultas automáticas geradas a partir das suas inputs. 
1. Na mesa: 
    * Clique duas vezes no novo nome da coluna para editar.
    * Selecione novos cabeçalhos de coluna e faça qualquer um dos seguintes cabeçalhos:
    
|Ação         |Descrição                                  |
|-----------------|-------------------------------------------|
|Alterar o tipo de dados |Alterar o tipo de dados do selecionado automaticamente pelo serviço para um dos outros tipos de [dados suportados](#edit-the-schema)|
|Coluna de renome    |Alterar o nome da coluna |
|Nova coluna       |Adicione uma nova coluna|
|Excluir coluna    |Eliminar a coluna selecionada|
|Ordenação ascendente   |Ordenar a tabela pela coluna selecionada por ordem ascendente (apenas colunas existentes)|
|Ordenação descendente  |Ordenar a tabela pela coluna selecionada por ordem descendente (apenas colunas existentes) |

> [!Note]
> Para formatos tabular, cada coluna pode ser ingerida numa coluna no Azure Data Explorer.
> Pode criar novas colunas a partir de diferentes níveis JSON.

[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Iniciar a ingestão

**Selecione Iniciar a ingestão** para criar uma tabela e mapeamento e iniciar a ingestão de dados.
![Iniciar a ingestão](media/one-click-ingestion-new-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Ingestão de dados concluída

Na janela concluída de **ingestão** de dados, os três passos serão marcados com marcas de verificação verde se a ingestão de dados terminar com sucesso.
 
![Ingestão de dados de um clique completa](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

**Ingestão contínua**

A ingestão contínua permite criar uma grelha de eventos que ouça o recipiente de origem. Qualquer nova bolha que satisfaça os critérios dos parâmetros pré-definidos (prefixo, sufixo, e assim por diante) será automaticamente ingerida na tabela de destino.

> [!Note]
> A ingestão contínua só é relevante quando se ingerir a partir de um recipiente.

1. Selecione **ingestão contínua** para abrir o portal Azure. A página de ligação de dados abre com o conector de dados da rede de eventos aberto e com parâmetros de origem e alvo já introduzidos (recipiente de origem, tabelas e mapeamentos).

1. Selecione **Criar** para criar uma ligação de dados que ouça quaisquer alterações no recipiente. 

## <a name="next-steps"></a>Passos seguintes

* [Dados de consulta na Web UI do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web](/azure/data-explorer/web-query-data)
* [Escreva consultas para O Explorador de Dados Azure usando a linguagem da consulta de Kusto](/azure/data-explorer/write-queries)
