---
title: Use marcadores de caça para investigações de dados em Azure Sentinel
description: Este artigo descreve como usar os marcadores de caça Azure Sentinel para acompanhar os dados.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 5e3b6802bab2c5fae97be8e55c8d5c11ff570f78
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651317"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Acompanhe os dados durante a caça com o Azure Sentinel

A caça a ameaças normalmente requer a revisão de montanhas de dados de registo à procura de evidências de comportamento malicioso. Durante este processo, os investigadores encontram eventos que querem recordar, revisitar e analisar como parte da validação de potenciais hipóteses e compreensão da história completa de um compromisso.

Os marcadores de caça em Azure Sentinel ajudam-no a fazê-lo, preservando as consultas que executou em **Azure Sentinel - Logs,** juntamente com os resultados de consulta que considere relevantes. Também pode registar as suas observações contextuais e fazer referência às suas descobertas adicionando notas e etiquetas. Os dados reservados são visíveis para si e para os seus companheiros de equipa para uma colaboração fácil.

Pode revisitar os seus dados reservados a qualquer momento no separador **Bookmarks** do painel **de caça.** Pode utilizar opções de filtragem e pesquisa para encontrar rapidamente dados específicos para a sua investigação atual. Em alternativa, pode ver os seus dados marcados diretamente na tabela **HuntingBookmark** no seu espaço de trabalho Log Analytics. Por exemplo:

> [!div class="mx-imgBorder"]
> ![ver tabela HuntingBookmark](./media/bookmarks/bookmark-table.png)

A visualização de marcadores da tabela permite filtrar, resumir e juntar dados com outros dados, facilitando a procura de provas corroborantes.

Atualmente em pré-visualização, se encontrar algo que precise urgentemente de ser abordado enquanto caça nos seus registos, em alguns cliques, pode criar um marcador e promovê-lo a um incidente, ou adicionar o marcador a um incidente existente. Para obter mais informações sobre incidentes, consulte [Tutorial: Investigue incidentes com Azure Sentinel.](tutorial-investigate-cases.md) 

Também em pré-visualização, pode visualizar os seus dados reservados, clicando **em Investigar** a partir dos detalhes do marcador. Isto lança a experiência de investigação na qual você pode ver, investigar e comunicar visualmente as suas descobertas usando um diagrama e linha do tempo de gráfico de entidades interativas.

## <a name="add-a-bookmark"></a>Adicionar um marcador

1. No portal Azure, **Sentinel** navegue até à  >  **gestão da Sentinel Threat**  >  **Hunting** para executar consultas por comportamento suspeito e anómalo.

2. Selecione uma das consultas de caça e à direita, nos detalhes da consulta de caça, selecione **Run Consulta**. 

3. Selecione **Ver resultados de consulta**. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![ver resultados de consulta de Azure Sentinel caça](./media/bookmarks/new-processes-observed-example.png)
    
    Esta ação abre os resultados da consulta no **painel de registos.**

4. A partir da lista de resultados da consulta de registo, utilize as caixas de verificação para selecionar uma ou mais linhas que contenham as informações que considera interessantes.

5. **Selecione Adicionar marcador:**
    
    > [!div class="mx-imgBorder"]
    > ![Adicione o marcador de caça à consulta](./media/bookmarks/add-hunting-bookmark.png)

6. À direita, no painel **de marcadores Add,** opcionalmente, atualize o nome do marcador, adicione tags e notas para ajudá-lo a identificar o que era interessante sobre o item.

7. Na secção **Informação de Consulta,** utilize as caixas de entrega para extrair informações dos resultados da consulta para os tipos de entidades **de endereços Conta,** **Anfitrião** e **IP.** Esta ação mapeia o tipo de entidade selecionada para uma coluna específica a partir do resultado da consulta. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Tipos de entidade de mapa para marcador de caça](./media/bookmarks/map-entity-types-bookmark.png)
    
    Para visualizar o marcador no gráfico de investigação (atualmente em pré-visualização), deve mapear pelo menos um tipo de entidade que seja **conta,** **endereço anfitrião** ou **IP**. 

5. Clique **em Guardar** para cometer as suas alterações e adicionar o marcador. Todos os dados reservados são partilhados com outros investigadores, e é um primeiro passo para uma experiência de investigação colaborativa.

 
> [!NOTE]
> Os resultados da consulta de registo suportam marcadores sempre que este painel é aberto a partir de Azure Sentinel. Por exemplo, **General** seleciona  >  **Registos Gerais** da barra de navegação, selecione links de eventos no gráfico de investigações ou selecione um ID de alerta a partir de todos os detalhes de um incidente (atualmente em pré-visualização). Não é possível criar marcadores quando o painel **de registos** é aberto a partir de outros locais, como é o caso do Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Ver e atualizar marcadores 

1. No portal Azure, **Sentinel** navegue para a  >  **Sentinel Threat Management**  >  **Hunting**. 

2. Selecione o **separador Marcadores** para ver a lista de marcadores.

3. Para ajudá-lo a encontrar um marcador específico, use a caixa de pesquisa ou opções de filtro.

