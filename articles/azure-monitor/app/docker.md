---
title: Monitor estivador aplicações em Azure Application Insights / Microsoft Docs
description: Os contadores perf, eventos e exceções do Docker podem ser exibidos no Application Insights, juntamente com a telemetria das aplicações contentorizadas.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 272b4e8af7b1ed3d01d8af0979b56954585f795d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977578"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Aplicações monitor Docker em Insights de Aplicação (Deprecada)

> [!NOTE]
> Esta solução foi depreocada. Para saber mais sobre os nossos investimentos atuais na monitorização de contentores, recomendamos que se verifique o [Azure Monitor para obter contentores.](../insights/container-insights-overview.md)

Eventos de ciclo de vida e contadores de desempenho de contentores [Docker](https://www.docker.com/) podem ser cartografados em Application Insights. Instale a imagem ['Insights'](https://hub.docker.com/r/microsoft/applicationinsights/) de Aplicação num recipiente no seu anfitrião e apresentará contadores de desempenho para o anfitrião, bem como para as outras imagens.

Com o Docker, distribui as suas aplicações em recipientes leves, com todas as dependências. Vão funcionar em qualquer máquina hospedeira que comanda um Motor Docker.

Quando executar a [imagem Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) no seu anfitrião Docker, obtém estes benefícios:

* Telemetria de ciclo de vida sobre todos os recipientes que correm no hospedeiro - comece, pare, e assim por diante.
* Contadores de desempenho para todos os contentores. CPU, memória, uso da rede, e muito mais.
* Se [instalou o Application Insights SDK para a Java](./java-get-started.md) nas aplicações em execução nos contentores, toda a telemetria dessas aplicações terá propriedades adicionais que identifiquem o contentor e a máquina hospedeira. Assim, por exemplo, se tiver casos de uma aplicação em execução em mais de um anfitrião, pode facilmente filtrar a telemetria da sua aplicação por anfitrião.

## <a name="set-up-your-application-insights-resource"></a>Configurar o seu recurso Application Insights

1. Inscreva-se no [portal Microsoft Azure](https://azure.com) e abra o recurso Application Insights para a sua aplicação; ou [criar um novo.](./create-new-resource.md) 
   
    *Que recurso devo usar?* Se as aplicações que está a executar no seu anfitrião foram desenvolvidas por outra pessoa, então precisa de [criar um novo recurso Application Insights](./create-new-resource.md). É aqui que vê e analisa a telemetria. (Selecione 'General' para o tipo de aplicação.)
   
    Mas se você é o desenvolvedor das aplicações, então esperamos que tenha [adicionado App Insights SDK](./java-get-started.md) a cada uma delas. Se forem todos componentes de uma única aplicação de negócio, então pode configurar todos eles para enviar telemetria para um recurso, e usará o mesmo recurso para exibir o ciclo de vida e os dados de desempenho do Docker. 
   
    Um terceiro cenário é que desenvolveu a maioria das aplicações, mas está a usar recursos separados para exibir a sua telemetria. Nesse caso, provavelmente também pretende criar um recurso separado para os dados do Docker.

2. Clique no **drop-down Essentials** e copie a tecla de instrumentação. Usa isto para dizer ao SDK para onde enviar a sua telemetria.

Mantenha a janela do navegador à mão, pois voltará logo para ver a sua telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Execute o monitor Application Insights no seu anfitrião

Agora que tem um lugar para mostrar a telemetria, pode configurar a aplicação contentorizada que irá recolher e enviar.

1. Ligue-se ao seu anfitrião Docker.
2. Edite a sua chave de instrumentação neste comando e, em seguida, execute-a:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Apenas uma imagem de Insights de Aplicação é necessária por anfitrião Docker. Se a sua aplicação for implantada em vários anfitriões do Docker, repita o comando em cada hospedeiro.

## <a name="update-your-app"></a>Atualizar a sua aplicação
Se a sua aplicação for instrumentalizada com o [SDK de Insights de Aplicação para Java,](./java-get-started.md)adicione a seguinte linha no ficheiro ApplicationInsights.xml no seu projeto, sob o `<TelemetryInitializers>` elemento:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Isto adiciona informações do Docker, tais como o container e o ID do anfitrião a todos os itens de telemetria enviados da sua aplicação.

## <a name="view-your-telemetry"></a>Ver a telemetria
Volte ao seu recurso Application Insights no portal Azure.

Clique no azulejo do Docker.

Em breve verá os dados que chegam da aplicação Docker, especialmente se tiver outros contentores a funcionar no seu motor Docker.

### <a name="docker-container-events"></a>Eventos de contentores de estivadores
![A screenshot mostra a seleção de Pesquisa, uma janela de resultados de pesquisa de diagnóstico com uma seta apontando de um Evento Personalizado para uma janela com dados personalizados.](./media/docker/13.png)

Para investigar eventos individuais, clique em [Procurar.](./diagnostic-search.md) Procure e filtre para encontrar os eventos que deseja. Clique em qualquer evento para obter mais detalhes.

### <a name="exceptions-by-container-name"></a>Exceções por nome de contentor
![A screenshot mostra a página Metrics Explorer com um gráfico selecionado e a janela de detalhes da Tabela aberta no lado direito.](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexto estivador adicionado à telemetria de aplicações
O pedido de telemetria enviada a partir da aplicação instrumentada com AI SDK, é enriquecido com informações de contexto do Docker.

## <a name="q--a"></a>Perguntas e Respostas
*O que é que a Application Insights me dá que não posso obter do Docker?*

* Desagregação detalhada dos contadores de desempenho por contentor e imagem.
* Integrar dados de contentores e aplicativos num só painel de instrumentos.
* [Exporte telemetria](export-telemetry.md) para análise posterior a uma base de dados, Power BI ou outro dashboard.

*Como consigo telemetria da própria app?*

* Instale o App Insights SDK na aplicação. Saiba como: [aplicativos web Java,](./java-get-started.md) [aplicativos web Windows](./asp-net.md).

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes

* [Insights de Aplicação para Java](./java-get-started.md)
* [Application Insights para o Node.js](./nodejs.md)
* [Insights de Aplicação para ASP.NET](./asp-net.md)

