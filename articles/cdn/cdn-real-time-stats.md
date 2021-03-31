---
title: Estatísticas em tempo real em Azure CDN | Microsoft Docs
description: As estatísticas em tempo real fornecem dados em tempo real sobre o desempenho da Azure CDN ao entregar conteúdo aos seus clientes.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3af2e849aa6658e539b0b5bdbda4428cc28e5ce5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887231"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estatísticas em tempo real no Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição Geral
Este documento explica estatísticas em tempo real no Microsoft Azure CDN.  Esta funcionalidade fornece dados em tempo real, tais como largura de banda, estados de cache e ligações simultâneas ao seu perfil de CDN ao entregar conteúdo aos seus clientes. Isto permite uma monitorização contínua da saúde do seu serviço a qualquer momento, incluindo eventos ao vivo.

Estão disponíveis os seguintes gráficos:

* [Largura de banda](#bandwidth)
* [Códigos de Estado](#status-codes)
* [Estatuto cache](#cache-statuses)
* [Ligações](#connections)

## <a name="accessing-real-time-stats"></a>Aceder a estatísticas em tempo real
1. No [Portal Azure,](https://portal.azure.com)navegue pelo seu perfil CDN.
   
    ![Lâmina de perfil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão da lâmina de perfil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
3. Pairar sobre o **separador Analytics,** em seguida, pairar sobre o flyout **de estatísticas em tempo real.**  Clique em **HTTP Large Object**.
   
    ![Portal de gestão CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Os gráficos de estatísticas em tempo real são exibidos.

Cada um dos gráficos apresenta estatísticas em tempo real para o período de tempo selecionado, a partir do momento em que a página é carregada.  Os gráficos atualizam-se automaticamente a cada poucos segundos.  O botão **Refresh Graph,** se presente, limpará o gráfico, após o qual apenas apresentará os dados selecionados.

## <a name="bandwidth"></a>Largura de banda
![Gráfico de largura de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

O gráfico **de largura de banda** exibe a quantidade de largura de banda utilizada para a plataforma atual durante o período de tempo selecionado. A porção sombreada do gráfico indica o uso da largura de banda. A quantidade exata de largura de banda atualmente utilizada é apresentada diretamente abaixo do gráfico de linha.

## <a name="status-codes"></a>Código de Estado
![Gráfico de código de estado](./media/cdn-real-time-stats/cdn-status-codes.png)

O gráfico **de Códigos de Estado** indica com que frequência determinados códigos de resposta HTTP estão a ocorrer ao longo do período de tempo selecionado.

> [!TIP]
> Para obter uma descrição de cada opção de código de estado HTTP, consulte [códigos de estado HTTP HTTP DA Azure CDN](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Uma lista de códigos de estado HTTP é apresentada diretamente por cima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número atual de ocorrências por segundo para esse código de estado. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar apenas por monitorizar os códigos de estado que têm um significado especial para a sua configuração CDN. Para isso, verifique os códigos de estado pretendidos e limpe todas as outras opções e, em seguida, clique em **Refresh Graph**. 

Pode ocultar temporariamente os dados registados para um determinado código de estado.  A partir da legenda diretamente abaixo do gráfico, clique no código de estado que deseja esconder. O código de estado será imediatamente escondido do gráfico. Clicar novamente no código de estado fará com que essa opção seja novamente exibida.

## <a name="cache-statuses"></a>Estatuto cache
![Gráfico de Statuses cache](./media/cdn-real-time-stats/cdn-cache-status.png)

O gráfico **do Estado cache** indica com que frequência determinados tipos de estatutos de cache estão a ocorrer ao longo do período de tempo selecionado. 

> [!TIP]
> Para obter uma descrição de cada opção de código de estado de cache, consulte [os Códigos de Estado da Cache Azure CDN](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Uma lista de códigos de estado de cache é apresentada diretamente acima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número atual de ocorrências por segundo para esse código de estado. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar apenas por monitorizar os códigos de estado que têm um significado especial para a sua configuração CDN. Para isso, verifique os códigos de estado pretendidos e limpe todas as outras opções e, em seguida, clique em **Refresh Graph**. 

Pode ocultar temporariamente os dados registados para um determinado código de estado.  A partir da legenda diretamente abaixo do gráfico, clique no código de estado que deseja esconder. O código de estado será imediatamente escondido do gráfico. Clicar novamente no código de estado fará com que essa opção seja novamente exibida.

## <a name="connections"></a>Ligações
![Gráfico de conexões](./media/cdn-real-time-stats/cdn-connections.png)

Este gráfico indica quantas ligações foram estabelecidas nos seus servidores de borda. Cada pedido de um ativo que passa pelo nosso CDN resulta numa ligação.

## <a name="next-steps"></a>Passos Seguintes
* Seja notificado com [alertas em tempo real na Azure CDN](cdn-real-time-alerts.md)
* Cave mais fundo com [relatórios HTTP avançados](cdn-advanced-http-reports.md)
* Analisar [padrões de utilização](cdn-analyze-usage-patterns.md)

