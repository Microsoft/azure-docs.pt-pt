---
title: Manter o controle dos dados ao buscar no Azure Sentinel usando indicadores de busca | Microsoft Docs
description: Este artigo descreve como usar os indicadores de busca do Azure Sentinel para manter o controle dos dados.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: aa414e37470cc11b7dc83e7416590aa2babf6818
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240259"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Manter o controle dos dados durante a busca com o Azure Sentinel

A busca de ameaças normalmente requer a revisão de montanhas de dados de log, procurando evidências de comportamento mal-intencionado. Durante esse processo, os investigadores localizam eventos que desejam lembrar, revisitar e analisar como parte da validação de possíveis ameaças e de entender toda a história de um comprometimento.

Os indicadores de busca no Azure Sentinel o ajudam a fazer isso, preservando as consultas executadas em Log Analytics, juntamente com os resultados da consulta que você considera relevantes. Você também pode registrar suas observações contextuais e fazer referência às suas descobertas Adicionando anotações e marcas. Os dados marcados são visíveis para você e seus colegas de equipe para facilitar a colaboração.

Você pode revisitar seus dados marcados a qualquer momento na guia **indicador** do painel de **busca** . Você pode usar as opções de filtragem e pesquisa para localizar rapidamente dados específicos para sua investigação atual. Como alternativa, você pode exibir seus dados marcados diretamente na tabela **HuntingBookmark** em Azure monitor. Isso permite filtrar, resumir e unir dados marcados com outras fontes de dados, facilitando a procura de evidências corroboratings.

Atualmente em visualização, se você encontrar algo que precisa ser resolvido com urgência durante a busca em seus logs, em alguns cliques, você pode criar um indicador e promovê-lo para um incidente ou adicionar o indicador a um incidente existente. Para obter mais informações sobre incidentes, [consulte Tutorial: Investigue incidentes com o Azure](tutorial-investigate-cases.md)Sentinel. 

Também na visualização, você pode visualizar seus dados marcados, clicando em **investigar** nos detalhes do indicador. Isso inicia a experiência de investigação na qual você pode exibir, investigar e comunicar visualmente suas descobertas usando um diagrama de entidade-grafo e uma linha do tempo interativos.

## <a name="add-a-bookmark"></a>Adicionar um indicador

1. No portal do Azure, navegue até **sentinela** > **Threat Management** > **busca** para executar consultas para comportamento suspeito e anormal.

2. Selecione uma das consultas de busca e, à direita, nos detalhes da consulta de busca, selecione **Executar consulta**. 

3. Selecione **exibir resultados da consulta**. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![exibir resultados da consulta da busca do Azure Sentinel](./media/bookmarks/new-processes-observed-example.png)
    
    Essa ação abre os resultados da consulta no painel **logs** .

4. Na lista de resultados da consulta de log, expanda a linha que contém as informações que você acha interessante.

5. Selecione as reticências (...) à esquerda e, em seguida, selecione **Adicionar indicador de busca**:
    
    > [!div class="mx-imgBorder"]
    > ![Adicionar o indicador de busca à consulta](./media/bookmarks/add-hunting-bookmark.png)

6. À direita, no painel **Adicionar indicador de busca** , como opção, atualize o nome do indicador, adicione marcas e notas para ajudá-lo a identificar o que era interessante sobre o item.

7. Na seção **informações da consulta** , use as caixas suspensas para extrair informações dos resultados da consulta para os tipos de entidade **conta**, **host**e **endereço IP** . Essa ação mapeia o tipo de entidade selecionado para uma coluna específica do resultado da consulta. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Mapear tipos de entidade para o indicador de busca](./media/bookmarks/map-entity-types-bookmark.png)
    
    Para exibir o indicador no grafo de investigação (atualmente em visualização), você deve mapear pelo menos um tipo de entidade que seja **conta**, **host**ou **endereço IP**. 

5. Clique em **Adicionar** para confirmar suas alterações e adicionar o indicador. Todos os dados marcados são compartilhados com outros investigadores e é uma primeira etapa para uma experiência de investigação colaborativa.

 
> [!NOTE]
> Os resultados da consulta de log dão suporte a indicadores sempre que esse painel é aberto do Azure Sentinel. Por exemplo, você seleciona**logs** **gerais** > na barra de navegação, seleciona links de eventos no grafo de investigações ou seleciona uma ID de alerta a partir dos detalhes completos de um incidente (atualmente em visualização). Não é possível criar indicadores quando o painel **logs** é aberto de outros locais, como diretamente do Azure monitor.

## <a name="view-and-update-bookmarks"></a>Exibir e atualizar indicadores 