4. Selecione os marcadores individuais e veja os detalhes do marcador no painel de detalhes da mão direita.

5. Faça as alterações conforme necessário, que são automaticamente guardadas.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Explorando marcadores no gráfico de investigação

> [!IMPORTANT]
> Explorando os marcadores no gráfico de investigação e o próprio gráfico de investigação estão atualmente em pré-visualização pública.
> Estas características são fornecidas sem um contrato de nível de serviço, e não recomendadas para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. No portal Azure, **Sentinel** navegue no  >  **separador** Hunting Bookmarks de gestão de ameaças  >  **Hunting**  >  **Bookmarks** sentinelas e selecione o marcador ou marcadores que pretende investigar.

2. Nos detalhes do marcador, certifique-se de que pelo menos uma entidade está mapeada. Por exemplo, para **ENTIDADES**, consulte as entradas para **IP,** **Máquina,** ou **Conta.**

3. Clique **em Investigar** para ver o marcador no gráfico de investigação.

Para obter instruções para utilizar o gráfico de investigação, consulte [use o gráfico de investigação para mergulhar profundamente](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Adicione marcadores a um incidente novo ou existente

> [!IMPORTANT]
> A adição de marcadores a um incidente novo ou existente está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. No portal Azure, **Sentinel** navegue no  >  separador Hunting Bookmarks de **gestão de ameaças**  >  **Hunting**  >  **Bookmarks** sentinelas e selecione o marcador ou marcadores que pretende adicionar a um incidente.

2. Selecione **as ações de incidente (pré-visualização)** da barra de comando:
    
    > [!div class="mx-imgBorder"]
    > ![Adicione marcadores ao incidente](./media/bookmarks/incident-actions.png)

3. Selecione **criar um novo incidente** ou adicionar ao incidente **existente,** conforme necessário. Em seguida:
    
    - Para um novo incidente: Atualizar opcionalmente os detalhes do incidente e, em seguida, selecionar **Criar**.
    - Para adicionar um marcador a um incidente existente: Selecione um incidente e, em seguida, **selecione Add**. 

Para ver o marcador dentro do incidente: Navegue para **a Sentinel**  >  **Threat Management**  >  **Incidents** e selecione o incidente com o seu marcador. Selecione **Ver todos os detalhes** e, em seguida, selecione o **separador Marcadores.**

> [!TIP]
> Como alternativa à opção **De ações de Incidente (Pré-visualização)** na barra de comando, pode utilizar o menu de contexto **(...**) para um ou mais marcadores para selecionar opções para **criar novo incidente,** **Adicionar ao incidente existente,** e **Remover do incidente.** 

## <a name="view-bookmarked-data-in-logs"></a>Ver dados marcados em registos

Para visualizar consultas, resultados ou históricos com marcadores, selecione o marcador no separador **Marcadores** de Caça  >  **Bookmarks** e utilize os links fornecidos no painel de detalhes: 

- Consulte a **consulta de origem** para visualizar a consulta de origem no painel **de registos.**

- **Ver registos de marcadores** para ver todos os metadados de marcação de livros, que inclui quem fez a atualização, os valores atualizados e a hora em que a atualização ocorreu.

Também pode ver os dados do marcador em bruto para todos os marcadores, selecionando Registos de **Marcação** de Livros a partir da barra de comando no separador **Marcadores**  >  **de** Caça:

> [!div class="mx-imgBorder"]
> ![Diários de marcação](./media/bookmarks/bookmark-logs.png)

Esta vista mostra todos os seus marcadores com metadados associados. Pode utilizar consultas [de linguagem de identificação de palavras-chave](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) para filtrar até à versão mais recente do marcador específico que procura.

> [!NOTE]
> Pode haver um atraso significativo (medido em minutos) entre o momento em que cria um marcador e quando é exibido no **separador Marcadores.**

## <a name="delete-a-bookmark"></a>Apagar um marcador
 
1.  No portal Azure, **Sentinel** navegue no  >  **separador** Hunting Bookmarks de gestão de ameaças  >  **Hunting**  >  **Bookmarks** sentinelas e selecione o marcador ou marcadores que pretende eliminar. 

2. Clique com o botão direito nas suas seleções e selecione a opção de eliminar o marcador ou marcadores. Por exemplo, **Elimine o marcador** se selecionar apenas um marcador e **eliminar 2 marcadores** se selecionar dois marcadores.
    
A eliminação do marcador retira o marcador da lista no **separador Bookmark.** A tabela **HuntingBookmark** para o seu espaço de trabalho Log Analytics continuará a conter entradas de marcadores anteriores, mas a última entrada mudará o valor **softDelete** para verdadeiro, facilitando a filtragem de marcadores antigos. A eliminação de um marcador não retira nenhuma entidade da experiência de investigação que esteja associada a outros marcadores ou alertas. 


## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu a dirigir uma investigação de caça usando marcadores em Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:


- [Caça proativamente por ameaças](hunting.md)
- [Use cadernos para executar campanhas de caça automatizadas](notebooks.md)