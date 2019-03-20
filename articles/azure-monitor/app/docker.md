---
title: Monitorizar aplicações do Docker no Azure Application Insights | Documentos da Microsoft
description: Contadores de desempenho do docker, eventos e exceções podem ser exibidas no Application Insights, juntamente com a telemetria a partir das aplicações em contentores.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 115e2d6b041ecc3f38a2a6438d90777da9660221
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996469"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorizar aplicações do Docker no Application Insights (preterido)

> [!NOTE]
> Esta solução foi preterida. Para saber mais sobre os investimentos atuais na monitorização de contentores, recomendamos dar uma olhada [Monitor do Azure para contentores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

Os contadores de desempenho e eventos de ciclo de vida da [Docker](https://www.docker.com/) contentores podem ser colocada em gráficos no Application Insights. Instalar o [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) imagem num contentor no seu anfitrião e ele irá apresentar os contadores de desempenho para o anfitrião, bem como para as outras imagens.

Com o Docker, distribuir as suas aplicações em contentores leves, completos com todas as dependências. Que serão executados em qualquer máquina de anfitrião que executa um motor do Docker.

Quando executa o [imagem do Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) no anfitrião do Docker, obtém esses benefícios:

* Telemetria de ciclo de vida sobre todos os contentores em execução no anfitrião - iniciar, parar e assim por diante.
* Contadores de desempenho para todos os contentores. CPU, memória, utilização de rede e muito mais.
* Se [instalado o SDK do Application Insights para Java](../../azure-monitor/app/java-get-started.md) nas aplicações em execução nos contentores, toda a telemetria desses aplicativos terão propriedades adicionais, identificar a máquina de contentor e o anfitrião. Por exemplo, se tiver instâncias de uma aplicação em execução no anfitrião mais do que uma, pode filtrar facilmente a telemetria da sua aplicação por anfitrião.

## <a name="set-up-your-application-insights-resource"></a>Configurar o recurso do Application Insights

1. Inicie sessão no [portal do Microsoft Azure](https://azure.com) e abra o recurso do Application Insights para a sua aplicação; ou [criar um novo](../../azure-monitor/app/create-new-resource.md ). 
   
    *Qual o recurso que devo utilizar?* Se as aplicações que está a executar no seu anfitrião foram desenvolvidas por outra pessoa, em seguida, precisa [criar um novo recurso do Application Insights](../../azure-monitor/app/create-new-resource.md ). É onde ver e analisar a telemetria. (Selecione "Geral" para o tipo de aplicação).
   
    Mas se é desenvolvedor das aplicações, então Esperamos que [adicionado o SDK do Application Insights](../../azure-monitor/app/java-get-started.md) a cada um deles. Se eles são todos realmente componentes de um aplicativo de negócios único, em seguida, pode configurar todos eles para enviar telemetria para um recurso e que usará esse mesmo recurso para exibir os dados de desempenho e de ciclo de vida do Docker. 
   
    Um terceiro cenário é que desenvolveu a maioria dos aplicativos, mas estiver a utilizar recursos separados para apresentar a sua telemetria. Nesse caso, provavelmente também possível pretender criar um recurso separado para os dados de Docker.

2. Clique nas **Essentials** pendente e copie a chave de instrumentação. Utilize isto para indicar ao SDK para onde enviar a telemetria.

Mantenha essa janela do browser útil, uma vez que volte a ele em breve para ver a telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Executar o monitor de Application Insights no seu anfitrião

Agora que tem em algum lugar para apresentar a telemetria, pode configurar a aplicação em contentores que irá recolher e enviá-lo.

1. Ligar ao anfitrião do Docker.
2. Editar a sua chave de instrumentação para este comando e, em seguida, executá-lo:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Apenas uma imagem do Application Insights é necessária para cada anfitrião do Docker. Se a aplicação é implementada em vários anfitriões do Docker, em seguida, repita o comando em cada anfitrião.

## <a name="update-your-app"></a>Atualizar a sua aplicação
Se seu aplicativo está equipado com o [Application Insights SDK para Java](../../azure-monitor/app/java-get-started.md), adicione a seguinte linha no ficheiro applicationinsights. XML em seu projeto, no `<TelemetryInitializers>` elemento:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Esta ação adiciona informações de Docker, como o contentor e o id de anfitrião a todos os itens de telemetria enviado pela sua aplicação.

## <a name="view-your-telemetry"></a>Ver a telemetria
Volte ao recurso do Application Insights no portal do Azure.

Clicar no mosaico do Docker.

Verá em breve dados que chegam a partir da aplicação do Docker, especialmente se tiver outros contentores em execução no seu motor do Docker.

### <a name="docker-container-events"></a>Eventos de contentor do docker
![Exemplo](./media/docker/13.png)

Para investigar eventos individuais, clique em [pesquisa](../../azure-monitor/app/diagnostic-search.md). Procurar e filtrar para localizar os eventos que quiser. Clique em qualquer evento para obter mais detalhes.

### <a name="exceptions-by-container-name"></a>Exceções por nome do contentor
![Exemplo](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexto de docker adicionado para telemetria da aplicação
Telemetria de pedido enviada a partir da aplicação instrumentada com IA SDK, é enriquecida com informações de contexto do Docker.

## <a name="q--a"></a>P&R
*O que o Application Insights dá-me que eu não é possível a partir do Docker?*

* Divisão detalhada de contadores de desempenho ao contentor e imagem.
* Integre dados de contentor e a aplicação num dashboard.
* [Exportar telemetria](export-telemetry.md) para análise adicional para uma base de dados, Power BI ou outro dashboard.

*Como obter telemetria a partir da aplicação em si?*

* Instale o SDK do Application Insights na aplicação. Saiba como para: [Aplicações web Java](../../azure-monitor/app/java-get-started.md), [aplicações web do Windows](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos Seguintes

* [Application Insights para Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights para node. js](../../azure-monitor/app/nodejs.md)
* [Application Insights para ASP.NET](../../azure-monitor/app/asp-net.md)
