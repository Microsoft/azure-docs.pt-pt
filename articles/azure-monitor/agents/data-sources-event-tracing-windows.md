---
title: Recolha de rastreio de eventos para windows (ETW) Eventos para análise Registos do Monitor Azure
description: Saiba como recolher o Rastreio de Eventos para Windows (ETW) para análise em Registos monitores Azure.
services: azure-monitor
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 73135d95a56dc03790b3b7368a276ebfc99275fd
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025072"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Recolha de rastreio de eventos para windows (ETW) Eventos para análise Registos do Monitor Azure

*O Rastreio de Eventos para Windows (ETW)* fornece um mecanismo para instrumentação de aplicações de modo de utilizador e controladores em modo kernel. O agente Log Analytics é utilizado para [recolher eventos Windows escritos](./data-sources-windows-events.md) nos canais Administrativo e Operacional [DA ETW](/windows/win32/wes/eventmanifestschema-channeltype-complextype). No entanto, é ocasionalmente necessário capturar e analisar outros eventos, como os escritos para o canal Analítico.  

## <a name="event-flow"></a>Fluxo de eventos

Para recolher com sucesso [eventos ETW baseados em manifesto](/windows/win32/etw/about-event-tracing#types-of-providers) para análise em Registos do Monitor Azure, deve utilizar a extensão de [diagnóstico Azure](./diagnostics-extension-overview.md) para windows (WAD). Neste cenário, a extensão de diagnóstico atua como consumidor da ETW, escrevendo eventos para a Azure Storage (tabelas) como uma loja intermédia. Aqui será guardado numa tabela chamada **WADETWEventTable**. O Log Analytics recolhe então os dados da tabela do armazenamento Azure, apresentando-os como uma tabela chamada **ETWEvent**.

![Fluxo de eventos](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>Configuração da coleção de registos ETW

### <a name="step-1-locate-the-correct-etw-provider"></a>Passo 1: Localizar o fornecedor ETW correto

Utilize qualquer um dos seguintes comandos para enumerar os fornecedores DAEW num sistema windows de origem.

Linha de comando:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Opcionalmente, pode optar por canalizar esta saída PowerShell para Out-Gridview para ajudar na navegação.

Grave o nome do fornecedor ETW e o GUID que se alinha com o registo Analítico ou Debug apresentado no Visualizador de Eventos, ou com o módulo para o qual pretende recolher dados de eventos.

### <a name="step-2-diagnostics-extension"></a>Passo 2: Extensão de diagnóstico

Certifique-se de que a *extensão de diagnóstico* do Windows está [instalada](./diagnostics-extension-windows-install.md#install-with-azure-portal) em todos os sistemas de origem.

### <a name="step-3-configure-etw-log-collection"></a>Passo 3: Configurar a coleção de registos DAW

1. Navegue para a lâmina **de Definições** de Diagnóstico da máquina virtual

2. Selecione o **separador Registars**

3. Percorra para baixo e ative a **opção de rastreio de eventos do Windows (ETW)** ![ Screenshot das definições de diagnóstico](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Desafie o fornecedor GUID ou a classe fornecedor com base no fornecedor para o que está a configurar a recolha

5. Desapropusea o [**nível de registo**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session) conforme apropriado

6. Clique na elipse adjacente ao fornecedor fornecido e clique em **Configurar**

7. Certifique-se de que a **tabela de destino padrão** está definida para **etweventtable**

8. Desa estação um [**filtro de palavras-chave,**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) se necessário

9. Guarde as definições do fornecedor e do registo

Uma vez gerados eventos correspondentes, você deve começar a ver os eventos ETW que aparecem na tabela **WADetweventtable** no Azure Storage. Pode utilizar o Azure Storage Explorer para confirmar isto.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Passo 4: Configurar a recolha de conta de armazenamento Log Analytics

Siga [estas instruções](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) para recolher os registos do Azure Storage. Uma vez configurados, os dados do evento ETW devem aparecer no Log Analytics sob a tabela **ETWEvent.**

## <a name="next-steps"></a>Passos seguintes
- Use [campos personalizados](../logs/custom-fields.md) para criar estrutura nos seus eventos ETW
- Saiba mais [sobre consultas de registo](../logs/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.