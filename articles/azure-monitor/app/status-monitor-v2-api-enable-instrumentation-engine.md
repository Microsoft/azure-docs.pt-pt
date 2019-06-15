---
title: 'Referência de API do v2 de Monitor de estado do Azure: Ativar o motor de instrumentação | Documentos da Microsoft'
description: Referência da API v2 de Monitor de estado. Enable-InstrumentationEngine. Monitorizar o desempenho dos Web sites sem implementar novamente o Web site. Funciona com aplicações web ASP.NET alojadas no local, em VMs ou no Azure.
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514374"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>API de v2 de Monitor de estado: Enable-InstrumentationEngine (v0.2.1-alpha)

Este artigo descreve um cmdlet que faz parte do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> V2 de Monitor de estado está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não o recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas e algumas podem ter capacidades restringidas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrição

Permite que o mecanismo de instrumentação definindo algumas chaves de Registro.
Reinicie o IIS para que as alterações entrem em vigor.

O mecanismo de instrumentação pode complementar os dados recolhidos pelos SDKs do .NET.
Recolhe eventos e as mensagens que descrevem a execução de um processo gerenciado. Estes eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e texto do comando SQL.

Ative o mecanismo de instrumentação se:
- Já tiver ativado a monitorização com o cmdlet Enable, mas não tiver ativado o mecanismo de instrumentação.
- Manualmente tiver instrumentado a sua aplicação com os SDKs do .NET e pretende recolher telemetria adicional.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

> [!NOTE] 
> - Este cmdlet requer que reveja e aceite a nossa declaração de licença e de privacidade.
> - O mecanismo de instrumentação adiciona sobrecarga adicional e está desativada por predefinição.

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
 - [Explorar métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e utilização.
- [Pesquise eventos e registos](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- Uso [analytics](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas.
- [Criar dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Adicionar mais telemetria:
 - [Criar testes web](monitor-web-app-availability.md) para se certificar de que mantém o seu site em direto.
- [Adicionar telemetria de cliente web](../../azure-monitor/app/javascript.md) para ver as exceções de código de página da web e para ativar chamadas de rastreio.
- [Adicionar o Application Insights SDK ao seu código](../../azure-monitor/app/asp-net.md) para que possa inserir o rastreio e chamadas de registo.
 
 Faça mais com a v2 de Monitor de estado:
 - Utilize o nosso guia para [resolver problemas de](status-monitor-v2-troubleshoot.md) v2 de Monitor de estado.
 - [Obter a configuração de](status-monitor-v2-api-get-config.md) para confirmar que as suas definições foram registadas corretamente.
 - [Obter o estado](status-monitor-v2-api-get-status.md) para inspecionar a monitorização.
