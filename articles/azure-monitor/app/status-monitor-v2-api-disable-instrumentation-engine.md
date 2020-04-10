---
title: Motor de desativação de deficientes do agente de aplicação da aplicação Azure
description: Referência da API do Agente Insights de Aplicação Insights. Motor de desativação de instrumentação. Monitorize o desempenho do site sem reimplantar o website. Trabalha com ASP.NET aplicações web hospedadas no local, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998371"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Agente de Insights de Aplicação API: Motor de instrumentação para deficientes

Este artigo descreve um cmdlet que é membro do [módulo Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição
Desativa o motor de instrumentação removendo algumas teclas de registo.
Reinicie o IIS para que as alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão PowerShell com permissões de Administrador.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parâmetros 

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Utilize este interruptor para registos detalhados de saída.

## <a name="output"></a>Saída


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Saída de exemplo de desativar com sucesso o motor de instrumentação

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Passos seguintes

 Faça mais com o Agente de Insights de Aplicação:
 - Use o nosso guia para [resolver problemas](status-monitor-v2-troubleshoot.md) no Agente insights de aplicação.
