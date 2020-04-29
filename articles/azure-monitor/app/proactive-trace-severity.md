---
title: Degradação na relação de gravidade de vestígios - Insights de aplicação azure
description: Monitorize os rastreios de aplicação com Insights de aplicação Azure para padrões incomuns na telemetria de vestígios com deteção inteligente .
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671686"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradação na relação de gravidade dos vestígios (pré-visualização)

Os vestígios são amplamente utilizados em aplicações, pois ajudam a contar a história do que acontece nos bastidores. Quando as coisas correm mal, os vestígios proporcionam uma visibilidade crucial na sequência de eventos que conduzem ao estado indesejado. Embora os vestígios sejam geralmente desestruturados, há uma coisa que pode ser aprendida concretamente com eles – o seu nível de gravidade. No estado estável de uma aplicação, esperamos que a relação entre os traços "bons"*(Info* e *Verbose)* e os traços "maus"*(Aviso,* *Erro*e *Crítico*) se mantenha estável. O pressuposto é que os vestígios "maus" podem ocorrer regularmente, em certa medida, devido a qualquer número de razões (questões transitórias da rede, por exemplo). Mas quando um problema real começa a crescer, geralmente manifesta-se como um aumento na proporção relativa de traços "maus" vs traços "bons". Insights de aplicação Deteção inteligente analisa automaticamente os vestígios registados pela sua aplicação, e pode avisá-lo sobre padrões incomuns na gravidade da sua telemetria de vestígios.

Esta funcionalidade não requer configuração especial, para além de configurar o registo de rastreios para a sua aplicação (veja como configurar um ouvinte de registo de rastreio para [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) ou [Java).](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs) Está ativo quando a sua aplicação gera telemetria de exceção suficiente.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu receberia este tipo de notificação de deteção inteligente?
Pode obter este tipo de notificação se a relação entre vestígios "bons" (vestígios registados com um nível de *Info* ou *Verbose)* e vestígios "maus" (vestígios registados com um nível de *Aviso,* *Erro*ou *Fatal*) estiver a degradar-se num dia específico, em comparação com uma linha de base calculada nos sete dias anteriores.

## <a name="does-my-app-definitely-have-a-problem"></a>A minha aplicação tem um problema?
Não, uma notificação não significa que a sua aplicação definitivamente tenha um problema. Embora uma degradação no rácio entre vestígios "bons" e "maus" possa indicar um problema de aplicação, esta alteração no rácio pode ser benigna. Por exemplo, o aumento pode dever-se a um novo fluxo na aplicação que emite mais vestígios "maus" do que os fluxos existentes).

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para apoiar no processo de diagnóstico:
1. **A triagem.** A notificação mostra quantas operações são afetadas. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** O problema está a afetar todo o tráfego, ou apenas alguma operação? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** Pode utilizar os itens e relatórios relacionados que liguem à informação de apoio, para ajudá-lo a diagnosticar o problema.


