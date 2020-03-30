---
title: Referência da API do Agente de Insights de Aplicação Azure
description: Referência da API do Agente Insights de Aplicação Insights. Motor de instrumentação de ativação. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671362"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Agente de Insights de Aplicação API: Motor de instrumentação de ativação

Este artigo descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Ativa o motor de instrumentação, definindo algumas teclas de registo.
Reinicie o IIS para que as alterações entrem em vigor.

O motor de instrumentação pode complementar os dados recolhidos pelos SDKs .NET.
Recolhe eventos e mensagens que descrevem a execução de um processo gerido. Estes eventos e mensagens incluem códigos de resultados de dependência, verbos HTTP e texto de [comando SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Ativar o motor de instrumentação se:
- Já ativou a monitorização com o cmdlet Enable, mas não ativou o motor de instrumentação.
- Você instrumentou manualmente a sua aplicação com os SDKs .NET e quer recolher telemetria adicional.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de Administrador.

> [!NOTE] 
> - Este cmdlet requer que você reveja e aceite a nossa declaração de licença e privacidade.
> - O motor de instrumentação adiciona sobrecarga adicional e está desligado por defeito.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parâmetros

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Utilize este interruptor para aceitar a declaração de licença e privacidade em instalações sem cabeça.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para registos detalhados de saída.

## <a name="output"></a>Saída


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Saída de exemplo de permitir com sucesso o motor de instrumentação

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Passos seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e o uso.
- [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Use [analítica](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Criar testes Web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicione telemetria](../../azure-monitor/app/javascript.md) de cliente web para ver exceções do código da página web e para ativar chamadas de rastreio.
- [Adicione o SDK de Insights de Aplicação ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir chamadas de rastreio e log.
 
 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
 - [Faça com que o config](status-monitor-v2-api-get-config.md) confirme que as suas definições foram gravadas corretamente.
 - [Obtenha o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
