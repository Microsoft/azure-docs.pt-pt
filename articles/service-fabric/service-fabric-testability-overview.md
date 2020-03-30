---
title: Visão geral do Serviço de Análise de Falhas
description: Este artigo descreve o Serviço de Análise de Falhas em Tecido de Serviço para induzir falhas e executar cenários de teste contra os seus serviços.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d5c770a4d823ebe9b2700b081c407c54dd1d18a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465567"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introdução ao Serviço de Análise de Falhas
O Serviço de Análise de Falhas foi concebido para testar serviços que são construídos em Tecido de Serviço Microsoft Azure. Com o Serviço de Análise de Falhas pode induzir falhas significativas e executar cenários completos de teste contra as suas aplicações. Estas falhas e cenários exercitam e validam os inúmeros estados e transições que um serviço irá experimentar ao longo da sua vida, tudo de forma controlada, segura e consistente.

As ações são as falhas individuais que visam um serviço para testá-lo. Um desenvolvedor de serviços pode usá-los como blocos de construção para escrever cenários complicados. Por exemplo:

* Reinicie um nó para simular qualquer número de situações em que uma máquina ou VM é reiniciada.
* Mova uma réplica do seu serviço imponente para simular o equilíbrio de carga, a falha ou a atualização da aplicação.
* Invocar a perda de quórum num serviço imponente para criar uma situação em que as operações de escrita não podem prosseguir porque não há réplicas suficientes de "back-up" ou "secundário" para aceitar novos dados.
* Invoque a perda de dados num serviço imponente para criar uma situação em que todo o estado de memória é completamente eliminado.

Os cenários são operações complexas compostas por uma ou mais ações. O Serviço de Análise de Falhas fornece dois cenários completos embutidos:

* Cenário de caos
* Cenário de failover

## <a name="testing-as-a-service"></a>Teste como serviço
O Serviço de Análise de Falhas é um serviço de sistema de tecido de serviço que é automaticamente iniciado com um cluster de Tecido de Serviço. Este serviço funciona como hospedeiro para injeção de falhas, execução de cenário de teste e análise de saúde. 

![Serviço de Análise de Falhas][0]

Quando uma ação de avaria ou cenário de teste é iniciado, um comando é enviado para o Serviço de Análise de Falhas para executar a ação de falha ou cenário de teste. O Serviço de Análise de Falhas é imponente para que possa executar falhas e cenários de forma fiável e validar resultados. Por exemplo, um cenário de teste de longa duração pode ser executado de forma fiável pelo Serviço de Análise de Falhas. E como os testes estão a ser executados dentro do cluster, o serviço pode examinar o estado do cluster e os seus serviços para fornecer informações mais aprofundadas sobre falhas.

## <a name="testing-distributed-systems"></a>Testar sistemas distribuídos
O Service Fabric facilita significativamente o trabalho de escrita e gestão de aplicações escaláveis distribuídas. O Serviço de Análise de Falhas facilita o teste de uma aplicação distribuída. Há três questões principais que precisam de ser resolvidas durante os testes:

1. Simular/gerar falhas que possam ocorrer em cenários do mundo real: Um dos aspetos importantes do Tecido de Serviço é que permite que aplicações distribuídas recuperem de várias falhas. No entanto, para testar que a aplicação é capaz de recuperar destas falhas, precisamos de um mecanismo para simular/gerar estas falhas no mundo real num ambiente de teste controlado.
1. A capacidade de gerar falhas correlacionadas: Falhas básicas no sistema, tais como falhas de rede e falhas na máquina, são fáceis de produzir individualmente. Gerar um número significativo de cenários que podem acontecer no mundo real como resultado das interações destas falhas individuais não é trivial.
1. Experiência unificada em vários níveis de desenvolvimento e implantação: Existem muitos sistemas de injeção de falhas que podem fazer vários tipos de falhas. No entanto, a experiência em todas estas coisas é fraca quando se passa de cenários de desenvolvimento de uma caixa, para fazer os mesmos testes em grandes ambientes de teste, para usá-los para testes em produção.

Embora existam muitos mecanismos para resolver estes problemas, falta um sistema que faz o mesmo com garantias necessárias- todo o caminho de um ambiente de desenvolvimento de uma caixa, para testar em aglomerados de produção -- está em falta. O Serviço de Análise de Falhas ajuda os desenvolvedores de aplicações a concentrarem-se em testar a sua lógica de negócio. O Serviço de Análise de Falhas fornece todas as capacidades necessárias para testar a interação do serviço com o sistema distribuído subjacente.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simular/gerar cenários de fracasso no mundo real
Para testar a robustez de um sistema distribuído contra falhas, precisamos de um mecanismo para gerar falhas. Enquanto em teoria, gerar uma falha como um nó para baixo parece fácil, começa a atingir o mesmo conjunto de problemas de consistência que o Service Fabric está a tentar resolver. Como exemplo, se quisermos desligar um nó, o fluxo de trabalho necessário é o seguinte:

