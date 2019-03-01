---
title: Mantenha um registo de dados ao buscar sentinela na pré-visualização no Azure usando indicadores de sorte! | Documentos da Microsoft
description: Este artigo descreve como utilizar os indicadores de buscar sentinela do Azure para manter o controle de dados.
services: sentinel
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 484acdc16f57f37098d669f03c1b51c087308289
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993196"
---
# <a name="keep-track-of-data-during-hunting"></a>Mantenha um registo de dados durante a sorte!

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Detecção de ameaças requer, normalmente, a revisão Montanhas de dados de registo à procura de provas de um comportamento malicioso. Durante este processo, investigators encontrar eventos que pretende não se esqueça, rever e analisar como parte das hipóteses possíveis de validação e compreender a história completa de um comprometimento.
Indicadores de buscar ajudar a fazê-lo, por preservando as consultas que executou no Log Analytics, juntamente com os resultados da consulta que considerar relevantes. Também pode gravar sua observações contextuais e fazer referência a suas descobertas, adicionando observações e etiquetas. Dados indicados são visíveis para e seus colegas de equipa para colaboração fácil.   

Pode regressar ao seus dados indicados em qualquer altura no **marcador** separador da **Hunting** página. Pode utilizar a filtragem e as opções para encontrar rapidamente os dados específicos para a sua investigação atual de procura. Em alternativa, pode ver os seus dados indicados diretamente no **HuntingBookmark** tabela no Log Analytics. Isto permite-lhe filtrar, resumir e associar dados indicados com outras origens de dados, tornando mais fácil procurar corroborating evidências.

Também pode visualizar os seus dados indicados, ao clicar em **investigar**. Esta ação inicia a experiência de investigação em que pode ver, investigar e visualmente comunicar suas descobertas usando um diagrama interativo de entidade-graph e a linha cronológica.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Executar uma consulta do Log Analytics a partir do Azure sentinela

1. No portal do Azure sentinela, clique em **Hunting** para executar consultas para o comportamento suspeito e anómala.

1. Para executar uma campanha de procura, selecione uma das consultas sorte e a análise à esquerda, os resultados. 

1. Clique em **ver os resultados da consulta** na consulta de arquivo terá **detalhes** página para ver a consulta resulta no Log Analytics. Eis um exemplo do que vê se executou uma consulta de ataque de forçabruta SSH personalizada.
  
   ![Mostrar resultados](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Adicionar um marcador

1. Na lista de resultados de consulta do Log Analytics, expanda a linha que contém as informações que achar interessantes.

4. Selecione as reticências (...) no final da linha e selecione **adicionar indicadores de buscar**.
5. À direita, na **detalhes** página, atualize o nome e adicionar etiquetas e notas para o ajudar a identificar o que era interessante sobre o item.
6. Clique em **guardar** para consolidar as alterações. Todos os dados indicados é partilhada com outros investigators e é um primeiro passo na direção de uma experiência de investigação de colaboração.

   ![Mostrar resultados](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Também pode utilizar indicadores com as consultas do Log Analytics arbitrárias iniciadas a partir da página de registos de análise de registo sentinela do Azure ou consultas criadas na lista de opções da página do Log Analytics e aberta a partir da página de procura. Não será possível adicionar um marcador, se iniciar o Log Analytics de fora do Azure sentinela. 

## <a name="view-and-update-bookmarks"></a>Veja e atualize marcadores 

1. No portal do Azure sentinela, clique em **Hunting**. 
2. Clique nas **marcadores** separador no meio da página para ver a lista de marcadores.
3. Utilize as opções de filtro ou caixa de pesquisa para encontrar um marcador específico.
4. Selecione os indicadores individuais na grade abaixo para ver os detalhes de marcador no painel de detalhes à direita.
5. Para atualizar as etiquetas e notas, clique nas caixas de texto editável e clique em **guardar** para preservar as suas alterações.

   ![Mostrar resultados](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Um indicador de ver dados no Log Analytics 

Existem várias opções para ver os seus dados indicados no Log Analytics. 

A forma mais fácil de ver consultas indicadas, os resultados ou histórico é selecionar o marcador pretendido na **marcadores** de tabela e utilizar as ligações fornecidas no painel de detalhes. As opções incluem: 
- Clique em **consulta de vista** para ver a consulta de origem no Log Analytics.  
- Clique em **ver o histórico de marcador** para ver todos os marcadores de metadados, incluindo: quem as fez a atualização, os valores atualizados e o tempo que a atualização ocorreu. 

- Também pode ver os dados de marcador não processados de todos os indicadores, clicando em **registos de marcador** acima da grelha de marcador. Esta vista mostra todos os seus marcadores na tabela de marcador hunting com metadados associados. Pode utilizar consultas KQL para filtrar apenas a versão mais recente do indicador específica que está procurando.  


> [!NOTE]
> Pode haver atraso significativo (medido em minutos) entre a criação de um indicador e quando é apresentada no **HuntingBookmark** tabela. Recomenda-se para criar seus indicadores pela primeira vez, em seguida, analisá-los depois dos dados são ingeridos. 

## <a name="delete-a-bookmark"></a>Eliminar um indicador
Se pretender eliminar uma proceda de marcador do seguinte: 
1.  Abrir th **indicador Hunting** separador. 
2.  Selecione o indicador de destino.
3.  Selecione as reticências (...) no final da linha e selecione **eliminar indicador**.
    
A eliminar o indicador remove o indicador da lista na **indicador** separador.  O Log Analytics "HuntingBookmark" tabela irá continuar a conter entradas de marcador anterior, mas a entrada mais recente irá alterar o **SoftDelete** valor como true, tornando mais fácil filtrar indicadores antigos.  A eliminar um marcador não remove qualquer entidades da experiência de investigação que estão associados a outros indicadores ou alertas. 


## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a executar uma investigação de buscar utilizar marcadores no sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:


- [Proativamente hunt relativamente a ameaças](hunting.md)
- [Utilizar blocos de notas para executar as campanhas de detecção automática](notebooks.md)