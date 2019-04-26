---
title: Estatísticas em tempo real na CDN do Azure | Documentos da Microsoft
description: Estatísticas em tempo real fornecem dados em tempo real sobre o desempenho da CDN do Azure, ao entregar conteúdo aos seus clientes.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: eb20630533735fb46ea7743be75448329281938a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334630"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estatísticas em tempo real na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Este documento explica as estatísticas em tempo real na CDN do Microsoft Azure.  Esta funcionalidade fornece dados em tempo real, como a largura de banda, Estados de cache e conexões simultâneas para o perfil de CDN ao entregar conteúdo aos seus clientes. Isto permite que a monitorização contínua do Estado de funcionamento do seu serviço em qualquer altura, incluindo eventos de go-live.

Os gráficos seguintes estão disponíveis:

* [Largura de banda](#bandwidth)
* [Códigos de estado](#status-codes)
* [Estados da cache](#cache-statuses)
* [Ligações](#connections)

## <a name="accessing-real-time-stats"></a>Acesso a estatísticas em tempo real
1. Na [Portal do Azure](https://portal.azure.com), navegue para o perfil de CDN.
   
    ![Painel do perfil CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. No painel de perfil da CDN, clique a **gerir** botão.
   
    ![Botão de gerir do painel do perfil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
3. Paire o rato sobre o **Analytics** separador, em seguida, coloque o cursor sobre o **estatísticas em tempo real** submenu.  Clique em **HTTP de objeto grande**.
   
    ![Portal de gestão de CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Os gráficos de estatísticas em tempo real são apresentados.

Cada um dos gráficos apresenta estatísticas em tempo real para o intervalo de tempo selecionado, a partir de quando a página for carregada.  Os gráficos atualizar automaticamente a cada segundo.  O **Atualizar gráfico** botão, se estiver presente, irá eliminar o gráfico, após o qual apresentará apenas os dados selecionados.

## <a name="bandwidth"></a>Largura de Banda
![Gráfico de largura de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

O **largura de banda** gráfico apresenta a quantidade de largura de banda utilizada para a plataforma atual ao longo do período de tempo selecionado. A parte sombreada do gráfico indica a utilização de largura de banda. A quantidade exata de largura de banda atualmente a ser utilizada é apresentada diretamente abaixo do gráfico de linha.

## <a name="status-codes"></a>Códigos de estado
![Gráfico de código de estado](./media/cdn-real-time-stats/cdn-status-codes.png)

O **códigos de estado** gráfico indica a frequência com que determinados códigos de resposta HTTP estão a ocorrer ao longo do período de tempo selecionado.

> [!TIP]
> Para obter uma descrição de cada opção de código de estado HTTP, consulte [códigos de estado de HTTP do Azure CDN](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

É apresentada uma lista de códigos de estado HTTP diretamente acima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número atual de ocorrências por segundo para esse código de estado. Por predefinição, uma linha é exibida para cada um desses códigos de estado no gráfico. No entanto, pode optar por monitorizar apenas os códigos de estado que têm uma importância especial para a sua configuração de CDN. Para tal, verifique os códigos de estado pretendido e desmarque todas as outras opções e clique em **Atualizar gráfico**. 

Pode ocultar temporariamente dados registados para um código de estado específico.  Na legenda diretamente abaixo do gráfico, clique o código de estado que pretende ocultar. O código de estado será ocultado imediatamente do gráfico. Clicar nesse código de estado novamente fará com que essa opção seja exibida novamente.

## <a name="cache-statuses"></a>Estados da cache
![Gráfico de Estados de cache](./media/cdn-real-time-stats/cdn-cache-status.png)

O **Estados de Cache** gráfico indica a frequência com que determinados tipos de Estados de cache estão a ocorrer ao longo do período de tempo selecionado. 

> [!TIP]
> Para obter uma descrição de cada opção de código de estado de cache, consulte [códigos de estado de Cache do Azure CDN](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

É apresentada uma lista de códigos de estado de cache diretamente acima do gráfico. Esta lista indica cada código de estado que pode ser incluído no gráfico de linha e o número atual de ocorrências por segundo para esse código de estado. Por predefinição, uma linha é exibida para cada um desses códigos de estado no gráfico. No entanto, pode optar por monitorizar apenas os códigos de estado que têm uma importância especial para a sua configuração de CDN. Para tal, verifique os códigos de estado pretendido e desmarque todas as outras opções e clique em **Atualizar gráfico**. 

Pode ocultar temporariamente dados registados para um código de estado específico.  Na legenda diretamente abaixo do gráfico, clique o código de estado que pretende ocultar. O código de estado será ocultado imediatamente do gráfico. Clicar nesse código de estado novamente fará com que essa opção seja exibida novamente.

## <a name="connections"></a>Ligações
![Gráfico de ligações](./media/cdn-real-time-stats/cdn-connections.png)

Este gráfico indica quantas ligações foram estabelecidas para os seus servidores de borda. Cada solicitação para um recurso que passa pelo nossos resultados CDN numa ligação.

## <a name="next-steps"></a>Próximos Passos
* Ser notificado com [alertas em tempo real na CDN do Azure](cdn-real-time-alerts.md)
* Aprofunde os seus conhecimentos com [relatórios HTTP avançados](cdn-advanced-http-reports.md)
* Analisar [padrões de utilização](cdn-analyze-usage-patterns.md)