1. Do cliente, emita um pedido de nó de encerramento.
1. Envie o pedido para o nó direito.
   
    a. Se o nó não for encontrado, deve falhar.
   
    b. Se o nó for encontrado, só deve voltar se o nó estiver desligado.

Para verificar a falha numa perspetiva de teste, o teste precisa de saber que quando esta falha é induzida, a falha realmente acontece. A garantia que a Fabric de Serviço fornece é que ou o nó vai descer ou já estava em baixo quando o comando chegou ao nó. Em qualquer dos casos, o teste deve ser capaz de raciocinar corretamente sobre o Estado e ter sucesso ou falhar corretamente na sua validação. Um sistema implementado fora do Service Fabric para fazer o mesmo conjunto de falhas poderia atingir muitos problemas de rede, hardware e software, o que o impediria de fornecer as garantias anteriores. Na presença dos problemas anteriormente referidos, o Service Fabric irá reconfigurar o estado de cluster para resolver as questões, pelo que o Serviço de Análise de Falhas ainda poderá dar o conjunto certo de garantias.

### <a name="generating-required-events-and-scenarios"></a>Gerando eventos e cenários necessários
Embora simular uma falha no mundo real consistentemente é difícil de começar, a capacidade de gerar falhas correlacionadas é ainda mais difícil. Por exemplo, uma perda de dados ocorre num serviço persistiu quando acontecem as seguintes coisas:

1. Apenas um quórum de escrita das réplicas é apanhado na replicação. Todas as réplicas secundárias ficam atrás das primárias.
1. O quórum de escrita desce por causa das réplicas que descem (devido a um pacote de código ou nó a descer).
1. O quórum de escrita não pode voltar a subir porque os dados das réplicas são perdidos (devido à corrupção do disco ou à reimagem da máquina).

Estas falhas correlacionadas acontecem no mundo real, mas não tão frequentemente como falhas individuais. A capacidade de testar estes cenários antes de acontecerem na produção é fundamental. Ainda mais importante é a capacidade de simular estes cenários com cargas de trabalho de produção em circunstâncias controladas (a meio do dia com todos os engenheiros no convés). Isso é muito melhor do que acontecer pela primeira vez em produção às 2:00 da manhã.

### <a name="unified-experience-across-different-environments"></a>Experiência unificada em diferentes ambientes
A prática tradicionaltem sido a criação de três conjuntos diferentes de experiências, uma para o ambiente de desenvolvimento, uma para testes e outra para produção. O modelo era:

1. No ambiente de desenvolvimento, produza transições estatais que permitam testes unitários de métodos individuais.
1. No ambiente de teste, produza falhas para permitir testes de ponta a ponta que exerçam vários cenários de falha.
1. Mantenha o ambiente de produção imaculado para evitar eventuais falhas não naturais e para garantir que haja uma resposta humana extremamente rápida ao fracasso.

No Tecido de Serviço, através do Serviço de Análise de Falhas, propomos dar a volta a isto e utilizar a mesma metodologia do ambiente de desenvolvimento para a produção. Há duas formas de o conseguir:

1. Para induzir falhas controladas, utilize as APIs do Serviço de Análise de Falhas a partir de um ambiente de uma caixa até aos clusters de produção.
1. Para dar ao cluster uma febre que causa a indução automática de falhas, utilize o Serviço de Análise de Falhas para gerar falhas automáticas. Controlar a taxa de falhas através da configuração permite que o mesmo serviço seja testado de forma diferente em diferentes ambientes.

Com o Tecido de Serviço, embora a escala de falhas fosse diferente nos diferentes ambientes, os mecanismos reais seriam idênticos. Isto permite um pipeline de código-implementação muito mais rápido e a capacidade de testar os serviços sob cargas do mundo real.

## <a name="using-the-fault-analysis-service"></a>Utilização do Serviço de Análise de Falhas
**C #**

As funcionalidades do Serviço de Análise de Falhas encontram-se no espaço de nome System.Fabric no pacote Microsoft.ServiceFabric NuGet. Para utilizar as funcionalidades do Serviço de Análise de Falhas, inclua o pacote de nuget como referência no seu projeto.

**PowerShell**

Para utilizar o PowerShell, tem de instalar o SDK de Tecido de Serviço. Depois de instalado o SDK, o módulo ServiceFabric PowerShell é carregado automaticamente para utilizar.

## <a name="next-steps"></a>Passos seguintes
Para criar serviços verdadeiramente à escala de nuvem, é fundamental garantir, antes e depois da implantação, que os serviços possam suportar falhas no mundo real. No mundo dos serviços de hoje, a capacidade de inovar rapidamente e mover o código para a produção rapidamente é muito importante. O Serviço de Análise de Falhas ajuda os desenvolvedores de serviços a fazer precisamente isso.

Comece a testar as suas aplicações e serviços utilizando os cenários de [teste incorporados,](service-fabric-testability-scenarios.md)ou autor dos seus próprios cenários de teste utilizando as ações de [falha](service-fabric-testability-actions.md) fornecidas pelo Serviço de Análise de Falhas.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
