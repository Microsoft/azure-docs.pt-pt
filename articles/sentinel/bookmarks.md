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
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588693"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Acompanhe os dados durante a caça com o Azure Sentinel

A caça à ameaça normalmente requer rever montanhas de dados de registo à procura de provas de comportamento malicioso. Durante este processo, os investigadores encontram eventos que querem recordar, revisitar e analisar como parte da validação de potenciais hipóteses e compreender toda a história de um compromisso.

Os marcadores de caça em Azure Sentinel ajudam-no a fazê-lo, preservando as consultas que realizou no **Azure Sentinel - Logs,** juntamente com os resultados da consulta que considera relevantes. Também pode registar as suas observações contextuais e fazer referência às suas descobertas adicionando notas e etiquetas. Os dados marcados por livros são visíveis para si e para os seus companheiros de equipa para uma colaboração fácil.

Pode revisitar os seus dados marcados a qualquer momento no separador **Bookmarks** do painel **de Caça.** Pode utilizar opções de filtragem e pesquisa para encontrar rapidamente dados específicos para a sua investigação em curso. Em alternativa, pode ver os seus dados marcados diretamente na tabela **HuntingBookmark** no seu espaço de trabalho Log Analytics. Por exemplo:

> [!div class="mx-imgBorder"]
> ![ver tabela HuntingBookmark](./media/bookmarks/bookmark-table.png)

Ver marcadores da tabela permite-lhe filtrar, resumir e juntar dados marcados com outras fontes de dados, facilitando a procura de provas corroborantes.

Atualmente em pré-visualização, se encontrar algo que precisa urgentemente de ser abordado enquanto caça nos seus registos, em alguns cliques, pode criar um marcador e promovê-lo a um incidente, ou adicionar o marcador a um incidente existente. Para mais informações sobre incidentes, consulte [Tutorial: Investigue incidentes com Azure Sentinel.](tutorial-investigate-cases.md) 

Também em pré-visualização, pode visualizar os seus dados com marca ção, clicando em **Investigar** a partir dos detalhes do marcador. Isto lança a experiência de investigação na qual você pode ver, investigar e comunicar visualmente as suas descobertas usando um diagrama interativo de gráfico de entidade e linha de tempo.

## <a name="add-a-bookmark"></a>Adicionar um marcador

1. No portal Azure, navegue até à**gestão** > de ameaças **sentinelas** > **Caça** para executar consultas por comportamento suspeito e anómalo.

2. Selecione uma das consultas de caça e à direita, nos detalhes da consulta de caça, selecione **Run Query**. 

3. Selecione **Ver resultados**de consulta . Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![ver resultados da consulta da caça do Sentinel a Azure](./media/bookmarks/new-processes-observed-example.png)
    
    Esta ação abre os resultados da consulta no painel de **registos.**

4. A partir da lista de resultados da consulta de registo, utilize as caixas de verificação para selecionar uma ou mais linhas que contenham as informações que achar interessantes.

5. Selecione Adicionar marcador de **livro:**
    
    > [!div class="mx-imgBorder"]
    > ![Adicione o marcador de caça à consulta](./media/bookmarks/add-hunting-bookmark.png)

6. À direita, no painel **Add bookmark,** opcionalmente, atualizar o nome do marcador, adicionar etiquetas e notas para ajudá-lo a identificar o que era interessante sobre o item.

7. Na secção Informação de **Consulta,** utilize as caixas de entrega para extrair informações dos resultados da consulta para os tipos de entidades de **endereços De conta,** **anfitrião**e **IP.** Esta ação mapeia o tipo de entidade selecionada para uma coluna específica a partir do resultado da consulta. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Tipos de entidades de mapas para marcador de caça](./media/bookmarks/map-entity-types-bookmark.png)
    
    Para ver o marcador no gráfico de investigação (atualmente em pré-visualização), deve mapear pelo menos um tipo de entidade que seja **conta,** **anfitrião**ou **endereço IP**. 

5. Clique em **Guardar** para comprometer as suas alterações e adicionar o marcador. Todos os dados com livros são partilhados com outros investigadores, e é um primeiro passo para uma experiência de investigação colaborativa.

 
> [!NOTE]
> Os resultados da consulta de log resultam nos marcadores sempre que este painel é aberto a partir de Azure Sentinel. Por exemplo, seleciona**Registos** **Gerais** > da barra de navegação, selecione links de evento no gráfico de investigações ou selecione um ID de alerta a partir de todos os detalhes de um incidente (atualmente em pré-visualização). Não é possível criar marcadores quando o painel de **registos** é aberto a partir de outros locais, como diretamente do Monitor Azure.

## <a name="view-and-update-bookmarks"></a>Ver e atualizar os favoritos 

1. No portal Azure, navegue para a**caça à****caça** > de ameaça **sentinela.** >  

2. Selecione o separador **Marcadores** para ver a lista de marcadores.

3. Para ajudá-lo a encontrar um marcador específico, utilize a caixa de pesquisa ou as opções de filtro.

