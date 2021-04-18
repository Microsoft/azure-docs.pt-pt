---
title: Visão geral da disponibilidade de Insights de Aplicação
description: Crie testes web recorrentes para monitorizar a disponibilidade e capacidade de resposta da sua app ou website.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 3c2d248df98fcb9f75f5de9c05b9f600955cbac7
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600799"
---
# <a name="availability-tests-overview"></a>Visão geral dos testes de disponibilidade

Depois de ter implementado a sua aplicação web/website, pode configurar testes recorrentes para monitorizar a disponibilidade e capacidade de resposta. [O Application Insights](./app-insights-overview.md) envia pedidos web para a sua aplicação a intervalos regulares de pontos em todo o mundo. Pode alertá-lo se a sua aplicação não estiver a responder, ou se responder muito lentamente.

Pode configurar testes de disponibilidade para qualquer ponto final HTTP ou HTTPS que seja acessível a partir da Internet pública. Não tens de fazer alterações no website que estás a testar. Na verdade, nem sequer tem de ser um site que tens. Pode testar a disponibilidade de uma API REST de que o seu serviço depende.

## <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade

Existem três tipos de testes de disponibilidade:

* [Teste de urSA:](monitor-web-app-availability.md)Esta categoria tem dois testes simples que pode criar através do portal.
* [Teste web em várias etapas](availability-multistep.md): Uma gravação de uma sequência de pedidos web, que pode ser reproduzida para testar cenários mais complexos. Os testes web em várias etapas são criados na Visual Studio Enterprise e enviados para o portal para execução.
* [Testes de disponibilidade de faixas personalizadas](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): Se decidir criar uma aplicação personalizada para realizar testes de disponibilidade, o `TrackAvailability()` método pode ser usado para enviar os resultados para o Application Insights.

> [!IMPORTANT]
> Ambos, [teste de URL ping](monitor-web-app-availability.md) e teste web em [várias etapas](availability-multistep.md) dependem da infraestrutura pública de DNS da internet para resolver os nomes de domínio dos pontos finais testados. Isto significa que, se estiver a utilizar o DNS Privado, deve certificar-se de que todos os nomes de domínio do seu teste também são resolúveis pelos servidores de nomes de domínio público ou, quando não é possível, pode utilizar [testes de disponibilidade de faixas personalizadas.](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)

**Pode criar até 100 testes de disponibilidade por recurso Application Insights.**

## <a name="troubleshooting"></a>Resolução de problemas

Artigo dedicado [à resolução de problemas.](troubleshoot-availability.md)

## <a name="next-step"></a>Passo seguinte

* [Alertas de Disponibilidade](availability-alerts.md)
* [Testes web em várias etapas](availability-multistep.md)
* [Testes de URL](monitor-web-app-availability.md)
* [Crie e escdaure testes de disponibilidade personalizados utilizando funções Azure.](availability-azure-functions.md)