---
title: Degradação na relação de severidade de vestígios - Azure Application Insights
description: Monitor rastreie os vestígios da aplicação com a Azure Application Insights para padrões incomuns em telemetria de traços com Deteção Inteligente .
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 2b27860adfc1652b58fe9c51d4d0b0a6c271fc0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86539878"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradação na relação de severidade do traço (pré-visualização)

Os vestígios são amplamente utilizados em aplicações, pois ajudam a contar a história do que acontece nos bastidores. Quando as coisas correm mal, os vestígios dão visibilidade crucial à sequência de eventos que conduzem ao estado indesejável. Embora os vestígios sejam geralmente desestruturados, há uma coisa que pode ser aprendido concretamente com eles – o seu nível de gravidade. No estado estável de uma aplicação, esperaríamos que a relação entre os vestígios "bons"*(Info* e *Verbose)* e os vestígios "maus"*(Aviso,* *Erro* e *Crítico)* se mantivesse estável. O pressuposto é que os vestígios "maus" podem ocorrer regularmente, em certa medida, devido a várias razões (questões transitórias da rede, por exemplo). Mas quando um problema real começa a crescer, geralmente manifesta-se como um aumento na proporção relativa de traços "maus" vs traços "bons". A Application Insights Smart Detection analisa automaticamente os vestígios registados pela sua aplicação e pode alertá-lo sobre padrões incomuns na gravidade da telemetria de traços.

Esta funcionalidade não requer nenhuma configuração especial, além de configurar o registo de vestígios para a sua aplicação (ver como configurar um ouvinte de registo de traços para [.NET](./asp-net-trace-logs.md) ou [Java).](./java-trace-logs.md) Está ativo quando a sua aplicação gera telemetria de exceção suficiente.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu receberia este tipo de notificação de deteção inteligente?
Poderá receber este tipo de notificação se a relação entre os vestígios "bons" (vestígios registados com um nível de *Info* ou *Verbose)* e os vestígios "maus" (vestígios registados com um nível de *Aviso,* *Erro* ou *Fatal)* for degradante num dia específico, em comparação com uma linha de base calculada nos sete dias anteriores.

## <a name="does-my-app-definitely-have-a-problem"></a>A minha aplicação tem algum problema?
Não, uma notificação não significa que a sua aplicação definitivamente tem um problema. Embora uma degradação no rácio entre vestígios "bons" e "maus" possa indicar um problema de aplicação, esta mudança de rácio pode ser benigna. Por exemplo, o aumento pode dever-se a um novo fluxo na aplicação que emite mais vestígios "maus" do que os fluxos existentes).

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para suporte no processo de diagnóstico:
1. **A triagem.** A notificação mostra quantas operações são afetadas. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** O problema está a afetar todo o tráfego, ou apenas alguma operação? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** Pode utilizar os itens e relatórios relacionados que se ligam à informação de suporte, para ajudá-lo a diagnosticar ainda mais o problema.
