---
title: Telemetria do Application Insights no Visual Studio CodeLens | Microsoft Docs
description: Aceda rapidamente ao seu pedido do Application Insights e telemetria de exceção com o CodeLens no Visual Studio.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: d412441a7a80a14a0bc7a70de8020692f8f573d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642722"
---
# <a name="application-insights-telemetry-in-visual-studio-codelens"></a>Telemetria do Application Insights no Visual Studio CodeLens
Os métodos no código da sua aplicação Web podem ser anotados com telemetria sobre exceções de tempo de execução e tempos de resposta do pedido. Se instalar o [Azure Application Insights](./app-insights-overview.md) na sua aplicação, a telemetria é apresentada no Visual Studio [CodeLens](/visualstudio/ide/find-code-changes-and-other-history-with-codelens) - as notas na parte superior de cada função onde está habituado a ver informações úteis, como o número de locais onde a função é referida ou a última pessoa que a editou.

![CodeLens](./media/visual-studio-codelens/codelens-overview.png)

> [!NOTE]
> O Application Insights no CodeLens está disponível no Visual Studio 2015 Update 3 e posterior ou com a versão mais recente da [extensão do Developer Analytics Tools](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a). O CodeLens está disponível nas edições Enterprise e Professional do Visual Studio.
> 
> 

## <a name="where-to-find-application-insights-data"></a>Onde encontrar os dados do Application Insights
Procure a telemetria do Application Insights nos indicadores de CodeLens dos métodos de pedidos públicos da sua aplicação Web. Os indicadores de CodeLens são apresentados acima do método e de outras declarações de código C# e Visual Basic. Se os dados do Application Insights estiverem disponíveis para um método, verá indicadores para os pedidos e exceções, tais como "100 pedidos, 1% falhou" ou "10 exceções". Clique num indicador de CodeLens para obter mais detalhes. 

> [!TIP]
> Os indicadores de pedido e exceção do Application Insights podem demorar alguns segundos adicionais a carregar depois de aparecerem outros indicadores de CodeLens.
> 
> 

## <a name="exceptions-in-codelens"></a>Exceções no CodeLens
![A screenshot mostra 47 exceções exibidas no CodeLens.](./media/visual-studio-codelens/codelens-exceptions.png)

O indicador de CodeLens de exceção mostra o número de exceções que ocorreram nas últimas 24 horas a partir das 15 exceções que ocorrem mais frequentemente na sua aplicação durante esse período, ao mesmo tempo que processa o pedido servido pelo método.

Para ver mais detalhes, clique no indicador de CodeLens de exceções:

* A alteração de percentagem do número de exceções das 24 horas mais recentes em relação às 24 horas anteriores
* Escolha **aceda ao código** para navegar para o código de origem da função que gera a exceção
* Escolha **Pesquisa** para consultar todas as instâncias desta exceção que ocorreram nas últimas 24 horas
* Escolha **Tendência** para ver uma visualização de tendência das ocorrências desta exceção nas últimas 24 horas
* Escolha **Ver todas as exceções nesta aplicação** para consultar todas as exceções que ocorreram nas últimas 24 horas
* Escolha **Explorar tendências de exceção** para ver uma visualização de tendência para todas as exceções que ocorreram nas últimas 24 horas. 

> [!TIP]
> Se aparecer "0 exceções" no CodeLens, mas souber que existem exceções, certifique-se de que o recurso do Application Insights correto está selecionado no CodeLens. Para selecionar outro recurso, faça duplo clique no seu projeto no Explorador de Soluções e escolha **Application Insights > Escolher Origem da Telemetria**. O CodeLens é mostrado apenas para as 15 exceções mais frequentes que ocorreram na sua aplicação nas últimas 24 horas, por isso, se uma exceção for a 16.ª com mais frequência ou menos, verá "0 exceções". As exceções das vistas ASP.NET podem não aparecer nos métodos de controlador que geraram essas vistas.
> 
> [!TIP]
> Se vir "? exceções" no CodeLens, tem de associar a sua conta Azure ao Visual Studio ou a sua credencial de conta do Azure pode ter expirado. Em ambos os casos, clique em "? exceções" e selecione **Adicionar uma conta…** para introduzir as suas credenciais.
> 
> 

## <a name="requests-in-codelens"></a>Pedidos no CodeLens
![A Screenshot mostra detalhes de 684 pedidos, incluindo falhas de 7%.](./media/visual-studio-codelens/codelens-requests.png)

O indicador de CodeLens de pedido mostra o número de pedidos de HTTP que foram servidos por um método nas últimas 24 horas, bem como a percentagem desses pedidos que falharam.

Para ver mais detalhes, clique no indicador de CodeLens de pedidos:

* As alterações absolutas e de percentagem do número de pedidos, pedidos falhados e tempos médios de resposta durante as últimas 24 horas em comparação com as anteriores 24 horas
* A fiabilidade do método, calculada como a percentagem de pedidos que não falharam nas últimas 24 horas
* Escolha **Pesquisa** para procurar os pedidos ou os pedidos falhados para consultar todos os pedidos (falhados) que ocorreram nas últimas 24 horas
* Escolha **Tendência** para ver uma visualização de tendência de pedidos, pedidos falhados ou tempos médios de resposta nas últimas 24 horas.
* Escolha o nome do recurso Application Insights no canto superior esquerdo da vista de detalhes do CodeLens para alterar que recurso é a fonte de dados do CodeLens.

## <a name="next-steps"></a><a name="next"></a>Passos seguintes
* **[Trabalhando com a Application Insights no Estúdio Visual.](./visual-studio.md)** Procure telemetria, veja dados no CodeLens e configure o Application Insights. Tudo isto no Visual Studio. 
* **[Trabalhar com o portal Application Insights](./overview-dashboard.md)**. Dashboards, ferramentas de diagnóstico e análise poderosas, alertas, mapa de dependência em direto da aplicação e exportação de telemetria. 

