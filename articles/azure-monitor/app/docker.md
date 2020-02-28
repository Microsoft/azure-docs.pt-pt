---
title: Monitor Docker aplicações em Insights de Aplicação Azure  Microsoft Docs
description: Contadores, eventos e exceções do Docker perf podem ser exibidos em Application Insights, juntamente com a telemetria das aplicações contentorizadas.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ff3d6a2b31425a3fb6857c8c6aed45f7ef18d64
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669612"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitor Docker aplicações em Insights de Aplicação (Depreciado)

> [!NOTE]
> Esta solução foi depreciada. Para saber mais sobre os nossos investimentos atuais na monitorização de contentores, recomendamos verificar o [Monitor Azure para contentores.](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)

Eventos de ciclo de vida e contadores de desempenho dos recipientes [docker](https://www.docker.com/) podem ser cartografados em Insights de Aplicação. Instale a imagem De Insights de [Aplicação](https://hub.docker.com/r/microsoft/applicationinsights/) num recipiente no seu anfitrião e apresentará contadores de desempenho para o anfitrião, bem como para as outras imagens.

Com o Docker, distribui as suas aplicações em recipientes leves com todas as dependências. Vão funcionar com qualquer máquina de acolhimento que comande um Motor Docker.

Ao executar a imagem de Insights de [Aplicação](https://hub.docker.com/r/microsoft/applicationinsights/) no seu anfitrião Docker, obtém estes benefícios:

* Telemetria de ciclo de vida sobre todos os recipientes que circulam no hospedeiro - comece, pare, e assim por diante.
* Contadores de desempenho para todos os recipientes. CPU, memória, uso da rede, e muito mais.
* Se [instalou o Application Insights SDK para java](../../azure-monitor/app/java-get-started.md) nas aplicações que estão a funcionar nos contentores, toda a telemetria dessas aplicações terá propriedades adicionais identificando o recipiente e a máquina anfitriã. Assim, por exemplo, se tiver casos de uma aplicação em execução em mais de um anfitrião, pode facilmente filtrar a sua telemetria de aplicações pelo anfitrião.

## <a name="set-up-your-application-insights-resource"></a>Configurar o seu recurso Insights de Aplicação

1. Assine no [portal do Microsoft Azure](https://azure.com) e abra o recurso Application Insights para a sua aplicação; ou [criar um novo.](../../azure-monitor/app/create-new-resource.md ) 
   
    *Que recurso devo usar?* Se as aplicações que está a executar no seu anfitrião foram desenvolvidas por outra pessoa, então precisa [de criar um novo recurso Application Insights](../../azure-monitor/app/create-new-resource.md ). É aqui que se vê e analisa a telemetria. (Selecione 'General' para o tipo de aplicação.)
   
    Mas se você é o desenvolvedor das aplicações, então esperamos que você adicione Application [Insights SDK](../../azure-monitor/app/java-get-started.md) a cada uma delas. Se todos eles são realmente componentes de uma única aplicação de negócio, então você pode configurar todos eles para enviar telemetria para um recurso, e você usará esse mesmo recurso para exibir o ciclo de vida e dados de desempenho do Docker. 
   
    Um terceiro cenário é que desenvolveu a maioria das aplicações, mas está a usar recursos separados para mostrar a sua telemetria. Nesse caso, provavelmente também quer criar um recurso separado para os dados do Docker.

2. Clique na entrega **do Essencial** e copie a Chave de Instrumentação. Usa isto para dizer ao SDK para onde enviar a sua telemetria.

Mantenha a janela do navegador à mão, pois em breve voltará a olhar para a sua telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Executar o monitor de Insights de Aplicação no seu anfitrião

Agora que tem um lugar para exibir a telemetria, pode configurar a aplicação contentorizada que irá colecioná-la e enviá-la.

1. Ligue-se ao seu anfitrião Docker.
2. Edite a sua chave de instrumentação neste comando e, em seguida, execute-a:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Apenas uma imagem de Application Insights é necessária por anfitrião Docker. Se a sua aplicação for implantada em vários anfitriões do Docker, repita o comando em cada hospedeiro.

## <a name="update-your-app"></a>Atualize a sua aplicação
Se a sua aplicação for instrumentada com o SDK de Insights de [Aplicação para Java,](../../azure-monitor/app/java-get-started.md)adicione a seguinte linha no ficheiro ApplicationInsights.xml no seu projeto, sob o elemento `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Isto adiciona informações do Docker, como contentor estivae e id de hospedaria a todos os itens de telemetria enviados da sua aplicação.

## <a name="view-your-telemetry"></a>Ver a telemetria
Volte ao seu recurso Application Insights no portal Azure.

Clique no azulejo do Docker.

Em breve verá os dados que chegam da aplicação Docker, especialmente se tiver outros contentores a funcionar no seu motor Docker.

### <a name="docker-container-events"></a>Eventos de contentores docker
![Exemplo](./media/docker/13.png)

Para investigar eventos individuais, clique em [Procurar](../../azure-monitor/app/diagnostic-search.md). Procure e filtre para encontrar os eventos que deseja. Clique em qualquer evento para obter mais detalhes.

### <a name="exceptions-by-container-name"></a>Exceções por nome de contentor
![Exemplo](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexto docker adicionado à telemetria de aplicativos
A telemetria de pedidos enviada da aplicação instrumentada com AI SDK, é enriquecida com informações de contexto do Docker.

## <a name="q--a"></a>Q e A
*O que é que os Insights de Aplicação me dão que não posso obter do Docker?*

* Desagregação detalhada dos contadores de desempenho por recipiente e imagem.
* Integre os dados do recipiente e da aplicação num painel de instrumentos.
* [Exportar telemetria](export-telemetry.md) para análise mais aprofundada a uma base de dados, Power BI ou outro dashboard.

*Como recebo telemetria da própria app?*

* Instale o SDK de Insights de Aplicação na aplicação. Saiba como: [Java web apps](../../azure-monitor/app/java-get-started.md), Windows web [apps](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes

* [Insights de aplicação para Java](../../azure-monitor/app/java-get-started.md)
* [Insights de aplicação para Nó.js](../../azure-monitor/app/nodejs.md)
* [Insights de aplicação para ASP.NET](../../azure-monitor/app/asp-net.md)