1. No portal do Azure, navegue até **sentinela** > **Threat Management** > **busca**. 

2. Selecione a guia **indicadores** para exibir a lista de indicadores.

3. Para ajudá-lo a encontrar um indicador específico, use a caixa de pesquisa ou as opções de filtro.

4. Selecione indicadores individuais e exiba os detalhes do indicador no painel de detalhes à direita.

5. Faça as alterações conforme necessário, que são salvas automaticamente.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Explorando indicadores no grafo de investigação

> [!IMPORTANT]
> A exploração de indicadores no grafo de investigação e o próprio gráfico de investigação estão atualmente em visualização pública.
> Esses recursos são fornecidos sem um contrato de nível de serviço e não são recomendados para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Na portal do Azure, navegue até a guia **sentinela** > de**Gerenciamento** > de ameaças**procurando** > **indicadores** e selecione o indicador ou indicadores que você deseja investigar.

2. Nos detalhes do indicador, verifique se pelo menos uma entidade está mapeada. Por exemplo, para **entidades**, você vê entradas para **IP**, **máquina**ou **conta**.

3. Clique em **investigar** para exibir o indicador no grafo de investigação.

Para obter instruções sobre como usar o grafo de investigação, consulte [usar o grafo de investigação para aprofundar-se](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Adicionar indicadores a um incidente novo ou existente

> [!IMPORTANT]
> A adição de indicadores a um incidente novo ou existente está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Na portal do Azure, navegue até a guia **sentinela** > de**Gerenciamento** > de ameaças**procurando** > **indicadores** e selecione o indicador ou indicadores que você deseja adicionar a um incidente.

2. Selecione **ações de incidente (versão prévia)** na barra de comandos:
    
    > [!div class="mx-imgBorder"]
    > ![Adicionar indicadores ao incidente](./media/bookmarks/incident-actions.png)

3. Selecione **criar novo incidente** ou **Adicionar ao incidente existente**, conforme necessário. Em seguida:
    
    - Para um novo incidente: Opcionalmente, atualize os detalhes para o incidente e, em seguida, selecione **criar**.
    - Para adicionar um indicador a um incidente existente: Selecione um incidente e, em seguida, selecione **Adicionar**. 

Para exibir o indicador dentro do incidente: Navegue até **sentinela** > **incidentes** de**Gerenciamento** > de ameaças e selecione o incidente com seu indicador. Selecione **Exibir detalhes completos**e, em seguida, selecione a guia **indicadores** .

## <a name="view-bookmarked-data-in-logs"></a>Exibir dados marcados em logs

Para exibir consultas com indicadores, resultados ou seu histórico, selecione o indicador na guia**indicadores** de **busca** > e use os links fornecidos no painel de detalhes: 

- **Exiba a consulta de origem** para exibir a consulta de origem no painel **logs** .

- **Exiba os logs de indicadores** para ver todos os metadados de indicador, que incluem quem fez a atualização, os valores atualizados e a hora em que a atualização ocorreu.

Você também pode exibir os dados brutos do indicador para todos os indicadores selecionando **logs de indicadores** na barra de comandos na guia**indicadores** de **busca** > :

> [!div class="mx-imgBorder"]
> ![Logs de indicadores](./media/bookmarks/bookmark-logs.png)

Esta exibição mostra todos os indicadores com metadados associados. Você pode usar consultas KQL ( [palavra-chave Query Language](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) ) para filtrar a versão mais recente do indicador específico que você está procurando.

> [!NOTE]
> Pode haver um atraso significativo (medido em minutos) entre o momento em que você cria um indicador e quando ele é exibido na guia **indicadores** .

## <a name="delete-a-bookmark"></a>Excluir um indicador
 
1.  Na portal do Azure, navegue até a guia **sentinela** > de**Gerenciamento** > de ameaças**procurando** > **indicadores** e selecione o indicador ou indicadores que você deseja excluir. 

2. Selecione as reticências (...) no final da linha e selecione **Excluir indicador**.
    
A exclusão do indicador remove o indicador da lista na guia **indicador** . A tabela Log Analytics **HuntingBookmark** continuará a conter entradas de indicador anteriores, mas a entrada mais recente alterará o valor de **SoftDelete** para true, facilitando o filtro de indicadores antigos. A exclusão de um indicador não remove nenhuma entidade da experiência de investigação associada a outros indicadores ou alertas. 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a executar uma investigação de busca usando indicadores no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Busca de ameaças de forma proativa](hunting.md)
- [Use blocos de anotações para executar campanhas de busca automatizadas](notebooks.md)
