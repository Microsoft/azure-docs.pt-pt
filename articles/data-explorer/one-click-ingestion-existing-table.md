---
title: Utilize uma ingestão de um clique para ingerir dados numa tabela existente do Explorador de Dados do Azure
description: Ingeque (loading) dados numa tabela existente do Explorador de Dados do Azure simplesmente, utilizando uma ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a7fdab66394c132bcd9134669b841d178c559f28
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546215"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>Utilize uma ingestão de um clique para ingerir dados numa tabela existente no Azure Data Explorer

A ingestão de um clique permite-lhe ingerir rapidamente dados em JSON, CSV e outros formatos numa tabela. Utilizando o Azure Data Explorer Web UI, pode ingerir dados a partir do armazenamento, a partir de um ficheiro local ou de um recipiente. 

Este documento descreve a utilização do intuitivo assistente de um clique para ingerir dados JSON de um ficheiro numa tabela existente. Em seguida, pode editar a tabela e executar consultas com o Azure Data Explorer Web UI.

A ingestão de um clique é particularmente útil quando ingerir dados pela primeira vez, ou quando o esquema dos seus dados não lhe é familiar. 

Para uma visão geral da ingestão de um clique e de uma lista de pré-requisitos, consulte a [ingestão de um clique](ingest-data-one-click.md).
Para obter informações sobre a ingestão de dados numa nova tabela no Azure Data Explorer, consulte a [ingestão de um clique para uma nova tabela](one-click-ingestion-new-table.md)

## <a name="ingest-new-data"></a>Ingestão de novos dados

1. No menu esquerdo da UI Web, clique na direita numa base de *dados* ou *tabela* e selecione **Ingest novos dados (Pré-visualização)**.

    ![Selecione ingestão de um clique na UI Web](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. Na janela **Ingest new data (Preview),** o separador **Fonte** é automaticamente selecionado.

1. Se o campo **de mesa** não estiver automaticamente preenchido, selecione um nome de mesa existente no menu suspenso.
    > [!TIP]
    > Se selecionar **novos dados da Ingest (Pré-visualização)** numa linha de *tabela,* o nome da tabela selecionado aparecerá nos Detalhes do **Projeto**.

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]
    
**Selecione Editar esquemapara** visualizar e editar a configuração da coluna de tabela.

## <a name="edit-the-schema"></a>Editar o esquema

1. O diálogo das **colunas do Mapa** abre e pode mapear colunas de dados de origem para colunas de tabelas de alvos. 
    * Nos campos de **colunas Source,** introduza nomes de colunas para mapear com as **colunas Target**.
    * Para eliminar um mapeamento, selecione o ícone do caixote do lixo.

    ![Janela de colunas de mapas](media/one-click-ingestion-existing-table/map-columns.png)

1. Selecione **Atualizar**.
1. No separador **Schema:**
    1. Selecione **o tipo de compressão**e, em seguida, selecione-os **descomprimidos** ou **gzip**.

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. Se selecionar **JSON,** também deve selecionar **os níveis JSON,** de 1 a 10. Os níveis afetam a representação de dados da coluna de tabela.

    ![Selecione os níveis JSON](media/one-click-ingestion-existing-table/json-levels.png)

    * Se selecionar um formato diferente do JSON, pode selecionar a caixa de verificação **Incluir nomes** de colunas para ignorar a linha de título do ficheiro.
        
    ![Selecione Incluir nomes de colunas](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > Os formatos tabular podem ingerir dados de colunas numa única coluna numa tabela Do Explorador de Dados Azure. 

    * Os novos mapeamentos são definidos automaticamente, mas pode mudá-lo para usar um existente. 
    * Pode selecionar **colunas de mapas** para abrir a janela das **colunas do Mapa.**

## <a name="copy-and-paste-queries"></a>Consultas de cópia e pasta

1. Acima do painel do **Editor,** selecione o botão **v** para abrir o editor. No editor, pode visualizar e copiar os comandos automáticos gerados a partir das suas inputs. 
1. Na mesa: 
    * Selecione novos cabeçalhos de coluna para adicionar uma **nova coluna,** **eliminar**coluna, **ordenar ascendente,** ou **ordenar a descida**. Nas colunas existentes, apenas está disponível a triagem de dados.

    > [!Note]
    > * Não é possível atualizar o nome e o tipo de dados para as tabelas existentes.
    > * Os comandos de queda só reverterão as alterações que foram feitas pelo seu fluxo de ingestão (novas extensões e colunas). Nada mais será largado.

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Iniciar a ingestão

**Selecione Iniciar a ingestão** para criar uma tabela e mapeamento e iniciar a ingestão de dados.

![Iniciar a ingestão](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Ingestão de dados concluída

Na janela concluída de **ingestão** de dados, os três passos serão marcados com marcas de verificação verde se a ingestão de dados terminar com sucesso.
 
![Ingestão de dados de um clique completa](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Passos seguintes

* [Dados de consulta na Web UI do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web](/azure/data-explorer/web-query-data)
* [Escreva consultas para O Explorador de Dados Azure usando a linguagem da consulta de Kusto](/azure/data-explorer/write-queries)