4. Selecione marcadores individuais e veja os detalhes do marcador no painel de detalhes da mão direita.

5. Faça as alterações conforme necessário, que são automaticamente guardados.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Explorando os favoritos no gráfico de investigação

> [!IMPORTANT]
> Explorar os marcadores no gráfico de investigação e o gráfico de investigação em si estão atualmente em pré-visualização pública.
> Estas características são fornecidas sem um acordo de nível de serviço, e não recomendadas para cargas de trabalho de produção.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

1. No portal Azure, navegue para**Bookmarks** o**separador Canção** > De Caça de Caça de**Gestão** >  **de Ameaças Sentinela** > e selecione o marcador ou os marcadores que pretende investigar.

2. Nos detalhes do marcador, certifique-se de que pelo menos uma entidade está mapeada. Por exemplo, para **entidades,** vê entradas para **IP,** **Máquina**ou **Conta**.

3. Clique em **Investigar** para ver o marcador no gráfico de investigação.

Para obter instruções para utilizar o gráfico de investigação, consulte [Utilize o gráfico de investigação para mergulhar profundamente](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Adicione marcadores a um incidente novo ou existente

> [!IMPORTANT]
> A adição de marcadores a um incidente novo ou existente está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

1. No portal Azure, navegue para o separador De Caça de Caça de**Bookmarks** **Gestão** > **Hunting** > de Ameaças **Sentinela** > e selecione o marcador ou marcadores que pretende adicionar a um incidente.

2. Selecione **as ações incidentais (Pré-visualização)** a partir da barra de comando:
    
    > [!div class="mx-imgBorder"]
    > ![Adicione marcadores ao incidente](./media/bookmarks/incident-actions.png)

3. Selecione **Criar um novo incidente** ou adicionar ao incidente **existente,** conforme necessário. Em seguida:
    
    - Para um novo incidente: Atualize opcionalmente os detalhes do incidente e, em seguida, selecione **Criar**.
    - Para adicionar um marcador a um incidente existente: Selecione um incidente e, em seguida, selecione **Adicionar**. 

Para ver o marcador dentro do incidente: Navegue para os**incidentes** de**gestão** > de ameaças **de sentinela** > e selecione o incidente com o seu marcador. Selecione **Ver todos os detalhes**e, em seguida, selecione o separador **Marcadores.**

> [!TIP]
> Como alternativa à opção **incident actions (Preview)** na barra de comando, pode utilizar o menu de contexto (**...**) para um ou mais marcadores para selecionar opções para criar **novos incidentes,** adicionar ao **incidente existente,** e **remover do incidente**. 

## <a name="view-bookmarked-data-in-logs"></a>Ver dados marcados em diários de sessão

Para ver consultas, resultados ou o seu histórico, selecione o marcador do separador **Hunting** > **Bookmarks** e utilize os links fornecidos no painel de detalhes: 

- **Consulte** a consulta de origem para ver a consulta de origem no painel de **registos.**

- Consulte os registos de **marcadores** para ver todos os metadados de marca de livros, que inclui quem fez a atualização, os valores atualizados e a hora em que a atualização ocorreu.

Também pode ver os dados de marcadores brutos para todos os marcadores, selecionando **Registos** de Marca de Livros a partir da barra de comando no separador**Marcadores** **de Caça:** > 

> [!div class="mx-imgBorder"]
> ![Registos de marca de livros](./media/bookmarks/bookmark-logs.png)

Esta vista mostra todos os seus marcadores com metadados associados. Pode utilizar consultas de linguagem de consulta de [palavras-chave](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) para filtrar até à versão mais recente do marcador específico que procura.

> [!NOTE]
> Pode haver um atraso significativo (medido em minutos) entre o momento em que cria um marcador e quando é exibido no separador **Bookmarks.**

## <a name="delete-a-bookmark"></a>Apagar um marcador
 
1.  No portal Azure, navegue para o separador De Caça de Caça de**Bookmarks** **Gestão** > **Hunting** > de Ameaças **Sentinela** > e selecione o marcador ou os marcadores que pretende eliminar. 

2. Clique nas suas seleções e selecione a opção de eliminar o marcador ou os marcadores. Por exemplo, **elimine o marcador** se selecionasse apenas um marcador e **apague 2 marcadores** se selecionasse dois marcadores.
    
A eliminação do marcador remove o marcador da lista no separador **Bookmark.** A tabela **HuntingBookmark** para o seu espaço de trabalho Log Analytics continuará a conter entradas anteriores, mas a última entrada irá alterar o valor **SoftDelete** para verdadeiro, facilitando a filtragem de marcas antigas. A eliminação de um marcador não remove nenhuma entidade da experiência de investigação que esteja associada a outros marcadores ou alertas. 


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a dirigir uma investigação de caça usando marcadores em Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Caça proativamente por ameaças](hunting.md)
- [Use cadernos para executar campanhas de caça automatizadas](notebooks.md)
