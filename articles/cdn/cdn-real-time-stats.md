---
title: Estatísticas em tempo real no Azure CDN / Microsoft Docs
description: As estatísticas em tempo real fornecem dados em tempo real sobre o desempenho do Azure CDN ao entregar conteúdo aos seus clientes.
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
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593496"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estatísticas em tempo real no CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Este documento explica estatísticas em tempo real no CDN do Microsoft Azure.  Esta funcionalidade fornece dados em tempo real, tais como largura de banda, estado de cache e conexões simultâneas ao seu perfil CDN ao entregar conteúdo aos seus clientes. Isto permite uma monitorização contínua da saúde do seu serviço a qualquer momento, incluindo eventos go-live.

Estão disponíveis os seguintes gráficos:

* [Largura de banda](#bandwidth)
* [Códigos de Estado](#status-codes)
* [Estados de Cache](#cache-statuses)
* [Ligações](#connections)

## <a name="accessing-real-time-stats"></a>Aceder a estatísticas em tempo real
1. No [Portal Azure,](https://portal.azure.com)navegue para o seu perfil De CDN.
   
    ![Lâmina de perfil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão da lâmina de perfil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Abre o portal de gestão da CDN.
3. Paire sobre o separador **Analytics** e, em seguida, paire sobre as **estatísticas em tempo real.**  Clique em **HTTP Large Object**.
   
    ![Portal de gestão da CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Os gráficos de estatísticas em tempo real são apresentados.

Cada um dos gráficos apresenta estatísticas em tempo real para o período de tempo selecionado, a partir do momento em que a página carrega.  Os gráficos atualizam-se automaticamente a cada poucos segundos.  O botão **Refresh Graph,** se presente, limpará o gráfico, após o qual apenas apresentará os dados selecionados.

## <a name="bandwidth"></a>Largura de banda
![Gráfico de largura de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

O gráfico **de largura de banda** apresenta a quantidade de largura de banda utilizada para a plataforma atual durante o período de tempo selecionado. A porção sombreada do gráfico indica o uso da largura de banda. A quantidade exata de largura de banda atualmente utilizada é exibida diretamente abaixo do gráfico de linha.

## <a name="status-codes"></a>Código de Estado
![Gráfico de código de estado](./media/cdn-real-time-stats/cdn-status-codes.png)

O gráfico **de Códigos** de Estado indica a frequência com que determinados códigos de resposta HTTP estão a ocorrer ao longo do período de tempo selecionado.

> [!TIP]
> Para obter uma descrição de cada opção de código de estado HTTP, consulte os Códigos de [Estado do Azure CDN HTTP](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Uma lista de códigos de estado HTTP é apresentada diretamente acima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número atual de ocorrências por segundo para esse código de estado. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar por monitorizar apenas os códigos de estado que têm um significado especial para a sua configuração DeCDN. Para isso, verifique os códigos de estado desejados e limpe todas as outras opções, em seguida, clique em **Refresh Graph**. 

Pode ocultar temporariamente os dados registados para um determinado código de estado.  A partir da lenda diretamente abaixo do gráfico, clique no código de estado que pretende esconder. O código de estado será imediatamente escondido do gráfico. Clicar novamente nesse código de estado fará com que essa opção volte a ser exibida.

## <a name="cache-statuses"></a>Estados de Cache
![Gráfico de Estados cache](./media/cdn-real-time-stats/cdn-cache-status.png)

O gráfico **Cache Statuses** indica com que frequência certos tipos de cache socorrem ao longo do tempo selecionado. 

> [!TIP]
> Para obter uma descrição de cada opção de código de estado de cache, consulte os Códigos de [Estado do Cache Azure CDN](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Uma lista de códigos de estado de cache é apresentada diretamente acima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número atual de ocorrências por segundo para esse código de estado. Por predefinição, é apresentada uma linha para cada um destes códigos de estado no gráfico. No entanto, pode optar por monitorizar apenas os códigos de estado que têm um significado especial para a sua configuração DeCDN. Para isso, verifique os códigos de estado desejados e limpe todas as outras opções, em seguida, clique em **Refresh Graph**. 

Pode ocultar temporariamente os dados registados para um determinado código de estado.  A partir da lenda diretamente abaixo do gráfico, clique no código de estado que pretende esconder. O código de estado será imediatamente escondido do gráfico. Clicar novamente nesse código de estado fará com que essa opção volte a ser exibida.

## <a name="connections"></a>Ligações
![Gráfico de conexões](./media/cdn-real-time-stats/cdn-connections.png)

Este gráfico indica quantas ligações foram estabelecidas nos seus servidores de borda. Cada pedido de um ativo que passa através do nosso CDN resulta numa ligação.

## <a name="next-steps"></a>Passos Seguintes
* Seja notificado com [alertas em tempo real no Azure CDN](cdn-real-time-alerts.md)
* Cave mais fundo com [relatórios avançados](cdn-advanced-http-reports.md) do HTTP
* Analisar padrões de [utilização](cdn-analyze-usage-patterns.md)

