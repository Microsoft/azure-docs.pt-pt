---
title: Use listas de observação Azure Sentinel
description: Este artigo descreve como usar as listas de observação do Azure Sentinel investigam ameaças, importam dados de negócios, criam listas de permitir e enriquecem dados de eventos.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 25252b73f25a96f85d5e2cf1d68b76f9eaa3ca75
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979804"
---
# <a name="use-azure-sentinel-watchlists"></a>Use listas de observação Azure Sentinel

As listas de observação do Azure Sentinel permitem a recolha de dados de fontes de dados externas para a correlação com os eventos no seu ambiente Azure Sentinel. Uma vez criado, pode utilizar listas de vigilância na sua pesquisa, regras de deteção, caça a ameaças e reprodução de resposta. As listas de observação são armazenadas no seu espaço de trabalho Azure Sentinel como pares de valor-nome e estão em cache para um desempenho de consulta ideal e baixa latência.

Os cenários comuns para a utilização de listas de observação incluem:

- **Investigar ameaças** e responder rapidamente a incidentes com a rápida importação de endereços IP, hashes de ficheiros e outros dados de ficheiros CSV. Uma vez importados, pode utilizar pares de nomes de lista de observação para junções e filtros em regras de alerta, caça a ameaças, livros de trabalho, cadernos e consultas gerais.

- **Importar dados de negócios** como listas de observação. Por exemplo, importar listas de utilizadores com acesso privilegiado ao sistema ou funcionários despedidos, e depois usar a lista de observação para criar listas de permitir e negar listas usadas para detetar ou impedir que esses utilizadores acedam à rede.

- **Redução da fadiga de alerta**. Crie listas de permitir suprimir alertas de um grupo de utilizadores, como utilizadores de endereços IP autorizados que executam tarefas que normalmente desencadeariam o alerta e evitar que eventos benignos se tornem alertas.

- **Enriquecendo os dados do evento.** Utilize listas de observação para enriquecer os dados do seu evento com combinações de valor-nome derivadas de fontes de dados externas.

## <a name="create-a-new-watchlist"></a>Criar uma nova lista de observação

1. A partir do portal Azure, navegue até a Lista de Observação de Configuração **Azure Sentinel**  >  **Configuration**  >  **Watchlist** e, em seguida, selecione **Adicionar novo**.

    > [!div class="mx-imgBorder"]
    > ![nova lista de observação](./media/watchlists/sentinel-watchlist-new.png)

1. Na página **geral,** forneça o nome, descrição e pseudónimo para a lista de observação e, em seguida, selecione **Seguinte**.

    > [!div class="mx-imgBorder"]
    > ![página geral de lista de relógios](./media/watchlists/sentinel-watchlist-general.png)

1. Na página **'Origem',** selecione o tipo de conjunto de dados, carreque um ficheiro e, em seguida, selecione **Seguinte**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="página de origem de lista de relógios" lightbox="./media/watchlists/sentinel-watchlist-source.png":::


1. Reveja as informações, verifique se está correta e, em seguida, **selecione Criar**.

    > [!div class="mx-imgBorder"]
    > ![página de revisão de lista de relógios](./media/watchlists/sentinel-watchlist-review.png)

    Uma notificação aparece assim que a lista de observação é criada.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="página de origem de lista de relógios" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="use-watchlists-in-queries"></a>Use listas de observação em consultas

1. A partir do portal Azure, navegue para a Lista de Observações de Configuração **Azure Sentinel**  >  **Configuration**  >  **Watchlist**, selecione a lista de relógios que pretende utilizar e, em seguida, selecione **Ver em Log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="página de origem de lista de relógios" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::


1. Os itens na sua lista de observação são automaticamente extraídos para a sua consulta e aparecerão no **separador Resultados.** O exemplo abaixo mostra os resultados da extração dos campos **ServerName** e **IpAddress.**

    > [!NOTE]
    > A marca de tempo nas suas consultas será ignorada tanto na consulta como em alertas programados.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="página de origem de lista de relógios" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
## <a name="use-watchlists-in-analytics-rules"></a>Use listas de observação em regras de análise

Para utilizar listas de observação em regras de análise, a partir do portal Azure, navegue até **Azure Sentinel**  >  **Configuration**  >  **Analytics**, e crie uma regra utilizando a `_GetWatchlist('<watchlist>')` função na consulta.

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="página de origem de lista de relógios" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::


## <a name="view-list-of-watchlists-aliases"></a>Ver lista de pseudónimos de listas de relógios

Para obter uma lista de pseudónimos da lista de observação, a partir do portal Azure, navegue até **Azure Sentinel**  >  **General**  >  **Logs,** e faça a seguinte consulta: `_GetWatchlistAlias` . Pode ver a lista de pseudónimos no separador **Resultados.**

> [!div class="mx-imgBorder"]
> ![listas de observação listas](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a usar listas de vigilância no Azure Sentinel para enriquecer dados e melhorar as investigações. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

