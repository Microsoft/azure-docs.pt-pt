---
title: 'Referência de API do v2 de Monitor do Estado do Azure: Ativar o motor de instrumentação | Documentos da Microsoft'
description: Estado Monitor v2 API referência Enable-InstrumentationEngine. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações Web ASP.NET alojadas no local, em VMs ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: e993378634262de25449975431c0a9e3145ca9fb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255252"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>API de v2 de Monitor de estado: Enable-InstrumentationEngine (v0.2.1-alpha)

Este documento descreve um cmdlet que é enviado como parte da [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Descrição

Este cmdlet irá ativar o mecanismo de instrumentação definindo algumas chaves de Registro.
Reinicie o IIS para que essas alterações entrem em vigor.

O mecanismo de instrumentação pode complementar os dados recolhidos pelos SDKs do .NET.
Eventos e as mensagens serão recolhidas que descrevem a execução de um processo gerenciado. Incluindo mas não limitado a códigos de resultado de dependência, verbos HTTP e texto do comando SQL. 

Ative o mecanismo de instrumentação se:
- Já tiver ativado a monitorização utilizando o Enable cmdlet, mas não tiver ativado o InstrumentationEngine.
- Manualmente tiver instrumentado a sua aplicação com os SDKs do .NET e pretende recolher telemetria adicional.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

> [!NOTE] 
> Este cmdlet irá solicitar-lhe rever e aceitar a nossa declaração de licença e de privacidade.

> [!NOTE] 
> O mecanismo de instrumentação adiciona sobrecarga adicional e está desativada por predefinição.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parâmetros 

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Utilize este parâmetro para aceitar a licença e declaração de privacidade em instalações sem periféricos.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este parâmetro para a saída de registos detalhados.

## <a name="output"></a>Saída


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exemplo da saída da ativar com êxito o mecanismo de instrumentação

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Passos Seguintes

  Ver a telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e a utilização
- [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas
- [Análise](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md)
 
 Adicionar mais telemetria:
 - [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para permitir a inserção de chamadas de rastreio.
- [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo
 
 Faça mais com a v2 de Monitor de estado:
 - Utilize o nosso guia para [resolução de problemas](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
 - [Obter a configuração de](status-monitor-v2-api-get-config.md) para confirmar que as suas definições foram registadas corretamente.
 - [Obter o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
