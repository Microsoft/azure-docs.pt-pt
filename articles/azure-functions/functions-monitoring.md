---
title: Monitorizar as Funções do Azure
description: Aprenda a utilizar insights de aplicação Azure com funções Azure para monitorizar a execução da função.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperfq2, devx-track-js, devx-track-azurecli
ms.openlocfilehash: b27fb14341e07683d66418485158a94c18e7a997
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748165"
---
# <a name="monitor-azure-functions"></a>Monitorizar as Funções do Azure

[A Azure Functions](functions-overview.md) oferece integração incorporada com [a Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para monitorizar funções. Este artigo fornece uma visão geral das capacidades de monitorização fornecidas pela Azure para monitorizar as Funções Azure.

O Application Insights recolhe dados de registo, desempenho e erro. Ao detetar automaticamente anomalias de desempenho e apresentando ferramentas de análise poderosas, pode diagnosticar mais facilmente problemas e entender melhor como as suas funções são utilizadas. Estas ferramentas são concebidas para ajudá-lo a melhorar continuamente o desempenho e a usabilidade das suas funções. Pode até usar o Application Insights durante o desenvolvimento de projetos de aplicações de funções locais. Para mais informações, consulte [o que é Insights de Aplicação?](../azure-monitor/app/app-insights-overview.md)

Como a instrumentação de Insights de Aplicação é incorporada em Funções Azure, você precisa de uma chave de instrumentação válida para ligar a sua aplicação de função a um recurso De Insights de Aplicação. A chave de instrumentação é adicionada às definições da sua aplicação à medida que cria o recurso de aplicação de função em Azure. Se a sua aplicação de função ainda não tiver esta chave, pode [defini-la manualmente](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Preços e limites de Insights de Aplicação

Pode experimentar a integração de Application Insights com funções Azure gratuitamente, apresentando um limite diário para a quantidade de dados que são processados gratuitamente.

Se ativar o Applications Insights durante o desenvolvimento, poderá atingir este limite durante os testes. O Azure fornece notificações de portal e e-mail quando se aproxima do seu limite diário. Se perder esses alertas e atingir o limite, novos registos não aparecerão nas consultas de Insights de Aplicação. Esteja atento ao limite para evitar tempos desnecessários de resolução de problemas. Para obter mais informações, consulte [Gerir os preços e o volume de dados em Insights de Aplicação](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> A Application Insights tem uma funcionalidade [de amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir demasiados dados de telemetria em execuções concluídas em momentos de carga máxima. A amostragem é ativada por defeito. Se parecer faltar dados, poderá ter de ajustar as definições de amostragem para se adaptar ao seu cenário específico de monitorização. Para saber mais, consulte [a amostragem de Configure.](configure-monitoring.md#configure-sampling)

A lista completa das funcionalidades de Insights de Aplicação disponível para a sua aplicação de funções é detalhada em [Funcionalidades suportadas por Insights para Funções Azure.](../azure-monitor/app/azure-functions-supported-features.md)

## <a name="application-insights-integration"></a>Integração de Insights de Aplicação

Normalmente, cria uma instância de Insights de Aplicação quando cria a sua aplicação de função. Neste caso, a chave de instrumentação necessária para a integração já está definida como uma definição de aplicação denominada *APPINSIGHTS_INSTRUMENTATIONKEY* . Se, por alguma razão, a sua aplicação de função não tiver o conjunto de teclas de instrumentação, precisa de ativar a [integração do Application Insights](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Recolha de dados de telemetria

Com a integração de Application Insights ativada, os dados de telemetria são enviados para a sua instância de Insights de Aplicação conectada. Estes dados incluem registos gerados pelo anfitrião Funções, vestígios escritos a partir do código das suas funções e dados de desempenho. 

>[!NOTE]
>Além dos dados das suas funções e do anfitrião Funções, também pode recolher dados do controlador de [escala de funções.](#scale-controller-logs)   

### <a name="log-levels-and-categories"></a>Níveis e categorias de registos

Quando escrever vestígios do seu código de aplicação, deve atribuir um nível de registo aos vestígios. Os níveis de registo fornecem uma forma de limitar a quantidade de dados que são recolhidos a partir dos seus vestígios.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Para saber mais sobre os níveis de registo, consulte [os níveis de registo de configuração](configure-monitoring.md#configure-log-levels).

Ao atribuir itens registados a uma categoria, tem mais controlo sobre a telemetria gerada a partir de fontes específicas na sua aplicação de função. As categorias facilitam a análise sobre os dados recolhidos. Os vestígios escritos do seu código de função são atribuídos a categorias individuais com base no nome da função. Para saber mais sobre categorias, consulte [as categorias Configure.](configure-monitoring.md#configure-categories)

### <a name="custom-telemetry-data"></a>Dados de telemetria personalizados

Em [C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) e [JavaScript,](functions-reference-node.md#log-custom-telemetry)pode utilizar um SDK de Insights de Aplicação para escrever dados de telemetria personalizados.

### <a name="dependencies"></a>Dependências

Começando pela versão 2.x de Funções, o tempo de execução recolhe automaticamente dados sobre dependências para encadernações que utilizam determinados SDKs do cliente. A Application Insights recolhe dados sobre as seguintes dependências:

+ Azure Cosmos DB 
+ Hubs de Eventos do Azure
+ Azure Service Bus
+ Serviços de Armazenamento Azure (Blob, Fila e Tabela)

Os pedidos HTTP e as chamadas de base de dados que utilizam `SqlClient` também são capturados. Para obter a lista completa de dependências suportadas pela Application Insights, consulte [automaticamente as dependências rastreadas](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

O Application Insights gera um mapa de _aplicações_ de dados de dependência recolhidos. Segue-se um exemplo de um mapa de aplicação de uma função de gatilho HTTP com uma ligação de saída de armazenamento de fila.  

![Mapa de aplicação com dependência](./media/functions-monitoring/app-map.png)

As dependências são escritas ao `Information` nível. Se filtrar ou `Warning` ultrapassar, não verá os dados de dependência. Além disso, a recolha automática de dependências ocorre num âmbito não-utilizador. Para capturar dados de dependência, certifique-se de que o nível está definido para pelo menos `Information` fora do âmbito do utilizador ( ) no seu `Function.<YOUR_FUNCTION_NAME>.User` anfitrião.

Além da recolha automática de dados de dependência, também pode utilizar um dos SDKs de aplicação específicos da linguagem para escrever informações de dependência personalizadas para os registos. Para um exemplo como escrever dependências personalizadas, consulte um dos seguintes exemplos específicos da linguagem:

+ [Registar telemetria personalizada em funções C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Registar telemetria personalizada em funções JavaScript](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Escrevendo para registos 

A forma como escreve para os registos e as APIs que utiliza depende do idioma do seu projeto de aplicação de funções.   
Consulte o guia de desenvolvimento para saber mais sobre a escrita de registos a partir das suas funções.

+ [C# (biblioteca de classe.NET)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="streaming-logs"></a>Registos de streaming

Ao desenvolver uma aplicação, muitas vezes quer ver o que está a ser escrito nos registos em tempo real quando corre em Azure.

Existem duas formas de ver um fluxo dos dados de registo que estão a ser gerados pelas suas execuções de funções.

* **Streaming de registo incorporado** : a plataforma Serviço de Aplicações permite-lhe visualizar um fluxo de ficheiros de registo de aplicações. Este fluxo é equivalente à saída observada quando desorga as suas funções durante o [desenvolvimento local](functions-develop-local.md) e quando utiliza o separador **Teste** no portal. Todas as informações baseadas em registos são apresentadas. Para obter mais informações, consulte [os registos de stream](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Este método de streaming suporta apenas uma única instância, e não pode ser usado com uma aplicação em execução no Linux num plano de Consumo.

* **Live Metrics Stream** : quando a sua aplicação de função está [ligada ao Application Insights](configure-monitoring.md#enable-application-insights-integration), pode visualizar dados de registo e outras métricas em tempo real no portal Azure utilizando o Live [Metrics Stream](../azure-monitor/app/live-stream.md). Utilize este método ao monitorizar as funções em execução em múltiplas instâncias ou em Linux num plano de consumo. Este método utiliza [dados amostrados.](configure-monitoring.md#configure-sampling)

Os fluxos de log podem ser vistos tanto no portal como na maioria dos ambientes de desenvolvimento local. Para saber como ativar as correntes de registo, consulte [Ativar os registos de execução em funções de Azure](streaming-logs.md).

## <a name="diagnostic-logs"></a>Registos de diagnósticos

_Esta funcionalidade está em pré-visualização._ 

A Application Insights permite exportar dados de telemetria para armazenamento de longo prazo ou outros serviços de análise.  

Uma vez que as funções também se integram com o Azure Monitor, também pode utilizar definições de diagnóstico para enviar dados de telemetria para vários destinos, incluindo registos do Monitor Azure. Para saber mais, consulte [as funções de Azure monitor de monitorização com registos do monitor Azure](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Troncos de controlador de escala

_Esta funcionalidade está em pré-visualização._ 

O [controlador de escala Azure Functions](./functions-scale.md#runtime-scaling) monitoriza as instâncias do anfitrião Azure Functions em que a sua aplicação é executada. Este controlador toma decisões sobre quando adicionar ou remover casos com base no desempenho atual. Pode ter o controlador de escala a emitir registos para o Application Insights para entender melhor as decisões que o controlador de escala está a tomar para a sua aplicação de função. Também pode armazenar os registos gerados no armazenamento Blob para análise por outro serviço. 

Para ativar esta funcionalidade, adicione uma definição de aplicação nomeada `SCALE_CONTROLLER_LOGGING_ENABLED` para as definições da aplicação da sua função. Para saber como, consulte [os registos do controlador de escala Configure](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Comunicar problemas

Para reportar um problema com a integração de Application Insights em Funções, ou para fazer uma sugestão ou pedido, [criar um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

* [Application Insights](/azure/application-insights/)
* [Registo do núcleo de ASP.NET](/aspnet/core/fundamentals/logging/)
